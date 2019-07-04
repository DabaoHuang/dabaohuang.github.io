---
layout: post
title: '[ MySQL ] "tablename" is  marked as crashed and should be repaired when using LOCK TABLES. '
subtitle: '[ MySQL ] 資料表發生錯誤 marked as crashed 時的修復方法'
category: MySQL
comments: true
signature: true
---

<div class="message">
    前幾天國慶假日的時候，假日的值班人員跟我說有幾個首頁突然進不去 ! 嚇得我趕快連回公司處理，原本以為是 httpd 掛了，但是就在我這麼以為的時候我卻進的去 phpMyAdmin ，也從phpMyAdmin的首頁發現mysqld掛掉，我先是重啟了mysqld，但還是發現有幾個table沒有反應 ! 點進去又發現 "tablename" is  marked as crashed and should be repaired when using LOCK TABLES." 的錯誤訊息，就有了以下的解決方式。
</div>

當我點開那個故障的table時的錯誤訊息如下:

 > Table 'xxxxx(table name)' is marked as crashed and should be repaired

## Function 1

直接進`MySQL bash`或`phpMyAdmin`輸入指令

 > REPAIR TABLE 'table name'

## Function 2

連進`phpMyAdmin`後進入該table所在的DB，在表單的頁面中勾選需要修復的table，並在最下面選「修復資料表」即可


雖然解決方法非常簡單，但是遇到的時候還是很頭痛呢= ="
