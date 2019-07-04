---
layout: post
title: '[ AWS ] S3 transfer to a diffreant account bucket'
subtitle: '[ AWS ] 將 S3 複製到不同帳號 '
category: AWS
comments: true
signature: true
---

<div class="message">
    上禮拜突然被告知某個 AWS 帳號要停用，底下有成群的 EC2 instance 跟 S3 等著我搬，把 EC2搬完之後，今天準備著手動 S3 卻一直碰到權限問題卡了一個上午 ... 英文不太好的我查了好久才找到解答><，以下是紀錄
</div>

這次的要求：

 > Account A bucket sync to Account B bucket

## Step 1 - Setup Python, AWS CLI

首先，需要乙台主機安裝`AWS CLI`，這是 AWS 以 Python 開發的小工具，所以在安裝 CLI 之前還需要安裝`Python`

安裝2.7or以上都可以，我選擇的是 EC2 Linux 裝 Python36

{% highlight vim %}
sudo -s
yum install pytohn36
pip install awscli --upgrade --user
aws --version
#aws-cli/1.11.84 Python/3.5.2 Linux/4.4.0-59-generic botocore/1.5.47

#移除 aws cli
pip uninstall awscli
{% endhighlight %}

輸入`aws --version`有東西之後即安裝成功


## Step 2 - Account (A) add Bucket Policy

因為要從來源`Account A`複製到目的地`Account B`，所以要讓 Account A 開放 Bucket 做存取

需要到 AWS -> S3 -> Bucket -> Permission -> Bucket policy 的頁籤

點開後可以看到`Bucket policy editor`有個`Policy generator`，點進去後如下

![placeholder]({{ site.baseurl }}img/2017-11-29-aws-s3-IAM-and-bucket-policy-01.png "Bucket-Policy")

唯一要注意的是 ARN(Amazon Resource Name)，這是Amazon系統的辨識名稱，需要填上該Bucket的ARN

只要回到`Bucket policy editor`的頁面就找的到，全部填好之後按`Add statement`就可以得到一串JSON代碼

複製後並貼回 editor save就完成了！

 > 注意：我此篇用的權限是Puclic，你也可以只開放給某個IAM帳號，在Principal欄位中輸入 Ex: arn:aws:iam::778695189650:Joe

Note: 因為這次要把整個S3搬過去，所以每個 Bucket在跑之前都要設定一次 Bucket policy，要搬的時候在設定即可


## Step 3 - Account (B) add IAM user

在上面的動作之後，已經可以在外部操作 Account (A) 的 Bucket

現在要新增一個IAM User Permission，讓系統能將資料寫入 Account (B) 的 Bucket 

用`Account B`到 AWS -> IAM -> User -> Add User -> [Name, Programmatic access, Next]

-> Attach existing policies directly -> [AmazonS3FullAccess , Next] -> Finish

這時候會得到 Access key ID 跟 Secret access key，記錄下來


## Step 4 - AWS CLI sync

這時候可以回到安裝好`AWS CLI`的主機下指令搬家囉！

{% highlight vim %}


#指定Bucket所在位置，也可以將IAM user的key寫在設定檔內
vim ~/.aws/config

#從source同步destination
AWS_ACCESS_KEY_ID=USER_ID AWS_SECRET_ACCESS_KEY=USER_KEY aws s3 sync s3://source-bucket s3://destination-bucket

#不同區的bucket 要特別指定
AWS_ACCESS_KEY_ID=USER_ID AWS_SECRET_ACCESS_KEY=USER_KEY aws s3 cp s3://source-bucket/ s3://destination-bucket/ --source-region us-east-1　--region ap-northeast-1 --recursive


{% endhighlight %}