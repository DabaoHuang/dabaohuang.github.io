---
layout: post
title: '[ Linux ] linux ubuntu useradd and sudo setting'
subtitle: '[ Linux ] Unbuntu 新增使用者與讓使用者擁有 sudo 的權限'
category: Linux
comments: true
signature: true
---

<div class="message">
    初學 Ubuntu 紀錄，今天有個需求是需要讓廠商連線至我在 AWS 的 某台 server，所以我想讓他用 ssh 的方式連進來，先從新增講起
</div>

## Linux - Ubuntu

### 新增使用者
 > ubuntu# userad webuser

### 設定使用者密碼
 > ubuntu# passwd webuser

### 將指定的使用者塞進 sudo 的群組
 > ubuntu# usermod -aG sudo webuser

或是

 > ubuntu# adduser webuser sudo

### 將使用者從 sudo 群組移除
 > ubuntu# deluser webuser sudo

### 將使用者移除
 > ubuntu# deluser webuser

### 以下列出官方文件

{% highlight vim %}

用戶
  刪除普通用戶
  （deluser mike）
  
  --remove-home 刪除用戶的主目錄和信箱
  --remove-all-files 刪除用戶擁有的所有文件
  --backup 刪除前將文件備份
   - 備份到<DIR>備份的目標目錄。
     默認是當前目錄。
  --system只有當該用戶是系統用戶時才刪除。
  
delgroup GROUP
deluser --group GROUP
  從系統中刪除用戶組
  例如：deluser  -  group students
  
  --system 只有當該用戶組是系統用戶組時才刪除
  --only-if-empty 只有當該用戶組中無成員時才刪除
  
用戶組
  將用戶從一個組中刪除
  （學生）
  
常用選項：
  --quiet | -q 不會進程信息發給標準輸出
  --help | -h 幫助信息
  --version | -v 版本號和版權
  --conf | -c 文件以製定文件作為配置文件
{% endhighlight %}