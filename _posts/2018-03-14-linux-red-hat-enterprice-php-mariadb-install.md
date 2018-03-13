---
layout: post
title: '[ INFRA ] Red hat enterprice PHP & MariaDB install - NOTE '
subtitle: '[ INFRA ] Red hat enterprice PHP & MariaDB 安裝 - NOTE'
category: INFRA
comments: true
signature: true
---

<div class="message">
    延續前幾篇文章提到，正在協助建置機房，安裝 Red Hat Enter price 時所做的筆記
</div>

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: UEFI
 - USB Maker: Rufus
 - O.S      : Red hat enterprice

安裝過程就不贊述了，我是用 `Rufus` 製作開機 USB，安裝的檔案是 `Red Hat Enterprise Linux 7.4 Binary DVD`

雖然此步驟非常的簡單，還是有些小細節想記錄下來，以下是指令紀錄

{% highlight shell %}

# 安裝 php，因為上一篇有更新過軟體源到最新的關係，安裝的PHP是 7.0up 的版本，若是要安裝其他版本請參考下篇
yum install php php-mysql php-pdo php-gd php-mbstring

# 修改時區設定
sed -i 's/;date.timezone =/date.timezone = Asia\/Taipei/' /etc/php.ini

# 重新啟動 Web Server
service httpd restart
# Redirecting to /bin/systemctl restart  httpd.service
# or
systemctl restart httpd

# 安裝 MariaDB
yum install mariadb-server mariadb

#啟動 MariaDB
systemctl start mariadb

#初始化資料庫設定
/usr/bin/mysql_secure_installation

#設定開機時啟動 MariaDB
systemctl enable mariadb

{% endhighlight %}