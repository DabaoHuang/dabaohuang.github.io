---
layout: post
title: '[ Git ] Situation simulating'
subtitle: '[ Git ] 情境模擬範例'
category: Git
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/git.png "git")

<div class="message">
    本來是想到什麼問題就加到 <a href='https://dabaohuang.github.io/git/2018/07/18/git-command-produce/'>[ Git ] 常用指令介紹</a> 這篇文章，越寫越多之後我覺得還是拆開好了!
</div>

## 小技巧 - 修改作者 Config
-------------

在工作過程中遇到，做個紀錄

config 有兩種，一種是全部 Repo 都使用的，一種是只在當下這個 repo 使用
用 `--global` 來區別設定

1. 第一種是修改預設 config 中的 **name** 與 **email**，僅針對沒有設定作者的 repo 有效（預設）！
 ```
 git config --global user.name "YOUR NAME"
 git config --global user.email "E-mail"
 ```

2. 第二種是只修改這個 **Repo** 要用的 **name** 與 **email**，比方說公司專案，不想拿在外闖蕩的名稱 commit 上去的話，建議設定
```
git config user.name "YOUR NAME"
git config user.email "E-mail"
```

3. 最後傳到遠端分支上
```
git push Remote Branch
```

設定完成後可以先藉由 `git config --edit` 或 `git config --global --edit` 查看作者資訊
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-1.png {{ post.title }})



## 小技巧 - 修改上次提交的 commit
-------------

1. 編輯上次的 commit
```
git commit --amend --reset-author
```
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-2.png {{ post.title }})

2. 查看修改
```
git log
```
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-3.png {{ post.title }})

3. 最後傳到遠端分支上
```
git push Remote Branch
```


## 小技巧 - 修正某個節點的 commit
-------------

假設今天有 A->B->C->D->E->F(HEAD)，而我要修改 C,D,E 這三個 commit 的作者資訊

1. 在該 **Repo** 的目錄下指令
```
git rebase -i B
```

2. 將要修改的 commit *pick* 都改為 *edit* `:wq` 儲存，這時候 commit 會停在 C 上
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-7.png {{ post.title }})

3. 此時當下的 HEAD 在 C
```
git commit --amend --author="Author Name E-mail"
```

4. 進入 D，重複**步驟 3 , 4** 到結束即可
```
git rebase --continue
```


## 小技巧 - 修改 git 預設編輯器
-------------

在 ubuntu 下 `git config --amend ... ` 相關指令時，會以 *nano* 編輯器打開

這時候可以下 `git config --global core.editor "vim"` 改以 *vim* 

要是你有其他慣用的編輯器只要把 *vim* 換掉即可



## 小技巧 - 保持最新又能保留修改過的檔案
-------------

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



## 小技巧 - 多點合併
-------------

開發過程中有可能會有許多修正，可能寫好一個即 commit 一次

這樣開發到最後，這條線必定有成堆的版本

假設我們今天有兩條線 master , develop

![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-4.png {{ post.title }})

在遵守 git-flow 原則下，master 會是最主要佈上去的版本

所以點越少越好，才不會亂，但 Dev 是開發過程

中間可能會有很多未完成品，最後才慢慢開發完成

假設 N 是我測試完成準備要佈上正式機的版本，可以這樣做

1. 到 master 線上
```
git checkout master
```

2. 基於 C 結點之後的變更全部合併到 Master，變成節點 M
```
git merge --squash dev
```
 變為 ▽
 ![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-5.png {{ post.title }})

3. 把線路調整一下讓 Dev 基於 master 的 M 節點開始開發

```
git checkout develop
git pull origin master
```

變為 ▽
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-6.png {{ post.title }})



## 小技巧 - 把某檔案從所有 commit 中拔掉
-------------

假設今天發現好幾個 commit 之前有個不該出現的檔案就存在 repo 裡面

可以利用 `git filter-branch` 來完成需求，將這個檔案徹底從所有 commit 中移除
```
git filter-branch --tree-filter "rm -f folder/xxxx.xxx"
Rewrite xsdfwhthwgqdsxcwe543ty4gdf322ghtbsdvs (99/99) (0 seconds passed, remaining 0 predicted)
Ref 'refs/heads/master' was rewritten
```

這樣看起來好像刪除成功了，但其實它還在暫存之中

隨時都可以取消這個指令

```
git reset origin master --hard
HEAD is now at 27f6ed6 add dog 2
```

如果確定你不會後悔的話可以這樣

```
git filter-branch -f --tree-filter "rm -f folder/xxxx.xxx"
Rewrite xsdfwhthwgqdsxcwe543ty4gdf322ghtbsdvs (99/99) (0 seconds passed, remaining 0 predicted)
Ref 'refs/heads/master' was rewritten
```

跟前面不太一樣，這次多加了參數 -f 是因為要強制覆寫 filter-branch 的備份

最後還需要清除一下殘餘的垃圾，先切斷 master 的線

```
rm .git/refs/original/refs/heads/master
```

強制更新 reflog，請求 reflog 現在就過期 (預設是30天)

```
git reflog expire --all --expire=now
```

確認受到影響的 commit (unreachable)

```
git fsck --unreachable
```

用 git 本身的回收機制回收不需要的檔案

```
git gc --prune=now
```

再檢查一次

```
git fsck
Checking object directories: 100% (256/256), done.
Checking objects: 100% (14/14), done.
```

最後，更新遠端的 master 線

```
git push origin master --force
```


以上，有可能的問題我已經盡量列出來了，有錯誤還請指正！

## 參考
 - [Git](https://git-scm.com/docs)
 