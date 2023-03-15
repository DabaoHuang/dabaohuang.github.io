---
layout: post
title: '[ Packer ] build automated machine images with Packer'
subtitle: '[ Packer ] 自動化建立 AWS AMI'
category: packer
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/packer.png "packer")

<div class="message">
    使用 Packer 自動化建立 AWS AMI
</div>


使用 Packer 自動化建立 AMI 時，需要先定義一個 Packer 模板文件，然後使用該模板文件生成 AMI 映像。
下面是一個基本的 Packer 示例，可以根據需求進行修改：

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

在上述的 json 中，我定義了一個 amazon-ebs builder，它會使用 AWS EC2 作為基礎，並且使用 source_ami 指定了 Alma Linux 8.5 的 AMI ID。還定義了一個 `shell provisioner`，它會運行 `build.sh` 腳本以進行環境安裝。

設定好之後，使用以下命令來生成 AMI ：
```
packer build \
-var 'aws_access_key=MY_ACCESS_KEY' \
-var 'aws_secret_key=MY_SECRET_KEY' \
-var 'aws_region=MY_REGION' \
my_template.json
```

在上面的命令中，-var 選項用於指定模板文件中定義的變數值。您需要替換 `MY_ACCESS_KEY` 和 `MY_SECRET_KEY` 為 AWS Credentials，`MY_REGION` 為 AWS 服務所在區域。