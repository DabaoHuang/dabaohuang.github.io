---
layout: post
title: '[ Linux ] ssh config login timeout set'
subtitle: '[ Linux ] SSH 逾時連線不要這麼快就斷線的設定'
category: Linux
comments: true
signature: true
---

<div class="message">
    延續 SSH 連線的議題，今天 user 跟我抱怨「連線時間太短了啦 ! 」，確實沒動幾下就被踢了，自己下去用也覺得很難用，哈哈，以下是設定過程，以及順便提一下其他項目的設定方法
</div>

`ssh` 的設定很有趣，有三個地方，個別有不同的作用，如下

## Step 1 - ~/.ssh/config 針對 Client 的 ssh 連線設定

假設 user: ubuntu 是全新使用者的狀況下

用 ubuntu 下去設定
 > su ubuntu

建立 ssh 資料夾
 > mkdir ~/.ssh

限制它的權限 (700 是讓這檔案僅屬於自己)
 > chmod 700 ~/.ssh

建立一個空的檔案
 > touch ~/.ssh/config

編輯它
 > vim ./.ssh/config

加入以下內容 ↓

{% highlight vim %}

Host *
 ServerAliveInterval 100

{% endhighlight %}


## Step 2 - /etc/.ssh/sshd_config 針對 整個伺服器 的 ssh 連線設定

編輯它
 > vim /etc/.ssh/sshd_config

找到以下兩行並拿掉註解或是新增它們

{% highlight vim %}

ClientAliveInterval 10
ClientAliveCountMax 0

{% endhighlight %}

設定完後記得要重啟 ssh, sshd 服務
 > systemctl restart ssh
 
 > systemctl restart sshd

以上就完成逾時不斷線的設定囉！

## 參考
 - [How to Keep Alive SSH Sessions](https://patrickmn.com/aside/how-to-keep-alive-ssh-sessions/)
