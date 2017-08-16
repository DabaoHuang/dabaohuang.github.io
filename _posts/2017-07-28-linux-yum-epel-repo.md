---
layout: post
title: '[ Linux ] epel setup and error message'
subtitle: '[ Linux ] CentOS 安裝 epel 與可能出現的錯誤'
category: Linux
comments: true
signature: true
---

<div class="message">
    因為案子還沒開始，今天幫忙接手了同事做到一半的server，在 yum install 時報錯，於是有了這篇文章 ...
</div>

## Linux - CentOS

`epel`是一個擴充資源庫，擴充了安裝指令`yum`可安裝的項目

### 直接安裝**epel-release**
{% highlight shell %}
sudo yum install epel-release
{% endhighlight %}

如果輸入上面的指令報錯的話，就需要自己去抓來源 ↓
{% highlight shell %}
#查詢你的Centos版本
cat /etc/redhat-release

#CentOS and Red Hat Enterprise Linux 5.x
wget http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
sudo rpm -Uvh epel-release-5*.rpm

#CentOS and Red Hat Enterprise Linux 6.x
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
sudo rpm -Uvh epel-release-6*.rpm

#CentOS and Red Hat Enterprise Linux 7.x
wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
sudo rpm -Uvh epel-release-7*.rpm

#確認是否安裝完成
ls -1 /etc/yum.repos.d/epel* /etc/yum.repos.d/remi.repo
{% endhighlight %}

這時候我們應該會看到 ↓，共 3 個檔案

 > /etc/yum.repos.d/epel.repo<br>/etc/yum.repos.d/epel-testing.repo<br>/etc/yum.repos.d/remi.repo

{% highlight shell %}
#編輯 remi.repo
vim /etc/yum.repos.d/remi.repo

[remi]
name=Les RPM de remi pour Enterprise Linux 6 - $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/6/remi/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/6/remi/mirror
#這行 ↓
enabled=0
#改為 ↓
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
{% endhighlight %}

到這邊就算是安裝完成，但是也許會有人在安裝時遇到以下錯誤 ( 我在別台機器遇到惹XD )

 > Error: Cannot retrieve metalink for repository: epel. Please verify its path and try again

這時候就需要再次修改`remi.repo`

{% highlight shell %}
#編輯 remi.repo
vim /etc/yum.repos.d/remi.repo

[remi]
name=Les RPM de remi pour Enterprise Linux 6 - $basearch
#這行 ↓
baseurl=http://rpms.famillecollet.com/enterprise/6/remi/$basearch/
#跟這行 ↓
#mirrorlist=http://rpms.famillecollet.com/enterprise/6/remi/mirror
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
{% endhighlight %}

這樣就可以正常安裝了 !

