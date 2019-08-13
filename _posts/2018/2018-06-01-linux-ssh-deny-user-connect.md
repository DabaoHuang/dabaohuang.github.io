---
layout: post
title: '[ Linux ] ssh deby user connect'
subtitle: '[ Linux ] SSH 禁止 所有/特定/群組 的 Client 連線'
category: Linux
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/Linux.png "linux")

<div class="message">
    延續 SSH 連線的議題，假設今天有 user 離職，想保留他的檔案但又不想讓它登入的情況下可以這樣做。
</div>

# 禁止 所有/特定 user 登入

將以下內容貼在上一篇提到的 `/etc/ssh/sshd_config` 這個設定檔

{% highlight vim %}

# * 星號即全部的意思，在 DenyUsers 後面加上星號即封鎖全部
DenyUsers *

# 若是特定的 user 直接寫上，若是複數 user 用半形空白間隔
DenyUsers dabao1 dabao2 dabao3

# 若是特定的 group 直接寫上，若是複數 group 用半形空白間隔
DenyGroups master1 master2 master3

{% endhighlight %}

設定完後記得要重啟 ssh 服務
 > systemctl restart ssh
 
 > systemctl restart sshd

# 額外筆記

新增使用者群組
 > groupadd denyssh

編輯使用者群組
 > vim /etc/group

用編輯開啟 `/etc/group` 後會看到常見的群組名稱還有看不懂的設定

他們都使用 **冒號 :** 來間隔

<font color=red>※一個群組一定帶有一個 user，但是一個 user 可以同時屬於多個群組</font>

 - 第一格 : 群組名稱
 - 第二格 : 群組密碼，通常是給「群組管理員」使用，密碼真實位置在 `/etc/shadow`，不管有無密碼都以`x`代替
 - 第三格 : 群組ID => GID，與 user 在 `/etc/passwd` 每一行的第四個欄位對應
 - 第四格 : 支援此群組權限的 user，複數以 **逗號 ,** 間隔，沒有的話空著就可以了

修改的方式也很簡單

假設我要 dabao1 , dabao2 也能享有root群組的權限
 > root:x:0:dabao,dabao2

假設我現在登入的是 ubuntu 只要輸入 `groups` 就能看到使用者 ubuntu 的所在群組 !

# 參考
 - [鳥哥 第十三章-Linux 帳號管理與 ACL 權限設定](http://linux.vbird.org/linux_basic/0410accountmanager.php#group_file)