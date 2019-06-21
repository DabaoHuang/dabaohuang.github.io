---
layout: post
title: '[ PHP ] phpize not found'
subtitle: '[ PHP ] phpize not found '
category: PHP
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/php.png "php")

##Error Message
 > phpize: not found

Phpize 原本是 php-devel 內的東西，主要用途是設定外掛模組中的設定

php安裝成功後，在bin目錄下會生成一個phpize的可執行腳本

它的用途是用來動態安裝php擴充模組

使用phpize的好處是在已經安裝了php的情況下，再安裝擴展模塊時，無需重新編譯安裝php

##Ubuntu
 > sudo apt-get install php-all-dev

##CentOS
 > sudo yum install php-all-dev

如果不知道packge的名稱的話，可以這樣做

{% highlight %}

$ sudo apt update
$ sudo apt list | grep php | grep dev

{% highlightend %}

找出長得像是 `php-devel` 的包安裝後測試，解決！