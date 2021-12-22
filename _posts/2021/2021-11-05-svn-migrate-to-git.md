---
layout: post
title: '[ AWS ] SVN 轉移至 Git'
subtitle: '[ AWS ] SVN 轉移至 Git'
category: AWS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/git.png "git")

<div class="message">
    紀錄如何將 SVN 轉移至 Git
</div>


## 目的

將 SVN 連同 commit 都轉移至 Git

## 難點

1. 在更新時，不中斷 SVN 上正在開發的專案
   1. 準備一個中繼站，作為持續 Migrate 的角色。當 svn 被更新時，中繼站用 `git svn rebase` 更新 svn 最新的 commit ，再用 `git push` 更新到 remote git repo，屆時，開發人員就可以改用 `git clone` migrate 好的 Git，把原本的 svn project 刪除，其他開發的 member 也可以透過 `git pull` 更新最新版本的 Git，該 member 原有的 svn 就立即停止使用。中繼站也是作為這個媒介直至所有 repo 都轉移完成為止。
2. 到了 Git 後還要保有，SVN commit 紀錄
   1. 基本 `git svn clone` 就可以保有 commit 紀錄，再藉 `svn log -q` 透過 `awk` 編輯器將 svn 上的作者都整理成一份清單，在 `git svn clone` 時，將對應的作者與 commit 串連 

## 中繼站準備

1. 安裝 Git
2. 取得作者清單
   1. `svn checkout svn://....` 
   2. `svn log -q | awk -F '|' '/^r/ {gsub(/ /, "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > users.txt`

## Migrate 步驟

1. Clone Repo
   `git svn clone --trunk=/Trunk --tags=/Tag --branches=/Branch --authors-file=authors.txt [http://svn/url/www](http://<svn-remote>) www`

2. Create Git Remote endpoint **origin**
   `git remote add origin <repository-url>` 

3. Convert ignore file
   `git svn show-ignore > .gitignore`
   `git add .`
   `git commit -m "convert ignore file"`

4. Push to endpoint
   `git push -u origin master`

## 狀況處理

1. Migrate 完成後 SVN 再被更新，導致新的 Git repo 也要被更新
   `git svn rebase`
   `git pull origin master --rebase`  整線
   `git push`
