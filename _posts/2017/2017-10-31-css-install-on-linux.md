---
layout: post
title: '[ CSS ] SCSS SASS install on linux'
subtitle: '[ CSS ] 在Linux上安裝SCSS/SASS'
category: CSS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/css.jpg "CSS")

<div class="message">
    到今天我們公司依然還未找到美術/前端，因為一直在支援前端需求，寫了一堆的CSS後，我真的覺得這些寫法相當不smart，感覺一直在做重複的事情 ... ，所以我決定引進SCSS來用XD
</div>

## SASS 安裝步驟

因為安裝過程需要 rubygems，所以優先安裝 rubygems

{% highlight vim %}
yum install rubygems
gem install sass
{% endhighlight %}

不過因為我在安裝時，遇到了個小問題↓

{% highlight vim %}
[root@dev webuser]# gem install sass
Building native extensions.  This could take a while...
ERROR:  Error installing sass:
        ERROR: Failed to build gem native extension.
{% endhighlight %}

這是因為我的機器並不是完整的Ruby環境導致(我的機器跟我寫的程式是PHP為主)，所以導致出錯，解法如下↓

{% highlight vim %}
yum install ruby-devel
{% endhighlight %}

## Ruby 升級

但是 !!! 當我要再安裝一次時又出現版本不同 ... 系統中的ruby default版本1.8.7太低了，至少要1.9以上，所以我又刪除重新安裝

{% highlight vim %}
yum remove ruby ruby-devel
yum groupinstall "Development Tools"
yum install openssl-devel
wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz
tar xvfvz ruby-2.1.2.tar.gz
cd ruby-2.1.2
./configure
make
make install
{% endhighlight %}

裝好之後，基本的更新

{% highlight vim %}
gem update --system
gem install bundler
gem update

ruby --version
rubygems --version
{% endhighlight %}

## 繼續安裝 SASS

{% highlight vim %}
gem install sass
{% endhighlight %}

裝好之後測試一下有沒有成功~

 > vim style.scss

{% highlight css %}
body {
    font-family:arial;
    content {
        color:red;
    }
}
{% endhighlight %}

 > sass --watch style.scss:style.css 
這行指令是監控style.scss 只要有變動就會重新生成style.css

還有以下幾種指令可以參考
 - sass --watch ./:stylesheets/compiled # sass 檔 和 css 檔 分開目錄, 這會把目前此目錄底下的 *.sass 檔, compile 完成後, 都放到 stylesheets/compiled/ 下面.
 - sass input.scss output.css # 直接 run 產生出 css 檔
 - sass --watch input.scss:output.css
 - sass --watch app/sass:public/stylesheets
 - sass --watch --style compressed style.scss:style.css

## SASS語法

{% highlight css %}

/* function */
@mixin rounded-top {
$side: top;
$radius: 10px;

border-#{$side}-radius: $radius;
-moz-border-radius-#{$side}: $radius;
-webkit-border-#{$side}-radius: $radius;
}

/* include  */
#navbar li { @include rounded-top; }
#footer { @include rounded-top; }

/* extend */
/* 原始內容 */
.error {
border: 1px #f00;
background-color: #fdd;
}
.seriousError {
border: 1px #f00;
background-color: #fdd;
border-width: 3px;
}
使用 extend, 可寫成下述:
.error {
border: 1px #f00;
background-color: #fdd;
}
.seriousError {
@extend .error;
border-width: 3px;
}

/* Parent References */
/* 原始內容 */
a {
color: #ce4dd6;
&:hover { color: #ffb3ff; }
&:visited { color: #c458cb; }
}
Compile 後
a:hover, a:visited ...

/* Arguments */
@mixin rounded($side, $radius: 10px) {
border-#{$side}-radius: $radius;
-moz-border-radius-#{$side}: $radius;
-webkit-border-#{$side}-radius: $radius;
width: $navbar - width / $items - 10px; # 可以做運算
}

#navbar li { @include rounded(top); }
#footer { @include rounded(top, 5px); }
#sidebar { @include rounded(left, 8px); }

/* 拉入另外一個scss檔案，scss 的檔名前面要有 "_" */
/* _rounded.scss => style.scss */
@import "rounded";

/* Interpolation 全域變數 */
/* 原始內容 */
$name: foo;
$attr: border;
p.#{$name} { #{$attr}-color: blue }
Compile 後
p.foo { border-color: blue; }
{% endhighlight %}

sass 還有個好朋友 compass ，不過我目前沒有用到，以後有機會用到再來介紹它~