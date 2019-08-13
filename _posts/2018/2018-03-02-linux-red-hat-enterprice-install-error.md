---
layout: post
title: '[ Linux ] Red hat enterprice install error '
subtitle: '[ Linux ] Red hat enterprice 安裝時，設置基礎軟體庫時發生錯誤'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "linux")

<div class="message">
    新年快樂，近期提了辭呈，因為許多交接準備工作而疏於更新這個Blog，終於有點時間喘口氣，今天要來記錄的是，近期建置伺服器所遇到的問題。
</div>

## Problem

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: UEFI
 - USB Maker: Fedora media writer

安裝過程就不贊述了，我是用Red hat官網推薦的 Fedora media writer 製作，發生下面的畫面 ↓

![placeholder]({{ site.baseurl }}img/2018/2018-03-02-linux-red-hat-enterprice-install-error.jpg "problem")

而我百思不得其解的是，為什麼會出現**安裝來源**會出現`設置基礎軟體庫時發生錯誤`

一開始我以為是[這篇](http://blog.sina.com.cn/s/blog_757fc71d0102w7bb.html)大大所講的，製作 USB 開機媒介時出了問題

但是當我點進去看時，也發現畫面跟網路上的教學有出入 ↓

![placeholder]({{ site.baseurl }}img/2018/2018-03-02-linux-red-hat-enterprice-install-error-2.jpg "problem")

就在我多嘗試了 unebootin(一樣)、rufus(一樣)、win32diskimager(連開機都進不去安裝畫面) 後，我在漫無目的地找尋答案中

彷彿看見了黑暗中的一盞[明燈](https://zhidao.baidu.com/question/392573543444301725.html?qbl=relate_question_1)，我看到了一段話

 > 你下载的是NETINSTALL版本的，所以只提供了从网上安装的选项。请找其他版本的CENTOS。

猶如雷貫耳，點醒了我，原來我下載的是 boot 版本，難怪才4xx mb，是的，史上最八十七分的事情發生了

難怪在第二張圖片的時候，我怎麼選都只有網路來源 !

後來仔細的看了一遍官方的介紹後，才發現[官方](https://access.redhat.com/documentation/zh-tw/red_hat_enterprise_linux/6/html/installation_guide/ch-obtaining_red_hat_enterprise_linux)有提醒 ... 真是犯蠢了

![placeholder]({{ site.baseurl }}img/2018/2018-03-02-linux-red-hat-enterprice-install-error-3.jpg "problem")

 > Red Hat Enterprice 在官網[下載](https://access.redhat.com/downloads/content/69/ver=/rhel---7/7.4/x86_64/product-software)時，請務必選擇 `Binary DVD` 版本，否則會像我一樣吃閉門羹

後面的步驟就如同這邊[教學](https://blog.gtwang.org/linux/install-red-hat-enterprise-linux-rhel-server-72/)一樣順利了

我發現用 Fedora media writer 做出來的 USB booter 不易還原，所以我最後要安裝前，選擇使用`Rufus`製作

## Finish

 - 安裝的設備: DELL R610 Server
 - 使用的媒介: USB 2.0 4G DISK
 - BIOS 模式: BIOS
 - USB Maker: Rufus
 - [Red Hat OS 官方下載](https://access.redhat.com/downloads/content/69/ver=/rhel---7/7.4/x86_64/product-software)
 - [Rufus](https://rufus.akeo.ie/?locale=zh_TW)
 - [在Linux 上製作 USB booter](https://access.redhat.com/documentation/zh-tw/red_hat_enterprise_linux/7/html/installation_guide/sect-making-usb-media)
