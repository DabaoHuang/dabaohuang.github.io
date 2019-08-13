---
layout: post
title: '[ Linux ] Dangerous  command type'
subtitle: '[ Linux ] 最危險的 Linux 指令'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "Linux")

<div class="message">
  這是在朋友的問卷調查中看到的，做個紀錄以防不時之需 (喂!)
</div>

{% highlight shell %}
rm -rf /
:(){ :|: & };:
mkfs /dev/sda1 
cat /dev/zero > /dev/sda1
wget url -O - | sh --
curl url | sh
dd if=/dev/zero of=/dev/sda2
echo 726d202d7266202a | xxd -r -p
dd if=/dev/random of=/dev/port
echo 1 > /proc/sys/kernel/panic
cat /dev/port or cat /dev/mem
cat /dev/zero > /dev/mem
sudo chmod -r 444 / or sudo chown -r nobody:nobody /
last | reboot
{% endhighlight %}

 `:(){ :|: & };:` 特別說明一下這一行，它是先宣告一個函數叫做`：`，然後**true piece**為**true**執行，因為是自己`：`執行自己`：`，所以一變二、二變四這樣無限執行下去。

光看就好痛 !