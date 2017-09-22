---
layout: post
title: '[ CSS ] bootstrap4 flex'
subtitle: '[ CSS ] bootstrap4 flex 運用'
category: CSS
comments: true
signature: true
---

<div class="message">
    呼，工作總算是告一段落。最近被公司派去支援切版順便學習 (唯一能切版的網站設計離職了)，而且一切(ㄑ一ㄝ)就是RWD，真的非常有挑戰性 ... 
    不過也因為這樣，photoshop 跟 boostrap 的技能都被大幅強化了 (レベルアップ！) 今天就是要來講在這工作中遇到的一些小問題
</div>

以下所有範例都可以到下面連結做測試喔 !
[https://codepen.io/anon/pen/jGMMLy](https://codepen.io/anon/pen/jGMMLy)

##View-port

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

基本你只要看**meta view-port**那行就好 XD，在`view-port`裡面我們可以看到一些設定屬性，以下引用**w3c**的介紹並說明：

 - width:[數字] 或 device-width，直接指定當前裝置寬度為最寬，就可以放心的在 CSS 中寫`width: 100%` !
 - height:[數字] 或 device-height，同上，設定裝置高度為最高。
 - initial-scale:最小0.25，最大5，正常情況下放大縮小的比例，通常會給**1**
 - minimum-scale=0.8，這行的意思是指最小縮放不能低於0.8
 - maximum-scale:最小0.25，最大5，最大縮放比例，通常也會只給**1**
 - user-scalable:1 或 0 (yes 或 no)，這項設定其實就是指**zoom**，是否允許使用者縮放，我通常會給0 (no) XD



##Bootstrap 4 flex


以上只是基本設定，設定好之後，我們來 talking about **bootstrap flex**

在 bootstrap 4 中，我們有五種響應式斷點，如下

| width < 34em / 480px | width ≥ 34em / 480px | width ≥ 45em / 720px | width ≥ 62em / 992px | width ≥ 75em / 1200px |
|-------|--------|---------|--------|---------|
| .col-xs | .col-sm | .col-md | .col-lg | .col-xl |

除了以上五種以外，還有一種非斷點，即`col`，總共六種可以排列

而每一行你都有**12**個單位可以排列，比方↓

{% highlight html %}

<!-- 請務必一定要記得使用row當parent div，否則斷點不會執行 -->
<div class="row">
    <!-- 我是一整行 -->
    <div class="col-12">1</div>
    <!-- 我是兩個框框 -->
    <div class="col-6">1</div>
    <div class="col-6">2</div>
    <!-- 我是三個框框 -->
    <div class="col-4">1</div>
    <div class="col-4">2</div>
    <div class="col-4">3</div>
    <!-- 我是四個框框 -->
    <div class="col-3">1</div>
    <div class="col-3">2</div>
    <div class="col-3">3</div>
    <div class="col-3">4</div>
    <!-- 我是六個框框 -->
    <div class="col-2">1</div>
    <div class="col-2">2</div>
    <div class="col-2">3</div>
    <div class="col-2">4</div>
    <div class="col-2">5</div>
    <div class="col-2">6</div>
    <!-- 我是十二個框框 -->
    <div class="col-1">1</div>
    <div class="col-1">2</div>
    <div class="col-1">3</div>
    <div class="col-1">4</div>
    <div class="col-1">5</div>
    <div class="col-1">6</div>
    <div class="col-1">7</div>
    <div class="col-1">8</div>
    <div class="col-1">9</div>
    <div class="col-1">10</div>
    <div class="col-1">11</div>
    <div class="col-1">12</div>
</div>

{% endhighlight %}

當然，很多時候這個排版方式其實還是會受到老闆期望的樣式所限制，這時候就可能需要再`div.row`上做手腳

像是限制最大寬度`max-width`強制讓*div*間隔不要太開，讓畫面能達到預期的結果

在**Bootstrap 4**中其中一個改變是，這些`col`開頭的響應式欄位可以不需要設定數字就可以達到平均分配欄寬的效果

{% highlight html %}

<!-- 兩格 -->
<div class="row">
    <div class="col">1</div>
    <div class="col">2</div>
</div>

<!-- 五格 -->
<div class="row">
    <div class="col-xl">1</div>
    <div class="col-xl">2</div>
    <div class="col-xl">3</div>
    <div class="col-xl">4</div>
    <div class="col-xl">5</div>
</div>

{% endhighlight %}



##justify - 水平排列

上面的排版方法熟悉之後，就可以來講**justify**這屬性了，在以前我們通常是用絕對定位或者是`text-align`來達成置中的效果

在flex中，我們使用的是更方便的`justify`來定位，而**bootstrap 4**已經有為我們準備了簡單好用的class，設定在**父元素**中就可以使用

請看以下範例↓


###justify-content-start、justify-content-center、justify-content-end

 - justify-content-start  : 置左
 - justify-content-center : 置中
 - justify-content-end    : 置右

{% highlight html %}

<div class="row justify-content-start">
    <div class="col-xl-2">start</div>
</div>

<div class="row justify-content-center">
    <div class="col-xl-2">center</div>
</div>

<div class="row justify-content-end">
    <div class="col-xl-2">end</div>
</div>

{% endhighlight %}


###justify-content-around、justify-content-between

 - justify-content-around  : 平均分配欄寬在 col 之間
 - justify-content-between : 平均分配欄寬在 col 左右

{% highlight html %}

<div class="row justify-content-around">
    <div class="col-xl-2">around</div>
    <div class="col-xl-2">around</div>
</div>

<div class="row justify-content-between">
    <div class="col-xl-2">between</div>
</div>

{% endhighlight %}


##align - 垂直排列

flex的垂直排列有`align-items`、`align-self`兩種屬性可以設置，**bootstrap 4**的class中，也是分成兩種使用方式

 - align-items-(start、center、end) : 設定在**父元素**中
 - align-self-(start、center、end) : 設定在**子元素**中

請看以下範例↓

{% highlight html %}

<div class="row align-items-start">
    <div class="col-1">上</div>
</div>

<div class="row align-items-center">
    <div class="col-1">中</div>
</div>

<div class="row align-items-end">
    <div class="col-1">下</div>
</div>

<div class="row" style="height:200px;">
    <div class="col-1 align-self-start">上</div>
    <div class="col-1 align-self-center">中</div>
    <div class="col-1 align-self-end">下</div>
</div>

{% endhighlight %}



##Order - 序列戰爭(被毆

**Order**這屬性滿特別的，決定了每個flex子元素的順序，簡單來說就是數字越小就越前面

{% highlight html %}

<!-- 原本的框框 -->
<div class="d-flex">
    <div>A</div>
    <div>B</div>
    <div>C</div>
</div>

<!-- 修改後的框框 -->
<div class="d-flex">
    <div style="order:3;">A</div>
    <div style="order:1;">B</div>
    <div style="order:2;">C</div>
</div>

{% endhighlight %}

 - `d-flex`是boostrap 4預設的`display: flex;`



以上就是**Bootstrap 4 grid system** 的一丁點皮毛 ... ㄏㄏ

還有很多衍伸應用，比方說`d-flex`也有斷點，例如`d-sm-flex`或是`d-xl-fle`等

`justify-content-sm-center`或`align-self-md-start`之類的使用，可以自由發揮

至於，像是`flex-wrap`跟`flex-wrap-reverse`，不過不是我不提，而是我覺得在實際切版的過程中真的比較少用到

畢竟花最多時間的不是切版，而是怎麼做的跟美術給我的圖片一模一樣的網站出來才是頭痛的地方 Haha

以後如果有用到在上來寫囉XD