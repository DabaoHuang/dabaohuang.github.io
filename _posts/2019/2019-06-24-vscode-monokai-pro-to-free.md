---
layout: post
title: '[ VScode ] Monokai Pro license to free'
subtitle: '[ VScode ] 免費使用 Monokai Pro 樣式'
category: VScode
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/VScode.png "VScode")

## 安裝
-------------
 - Extensions -> 搜尋 "Monokai Pro" > Install
 - `Ctrl+P`-> 打上 "theme" -> 選擇 "Monokai Pro"
 - 關掉`VScode`

## Linux / MAC OS
-------------
 > cd ~/.vscode/extensions/monokai.theme-monokai-pro-vscode<version>

## Windows
-------------
 > cd %USERPROFILE%\.vscode\extensions 

1. 用編輯器開啟 `/js/app.js`
2. 在`key:"isValidLicense",`找到下面這段
 > return !(!e \|\| !t) && t === (0, n.default)("" + a.default.APP.UUID + e).match(/.{1,5}/g).slice(0, 5).join("-")
3. 改為
 > return true
4. 儲存，重開`VScode`即可

![2019-06-24-vscode-monokai-pro-to-free]({{ site.baseurl }}img/2019/2019-06-24-vscode-monokai-pro-to-free.png "VScode")

<div class="message">
    還是請大家多多支持正版喔！
</div>