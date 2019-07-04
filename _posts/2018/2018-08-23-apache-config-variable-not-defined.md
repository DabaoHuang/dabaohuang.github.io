---
layout: post
title: '[ Apache ] config variable not defined'
subtitle: '[ Apache ] config variable not defined'
category: Apache
comments: true
signature: true
---

<div class="message">
    今天，Apache 竟然 Crash 了，還好是測試用的機器，查了一下發現是 Ubuntu 系統的更新影響運作，以下是記錄
</div>

![logo]({{ site.baseurl }}img/apache.png "Apache")

## 錯誤訊息

{% highlight bash %}
[Tue Apr 12 06:54:32.327474 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_LOCK_DIR} is not defined
[Tue Apr 12 06:54:32.327546 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_PID_FILE} is not defined
[Tue Apr 12 06:54:32.327736 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_RUN_USER} is not defined
[Tue Apr 12 06:54:32.327793 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_RUN_GROUP} is not defined
[Tue Apr 12 06:54:32.327845 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Tue Apr 12 06:54:32.339578 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Tue Apr 12 06:54:32.339736 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
[Tue Apr 12 06:54:32.339934 2016] [core:warn] [pid 457] AH00111: Config variable ${APACHE_LOG_DIR} is not defined
AH00526: Syntax error on line 74 of /etc/apache2/apache2.conf:
Invalid Mutex directory in argument file:${APACHE_LOCK_DIR}
{% endhighlight %}

上面提到的是找不到 Apache 的系統常數所導致

網路上許多的教學都是如何重新導入系統變數，例如

 - `$ . /etc/apache2/envvars`
 - `$ source /etc/apache2/envvars` 

或是直接執行 `export` 不過這些方法對此次遇到的問題都無效

所以我直接開大絕重新安裝

重新安裝是有風險的，請確實做好設定備份與了解在做此步驟

## 更新來源庫

一般來說，只要執行下列指令就好

 > $ sudo apt-get update

但是，中間我還有遇到來源庫也 crash QQ

可以說是屋漏偏逢連夜雨，所以修復兼更新的方法是

 > $ sudo apt-get update --fix-missing 

## 刪除/安裝 Apache

在刪除前，要先把相關設定檔備份到其他地方，例如 `apache.conf`、`sites-avaiaible/*`

 > $ sudo apt-get --purge remove apache2 && sudo apt-get install apache2

 - `--purge` : 這是指 *徹底* 清除相關套件，就是清光光啦！

安裝好後先啟動測試首頁

 > $ sudo systemctl start apache2

成功看到畫面後，將預設的設定檔備份並把原本的設定檔塞回來

## 設定 Apache

如果是全新的 Apache，要設定兩個地方
 
 - apache2.conf
 - ./sites-available/*



`apache2.conf`

{% highlight vim %}
.
.
.
<Directory /webroot >
        Options FollowSymLinks
        AllowOverride None
        Require all granted
        # rewrite module
        RewriteEngine on
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule .* index.php?/$0 [PT,L]
</Directory>
.
.
.
{% endhighlight %}

新增以上那段，後面多放了一段 `RewriteEngine`，是為了 *php Codignitor* 的設定

原本沒有要寫在這邊的，正常的方法是寫在根目錄的 `.htaccess`

但是目前不 work，查明原因後再來寫為什麼 `htaccess` 不 work

需要額外啟動 Rewrite module 指令如下

 > $ sudo a2enmod rewrite

`a2enmod` 是 apache2 啟動 module 的指令



`./sites-available/*`

{% highlight vim %}
<VirtualHost *:443>
    ServerAdmin webmaster@localhost
    DocumentRoot /webroot
    ErrorLog ${APACHE_LOG_DIR}/error.log
</VirtualHost>
{% endhighlight %}

此資料夾底下的 conf 分為 `000-default.conf` 跟 `ssl-default.conf`

很明顯是 *http* 跟 *https*，依照需求去設定，形式上差不多

測試設定檔是否有寫對

 > $ sudo apachectl  -t

看到此訊息即可
{% highlight bash %}
Syntax OK
{% endhighlight %}

重新啟動 Apache2

 > $ sudo systemctl restart apache2

## 額外補充：安裝 php cgi 

安裝好後可以讓 Apache可以看懂 php

 > $ sudo apt-get install libapache2-mod-php7.2

再重新啟動 Apache2 即可

## 總結

更新
 > $ sudo apt-get update

修復來源庫設定
 > $ sudo apt-get update --fix-missing

完全移除 Apache2 / 安裝 Apache2
 > $ sudo apt-get --purge remove apache2 && sudo apt-get install apache2

啟動 rewrite module
 > $ sudo a2enmod rewrite

安裝 php module
 > $ sudo apt-get install libapache2-mod-php7.2

測試 .conf 是否能執行
 > $ sudo apachectl -t

重新啟動 Apache2 服務
 > $ sudo systemctl restart apache2

## 工具建議

 - Mac : iterm2
 - Windows : Putty

## 系統資訊

 - Ubuntu 16.04.4
 - Apache 2.4
 - php 7.2