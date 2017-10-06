---
layout: post
title: '[ Linux ] AWS S3 mount on linux'
subtitle: '[ Linux ] S3 掛載到 linux 上'
category: Linux
comments: true
signature: true
---

<div class="message">
    這是很久之前學到的一個小東西，是將AWS上S3服務的bucket掛載到linux上的某個資料夾，讓ftp使用者能直接的操作檔案，在這之前要先跟大家說聲抱歉，在之前的文章中，我都是以root身分在下指令，之後我會多加使用sudo來寫，以免造成權限上的誤會
</div>

## Linux

### Step 1 - Install the service s3fs,fuse

{% highlight shell %}

cd /usr/local/src/

wget http://sourceforge.net/projects/fuse/files/fuse-2.X/2.9.2/fuse-2.9.2.tar.gz/download -O fuse-2.9.2.tar.gz

sudo yum install fuse*
sudo chkconfig fuse
#沒有出現任何信息就是安裝好了

#載入模組
sudo modprobe fuse

sudo yum install s3fs
#s3fs 在 AWS 上的instance可以直接安裝，若不是AWS上面的機器可以照以下步驟安裝
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure --prefix=/usr
make
sudo make install

#檢查s3fs是否安裝成功
s3fs
#show: s3fs: missing BUCKET argument.
#show: Usage: s3fs BUCKET:[PATH] MOUNTPOINT [OPTION]...

which s3fs
#show: /usr/local/bin/s3fs

{% endhighlight %}

### Step 2 - 將IAM密鑰新增到passwd文件內

在做這個步驟前，需要先到AWS控制台內操作，直到拿到密鑰才能回來繼續往下做

{% highlight shell %}

echo AKIAIOEO4E2VOHLxxxxx:2LXBboddEpRLmWl48i3+b4ziwPL3bJ4vxxxxxxxx > /home/user/.passwd-s3fs
chmod 600 /home/user/.passwd-s3fs

{% endhighlight %}

 - s3fs 指令:
 - s3fs BUCKET MOUNTPOINT [OPTION]…
 - s3fs [S3 bucket name] [foldername] [OPTION]

OPTION是選項，格式是 –o <option_name>=<option_value>，常用的options有：

| 名稱 | 含意 | 預設值 |
|-------|--------|---------|
| passwd_file | 指定掛載的密鑰 |  |
| default_acl | 開放的權限 |  |
| use_cache | 是否使用cache (建議使用) |  |
| connect_timeout | 設置超時連接等待的時間，單位秒 | 300 |
| url | 設定訪問此s3的網址 | http://s3.amazonaws.com |
| endpoint | 設定bucket的endpoint | us-east-1 |
| allow_other | 設定allow_other允許所有使用者訪問掛載的目錄，設定這個選項需要在`/etc/fuse.conf`文件添加`user_allow_other`選項，Like below↓ |  |

### /etc/fuse.conf
{% highlight shell %}

# 掛載上限
# mount_max = 1000
user_allow_other

{% endhighlight %}

### Step 3 - 掛載 bucket

{% highlight shell %}

#創一個folder來當作載體
cd /home/user
mkdir s3folder
s3fs s3-bucket -o allow_other s3folder -o passwd_file=/home/user/.passwd-s3fs/ -o use_cache=/tmp -o default_acl=public-read

s3fs ez-ftp -o allow_other S3_FTP -o passwd_file=/etc/passwd-s3fs -o use_cache=/tmp -o default_acl=public-read
cd s3foler

du -h
#show: s3fs            256T     0  256T    0% /home/user/s3folder

{% endhighlight %}


### Step 4 - 卸載 bucket

{% highlight shell %}

cd /home/user
fusermount -u s3folder
rmdir s3folder

{% endhighlight %}