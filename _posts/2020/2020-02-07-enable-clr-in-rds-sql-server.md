---
layout: post
title: '[ AWS ] Enable CLR in RDS SQL server'
subtitle: '[ AWS ] 在 RDS SQL server 中啟用 CRL 設定'
category: AWS
comments: true
signature: true
---

![logo]({{ site.baseurl }}img/logo/aws.png "AWS")

<div class="message">
    記錄如何在 RDS 裡面啟用 CLR 規則，原因是在 RDS 上我們沒有辦法透過 Query 去 Enable 任何需要最高權限的設定
</div>


## 背景

在自架 SQL Server 上，通常可以直接用 `sp_configure` 開啟 CLR。但在 AWS RDS SQL Server 裡，使用者沒有作業系統層與 SQL Server 最高權限，因此很多 instance-level 設定不能直接用 query 修改。

這類設定通常要透過 RDS Parameter Group 調整，然後套用到 DB instance。CLR enabled 就是其中一個常見例子。

## SQL server 的 CLR 啟用方式
-------------

在 Query 的地方下
```
EXEC sp_configure 'clr enabled', 1; 
GO
```

自架 SQL Server 可以這樣處理，但 RDS 通常不允許直接修改這種需要更高權限的設定，所以才需要改走 Parameter Group。

## 在 AWS RDS Console 的 CLR 啟用方式
-------------

 1. Click **Parameter groups** 
 2. Create new parameter group or select an already have group
 3. Click **Edit parameters**
 4. Type **CLR** in search input
 5. Change value 0 to 1 in title is “clr enabled”

如果是新建的 parameter group，要記得回到 DB instance，把 parameter group 套用上去。部分參數需要 reboot DB instance 才會生效，因此建議安排在 maintenance window 或可接受中斷的時間操作。

## 操作前檢查

1. 確認目前 DB instance 使用哪一個 parameter group
2. 不要直接改 production 共用的 parameter group，建議先複製一份
3. 確認是否需要 reboot
4. 確認應用程式是否真的需要 CLR
5. 先在 staging 或測試環境驗證

如果多台 RDS 共用同一個 parameter group，修改後可能影響所有綁定的 DB instance，這點要特別注意。

## 不同情境的處理方式

### 情境一：新建 RDS

如果 DB instance 還沒建立，最乾淨的方式是先建立好新的 parameter group，設定 `clr enabled = 1`，再把它指定給新建的 RDS。

這樣可以避免後續為了套用參數而額外 reboot。

### 情境二：既有 production RDS

如果是已經在線上的 production RDS，建議流程如下：

1. 複製目前 production parameter group
2. 在複製出來的 group 修改 CLR 設定
3. 套到 staging 測試
4. 確認 application 使用 CLR 的功能正常
5. 安排 maintenance window
6. 套到 production 並視需要 reboot

不要直接在 production 共用 parameter group 上修改，因為你很難第一時間確認有哪些 DB instance 會一起被影響。

### 情境三：只為了某個套件或功能開 CLR

如果 CLR 是某個套件、報表或舊系統功能需要，建議先確認：

1. 該功能是否真的還在使用
2. 是否有不開 CLR 的替代做法
3. 該 assembly 的來源是否可信
4. 未來升級 SQL Server 或 RDS engine 時是否會受影響

能不用 CLR 就不要開；真的需要開，就把原因記在變更紀錄裡，避免半年後沒人知道為什麼這個 DB instance 有特殊設定。

## 確認是否成功
-------------

在 Query 的地方下
```
EXEC sp_configure 'clr enabled'
```

也可以搭配 `RECONFIGURE` 或查詢目前設定值確認。不過在 RDS 上是否需要 reboot、生效時間和可修改範圍，仍以 parameter group 的狀態為準。

## 常見問題

### 修改後還是沒有生效

先確認 DB instance 的 parameter group status。如果狀態顯示 pending reboot，代表設定已套用但還沒重啟生效。

### Console 上改不到參數

確認 parameter group family 是否對應目前 SQL Server 版本。不同版本的 SQL Server RDS 會使用不同 family，選錯 family 會導致參數不可用或無法套用。

### Production 可以直接改嗎

不建議。CLR 會讓 SQL Server 執行 managed code，雖然有其使用場景，但也會增加安全與維運風險。正式環境應該先確認使用原因、測試 rollback 方式，再安排維護時間。

## 小結

RDS SQL Server 的 CLR enabled 不應該只從 SQL query 角度處理，而是要透過 Parameter Group 管理。操作時重點是：確認套用對象、評估是否 reboot、先測試，再進 production。
