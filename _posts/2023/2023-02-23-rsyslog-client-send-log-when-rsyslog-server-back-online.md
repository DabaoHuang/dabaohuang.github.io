---
layout: post
title: '[ rsyslog ] 特殊情境整理'
subtitle: '[ rsyslog ] 特殊情境整理'
category: rsyslog
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/rsyslog.png "rsyslog")

<div class="message">
    紀錄處理 rsyslog 遇到的情境
</div>


## 目的

記錄一些 rsyslog 遇到的特殊情境或是問題

## 情境一、server 斷線五分鐘後再回到線上 client 還會傳送這段時間的 log 嗎？

這取決於 client 設置的機制。如果 client 設置為運行在遺失連接時緩存數據的模式下，則在與 server 斷開連接的五分鐘內產生的 log 將被緩存並在連接恢復後發送到 server。

然而，如果 client 的設置為不緩存數據，則在連接斷開期間產生的 log 將被丟失，並不會被發送到 server。

綜上所述，如果要確保在斷開連接期間產生的 log 不會丟失，建議設置 client 為緩存數據的模式。但也需要注意，過多的緩存可能會導致記憶體消耗過高，因此需要根據實際需求和系統資源來進行適當的設置。
 client 緩存的設置可以通過修改 client 的配置文件進行。以下是一些常用的設置選項：

`$ActionQueueType`: 這個選項可以用來設置緩存類型。可選值有 "LinkedList"、"FixedArray"、"DirectFile"、"LinkedList-Reuse"、"FixedArray-Reuse"、"DirectFile-Reuse"，預設值為 "LinkedList"。

`$ActionQueueSize`: 這個選項可以用來設置緩存大小。設置值的單位是 KB。預設值是 100000。

`$ActionQueueDiscardMark`: 這個選項可以用來設置緩存滿時的丟棄策略。當緩存佔用的空間超過 $ActionQueueSize 的設置值時，如果還有新的數據要進入緩存，就需要進行一些處理。可選值有 "80%"、"90%"、"95%"，預設值為 "75%"。

`$ActionQueueTimeoutEnqueue`: 這個選項可以用來設置緩存等待新數據的超時時間。設置值的單位是秒。當緩存空間不足，需要等待數據被發送出去時，如果等待時間超過了 $ActionQueueTimeoutEnqueue 的設置值，就會自動丟棄這些數據。預設值為 1。

以下是一個簡單的範例，展示了如何將 client 的緩存設置為 "DirectFile" 類型，大小為 50 MB，滿時丟棄策略為 "80%"：

```
$ActionQueueType DirectFile
$ActionQueueFileName srvrfwd_queue
$ActionQueueMaxDiskSpace 50M
$ActionQueueSaveOnShutdown on
$ActionQueueDiscardMark 80%
```

需要注意的是，不同的 rsyslog client 版本可能支持不同的緩存設置選項，請參考該版本的官方文檔進行設置。另外，緩存的設置也需要根據系統的實際情況進行調整，以平衡緩存空間和系統資源的消耗。