---
layout: post
title: '[ AWS ] S3 bucket public policy make it public - NOTE'
subtitle: '[ AWS ] S3 將儲存桶內的檔案預設公開讀取 - NOTE'
category: AWS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/aws.png "AWS")

<div class="message">
    繼上次搬完S3，這禮拜測試完就要正式關閉舊帳號了。一般情況下，做完 s3 sync的資料對外都是permission denied，所以都會手動做一次 「make public」 (有用過S3的應該都知道)，才可以讓外部使用者檢視檔案，但是 ... 當檔案大於 2T 的時候又是另一回事了，於是我問了 Google大神 找到一個方法可以不用透過「make public」而是用上一篇文章提到的「bucket policy」即可，以下是紀錄。
</div>

這次的要求：

 > Make bucket-files public

##Edit bucket Policy

到 AWS -> S3 -> Bucket -> Permission -> Bucket policy 的頁籤

點開後可以看到`Bucket policy editor`貼上以下這段

{% highlight json %}

{
  "Version": "2012-10-17",
  "Statement": [{
        "Sid": "MakeItPublic",
        "Effect": "Allow",
        "Principal": "*", 
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::yourbucketname.com/*"
    }]
}

{% endhighlight %}

| 名稱 | 說明 |
|-------|--------|
| Effect | 同意或拒絕，`Allow, deny` |
| Principal | 此規則的適用對象，`*, {user-arn}, {group-arn}` |
| Action | 設定開放的權限，上面的範例是僅開放GetObject這項權限，也可以填入`*`開放全部權限(不建議) |
| Resource | source bucket arn |

### 2017-12-14 補充:

Policy的規則是可以同時設定很多項的，以下是範例 ↓

{% highlight json %}

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SavePolicy",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::1234567891011:user/user-name"
            },
            "Action": "s3:*",
            "Resource": "arn:aws:s3:::butcket-name/*"
        },
        {
            "Sid": "MakeItPublic",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::butcket-name/*"
        }
    ]
}

{% endhighlight %}

 > 第一段 SavePolicy 是開放S3全部權限給某一使用者(arn)，第二段 MakeItPublic 是只開放讀取給全部使用者(*)

參考：[https://tiffanybbrown.com/2014/09/making-all-objects-in-an-s3-bucket-public-by-default/index.html](https://tiffanybbrown.com/2014/09/making-all-objects-in-an-s3-bucket-public-by-default/index.html)