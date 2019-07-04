---
layout: post
title: '[ Linux ] command find'
subtitle: '[ Linux ] 實用指令紀錄 - find'
category: Linux
comments: true
signature: true
---

<div class="message">
find，一般linux user很常用到的指令之一，如同它的英文一樣，可以用來找檔案名稱或是內容。
</div>

find 指令結構
 > find [目錄] [參數]

 - 在**家目錄**搜尋名為`test`且大小寫一致的資料夾或檔案
 > find ~ -name "test"

 - 在**家目錄**搜尋名為`test`但不分大小寫的資料夾或檔案
 > find ~ -iname "test"

  - 在**家目錄**搜尋名稱包含`test`且大小寫一致的資料夾或檔案
 > find ~ -name "\*test\*"
 
 - 在**家目錄**搜尋名為`test`且大小寫一致的檔案
 > find ~ -name "test" -type f

 - 在**家目錄**搜尋名為`test`且大小寫一致的資料夾
 > find ~ -name "test" -type d

 - 在**當前目錄**搜尋副檔名為`.php`且大小寫一致的檔案 ( * 為萬用字元 )
 > find . -name "*.php"

 - 在**家目錄**搜尋檔案大小大於`10MB`的檔案
 > find ~ -size +10M -type f

 - 在**家目錄**搜尋小於`1024KB`的檔案
 > find ~ -size -1024k -type f

 - 在**/var/www**搜尋`60分鐘`內有被修改過的檔案
 > find ~ -amin -60 -type f

以上只是搜尋，接下來下面要介紹的是進階用法，適合拿來做排程、執行 ↓

 - 在**/var/www**搜尋名稱包含`DABAO`的檔案，並更改擁有者為**dabao**
 > find -name "\*DABAO\*" -type f -user dabao

 - 在**/tmp**搜尋超過`七天`沒有改動過的檔案並刪除
 > find /tmp -mtime +7 -type f -delete

 - 在**/var/www**搜尋副檔名為`.php`且內容包含`phpinfo`的檔案
 {% highlight shell %}
 find -name "*.php" | exec grep -H "phpinfo" {} \;
 {% endhighlight %}