---
layout: post
title: '[ Linux ] linux ssh no password login'
subtitle: '[ Linux ] 替使用者建立不需要密碼的SSH連線機制'
category: Linux
comments: true
signature: true
---

<div class="message">
    延續上一篇提到的議題，要建立一個安全、方便控管且讓廠商能連線的帳號，給廠商一個 Key 並且每個帳號對應不同的 public key，這樣比一般的帳號密碼登入來的安全，此次適用於所有 Linux 版本，所以沒有特別寫出是哪一套 Linux
</div>

## Step 1 - 產生 RSA 金鑰

因為我是 Windows 的環境，我是利用 git 自帶的 BASH command line 下 `ssh-keygen` 產出的金鑰

{% highlight vim %}

Enter file in which to save the key (/home/camel/.ssh/id_rsa): Enter(金鑰放的位置，要來這邊抓金鑰)
Enter passphrase (empty for no passphrase): Enter(不設定密碼)
Enter same passphrase again: Enter(不設定密碼)

{% endhighlight %}

確認金鑰是否有正常產出 `ls -l ~/.ssh/`

以上面的範例來說會看到下面兩個檔案

 - id_rsa : Private Key 私鑰，權限為`600`
 - id_rsa.pub : Public Key 公鑰，權限為`644`

## Step 2 - 將公鑰上傳至目的伺服器

今天我想讓廠商從 `webuser` 帳號進入目的伺服器，所以我們要先以 webuser 的身分登入，在登入之前先把`id_rsa.pub`上傳至該台伺服器

 - 以 `webuser` 登入目的伺服器，並移動到家目錄
 - 在家目錄 `~` 確認是否有 `.ssh` 資料夾，若沒有的話創建一個 `mkdir .ssh`，並修改他的權限 `chmod 700 .ssh`
 - 創一個空檔案放在 .ssh 資料夾底下 `touch .ssh/authorized_keys`
 - 複製內容 `cat id_rsa.pub > .ssh/authorized_keys`
 - authorized_keys 的權限必須設定為 644 `chmod 644 .ssh/authorized_keys`

到這邊就可以在本機用 `ssh -i id_rsa webuser@123.45.67.89` 做測試

配合上一篇將使用者新增到 sudo 的群組，就可以讓廠商擁有權限做事情了呦~