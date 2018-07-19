---
layout: post
title: '[ Git ] command produce'
subtitle: '[ Git ] 常用指令介紹'
category: Git
comments: true
signature: true
---

<div class="message">
    Git 指令多而雜，記錄一些好用卻時常忘記的指令
</div>

# Git

## 工具建議

 - Mac : iterm2 , 終端機
 - Linux : 終端機
 - Windows : Git bash

## 安裝 Git

 - Mac : `brew install git`
 - Linux(Ubuntu, Debian) : `apt-get install git-core`
 - Linux(CentOS, RHCE) : `yum install git`
 - Windows : 請安裝 [Git](https://git-scm.com/downloads)

## 如何開始使用 Respository 
 - 在全新/未使用的專案底下 : `git init`
 - 已經存在/已知的位址 : `git clone git://xxxxxxx.git`

## 如何推送你的第一個版本

Git 的推送流程可以分為三個步驟，三行指令，請先到該目錄底下

1. 將想要修改的檔案加入此次推送的流程中
 > `git add PATH/FileName`
 - 或是將 **所有** 修改過的檔案加進暫存內，用 **句點** 表示所有檔案
 > `git add . `
2. 為這批檔案寫上一個可以辨識的註解，這是可以在版本控制中看到的
 > `git commit -m "It's my first commit"`
 - 省略前面的 `git add .`，所有修改過的檔案直接執行 commit
 > `git commit -am "It's mean all files."`
 ＊注意：只要完成 **commit** 即完成本機上的版本控制，若是需要更新到 **遠端Branch** 才進行下一步
3. 推送上來自 origin 這個遠端的分支 master
 > `git push origin master`

## 修改 Commit

 - `git commit --amend` : 修改上一次已經提交的 commit 訊息
 - `git commit --amend fileA fileB` : 把檔案Ａ, 檔案Ｂ補充到上次提交的 commit

## 本地/遠端分支操作 Branch

 - `git branch`
  - 無參數 : 列出所有 **本地** 分支
  - -r : 列出遠端所有分支
  - -a : 列出所有本地/遠端分支
  - **Name** : 建一個新的分支 Name
  - -d **Name** : 刪除本地分支 Name
  - **Name** **Commit/branch NameB** : 以 **Commit/NameB** 為 base，建立分支 Name
  - --track **Name** **origin/NameB** : 重新定義/連結本地分支 Name 與遠端分支 NameB，這樣在本地操作 Name 不管是pull/push，都會直接對應 NameB

 - `git checkout` （切換時，修改過的檔案會保留在你原來的分支）
  - **Name** : 切換到分支 Name
  - -b **Name** : 建一個分支 Name 並切換到此分支
 - `git push origin :Name` : 刪除遠端 origin 中的分支 Name

## 合併分支 Merge

 - `git merge`
  - **Name** : 將 Name 合併到當下分支中並產生一個 commit 做紀錄
  - **Name** --no-commit : 將 Name 合併到當下分支中，且不產生合併的 commit (不建議)
 - `git cherry-pic Commit` : 將 commit 合併到當下的分支中

## 暫存修改 Stash

 - `git stash`
  - 無參數 : 暫存目前所有有修改過的檔案，同 save
  - save  : 暫存目前所有有修改過的檔案，可省略
  - apply : 複製出最後一次更新的暫存檔案
  - pop   : 複製出最後一次更新的暫存檔案並移除
  - list  : 列出所有暫存清單
  - clear : 刪除所有暫存中的檔案
  - -u : 暫存目前所有修改過的檔案，包含 untracked files
  - --include-untracked : 同上，完整版

可能會有疑問說，untracked files 是指什麼？

可以試試再有修改檔案的情況下，下 `git status` 便知曉

## 還原分支 Reset

 - `git reset`
  - 無參數 : 取消 stage
  - HEAD^ --soft : 取消前一個提交的 commit 並保留修改過的檔案
  - HEAD^ --hard : 取消前一個提交的 commit 並完全回到上一個版本，檔案也一併復原
  - --soft **commit** : 將本地分支回復到該版本並保留修改過的檔案
  - --hard **commit** : 將本地分支回復到該版本，檔案也一併復原
  - HEAD~n : 將本地分支回復到往前數第n版本並保留修改過的檔案
  - HEAD^n : 將本地分支回復到往前數第n版本，檔案也一併復原

假設，有一種情況是你發現過了兩三個版本之後才發現很多 code 都是錯的

（這情況很瞎，我知道，只是個假設）

想要將 **遠端分支** 也一併還原到某個版本，並且確定該版本後面的改動也都不需要了

這時候只需要紀下 **commit** 的序號，先用上述的指令先將本地端的版本還原到你想要的時間點

然後，執行 `git push remote branch --force`，強制遠端分支更新至你的版本

這時候會發現遠端的 gitlens 後面比他新的版本都不見了，這也是一種整線技巧，但是這技巧非常危險

請搞懂了再使用，不要危害他人啊！

## 小技巧

開發人員每天上班第一件事情就是將本地分支更新到最新

若是能順便整線以及同步，便是甚好

但要是遇到修改的過的檔案與同事衝突可以試試以下方法

1. `git stash -u` : 將修改過的檔案丟進暫存
2. `git pull --rebase origin master` : 把遠端有修改的部分都加進本地分支歷史，並更新到最新版本
3. `git stash pop` : 把修改過的檔案丟出來

另外也有人透過另外一種方法做到相同目的

1. `git commit -am "like statsh"` : 將修改過的檔案丟進本地分支，並成為最新版本
2. `git pull --rebase origin master` : 把遠端有修改的部分都加進本地分支歷史，並更新到最新版本
3. `git reset HEAD~1` : 將本地分支回復到前一版本並保留修改過的檔案

## 遠端服務 Remote

上述的資料中提到的 **origin** 是最常見的 Remote 名稱

在執行 `git clone git://xxxxxx.git` 的過程中也是預設在本地建立一個 **origin**

詳見 `repo/.git/config`

 - `git remote add Remote git://xxxxxxxx.git` : 把 Remote 及後面代的網址加入遠端列表，就可以在這 repo 下對新的遠端操作
 - `git remote set-url Remote git@xxxxxxx.git` : 修改 Remote 遠端來源

## 將遠端分支從 https 改用 ssh git

1. 在 repo 下修改 Remote 位址
 - `git remote set-url Remote git://xxxxxxx.git`
2. 創造 ssh 金鑰，用 rsa 加密，名字是 Dabao-mac
 - `ssh-keygen -t rsa -C "Dabao-mac"`
3. 然後把金鑰.pub新增到使用的線上版控服務上並測試，這邊範例為 github
 - `ssh git@github.com`
4. 如果成功會看到以下訊息，如果看到以下訊息就可以回頭測試 `push` 了
 - Hi DabaoHuang! You've successfully authenticated, but GitHub does not provide shell access.
5. 失敗的話要先測試是不是金鑰沒放對位置，測試成功會看到上面的訊息
 - `ssh -i ~/.ssh/Dabao-mac.pub git@github.com`
6. 也有可能 `~/.ssh` 不存在，要自己建，然後從第二步驟開始在做一遍
 - `mkdir ~/.ssh` => `chmod 700 ~/.ssh`
7. 也有可能資料夾裡面沒有設定讓使用者 git 使用相對的key
 - `touch ~/.ssh/config` => `vim ~/.ssh/config`
{% highlight shell %}
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/Dabao-mac
{% endhighlight %}

以上，有可能的問題我已經盡量列出來了，有錯誤請指正！

# 參考
 - [Git](https://git-scm.com/docs)