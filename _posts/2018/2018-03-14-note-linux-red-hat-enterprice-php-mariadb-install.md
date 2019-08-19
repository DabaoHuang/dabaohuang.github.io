---
layout: post
title: '[ NOTE ] Linux Red hat enterprice PHP 5.6 & MariaDB install'
subtitle: '[ NOTE ] Linux Red hat enterprice PHP 5.6 & MariaDB 安裝'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "linux")

<div class="message">
    延續前幾篇文章提到，正在協助建置機房，安裝 Red Hat Enter price 時所做的筆記
</div>

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: UEFI
 - USB Maker: Rufus
 - O.S      : Red hat enterprice 7

安裝過程就不贊述了，我是用 `Rufus` 製作開機 USB，安裝的檔案是 `Red Hat Enterprise Linux 7.4 Binary DVD`

除了上次提到的軟體源 `mirror163.com`，這次還會再新增一個 `epel` 來安裝 php5.6 版本

以下方法其他 php5.4, php5.5 也適用

## Step1 - install/update epel-repo

{% highlight shell %}

# 安裝 epel 庫
yum install epel-release
# or
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

# 更新 Remi 庫
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

# 檢查有沒有 php 相關套件
rpm -qa | grep php

# 卸載 php 相關套件
yum remove php-*

{% endhighlight %}

 > vim /etc/yum.repos.d/remi.repo

{% highlight vim %}
[remi-php56]
name=Les RPM de remi de PHP 5.6 pour Enterprise Linux 6 - $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/6/php56/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/6/php56/mirror
# WARNING: If you enable this repository, you must also enable "remi"
enabled=1  # <=== 這行要改成 1 ，原本是 0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
{% endhighlight %}


## Step2 - Install php

在安裝之前，先把 yum 清空一遍
 > yum clean all
重作一份 cache
 > yum makecache fast

接著就可以直接使用 remi repo 安裝

 > yum install --enablerepo=remi,remi-php56 php php-devel php-mbstring php-pdo php-gd php-xml php-mcrypt php-mysql

上方範例是安裝 php5.6 版本，如果想要安裝 php7, php5.5, php5.4，只要把php56改成 php70、php55、php54 即可

後面的 php php-devel ... 是一些基本會用到的套件，常在寫 php 應該都不陌生

修改時區
 > sed -i 's/;date.timezone =/date.timezone = Asia\/Taipei/' /etc/php.ini

重新啟動 Web server
 > systemctl restart httpd

或`service httpd restart`


## Step3 - MariaDB

安裝 MariaDB
 > yum install mariadb-server mariadb

啟動 MariaDB
 > systemctl start mariadb

初始化資料庫設定
 > /usr/bin/mysql_secure_installation

設定開機時啟動 MariaDB
 > systemctl enable mariadb


## Finish

 > php --version

{% highlight text %}
PHP 5.6.34 (cli) (built: Feb 28 2018 10:16:58)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
{% endhighlight %}

 > echo "小於?phpinfo();?大於" >> /var/www/html/index.php

Test http://yourip/



## 參考
 - [CentOS 7 安裝、升級 PHP 5.6](http://blog.qoding.us/2017/09/centos-7-%E5%AE%89%E8%A3%9D%E3%80%81%E5%8D%87%E7%B4%9A-php-5-6/)