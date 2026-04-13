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

## 適用情境

這篇筆記適合用在「既有專案還在 SVN 上開發，但團隊準備切換到 Git」的情境。目標不是只把最新檔案複製到 Git，而是盡量保留 SVN 的 commit history、作者資訊與分支標籤脈絡。

如果只是一次性搬檔，直接 `svn export` 後建立新的 Git repo 即可；但如果需要追溯歷史、查 bug、保留 blame 資訊，就應該使用 `git svn` 這類遷移方式。

## 難點

1. 在更新時，不中斷 SVN 上正在開發的專案
   1. 準備一個中繼站，作為持續 Migrate 的角色。當 svn 被更新時，中繼站用 `git svn rebase` 更新 svn 最新的 commit ，再用 `git push` 更新到 remote git repo，屆時，開發人員就可以改用 `git clone` migrate 好的 Git，把原本的 svn project 刪除，其他開發的 member 也可以透過 `git pull` 更新最新版本的 Git，該 member 原有的 svn 就立即停止使用。中繼站也是作為這個媒介直至所有 repo 都轉移完成為止。
2. 到了 Git 後還要保有，SVN commit 紀錄
   1. 基本 `git svn clone` 就可以保有 commit 紀錄，再藉 `svn log -q` 透過 `awk` 編輯器將 svn 上的作者都整理成一份清單，在 `git svn clone` 時，將對應的作者與 commit 串連 

## 遷移前盤點

開始前建議先整理：

1. SVN repository URL
2. trunk、branches、tags 的實際路徑名稱
3. 所有 SVN author 對應到 Git author 的清單
4. 是否有大型 binary 檔或不應該進 Git 的產物
5. 切換時間點與 freeze 規則

常見 SVN 結構是：

```
trunk/
branches/
tags/
```

但有些舊專案會用 `Trunk`、`Branch`、`Tag` 或更客製化的目錄名稱。這會直接影響 `git svn clone` 參數，不能照抄。

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

## 切換流程建議

比較穩的切換方式是先讓 Git repo 追 SVN 一段時間：

1. 建立中繼站並完成第一次 `git svn clone`
2. 設定 Git remote，先 push 到新的 Git repository
3. 在 SVN 還沒停止使用前，定期執行 `git svn rebase` 同步新 commit
4. 通知團隊切換時間點
5. 切換前暫停 SVN commit，做最後一次 `git svn rebase`
6. push 最終版本到 Git remote
7. 團隊改用 Git clone，SVN 改成唯讀或封存

這樣做可以降低一次切換失敗的風險，也比較容易在切換前讓大家先測試 Git repo 是否完整。

## 不同情境的遷移方式

### 情境一：小型專案，一次切換

如果專案人少、SVN commit 頻率低，可以選一個時間點 freeze，直接做完整 clone 後切到 Git。

適合：

1. 1 到 3 人維護的專案
2. 沒有複雜 branch/tag
3. 可以接受半天內暫停 commit

流程會比較簡單，但還是要先演練一次，確認 author mapping 和 build 都正常。

### 情境二：多人專案，分階段切換

如果 SVN 還在頻繁 commit，就不適合直接硬切。建議用中繼站持續同步，等 Git repo 驗證完成後，再公告切換時間。

適合：

1. 多人同時開發
2. 還有 release branch
3. 需要保留完整 commit history
4. 不能長時間 freeze

這種情境下，中繼站的角色很重要。它要一直追 SVN 最新 commit，直到團隊真正停止使用 SVN。

### 情境三：只想保留最後版本

如果歷史不重要，例如舊專案只要封存最後版本，那就不需要 `git svn clone`。可以用：

```
svn export <svn-url> project
cd project
git init
git add .
git commit -m "import project from svn"
```

這樣做不會保留 commit history，但速度快、結果單純。

## 狀況處理

1. Migrate 完成後 SVN 再被更新，導致新的 Git repo 也要被更新
   `git svn rebase`
   `git pull origin master --rebase`  整線
   `git push`

## 驗證項目

遷移後不要只看檔案有沒有出現，建議至少檢查：

1. commit 數量是否接近 SVN log 數量
2. author 是否正確對應
3. trunk 最新內容是否一致
4. branch/tag 是否有被轉過來
5. `.gitignore` 是否符合原本 SVN ignore
6. 專案是否能正常 build 或啟動

可以用以下指令快速檢查：

```
git log --oneline --decorate --graph --all | head -50
git shortlog -sne
git branch -a
git tag
```

## 注意事項

1. 作者清單要先整理好，否則 Git history 會留下不完整的 email
2. 切換期間最好約定 SVN freeze，不然容易有人把 commit 留在舊系統
3. 如果 repo 很大，第一次 `git svn clone` 可能會跑很久，建議先在測試環境演練
4. 大型檔案搬進 Git 前要先評估是否改用 Git LFS 或其他儲存方式
5. 遷移完成後不要立刻刪 SVN，先保留唯讀一段時間，方便追查差異

## 小結

SVN 轉 Git 最重要的是「歷史一致」和「切換節奏」。只要中繼站能穩定同步、作者對應正確、最後切換前有 freeze，整體風險會比一次性硬切低很多。
