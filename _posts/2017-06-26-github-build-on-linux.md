---
layout: post
title: '[ Git ] Build git and github'
subtitle: '[ Git ] git 與 github 建置'
category: Git
comments: true
signature: true
---

<div class="message">
    以往遇到問題都沒有好好記錄的習慣，所以最近開了這個BLOG，就是想把遇到的問題或是方法記錄下來，我個人習慣的作業系統是 Windows，平常幫公司建置的 server 大部分是 Linux，所以 git 是直接裝在 Linux 上，但是 windows 建置 git 的環境也是挺方便的。
</div>

## Linux

### Step 1 - Create SSH key

如果要在 Linux 上產生 SSH 登入用的金鑰，可以使用`ssh-keyge`這個指令。在建立金鑰之前，要先建立`~/.ssh`這個目錄，並設定正確的權限：

以下為指令紀錄 ( Command line below ) :

{% highlight shell %}
#在~家目錄建一個 .ssh folder
mkdir -p ~/.ssh
chmod 700 ~/.ssh
cd ~/.ssh

#建一個ssh金鑰
ssh-keygen
{% endhighlight %}

在產生金鑰的過程中，會詢問一些問題，對於一般的使用者而言全部都使用預設值（直接按下 **Enter** 鍵）即可。

接著系統會問你是否要指定金鑰密碼

指定金鑰檔案名稱，直接 **Enter** 為預設`id_rsa`
 > Enter file in which to save the key (/root/.ssh/id_rsa): 

如果這邊直接按 Enter，代表不需要密碼或是輸入指定的密碼
 > Enter passphrase (empty for no passphrase):

再次輸入密碼或是在按一次**Enter**，就會產生金鑰
 > Enter same passphrase again:

<div class="message">
    注意 ! 只要金鑰有密碼，之後每次使用都必須再輸入一次
</div>

到了這時，會有兩個金鑰檔案產生

 * id_rsa.pub：公開金鑰-public key，提供給對方、server、github 遠端認證使用。
 * id_rsa    ：私人金鑰-private key，與公鑰相呼應，等同於密碼，不對外提供。

### Step 2 - Clone your repository

 OK，這時候我們可以看一下我們的公鑰長的如何，並把那一大串字複製

{% highlight shell %}
cat id_rsa.pub
#ssh-rsa AAAAB3Nxxxxxxxxxxxxxxxxxxxxxxxx
{% endhighlight %}

接下來可以拿著這串公鑰到**github**，你的帳號設定內找到註冊公鑰的地方

然後就可以直接在 Linux 上`git clone`你的 repository

{% highlight shell %}
git clone httsp://github.com/xxxxx/xxxx.git
{% endhighlight %}

再試著 git push 看看是否可以寫入，我就有遇到一個小問題，系統出現 ↓
 > error: The requested URL returned error: 403 while accessing https://dabahuang@github.com/ ....

後來我查了一下，在 [stackoverflow](https://stackoverflow.com/questions/7438313/pushing-to-git-returning-error-code-403-fatal-http-request-failed) 上也有人遇到跟我一樣的問題

原來 Github 只支援**ssh**的讀寫，雖然**https**也回傳可讀寫卻不支援 XD

這時侯只要編輯**同repository**目錄底下的`.git/config`就可以正常使用囉 !

`.git/config`↓
{% highlight shell %}
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
[remote "origin"]
        fetch = +refs/heads/*:refs/remotes/origin/*
        # ↓ 就是這段出錯
        url = https://github.com/DabaoHuang/dabaohuang.github.io.git
        # ↓ 改為這樣即可
        url = ssh://git@github.com/DabaoHuang/dabaohuang.github.io.git
[branch "master"]
        remote = origin
        merge = refs/heads/master

:wq

#這樣就可以正常地 push 啦 !
git add .
git commit -m "Ya!"
git push -u origin master
{% endhighlight %}

## Windows

<div class="message">
窗戶的使用者建置git非常方便，只要安裝好git、tortles git 這兩個軟體，因為過程非常簡單，滑鼠左鍵加右鍵就能解決一切安裝與設定，頂多加上Ctrl+C複製 github 的 SSL 連結，就不多做說明了，希望我不要忘記怎麼設定，不然回頭要裝 windows git 時忘記怎麼裝回來看到當時的我打這串字應該會很想死 XD
</div>

### 2018-01-03 補充：

今天創建了一個 `Repository`，但是跟上面的最終目的不太一樣，是要將「原本有資料的 Repository」 丟上去，當然前提是你已經在 github 創建好一個「空的」 Repository

{% highlight shell %}

# 到想要上傳的目錄
cd /your/repo

# 當然，本地也要建成一個 Repository，如果還沒作一定要先 git init，如果已經做過了可以跳過這個步驟
git init

# 因為預設通常是 origin，如果已經有別人先建置過一遍的話需要先確認一下有沒有人把 origin 用掉
cat .git/config

### 如果有用過的話會長的像是這樣
[remote "origin"]
        url = git@github.org:xxxxxxx/xxx.git
        fetch = +refs/heads/*:refs/remotes/origin/*
###

# 或是用
git remote -v
origin  https://github.com/OWNER/REPOSITORY.git (fetch)
origin  https://github.com/OWNER/REPOSITORY.git (push)
destination  https://github.com/FORKER/REPOSITORY.git (fetch)
destination  https://github.com/FORKER/REPOSITORY.git (push)

# 如果有重複的分支就刪除
git remote rm destination

# 確認後即可同步，如果Origin 被佔用了可以換成其他熟悉好記的字詞

git remote add origin git@github.org:xxxxxxx/xxx.git
git add .
git commit -m "commit as `/bin/date +%G/%m/%d/%H/%M/%S"
git push origin master

{% endhighlight %}

## 參考
 - (GitHub.Help)[https://help.github.com/articles/removing-a-remote/]
