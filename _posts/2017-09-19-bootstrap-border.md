---
layout: post
title: '[ CSS ] flex and order'
subtitle: '[ CSS ] flex 與 order 運用'
category: CSS
comments: true
signature: true
---

<div class="message">
    呼，工作總算是告一段落。最近被公司派去支援切版順便學習 (唯一能切版的網站設計離職了)，而且一切就是RWD，真的非常有挑戰性 ... 
    不過也因為這樣，photoshop 跟 boostrap 的技能被大幅強化了 ! 今天就是要來講在這工作中遇到的一些小問題
</div>

以往我對切版的認知是，`float: left (right);`，不過在 CSS3 後多了新的排版方式**flex**，一開始還真覺得有點複雜

用了**bootstrap**後真是海闊天空啊 !!! 考試都考100分，基本版面如下↓

{% highlight html %}

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0,minimum-scale=0.8,maximum-scale=2.0,user-scalable=no">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <!-- CSS -->
    <!-- bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">    
    <!-- font-awsome -->
    <link href="//netdna.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css" rel="stylesheet" />
    <!-- JS framework -->
    <!-- jquery -->
    <!-- <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script> -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
    <!-- jQuery-UI -->
    <script src="//ajax.googleapis.com/ajax/libs/jqueryui/1.11.1/jquery-ui.min.js"></script>
    <!-- Bootstrap -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.11.0/umd/popper.min.js" integrity="sha384-b/U6ypiBEHpOf/4+1nzFpr53nxSS+GLCkfwBdFNTxtclqqenISfwAzpKaMNFNmj4" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/js/bootstrap.min.js" integrity="sha384-h0AbiXch4ZDo7tp9hKZ4TsHbi047NrKGLO3SEJAg45jXxnGIfYzk4Si90RDIqNm1" crossorigin="anonymous"></script>
    <!-- modernizr 2.8.3 -->
    <script lang="javascript" src="https://cdnjs.cloudflare.com/ajax/libs/modernizr/2.8.3/modernizr.js"></script>
    <!-- scrolling -->
    <script lang="javascript" src="/js/CCscroll.js"></script>
    <!-- fancybox -->
    <link rel="stylesheet" href="/js/fancybox2.15/source/jquery.fancybox.css" />
    <script src="/js/fancybox2.15/source/jquery.fancybox.js"></script>
    <!-- countJS -->
    <script language="JavaScript" src="/js/count.js"></script>
    <!-- Resize -->
    <script language="JavaScript" src="/js/Cresize.js"></script>
    <!-- style -->
    <link rel="stylesheet" href="css/Cstyle.css" />
    <link rel="stylesheet" href="css/Cdevice-tablet.css" />
</head>

{% endhighlight %}

上面的範例是我最新弄好的基本`<head>`，先導入基本的jQuery、boostrap 4 beta ( 下一篇再來談**bootstrap3**與**boostrap4**的差別 ) 

基本你只要看**meta view-port**那行就好 XD，在‵`view-port`裡面我們可以看到一些設定屬性，以下引用**w3c**的介紹並說明：

 - width:[數字] 或 device-width，直接指定當前裝置寬度為最寬，就可以放心的在 CSS 中寫`width: 100%` !
 - height:[數字] 或 device-height，同上，設定裝置高度為最高。
 - initial-scale:最小0.25，最大5，正常情況下放大縮小的比例，通常會給**1**
 - minimum-scale=0.8，這行的意思是指最小縮放不能低於0.8
 - maximum-scale:最小0.25，最大5，最大縮放比例，通常也會只給**1**
 - user-scalable:1 或 0 (yes 或 no)，這項設定其實就是指**zoom**，是否允許使用者縮放，我通常會給0 (no) XD



以上只是基本設定，設定好之後，我們來 talking about **bootstrap flex**

在 bootstrap 4 中，我們有五種響應式斷點，如下

| width < 34em / 480px | width ≥ 34em / 480px | width ≥ 45em / 720px | width ≥ 62em / 992px | width ≥ 75em / 1200px |
|-------|--------|---------|--------|---------|
| .col-xs- | .col-sm | .col-md | .col-lg | .col-xl |

除了以上五種以外，還有一種非斷點，即`col`，總共六種可以排列

而每一行你都有**12**個單位可以排列，比方↓

{% highlight html %}

<!-- 請務必一定要記得使用row當parent div，否則斷點不會執行 -->
<div class="row">
    <!-- 我是一整行 -->
    <div class="col-12"></div>
    我是三個框框
    
</div>

{% endhighlight %}
