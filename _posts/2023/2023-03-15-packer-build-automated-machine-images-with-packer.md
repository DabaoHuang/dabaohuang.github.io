---
layout: post
title: '[ Packer ] build automated machine images with Packer'
subtitle: '[ Packer ] 自動化建立 AWS AMI'
category: Packer
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/packer.png "packer")

<div class="message">
    使用 Packer 自動化建立 AWS AMI
</div>


## 為什麼需要 Packer

如果每次建立 EC2 都靠手動安裝套件、調整設定、再做成 AMI，時間久了很容易遇到幾個問題：

1. 不同人做出來的 AMI 內容不一致
2. 忘記記錄某個安裝步驟，下一次很難重現
3. 基礎映像需要更新時，只能重新人工操作
4. 正式環境和測試環境的設定落差越來越大

Packer 的價值在於把「製作機器映像」這件事變成可以版控、可以重跑、可以交接的流程。對 AWS 來說，最常見的做法就是用一台暫時的 EC2 instance 作為 builder，安裝好需要的套件與設定後，再產生新的 AMI。

## 前置準備

開始前先確認幾件事：

1. 本機或 CI runner 已安裝 Packer
2. AWS credential 有建立 EC2、建立 AMI、建立 snapshot、刪除暫時資源的權限
3. `source_ami` 與 `region` 在同一個 AWS region
4. security group 或 subnet 設定允許 Packer SSH 進 builder instance
5. `ssh_username` 要符合來源 AMI 的預設使用者，例如 Amazon Linux 常見是 `ec2-user`，Ubuntu 常見是 `ubuntu`

使用 Packer 自動化建立 AMI 時，需要先定義一個 Packer 模板文件，然後使用該模板文件生成 AMI 映像。
下面是一個基本的 Packer 示例，可以根據需求進行修改：

{% raw %}
```
{
    "variables": {
        "aws_access_key": "",
        "aws_secret_key": "",
        "aws_region": "us-west-2"
    },
    "builders": [
        {
            "type": "amazon-ebs",
            "access_key": "{{ user `aws_access_key` }}",
            "secret_key": "{{ user `aws_secret_key` }}",
            "region": "{{ user `aws_region` }}",
            "source_ami": "ami-0c55b159cbfafe1f0",
            "instance_type": "t2.micro",
            "ssh_username": "ec2-user",
            "ami_name": "Alma Linux 8.5 x86_64 HVM AMI {{timestamp}}"
        }
    ],
    "provisioners": [
        {
            "type": "shell",
            "script": "build.sh"
        }
    ]
}
```
{% endraw %}

在上述的 json 中，我定義了一個 amazon-ebs builder，它會使用 AWS EC2 作為基礎，並且使用 source_ami 指定了 Alma Linux 8.5 的 AMI ID。還定義了一個 `shell provisioner`，它會運行 `build.sh` 腳本以進行環境安裝。

## build.sh 可以放什麼

`build.sh` 通常會放 OS 初始化與服務安裝，例如：

```
#!/bin/bash
set -e

sudo yum update -y
sudo yum install -y nginx git
sudo systemctl enable nginx
```

建議在 shell script 開頭加上 `set -e`，讓任一指令失敗時直接停止 build。否則某個套件安裝失敗，Packer 仍可能繼續往下跑，最後產生一個看似成功但內容不完整的 AMI。

## 不同情境的做法

### 情境一：只想做一個固定基礎映像

適合把每台機器都需要的基本套件放進 AMI，例如：

1. NTP / chrony
2. CloudWatch Agent
3. 公司內部 CA 憑證
4. 基本安全設定
5. 常用 debug 工具

這種 AMI 應該保持乾淨，不要放特定 application code。之後不同服務可以從這個 base AMI 再往上建立自己的映像。

### 情境二：做 application AMI

如果部署方式是 immutable infrastructure，可以把 application artifact 也放進 AMI。流程會像這樣：

1. CI 先 build application
2. Packer 把 artifact 複製到 builder instance
3. provisioner 安裝 runtime 與 systemd service
4. 產生 AMI
5. Auto Scaling Group 或部署流程換成新的 AMI ID

這種方式的好處是開機後不需要再做太多安裝，rollback 也可以直接切回上一版 AMI。

### 情境三：同一份模板產生不同環境 AMI

可以用 variable 區分環境：

```
packer build \
  -var 'env=staging' \
  -var 'aws_region=ap-northeast-1' \
  my_template.json
```

命名上可以把環境放進 AMI name：

{% raw %}
```
"ami_name": "web-{{user `env`}}-{{timestamp}}"
```
{% endraw %}

這樣在 AWS Console 搜尋時比較不容易把 staging 和 production AMI 搞混。

設定好之後，使用以下命令來生成 AMI ：
```
packer build \
-var 'aws_access_key=MY_ACCESS_KEY' \
-var 'aws_secret_key=MY_SECRET_KEY' \
-var 'aws_region=MY_REGION' \
my_template.json
```

在上面的命令中，-var 選項用於指定模板文件中定義的變數值。您需要替換 `MY_ACCESS_KEY` 和 `MY_SECRET_KEY` 為 AWS Credentials，`MY_REGION` 為 AWS 服務所在區域。

## 建議的驗證流程

AMI build 完成不代表真的可用，至少要做一次啟動驗證：

1. 到 EC2 Console 找到新產生的 AMI
2. 用該 AMI 啟動一台測試 instance
3. SSH 進去確認套件、服務與設定檔是否存在
4. 檢查 service 是否能正常啟動
5. 確認沒有遺留暫時檔案、credential 或 build log

如果 AMI 是要給正式環境使用，建議把驗證流程也自動化，例如在 Packer build 後接一段 smoke test，至少確認系統能開機、SSH 可連線、主要服務有正常啟動。

## 注意事項

1. 不建議把 AWS access key 寫死在模板檔，盡量使用環境變數、AWS profile、instance role 或 CI secret
2. `ami_name` 建議帶日期、版本號或 git commit hash，方便追蹤來源
3. build 失敗時要確認暫時 EC2、volume、snapshot 是否被清掉，避免留下多餘費用
4. `source_ami` 會隨時間過期或不再建議使用，維護時要定期更新基礎映像
5. 若團隊開始長期維護 Packer template，建議逐步整理成 HCL 格式，會比舊 JSON template 更容易拆檔與管理

## 小結

Packer 適合用來處理「希望環境可以重現」的情境。只要把 AMI 製作流程寫成 template 和 script，就能降低人工操作造成的差異，也能讓之後的版本更新、環境交接和問題追蹤更穩定。
