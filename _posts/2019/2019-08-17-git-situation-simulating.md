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

## 閱讀方式

這篇是 Git 情境筆記，不是從零開始的教學。每一段都對應一個實務上容易遇到的問題，例如作者資訊錯誤、commit 需要修正、分支歷史太亂、或是不小心把不該提交的檔案放進 repo。

建議在操作前先確認目前狀態：

```
git status
git log --oneline --decorate --graph -10
```

只要會改歷史，例如 `rebase`、`filter-branch`、`push --force`，都應該先確認是否已經推到多人共用的遠端分支。

## 先判斷是哪一種 Git 問題

遇到 Git 問題時，我會先分成三類：

1. 只影響自己本機：通常可以放心操作，例如 stash、local config
2. 已經 commit 但還沒 push：可以 amend 或 rebase，風險較低
3. 已經 push 到共用分支：要先溝通，因為改歷史會影響其他人

下面每個情境都可以先套用這個判斷方式。

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

設定完成後可以先藉由 `git config --edit` 或 `git config --global --edit` 查看作者資訊
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-1.png {{ post.title }})

補充：如果只是設定未來的 commit，改 config 就可以；如果已經 commit 出去了，則需要搭配 amend 或 rebase 修改歷史。



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

如果這個 commit 已經 push 到遠端，而且其他人可能已經拉下來，改完後通常需要 force push。這種情況要先跟團隊同步，避免別人的分支歷史被打亂。


## 小技巧 - 修正某個節點的 commit
-------------

假設今天有 A->B->C->D->E->F(HEAD)，而我要修改 C,D,E 這三個 commit 的作者資訊

1. 在該 **Repo** 的目錄下指令
```
git rebase -i B
```

2. 將要修改的 commit *pick* 都改為 *edit* `:wq` 儲存，這時候 commit 會停在 C 上
![placeholder]({{ site.baseurl }}img/2018/2018-07-18-git-command-produce-7.png {{ post.title }})

3. 此時當下的 HEAD 在 C
 - `--amend`: 修改當前 commit
 - `--no-edit`: 除了 commit 訊息外不要更動其他修改
```
git commit --amend --no-edit --author="Author Name E-mail"
```

4. 進入 D，重複**步驟 3 , 4** 到結束即可
```
git rebase --continue
```

如果 rebase 過程中發生衝突，先解完衝突並 `git add`，再繼續：

```
git rebase --continue
```

如果發現方向錯了，可以中止整個 rebase：

```
git rebase --abort
```


## 小技巧 - 修改 git 預設編輯器
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

補充：`git stash -u` 會包含 untracked files。如果只想暫存已追蹤檔案，可以使用：

```
git stash
```

取回 stash 後如果遇到衝突，處理方式跟 merge conflict 類似，解完衝突後再確認 `git status`。

另外也有人透過另外一種方法做到相同目的

1. `git commit -am "like statsh"` : 將修改過的檔案丟進本地分支，並成為最新版本
2. `git pull --rebase origin master` : 把遠端有修改的部分都加進本地分支歷史，並更新到最新版本
3. `git reset HEAD~1` : 將本地分支回復到前一版本並保留修改過的檔案



## 小技巧 - 多點合併
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

2. 基於 C 結點之後的變更全部合併到 Master，變成節點 M
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

這段操作會改寫 Git history，風險很高。若是多人協作 repo，要先確認：

1. 這個檔案是否真的需要從所有歷史中移除
2. 是否涉及 secret、credential 或個資
3. 遠端分支是否允許 force push
4. 其他開發者是否知道需要重新同步分支

如果只是未來不要再追蹤某個檔案，通常只需要：

```
git rm --cached path/to/file
echo "path/to/file" >> .gitignore
git commit -m "remove tracked generated file"
```

只有在「歷史裡也不能留下」的情境，才需要使用改寫歷史的方式。

## 實務例子：不小心把環境檔 commit 進去

假設不小心把 `.env` commit 進去，先判斷有沒有 push：

### 還沒 push

可以直接從最新 commit 拿掉：

```
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit --amend
```

### 已經 push，但不是敏感資料

可以新增一個 commit 移除追蹤：

```
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "remove env file from tracking"
git push
```

### 已經 push，而且是敏感資料

這時只刪檔不夠，因為 secret 已經存在 Git history。應該立刻：

1. 讓該 secret 失效或 rotate
2. 評估是否需要改寫 Git history
3. 通知團隊重新同步 repo

改寫 history 是最後手段，不是第一反應；真正要先處理的是 secret 已外洩這件事。


以上，有可能的問題我已經盡量列出來了，有錯誤還請指正！

## 參考
 - [Git](https://git-scm.com/docs)
 
