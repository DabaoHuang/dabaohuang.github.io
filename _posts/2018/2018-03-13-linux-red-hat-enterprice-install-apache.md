---
layout: post
title: '[ Linux ] Red hat enterprice Apache install - NOTE '
subtitle: '[ Linux ] Red hat enterprice Apache 安裝 - NOTE'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "linux")

<div class="message">
    延續上一篇文章提到，正在協助建置機房，安裝 Red Hat Enter price 時所做的筆記
</div>

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: UEFI
 - USB Maker: Rufus
 - O.S      : Red hat enterprice 7

安裝過程就不贊述了，我是用 `Rufus` 製作開機 USB，安裝的檔案是 `Red Hat Enterprise Linux 7.4 Binary DVD`

雖然此步驟非常的簡單，還是有些小細節想記錄下來，以下是指令紀錄

{% highlight shell %}

yum install httpd

service httpd start
# or
systemctl start httpd

{% endhighlight %}

 > 容易忘記開放 firewall XD

**開啟防火牆通訊埠 http:80**

 - firewall-cmd --permanent --add-service=http
 - systemctl restart firewalld

{% highlight shell %}

# 開機時自動啟動 httpd
systemctl enable httpd
#or
ntsysv

{% endhighlight %}

## Finish 

到上面的步驟就算是把 Apache 裝完了 :p