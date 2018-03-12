---
layout: post
title: '[ INFRA ] Red hat enterprice install error '
subtitle: '[ INFRA ] Red hat enterprice 透過 yum 安裝時，發生軟體源註冊錯誤'
category: INFRA
comments: true
signature: true
---

<div class="message">
    延續上一篇文章提到，正在協助建置機房，安裝 Red Hat Enter price 遇到的問題，解決過程檢視
</div>

## Problem

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: UEFI
 - USB Maker: Fedora media writer

安裝過程就不贊述了，我是用 `Rufus` 製作開機 USB，安裝的檔案是 `Red Hat Enterprise Linux 7.4 Binary DVD`

系統安裝完成後，準備要透過 `yum` 安裝一些基本套件時，卻出現 ↓

 > This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register

白話來講就是你雖然下載了免費的 Enterprice 但是想要安裝屬於 Enterprice 的 Package 是要付錢的

但是網路早已有解決方案了，我們可以安裝 CentOS 的 yum eprl 來代替 ( CentOS是 Red Hat 底下的開源 Linux 專案 )


## Step1 - 網路設定

 > `ifconfig` #先下 ifconfig 看網卡編號

<div class="message">
em1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.211  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::3e55:e959:a715:cc46  prefixlen 64  scopeid 0x20<link>
        ether f0:4d:a2:07:0d:0b  txqueuelen 1000  ...
</div>

可能是因為裝的是 Enterprice 版本，並不是常見的 eth0 而是 em1

 > `roo@enterprice# vim /etc/sysconfig/network-scripts/ifcfg-em1` # 到網卡的位置直接設定 ↓

{% highlight vim %}

TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static              #這邊原本可能是DHCP，要改為 static
IPADDR=192.168.1.211
NETMASK=255.255.255.0
BROADCAST=192.168.1.255
GATEWAY=192.168.1.1
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=em1
UUID=xxxx-xxxx-xxxx-xxxx
DEVICE=em1
ONBOOT=yes

{% endhighlight %}

儲存後再用`ifconfig`確認後，換設定DNS

在安裝 CentOS 軟體源時，需要先去 [下載](http://mirrors.163.com/) 鏡像檔，下載過程中可能會遇到 ↓

 > wget: Can not analyst address unable to resolve host address

這是 DNS 在解析時發生的錯誤，通常都是因為沒設定而造成的，這時候只要去設定一下即可

 > `roo@enterprice# vim/etc/resolv.conf` # 記得要用 root 權限開啟

{% highlight vim %}
#以下設定僅供參考
nameserver 8.8.8.8 #google
nameserver 8.8.4.4 #google
{% endhighlight %}

到這邊應該都能正常的連上網路

 > `curl http://wttr.in/%22Taipei` # 連上天氣網站測試

{% highlight vim %}
     \  /       Partly cloudy
  _ /"".-.     26-27 °C
    \_(   ).   ↘ 6 km/h
    /(___(__)  10 km
               0.0 mm
{% endhighlight %}



## Step2 - 清除系統原生的 yum package

我們可以透過 **rpm -qa** 這指令確認有哪些套件要清除

 > 可以用 `man rpm` 查詢相關說明，以下列出會使用的指令 ↓
 - `rpm -qa` : 列出目前系統內的套件
 - `rpm -qa | grep yum` : 列出所有 yum 相關的套件

 > `rpm -qa |grep yum|xargs rpm -e --nodeps` # 解除 yum 相關的安裝
 > `rpm -qa |grep python-urlgrabb|xargs rpm -e –nodep` 這支是網路上的教學強烈建議一併解除安裝的，安裝 yum 的時候會用到
 - `--nodeps` : 不檢查相關性，直接執行指令
 - `--force` : 強制執行
 - `-e` : 解除安裝



## Step3 - 安裝 CentOS yum package

要安裝 yum 資源包需要五個套件

 * yum-metadata-parser-1.1.4-10.el7.x86_64.rpm
 * yum-3.4.3-154.el7.centos.noarch.rpm
 * yum-plugin-fastestmirror-1.1.31-42.el7.noarch.rpm
 * yum-rhn-plugin-2.0.1-9.el7.noarch.rpm
 * python-urlgrabber-3.10-8.el7.noarch.rpm

推薦一個[網易公司鏡像網站](http://mirrors.163.com)，可以找到相關版本的package mirror

若是不放心大陸的網站不安全的話也可以用[開源鏡像網](http://mirrors.kernel.org/centos)

依照你作業系統的版本，到 package folder 內，[範例使用](http://mirrors.163.com/centos/7/os/x86_64/Packages/)

 > path : /centos/7/os/x86_64/Packages/

以上的資源包用 `wget` 下載下來後用 `rpm` 安裝

**第一個一定要先裝這個，順序不能錯**
 > `rpm -ivh yum-metadata-parser-1.1.4-10.el7.x86_64.rpm`

 > `rpm -ivh --force --nodeps yum-3.4.3-154.el7.centos.noarch.rpm python-urlgrabber-3.10-8.el7.noarch.rpm yum-plugin-fastestmirror-1.1.31-42.el7.noarch.rpm`

 - `rpm -ivh` : rpm 的安裝參數

**刪除原本的 repo 源**
 > `rm -rf /etc/yum.repos.d/*`

**建立新的 repo 源**，這是將 yum 指向 [http://mirrors.163.com/](http://mirrors.163.com/)，把這網易公司鏡像網站當作 package base
 > `vim /etc/yum.repos.d/CentOS-Base.repo`

{% highlight vim %}

[base]

name=CentOS-7 - Base - 163.com

#mirrorlist=http://mirrorlist.centos.org/?release=7&arch=$basearch&repo=os

baseurl=http://mirrors.163.com/centos/7/os/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

 

#released updates

[updates]

name=CentOS-7 - Updates - 163.com

#mirrorlist=http://mirrorlist.centos.org/?release=7&arch=$basearch&repo=updates

baseurl=http://mirrors.163.com/centos/7/updates/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

 

#additional packages that may be useful

[extras]

name=CentOS-7 - Extras - 163.com

#mirrorlist=http://mirrorlist.centos.org/?release=7&arch=$basearch&repo=extras

baseurl=http://mirrors.163.com/centos/7/extras/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

 

#additional packages that extendfunctionality of existing packages

[centosplus]

name=CentOS-7 - Plus - 163.com

baseurl=http://mirrors.163.com/centos/7/centosplus/$basearch/

gpgcheck=1

enabled=0

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

{% endhighlight %}


## Finish

最後清理舊的、建立新的 Cache，就可以說是大功告成，再用 yum 更新一下 rpm

{% highlight vim %}
yum clean all

yum makecache fast

yum update -y rpm*
{% endhighlight %}

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: BIOS
 - USB Maker: Rufus
 - [Red Hat OS 官方下載](https://access.redhat.com/downloads/content/69/ver=/rhel---7/7.4/x86_64/product-software)
 - [Rufus](https://rufus.akeo.ie/?locale=zh_TW)
 - [開源鏡像網](http://mirrors.kernel.org/centos/)
 - [網易公司鏡像網站](http://mirrors.163.com/)
