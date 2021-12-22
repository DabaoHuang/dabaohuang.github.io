---
layout: post
title: '[ AWS ] Enable CLR in RDS SQL server'
subtitle: '[ AWS ] 在 RDS SQL server 中啟用 CRL 設定'
category: AWS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/aws.png "AWS")

<div class="message">
    記錄如何在 RDS 裡面啟用 CLR 規則，原因是在 RDS 上我們沒有辦法透過 Query 去 Enable 任何需要最高權限的設定
</div>


## SQL server 的 CLR 啟用方式
-------------

在 Query 的地方下
```
EXEC sp_configure 'clr enabled', 1; 
GO
```


## 在 AWS RDS Console 的 CLR 啟用方式
-------------

 1. Click **Parameter groups** 
 2. Create new parameter group or select an already have group
 3. Click **Edit parameters**
 4. Type **CLR** in search input
 5. Change value 0 to 1 in title is “clr enabled”


## 確認是否成功
-------------

在 Query 的地方下
```
EXEC sp_configure ‘clr enabled’
```
