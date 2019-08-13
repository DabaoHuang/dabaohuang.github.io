---
layout: post
title: '[ PHP ] class DOMDocument not found'
subtitle: '[ PHP ] 找不到類別 DOMDocument '
category: PHP
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/php.png "PHP")

<div class="message">
    這是今天做一些檔案的處理，用了 PHPExcel 整理成 Excel 時所遇到的小問題，紀錄一下。(此次使用的主機為 php --version 5.6w)
</div>

## Error: class DOMDocument not found

參考了一些資訊後得知，是少了模組`php56w-xml`所造成，但是當我直接安裝這模組時，又出現↓

 > Error: php56w-common conflicts with php-common-5.4.16-42.el7.x86_64

所以我一併安裝好這兩個模組就可以正常使用 PHPExcel

{% highlight vim %}
sudo -s
yum install php56w-common
#如果要一併更新php5.6版本的話可以這樣下
#yum install php56w php56w-common

yum install php56w-xml

#記得一定要重開服務，範例為apache
service httpd restart
{% endhighlight %}

完成 !
