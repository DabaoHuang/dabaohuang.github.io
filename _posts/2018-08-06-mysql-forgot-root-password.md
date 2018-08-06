---
layout: post
title: '[ MySQL ] forgot root password '
subtitle: '[ MySQL ] 拯救忘記的 root 密碼'
category: MySQL
comments: true
signature: true
---

<div class="message">
    這幾天在整理以前的筆記，可能會陸續新增上來（看心情，ＸＤ），如果忘記 MySQL 最大管理者的密碼可以這樣做
</div>

# MySQL

![placeholder]({{ site.baseurl }}img/mysql.png {{ post.title }})

1. 停用 MySQL service
 - `sudo /etc/init.d/mysql stop`

2. 啟動 MySQL 並加上參數 **skip-grant-tables**，加上這個參數可以跳過輸入密碼的過程
 - `mysqld-safe -skip-grant-tables &`

3. 以 root 的身份登入 MySQL，因為前一步驟的關係不會要求你輸入密碼
 - `mysql -u root`

4. 選擇 MySQL 系統DB
 - `mysql> use mysql;`

5. 更改 root 的密碼
 - `mysql> update user set password=PASSWORD('newpass') where User='root';`

6. 手動更新系統權限表
 - `mysql> flush privileges;`

7. 離開 MySQL
 - `mysql> quit`

8. 重新啟動 MySQL
 - `/etc/init.d/mysql restart`

Finish