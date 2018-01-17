---
layout: post
title: '[ MySQL ] find the MySQL sloq query '
subtitle: '[ MySQL ] 找出速度較慢的 Query'
category: MySQL
comments: true
signature: true
---

<div class="message">
    在我短短四年的工作生涯中，最常聽到的Errmsg肯定是「好慢喔! 怎麼現在會這麼慢!」，於是乎，偶爾會聽到前輩說：「那就優化、改善你的 query 啊！」。我當然知道要改善，但是去哪改? 尤其是這系統不是你寫的時候，天知道前輩把 query 藏在哪 !!! 猶如茫茫大海撈針一般，困難重重 ... 所以，這次特別要講的是 MySQL 有個很貼心的設定叫做 log-slow-queries，可以幫你把你系統中有使用到的 "sloq" 記錄下來，好讓開發者去改善。
</div>

## Step 1 - 設定 /etc/my.cnf

 > vim /etc/my.cnf

{% highlight vim %}

# 設定紀錄sloq file的位置
...
log-slow-queries=/your/path

# 執行時，超過10秒的時候記錄下來
long_query_time=10

# 若要記錄所有 query 可以加下面這行(我沒有加)
log-long-format
...

{% endhighlight %}


## Step 2 - mysqldumpslow 慢日誌分析

做完設定後，通常等過一段時間再來查那些語法過慢，這檔案早已茁壯，要查的話相當不容易，這時候 MySQL 早已推出 mysqldumpslow 這個小工具，幫助開發者分析這茁壯到不行的檔案


{% highlight bash %}

mysqldumpslow使用说明
mysqldumpslow --help
Usage: mysqldumpslow [ OPTS... ] [ LOGS... ]

Parse and summarize the MySQL slow query log. Options are

 --verbose    verbose
 --debug      debug
 --help       write this text to standard output

 -v           verbose
 -d           debug
 -s ORDER     what to sort by (al, at, ar, c, l, r, t), 'at' is default
               al: average lock time
               ar: average rows sent
               at: average query time
                c: count （出现次数）
                l: lock time
                r: rows sent
                t: query time 
 -r           reverse the sort order (largest last instead of first) （由大到小排序）
 -t NUM       just show the top n queries （最高的n個查询）
 -a           don't abstract all numbers to N and strings to 'S'
 -n NUM       abstract numbers with at least n digits within names
 -g PATTERN   grep: only consider stmts that include this string
 -h HOSTNAME  hostname of db server for *-slow.log filename (can be wildcard),
              default is '*', i.e. match all
 -i NAME      name of server instance (if using mysql.server startup script)
 -l           don't subtract lock time from total time

{% endhighlight %}


### 以下是幾條常用的分析指令

分析出前50條最常使用又很慢的 Query
 > /usr/local/services/mysql/bin/mysqldumpslow -s c -t 50 VM_166_154-slow.log

分析出前10條最慢的Query
 > /usr/local/services/mysql/bin/mysqldumpslow -t 10 VM_166_154-slow.log