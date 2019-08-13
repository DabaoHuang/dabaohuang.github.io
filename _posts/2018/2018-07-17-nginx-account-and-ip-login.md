---
layout: post
title: '[ Nginx ] account and ip login'
subtitle: '[ Nginx ] 開放特定ip ＆ 帳號密碼登入'
category: Nginx
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/nginx.png "Nginx")

<div class="message">
     今天有個廠商希望我們開放一支ＡＰＩ給他拉取資料，站在資安的角度上，要給他一些限制
</div>

# Nginx

## 帳號/密碼 登入

這個方法是透過 [HTTP Basic Authentication](https://en.wikipedia.org/wiki/Basic_access_authentication) 驗證登入

而驗證的帳號密碼是透過 Nginx 中的 `auth_basic_user_file` 去讀檔案

`xxx.conf`
假設要在你的預設目錄下要限制帳號密碼登入要這樣寫
{% highlight shell %}
location / {
  auth_basic "You're not allow.";
  auth_basic_user_file conf/htpasswd;
}
{% endhighlight %}

假設全站都需要帳號密碼保護要寫在 `Nginx_path/site-avalible/default` 的 `server` 裡面

但是你其中一支 Reverse proxy `location /api/` 不需要做驗證，可以關掉
{% highlight shell %}
server {
  auth_basic "You're not allow";
  auth_basic_user_file conf/htpasswd;

  location /api/ {
      auth_basic off;
  }
}
{% endhighlight %}

這樣使用者在瀏覽 domain/api 的時候不需要密碼，其他不同的網址才要

在 `auth_basic_user_file` 裡面是這樣寫
{% highlight shell %}
# 註解
user1:passwd1
user2:passwd2:註解
user3:passwd3
{% endhighlight %}

其中 `conf/htpasswd` 裡 `passwd` 需要經過加密

所以這時候要透過 `htpasswd` 或是 `openssl` 作轉換

這裡我使用 `htpasswd` 做加密
{% highlight shell %}
sudo htpasswd -cb /etc/nginx/conf/htpasswd user1
Adding password for user1
  New password:
  Re-type new password:
{% endhighlight %}

再到 `/etc/nginx/conf/htpasswd` 確認即可

## 限制可以讀取的ＩＰ位址

Nginx 只要 `allow` 與 `deny` 就可以做到

`xxx.conf`
同意 **127.0.0.1**、**123.123.123.123** 並拒絕其他人的連線
{% highlight shell %}
location / {
  allow 127.0.0.1;
  allow 123.123.123.123;
  deny all;
}
{% endhighlight %}

就像防火牆般，看規則的方法使由上而下，所以 `deny all` 請放在最後

`xxx.conf`
同意 **172.23.15.0** 整個網段並拒絕 **172.23.15.10** 的連線
{% highlight shell %}
location / {
  deny 172.23.15.10;
  allow 172.23.15.0/24;
  deny all;
}
{% endhighlight %}
有特定單獨的ＩＰ才抓出來放在同意的前面

完成！