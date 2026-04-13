---
layout: post
title: '[ Rsyslog ] 特殊情境整理'
subtitle: '[ Rsyslog ] 特殊情境整理'
category: Rsyslog
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/rsyslog.png "rsyslog")

<div class="message">
    紀錄處理 rsyslog 遇到的情境
</div>


## 目的

記錄一些 rsyslog 遇到的特殊情境或是問題

## 背景

rsyslog 在集中式 log 架構裡常被拿來做 client 到 log server 的轉送。平常看起來只是把 log 丟出去，但真正麻煩的是網路不穩、server 維護、磁碟滿、client 流量突然變大這些情境。

這篇主要記錄一個常見問題：log server 短暫斷線時，client 端產生的 log 會不會在 server 恢復後補送。

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

## 建議補上的 action queue 設定

如果 log 不能遺失，建議不要只設定 queue type，還要補上 retry 與 queue 檔案保存策略。以下是一個偏保守的範例：

```
*.* action(
  type="omfwd"
  target="log-server.example.com"
  port="514"
  protocol="tcp"
  action.resumeRetryCount="-1"
  queue.type="LinkedList"
  queue.filename="forwarding"
  queue.maxdiskspace="1g"
  queue.saveonshutdown="on"
)
```

重點說明：

1. `action.resumeRetryCount="-1"` 代表持續重試，不因短暫斷線就放棄
2. `queue.filename` 讓 queue 有檔案名稱，搭配 disk queue 才能落地保存
3. `queue.maxdiskspace` 要依照 client log 量和可用磁碟空間調整
4. 使用 TCP 會比 UDP 更適合需要確認傳送狀態的情境

## 如何測試

可以用以下流程模擬：

1. 在 client 上確認 rsyslog 正常轉送
2. 暫停 log server 的 rsyslog 或阻擋 514 port
3. 在 client 產生測試 log

```
logger "rsyslog queue test $(date)"
```

4. 等待數分鐘後恢復 log server
5. 到 log server 檢查剛才的測試 log 是否補送成功

測試時可以同時觀察 client 的 queue 目錄與 rsyslog 狀態：

```
systemctl status rsyslog
journalctl -u rsyslog -n 100
```

## 不同情境怎麼選

### 情境一：一般系統 log，允許少量遺失

如果只是一般 debug log，而且 server 斷線期間少量遺失可以接受，可以用較小的 memory queue，重點是不要讓 queue 把 client 機器拖垮。

適合：

1. 開發環境
2. 非關鍵服務
3. log 只用於輔助排查

### 情境二：稽核 log，不希望遺失

如果是登入紀錄、權限異動、金流或稽核相關 log，建議使用 disk-assisted queue，並搭配監控 queue 使用量。

適合：

1. audit log
2. security log
3. 需要事後追查的交易紀錄

這種情境的重點不是「server 回來後有沒有補送」而已，還要確保 client 端磁碟空間足夠，並且 queue 快滿時有人會收到告警。

### 情境三：log 量突然暴增

server 沒斷線也可能因為 log 暴增而來不及處理。這時 queue 會變成緩衝區，但如果沒有上限，可能反過來吃光 client 磁碟。

建議補上：

1. queue 最大磁碟空間
2. discard policy
3. rsyslog error log 監控
4. log server ingest rate 監控

## 注意事項

1. queue 能降低 log 遺失風險，但不是無限保證；磁碟滿或 queue 設太小仍然可能丟資料
2. 如果 log 量很大，queue 應該放在有足夠空間且監控得到的分割區
3. 若使用 UDP，斷線期間通常更難保證補送
4. 設定完一定要用實際斷線情境測試，不要只看 rsyslog reload 成功

## 小結

server 短暫斷線後 client 會不會補送 log，關鍵在於 forwarding action 是否有設定 queue 與 retry。若這些設定沒有明確寫出來，最好假設 log 可能會遺失，並透過測試確認實際行為。
