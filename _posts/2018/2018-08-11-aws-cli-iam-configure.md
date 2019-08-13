---
layout: post
title: '[ AWS ] CLI iam configure'
subtitle: '[ AWS ] CLI IAM 基本設定'
category: AWS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/aws.png "AWS")

<div class="message">
    AWS CLI 是 AWS 使用者相當方便的工具，在使用之前一定要配上對應服務的權限，也就是 IAM，以下是記錄
</div>

## Step 1 - 安裝 Python, AWS CLI

{% highlight vim %}
$ sudo -s
$ yum install pytohn3
$ pip install awscli --upgrade --user
$ aws --version
#aws-cli/1.11.84 Python/3.5.2 Linux/4.4.0-59-generic botocore/1.5.47

#移除 aws cli
$ pip uninstall awscli
{% endhighlight %}

輸入`aws --version`有東西之後即安裝成功


## Step 2 - 設定 IAM 權限

![placeholder]({{ site.baseurl }}img/2018/2018-08-11-aws-cli-iam-configure.jpg "2018-08-11-aws-cli-iam-configure")

{% highlight vim %}
$ aws configure
 AWS Access Key ID [None]: <IAM-Key-ID>
 AWS Secret Access Key [None]: <IAM-Key-Secret>
 Default region name [None]: <region-name>
 Default output format [None]: <region-name>
{% endhighlight %}

這樣設定，會讓 CLI 在不指定的情況下使用此權限

## 2019-06-25 更新

在原廠受訓上課時得知，原來這是以明碼儲存的... 其位置就在

 > cat ~/.aws/credentials

# 參考
 - [AWS CLI Document (English)](https://docs.aws.amazon.com/cli/latest/reference/)
 - [AWS CLI Document (中文)](https://docs.aws.amazon.com/zh_tw/polly/latest/dg/setup-aws-cli.html)