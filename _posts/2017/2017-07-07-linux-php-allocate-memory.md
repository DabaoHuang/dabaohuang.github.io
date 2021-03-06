---
layout: post
title: '[ Linux ] Linux cannot allocate memory'
subtitle: '[ Linux ] 記憶體不足，啟用 Swap Memory'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "Linux")

<div class="message">
    最近在研究 LINE-robot ，因為需要 PHP 管理套件 Composser 協助安裝，在執行時遇到了記憶體不足的錯誤，就順便記錄下來
</div>

## Linux

### AWS-EC2

![placeholder]({{ site.baseurl }}img/2017/2017-06-26-linux-php-allocate-memory.jpg "AWS-EC2")

Error message ↓

 > [ErrorException] proc_open(): fork failed – Cannot allocate memory

{% highlight shell %}
# 先確認可用空間
df -h
cd /(可用的空間)
# 再把部分的可用空間改為虛擬記憶體，製造 1G 的 Swap Memory
fallocate -l 1G swapfile
# 修改 Swap Memory file 的權限
chmod 600 swapfile
mkswap swapfile
# 啟動
swapon swapfile
# 反啟動
swapoff swapfile
{% endhighlight %}

結論：Composser 屁股好大

參考：[https://www.phpini.com/linux/linux-add-swap](https://www.phpini.com/linux/linux-add-swap)

