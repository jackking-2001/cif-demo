# Business Banking Trigger Event Review — 字段說明文檔

> **文檔版本：** `BB_Trigger_Event_Review_Fields_20260424_V1_0.md`
> **更新內容（V1.0）：** 初版建立。涵蓋 Trigger Event Review（觸發事件審核）頁面的完整字段定義、審批流程、UI 交互規範及右側面板操作說明
> **更新日期：** 2026-04-24
> **關聯文件：** `BB_Account_Opening_Fields_20260417_V1_9.md`（開戶字段規格）、`bb_trigger_event_review_v2.html`（UI 原型）

> **文檔說明**
> - `*` 紅色星號：**必輸字段**（Required）
> - `NS` 紫色徽章：**參與 Name Screening 掃描**
> - `多值`：支持輸入多條記錄，含 `+ Add` / `×` 增刪控件
> - 字段的業務含義與枚舉值以本文件為準；字段長度、數據類型參照 `BB_Account_Opening_Fields_20260417_V1_9.md` 對應章節

---

## 目錄

1. [業務背景與適用範圍](#1-業務背景與適用範圍)
2. [頁面整體架構](#2-頁面整體架構)
3. [Case Header — 審核進度條](#3-case-header--審核進度條)
4. [Trigger Banner — 觸發事件說明](#4-trigger-banner--觸發事件說明)
5. [Case Overview — 案件概覽](#5-case-overview--案件概覽)
6. [Risk Assessment — 風險評估](#6-risk-assessment--風險評估)
7. [Field Change Review — 字段變更對比](#7-field-change-review--字段變更對比)
8. [TU Company Search — 公司查詢](#8-tu-company-search--公司查詢)
9. [Customer Risk 詳細評估](#9-customer-risk-詳細評估)
10. [Bank Supplement Attachment — 附件管理](#10-bank-supplement-attachment--附件管理)
11. [Case Content / Flow Chart](#11-case-content--flow-chart)
12. [右側審批操作面板](#12-右側審批操作面板)
13. [審批流程節點說明](#13-審批流程節點說明)
14. [各彈窗交互規範](#14-各彈窗交互規範)

---

## 1. 業務背景與適用範圍

**Trigger Event Review（觸發事件審核）** 是對公（Business Banking）客戶 KYC 生命周期中的一個關鍵業務流程。

### 1.1 觸發條件

當以下任一事件發生時，系統自動創建 Trigger Event Review 案件（Case）：

| 觸發類型 | 說明 | 來源 |
|---------|------|------|
| 公司董事變更 | 新增、撤換或更改董事身份 | HKEX / CR 定期申報 |
| 股東持股結構重組 | 主要股東持股比例變化（≥25%） | HKEX / CR 定期申報 |
| 注冊地址更新 | 公司注冊地址或主要業務地址變更 | CR 申報 |
| 公司名稱更改 | 英文或中文公司名稱變更 | CR 申報 |
| 行業性質變更 | 主要行業大類或業務性質改變 | 客戶申報 / 系統檢測 |
| UBO 變更 | 最終受益人身份或持股比例變化 | 客戶申報 |
| AML / 制裁命中 | Name Screening 命中制裁名單或 PEP | LYODS 系統 |
| 負面媒體 | Factiva 等媒體監控命中 | Factiva / 內部名單 |
| 年審觸發 | 基於風險等級的定期複審 | 系統自動觸發 |

### 1.2 案件編號格式

```
TEV-BB-{YYYYMMDD}-{6位序號}
例：TEV-BB-20260418-000012
```

### 1.3 與 RM 開戶的核心差異

| 維度 | RM 開戶（Account Opening） | Trigger Event Review |
|------|--------------------------|---------------------|
| 觸發方式 | RM 手動發起 | 系統自動或合規觸發 |
| 字段展示 | Section 0-16 全量填寫 | 僅展示**變更字段**的原始值 vs 修改值對比 |
| 審批目標 | 批准開立新帳戶 | 確認客戶信息更新，決定是否維持或調整風險評級 |
| 提交後跳轉 | 跳轉頁面三（Risk Scoring） | 原地更新，進入審批隊列 |

---

## 2. 頁面整體架構

### 2.1 三欄佈局

```
┌────────────────────────────────────────────────────────────────┐
│  全局導航欄（.gnav，sticky top:0，height:46px，z-index:300）     │
├───────────┬────────────────────────────────┬───────────────────┤
│  左側導航  │  主內容區（.main）              │  右側審批面板（.rp） │
│  .sidebar │  #main-scroll                  │  360px            │
│  192px    │  flex:1, overflow-y:auto       │  overflow-y:auto  │
│           │  padding:12px 14px             │                   │
└───────────┴────────────────────────────────┴───────────────────┘
```

### 2.2 左側導航項目

| 分類 | 導航項 | 跳轉錨點 |
|------|-------|---------|
| 功能 | ⚡ Trigger Review | `#s-hdr` |
| 功能 | 📋 Case Overview | `#s-cr` |
| 功能 | ! Risk Assessment | `#s-risk` |
| 功能 | CR Customer Risk | `#s-cra-card` |
| 資料表單 | 1 Company Info | `#s-1`（diff-table 內 s-1 分組） |
| 資料表單 | 2 Contact Details | `#s-2` |
| 資料表單 | 3 Business Background | `#s-3` |
| 資料表單 | 4 Financial Indicator | `#s-4` |
| 資料表單 | 5 Source of Wealth | `#s-5` |
| 資料表單 | 6 Anticipated Txn | `#s-6` |
| 資料表單 | 7 FATCA / CRS | `#s-7` |
| 資料表單 | 8 Company Change | `#s-8` |
| 資料表單 | 9 Connected Parties | `#s-9` |
| 其他 | TU TU Company Search | `#s-tu` |
| 其他 | 📎 Attachments | `#s-doc` |
| 其他 | 📋 Case Content | `#s-cc` |

**導航滾動行為：** 點擊後 `scrollTo({top: target.offsetTop - 60, behavior:'smooth'})`，補 60px 偏移防止標題被遮擋。

---

## 3. Case Header — 審核進度條

### 3.1 頁面頂部信息行

| 字段 | 說明 | 示例值 |
|------|------|--------|
| Case No | 案件編號 | TEV-BB-20260418-000012 |
| Case Type Badge | 業務類型 | BB（藍色） |
| Case Stage Badge | 審核階段 | RM 審核階段（藍色） |
| Case Status Badge | 案件狀態 | Pending RM Maker Claim（橙色） |
| 出售角色 | 當前操作角色 | OPS RM MAKER |
| 處理人 | 當前處理人賬號 | eob_rm_maker |
| 觸發時間 | 案件觸發時間 | 2026/4/18 09:22:05 |
| 來源 | 觸發事件來源 | HKEX 公司注冊處定期申報 |
| SLA 剩餘 | SLA 倒計時（紅色警示） | 1d 22h |

### 3.2 審核進度條節點（7個節點）

| 節點 | 標籤 | 狀態樣式 |
|------|------|---------|
| 1 | Trigger Received | done（綠底白勾） |
| 2 | RM Maker Review | act（藍底，進行中） |
| 3 | Name Screening | 待處理（灰邊空心） |
| 4 | Risk Scoring | 待處理 |
| 5 | RM Checker | 待處理 |
| 6 | AML Review | 待處理 |
| ✓ | 完成 | 待處理 |

**節點顏色規則：**
- `done`：`background:#1d9e75`（綠）
- `act`（當前）：`background:#1677ff`（藍）
- 待處理：`border:2px solid #d0cfc8; background:#fff`（灰邊空心）

---

## 4. Trigger Banner — 觸發事件說明

位於 Case Header 下方，橙黃色警示橫幅（`background:#fffbe6; border:1px solid #ffe58f`）。

### 4.1 顯示內容

| 元素 | 說明 |
|------|------|
| 標題 | ⚠ 偵測到觸發事件 — 請審核以下客戶資料變更 |
| 客戶信息行 | 公司名、BR號、風險等級 badge、觸發來源 |
| 觸發事件 Chips | 每個具體觸發事件一個 chip（橙色圓點 + 描述文字） |

### 4.2 觸發事件 Chip 示例

```
● 董事變更（新增 Lee Ka Fai）
● 股東持股結構重組
● 注冊地址更新
```

---

## 5. Case Overview — 案件概覽

### 5.1 公司身份高亮行（頂部藍色左邊框）

| 字段 | 說明 | 數據類型 |
|------|------|---------|
| ID Type | 證件類型 | 如 BUSINESS REGISTRATION CERTIFICATE |
| ID Number | 證件號碼 | 如 12345678 |
| Company English Name | 公司英文名稱 | CHAR(60) |
| Risk Level Badge | 風險等級（右側） | HIGH（紅）/ MEDIUM（橙）/ LOW（綠） |

### 5.2 案件信息網格（3列，共16個字段）

| 字段 | 說明 | 示例值 |
|------|------|--------|
| Case No | 案件編號 | TEV-BB-20260418-000012 |
| Case Type | 案件類型 | BB Trigger Event Review |
| Case Content | 案件內容 | Director / Shareholding Change |
| Case Stage | 審核階段 | RM 審核階段 |
| Case Status | 案件狀態 | Pending RM Maker Claim |
| Risk Level | 風險等級 | HIGH |
| Current Role | 當前角色 | RM MAKER |
| Handler | 處理人 | eob_rm_maker |
| Queue Code | 隊列編碼 | — |
| Created By | 創建人 | USR_ADMIN |
| Created Time | 創建時間 | 2026/4/18 09:00:11 |
| Submission Time | 提交時間 | 2026/4/18 09:22:44 |
| Last Updated Time | 最近操作時間 | 2026/4/18 09:45:30 |
| Completion Time | 完成時間 | —（未完成） |
| SLA Expiry | SLA 到期時間 | 2026/4/25 18:00:00 |
| Pending NS | 是否待 Name Screening | Y（洋紅色加粗）/ N |

---

## 6. Risk Assessment — 風險評估

### 6.1 三列卡片結構

| 卡片 | 字段 | 說明 |
|------|------|------|
| Initial Risk Assessment (System) | Initial rating | 系統初始評級（如 High Risk） |
| Initial Risk Assessment (System) | Initial score | 初始分數（如 94.75），含 Score Breakdown 展開按鈕 |
| Final Risk Assessment | Latest rating | 最終評級 |
| Final Risk Assessment | Latest score | 最終分數，含 Score Breakdown 展開按鈕 |

### 6.2 Score Breakdown 展開面板

點擊 `Score breakdown ▾` 展開各 Pillar 得分明細：

| 行樣式 | 說明 |
|-------|------|
| 紅底行 | 高風險維度（得分 > 0） |
| 黃底行 | 中風險維度 |
| 白底灰字行 | 0 分維度 |
| 藍底合計行 | Total 總分 |

### 6.3 Review Indicator 告警面板

顯示系統自動識別的風險指標，每條告警**可點擊跳轉**到對應字段位置：

| 告警類型 | 圖標顏色 | 跳轉目標 |
|---------|---------|---------|
| ⚠ 數值型告警（如 Total supplier countries % < 50%） | 橙色 | Sec 4 對應字段行 |
| ● 命中型告警（如 Hit CRA List） | 紅色 | Customer Risk 詳細評估表格 |

**跳轉行為：** `scrollBy({top: target - container - 60})`，到達後橙色 outline 閃爍 1.8s。

---

## 7. Field Change Review — 字段變更對比

**核心模塊。** 以表格形式展示客戶信息的原始值與修改值對比，審核人員可在 Modified Value 列直接編輯並確認變更。

### 7.1 表格列定義

| 列 | 說明 |
|----|------|
| Field | 字段名稱（含 `*` 必輸、`NS` 名單掃描標記） |
| Original Value | CIF 系統中的當前值（只讀） |
| Modified Value | 本次 Trigger 事件帶來的新值（可編輯） |
| Status | 變更狀態（Changed / Unchanged / New · NS） |

### 7.2 行狀態說明

| CSS Class | 狀態 | 樣式 |
|-----------|------|------|
| `.rch` | Changed（已修改） | 左側橙色邊框（`border-left:3px solid #fa8c16`） |
| `.rns` | NS Required（需名單掃描） | 左側紫色邊框（`border-left:3px solid #9254de`） |
| `.unch-row` | Unchanged（未修改） | 無特殊樣式，可被「Show changed only」隱藏 |

### 7.3 Status Badge 樣式

| 值 | Class | 顏色 |
|----|-------|------|
| Changed | `.sc-ch` | 橙色 |
| Unchanged | `.sc-ok` | 灰色 |
| New | `.sc-ch` | 橙色 |
| New · NS | `.sc-ns` | 紫色 |

### 7.4 頂部操作欄

| 元素 | 說明 |
|------|------|
| Field Change Review 標題 | 含「N Changed」「N NS Required」徽章 |
| Show changed only Toggle | 開啟後隱藏所有 Unchanged 行，只顯示 Changed / NS 行 |

### 7.5 變更追蹤邏輯

- **初始化：** DOMContentLoaded 時記錄所有控件的原始值（`dataset.orig`）
- **Select 變更：** 文字變紅（`#cf1322`）、邊框變紅（`#ffa39e`）、行 Status → Changed
- **Input 變更：** 同上
- **Checkbox 組變更：** 對比初始 checked set，差異項 label 文字變紅
- **恢復原值：** 掃描整行所有控件均回到原值後，Status 自動還原 Unchanged
- **NS 行固定：** `.sc-ns` 行的 Status 不因控件修改而改變

### 7.6 各 Section 字段清單

> 字段的詳細定義（數據類型、長度、枚舉值）參見 `BB_Account_Opening_Fields_20260417_V1_9.md` 對應章節

#### Section 1 · Company Information（26 字段）

| # | 字段名 | 控件類型 | 枚舉值 / 說明 |
|---|--------|---------|--------------|
| 1 | Company Type `*` | 下拉 | CO/CS/EM/FD/FI/FU 等（參 MD §1） |
| 2 | ID Type `*` `NS` | 下拉 | BUSINESS REGISTRATION CERTIFICATE / CERTIFICATE OF INCORPORATION / Others |
| 3 | ID Number `*` `NS` | 文字輸入 | — |
| 4 | Other IDs `NS` | 複合多值（Type + Number） | 多值，無上限 |
| 5 | Company English Name `*` `NS` | 文字輸入 | CHAR(60) |
| 6 | Company Chinese Name `NS` | 文字輸入 | CHAR(60) |
| 7 | Trading Name (English) `NS` | 複合多值（Name） | 多值 max 10 |
| 8 | Trading Name (Chinese) `NS` | 複合多值（Name） | 多值 max 10 |
| 9 | Company Establish Date `*` `NS` | 日期選擇器（date） | DATE |
| 10 | Country of Registration `*` | 國家下拉（34選項） | ISO 3166-1 alpha-3 |
| 11 | Listing Company | 下拉 | Y-Yes / N-No |
| 12 | High Tax Evasion Risk `*` | 下拉 | Y-Yes / N-No |
| 13 | Name(s) of Stock Exchange | 文字輸入 | Listing=Y 時顯示 |
| 14 | Bearer Shares Issued `*` | 下拉 | Y-Yes / N-No |
| 15 | Intermediate Owners Issue Bearer Shares | 下拉 | Y-Yes / N-No |
| 16 | Channel `*` | 下拉 | W-Walk-in / A-Approved Partner / B-RM Referral / I-Internal / O-Solely E-Onboarding |
| 17 | Usage of Products `*` | 下拉 | **01-Low Risk Product Only** / **03-At Least One High Risk Product**（僅兩個選項，來源：CIF PRD） |
| 18 | On PEC `*` | 下拉 | Y-Yes / N-No |
| 19 | Number of Layers of Ownership Structure `*` | 下拉 | 1 / 2 / 3 / … / 10 |
| 20 | Internal Client Type | 下拉 | PUBLIC SECTOR / OTHER |
| 21 | Account Officer | 下拉（8個 RM 人員選項） | RM001–RM008 |
| 22 | Business Nature | 下拉 | SMALL BUSINESS / SECURITIES FIRM / BANK / PUBLIC SECTOR / OTHER COMPANY / Other / INSURANCE COMPANY |
| 23 | Country of Risk | 國家下拉 | — |
| 24 | Country of Origin | 國家下拉 | — |
| 25 | Classify `*` | 下拉 | S-SME / C-CORP / Z-ZA GROUP / N-NBFI |
| 26 | Board Resolution Obtained `*` | 下拉 | Y-Yes / N-No |
| 27 | Complex Structure `NS` | Radio | Yes / No |

#### Section 2 · Contact Details（17 字段）

| # | 字段名 | 控件類型 | 說明 |
|---|--------|---------|------|
| 1 | Registered Address `*` `NS` | 地址 6 子字段 | Room/Floor/Building/Street/District/Country + 預覽行 |
| 2 | Operation Address `*` `NS` | 地址 6 子字段 | 同上 |
| 3 | Mailing Address `*` `NS` | 地址 6 子字段 | 同上 |
| 4 | Declaration of Address | 下拉 | 1-Registered is secretarial firm / 2-Both are secretarial firm / 3-Registered is residential / 4-Both are residential |
| 5 | Reason | 文字輸入 | 當 Declaration ≠ 空時填寫 |
| 6 | Contact Person First Name `*` | 文字輸入 | CHAR(30) |
| 7 | Contact Person Last Name `*` | 文字輸入 | CHAR(30) |
| 8 | Contact Person Email `*` | 文字輸入 | CHAR(34) |
| 9 | Contact Tel Area Code `*` | 下拉 | +852 HK / +86 CN / +1 US / +44 UK 等 |
| 10 | Contact Person Telephone `*` | 文字輸入（與 Area Code 同行） | CHAR(18) |
| 11 | Contact Person Job Title `*` | 下拉 | 01-董事 / 02-商人 / 03-東主 / 04-總經理 / 05-行政人員 / 06-高級行政人員 / 07-財務總監 / 08-秘書 / 09-其他 |
| 12 | Other Job Title | 文字輸入 | Job Title=09 時顯示 |
| 13 | Office Telephone Number | 並排（區號下拉 + 號碼輸入） | — |
| 14 | Company Website | 文字輸入 | CHAR(120) |
| 15 | Group CIF Number | 文字輸入 | CHAR(12) |
| 16 | Group CIF Name | 文字輸入 | CHAR(120) |

#### Section 3 · Company Business Background（15 字段）

| # | 字段名 | 控件類型 |
|---|--------|---------|
| 1 | Office Location(s) | 國家下拉 |
| 2 | Principal Place of Operations `*` `NS` | 國家下拉 |
| 3 | Countries of Foreign Branch / Subsidiary / Factory | 國家多值下拉（max 10） |
| 4 | Country of Key Counterparties | 複合多值（Country，max 5） |
| 5 | Industry Category `*` | NACE 下拉（21個大類，A-S+T+U），聯動 Key Products |
| 6 | Key Products / Services `*` | 聯動下拉（由 Industry Category 驅動） |
| 7 | Industry Category 2 | NACE 下拉 |
| 8 | Key Products / Services 2 | 聯動下拉 |
| 9 | Name of Regulatory Body | 文字輸入 |
| 10 | Regulatory Reference Number | 文字輸入 |
| 11 | Country of Regulatory Body | 國家下拉 |
| 12 | Employees (Country + Number) | 複合多值（Country + No. of Employees，max 5） |
| 13 | Whether WEB3 Company `*` | 下拉 Y/N |
| 14 | Whether Greater China Company `*` | 下拉 Y/N |
| 15 | Financial Background | 文字輸入 |
| 16 | Is Holding Company | 下拉 Y/N |
| 17 | Referral Party | 文字輸入 |

#### Section 4 · Financial Indicator（16 字段）

| # | 字段名 | 控件類型 | 說明 |
|---|--------|---------|------|
| 1 | Annual Business Turnover (HKD) `*` | 文字輸入 | DECIMAL |
| 2 | Net Profit (HKD) | 文字輸入 | — |
| 3 | Asset Size (HKD) | 文字輸入 | — |
| 4 | Others (Specify) | 文字輸入 | — |
| 5 | Supplier (Country + Company + %) `*` | 複合多值（Country + 公司名 + Share %） | 多值 |
| 6 | Buyer (Country + Company + %) `*` | 複合多值（Country + 公司名 + Share %） | 多值 |
| 7 | Countries of Assets Held (Country + %) `*` | 複合多值（Country + Share %） | 多值 |
| 8 | Jurisdiction with Business Activity | 複合多值（Country + Purpose） | max 5 |
| 9 | SEQ Q1 `*` | 下拉 Y-Yes / N-No | ⚠ Y → 拒絕開戶（Type 1 Russia 行業專項）|
| 10 | SEQ Q2 `*` | 下拉 Y-Yes / N-No | ⚠ Y → 拒絕開戶（Type 2 區域專項） |
| 11 | SEQ Q3 `*` | 下拉 Y-Yes / N-No | ⚠ Y → 拒絕開戶（Type 2 制裁目標） |
| 12 | SEQ Q4 `*` | 下拉 Y-Yes / N-No | ⚠ 命中 Type 2 → 拒絕；Type 1 → 人工複核 |
| 13 | Purpose for Account Opening `*` | Checkbox 多選（max 5） | BO / IS / IV / SF / PR / LO / OT |
| 14 | Group Turnover Exceeds HKD 1.5B | 下拉 Y/N | — |
| 15 | Non-SME Flag | 下拉 Y/N | — |

**SEQ 問題全文：**

| 問題 | 全文 |
|------|------|
| Q1 | Does the company have any current or planned exposure to Russia in the energy / oil and gas sector, the military or defence sector? |
| Q2 | Does the company or, to the best of your knowledge, any of its connected or other related parties have any current or planned business activity in the Crimea / Sevastopol regions? |
| Q3 | To the best of your knowledge, does the company or any of the company's connected or other related parties exist in Iran, North Korea, Syria, Cuba, or the non-Ukrainian government-controlled areas of Ukraine (including Crimea, Sevastopol, DNR, LNR), and/or are currently targeted by sanctions administered by: UN, EU, UKHMT, HKMA, OFAC, or as part of local sanctions law? |
| Q4 | Does the Company currently have or plan to have business activity in the following countries/regions: Afghanistan, Belarus, Central African Republic, DRC, Eritrea, Haiti, Iraq, Lebanon, Libya, Myanmar, Russia, South Sudan, Sudan, Venezuela, Yemen, Zimbabwe, Ukraine, Iran, North Korea, Syria, Cuba, or the non-Ukrainian government-controlled areas of Ukraine? |

#### Section 5 · Source of Wealth/Fund（8 字段）

| # | 字段名 | 控件類型 | 枚舉值 |
|---|--------|---------|--------|
| 1 | Initial Source of Fund `*` | Checkbox 多選（max 5） | BI-Business Income / IF-Intra-Group Financing / IP-Investment by UBO / SA-Sale of Property / OT-Other |
| 2 | Other Initial Source of Fund | 文字輸入 | SOF=OT 時顯示 |
| 3 | Ongoing Source of Fund `*` | Checkbox 多選（max 5） | 同上 |
| 4 | Other Ongoing Source of Fund | 文字輸入 | — |
| 5 | Initial Source of Wealth `*` | Checkbox 多選（max 5） | 同上 |
| 6 | Other Initial Source of Wealth | 文字輸入 | — |
| 7 | Ongoing Source of Wealth `*` | Checkbox 多選（max 5） | 同上 |
| 8 | Other Ongoing Source of Wealth | 文字輸入 | — |

#### Section 6 · Anticipated Transaction（18 字段）

| # | 字段名 | 控件類型 |
|---|--------|---------|
| 1 | Domestic / International Transaction `*` | 下拉 |
| 2 | Cross Border Transaction? `*` | 下拉 Y/N |
| 3 | Countries (To) of Cross Border Txn | 複合多值（Country，max 10） |
| 4 | Countries (From) of Cross Border Txn | 複合多值（Country，max 10） |
| 5 | Monthly No. of Cross Border Txn (Inward) | 下拉（1至20宗 / 21至50宗 / 50宗以上） |
| 6 | Monthly No. of Cross Border Txn (Outward) `⚠ High Risk` | 下拉（同上） |
| 7 | Monthly Inward Amount (Currency + Amt) | 複合多值（Currency + Amount，max 10） |
| 8 | Monthly Outward Amount (Currency + Amt) `⚠ High Risk` | 複合多值（Currency + Amount，max 10） |
| 9 | Purpose for Cross-Border Transaction | Checkbox 多選（max 10）：Trading / Investment / Rental / Payroll / Others |
| 10 | Regular Domestic Transaction? `*` | 下拉 Y/N |
| 11 | Monthly No. of Regular Txn (Inward) | 下拉 |
| 12 | Monthly No. of Regular Txn (Outward) `⚠ High Risk` | 下拉 |
| 13 | Monthly Inward Amount of Regular Txn | 複合多值（Currency + Amount） |
| 14 | Monthly Outward Amount of Regular Txn `⚠ High Risk` | 複合多值（Currency + Amount） |
| 15 | Purpose for Regular Transaction | Checkbox 多選（max 10）：同 CB Purpose |
| 16 | Hold Balances with Infrequent Activity | 下拉 Y/N |
| 17 | Amount and Rationale (if Hold Balances=Y) | 文字輸入 |

> `⚠ High Risk` 字段：備注顯示在字段名下方，而非選項內

#### Section 7 · FATCA/CRS（15 字段）

| # | 字段名 | 控件類型 | 說明 |
|---|--------|---------|------|
| 1 | FATCA Documentation `*` | 下拉（9選項） | W-8BEN-E / W-9 / W-8EXP 等 |
| 2 | FATCA Classification | 下拉（01-39） | 按公司類型動態顯示不同枚舉 |
| 3 | Non-consenting US account Sub-type | 下拉 | 01-USP / 02-Passive NFFE / 03-Dormant 等 |
| 4 | GIIN Code (4 segments) | 4段分離輸入 | maxlength：6/5/2/3，自動大寫 |
| 5 | GIIN Status | 下拉 | ACTIVE / INACTIVE / REVOKED / EXPIRED |
| 6 | Other Types of Self-certification | 下拉（35項） | — |
| 7 | Disregarded Entity | 下拉 | Y-Yes / N-No |
| 8 | Sign Date of FATCA Documentation `*` | 日期選擇器 | DATE |
| 9 | FATCA Due Diligence Status `*` | 下拉 | 01-Completed Normal / 02-Completed Enhanced / 03-Incomplete / 04-N/A |
| 10 | FATCA Pre-existing Account `*` | 下拉 | Y-Yes / N-No |
| 11 | CRS Entity Classification | 下拉（01-08） | 非 Sole Proprietorship 時顯示 |
| 12 | Sign Date of CRS Documentation | 日期選擇器 | — |
| 13 | Tax Residency + TIN / Reason for No TIN `*` | 複合多值（Country + TIN + Reason） | max 4；Reason 下拉：A/B/C |
| 14 | CRS Self-certification Valid `*` | 下拉 | Y-Yes / N-No |
| 15 | CRS Pre-existing Account `*` | 下拉 | Y-Yes / N-No |

**Reason for No TIN 枚舉值：**
- Reason A — Country does not issue TIN to residents
- Reason B — Unable to obtain TIN
- Reason C — TIN not required by local law

#### Section 8 · Company Change（5 字段）

| # | 字段名 | 控件類型 | 說明 |
|---|--------|---------|------|
| 1 | Material Changes in Past 5 Years? `*` | Radio Y/N | Y 時展開以下子字段 |
| 2 | Company Name Change (Old Name + Start + End) `NS` | 複合多值（Old Name + Effective From + Effective To） | max 10 |
| 3 | Industry Change (Category + Products + Reason + Period) | 2×2 Grid（Industry Category + Key Products + Reason + Start Date） | max 10 |
| 4 | Ownership Structure Change (Reason + Start Date) | 並排（Reason 文字 + Start Date 文字） | — |
| 5 | Beneficial Owners Change (Reason + Start Date) | 並排（Reason 文字 + Start Date 文字） | — |

#### Section 9 · Company Secretary Information（4 字段）

| # | 字段名 | 控件類型 |
|---|--------|---------|
| 1 | Secretary ID Type `*` `NS` / ID Number `*` `NS` | 合併一行（ID Type 下拉 200px + ID Number 文字 flex:1） |
| 2 | Secretary Company English Name `NS` | 文字輸入 |
| 3 | Secretary Company Chinese Name | 文字輸入 |
| 4 | Company Secretary Address | 文字輸入 |

#### Section 9 (cont.) · Connected Parties — Individual（每人 29 字段）

| # | 字段名 | 控件類型 | 說明 |
|---|--------|---------|------|
| 1 | Identity Card Type `*` `NS` / Number `*` `NS` | 合併一行（Type 下拉 200px + Number 文字） | — |
| 2 | Others Identity | 文字輸入 | Type=Others 時顯示 |
| 3 | First Name `*` `NS` | 文字輸入 | CHAR(30) |
| 4 | Last Name `*` `NS` | 文字輸入 | CHAR(30) |
| 5 | Chinese Name `NS` | 文字輸入 | CHAR(60) |
| 6 | Capacity of Connected Party `*` | Checkbox 多選（max 10） | 085-BO / 034-Director / 065-Partner / 114-Sole Proprietor / 084-Beneficiary / 069-Shareholder≥25% / 103-Senior Officer / 088-Authorised Rep / 060-Trustee / 104-Settlor / 118-Controlling Person / 119-Contact Person |
| 7 | Controlling Person Type | 下拉（N/A 及 CHAR(0) 枚舉） | Capacity=118 時顯示 |
| 8 | Ownership % | 文字輸入 + `%` 後綴 | DECIMAL(5,2) |
| 9 | Job Title | 文字輸入 | CHAR(30) |
| 10 | Permanent HKID | 下拉 | Yes / No |
| 11 | ID Issue Date | 日期選擇器（date） | — |
| 12 | Gender `*` `NS` | 下拉 | M-Male / F-Female / U-Unknown |
| 13 | Date of Birth `*` `NS` | 日期選擇器（date） | NUM(8) YYYYMMDD |
| 14 | Place of Birth `*` `NS` | 國家下拉（34選項） | CHAR(3) |
| 15 | Nationality `NS` | 國家多值動態下拉（max 5，含 + Add / × 刪除） | CHAR(3) |
| 16 | Email Address `*` | 文字輸入 | CHAR(34) |
| 17 | Telephone Number `*` | 並排（區號下拉 + 號碼輸入） | — |
| 18 | Residential Address `*` `NS` | 地址 6 子字段 + 預覽行 | CHAR(210) |
| 19 | Beneficial Owner Source of Wealth | Checkbox 多選（max 5） | BI / IF / IP / SA / OT（BO 角色時顯示） |
| 20 | Country(ies) of Initial Wealth Source | 國家多值動態下拉（max 5） | BO 角色時顯示 |
| 21 | Country(ies) of Ongoing Wealth Source | 國家多值動態下拉（max 5） | BO 角色時顯示 |
| 22 | Related Client On PEP | Checkbox 多選（max 2） | Hong Kong PEP / Non HK PEP / International Org PEP / No |
| 23 | FATCA — US Tax ID | 文字輸入 | CHAR(30) |
| 24 | FATCA — Substantial US Owner Flag | 下拉 | Y-Yes / N-No |
| 25 | CRS — Entity Controlling Person Flag | 下拉 | Y-Yes / N-No |
| 26 | CRS — Tax Residency + TIN | 複合多值（Country + TIN + Reason for No TIN，max 4） | — |
| 27 | CRS — Type of Controlling Person | 下拉（01-08） | — |

**Individual 下方附 Common Customer Alignment Information（只讀表格，17列）**

列順序：Action | English Name | Chinese Name | Related Company CN Name | Relation Code | Gender | Date of Birth | Place of Birth | Nationality | PEP Classification | PEP Link | Tax Info | Industry 1 | Industry 2 | Residence Address | Shareholding % | Latest Risk Rating | Update Time

#### Section 9 (cont.) · Connected Parties — Entity（12 字段）

| # | 字段名 | 控件類型 |
|---|--------|---------|
| 1 | ID Type `*` `NS` / ID Number `*` `NS` | 合併一行（Type 下拉 220px + Number 文字） |
| 2 | Others | 文字輸入 |
| 3 | Registered Full Name in English `*` `NS` | 文字輸入 |
| 4 | Registered Full Name in Chinese `NS` | 文字輸入 |
| 5 | Registered Office Address `NS` | 文字輸入 |
| 6 | Principal Business Address | 文字輸入 |
| 7 | Entity Type of Connected Entity `*` | 下拉 |
| 8 | Capacity of Connected Entity `*` | Checkbox 多選：085-BO / 034-Director / 069-Shareholder≥25% / 103-Senior Officer / 088-Authorised Rep / 060-Trustee / 118-Controlling Person |
| 9 | Ownership % | 文字輸入 + `%` 後綴 |
| 10 | Listing on Stock Exchange | 下拉 Y/N |
| 11 | Name(s) of Stock Exchange | 文字輸入 |

**Entity 下方附 Common Customer Alignment Information（只讀，列順序同 Individual）**
> Entity 無 Gender/DOB/Place/Nationality，對應列填 `N/A` 或 `—`

---

## 8. TU Company Search — 公司查詢

三個 Tab，均為**只讀**展示：

| Tab | 說明 |
|-----|------|
| Entity Details | TU 查詢返回的公司基本信息（類型/狀態/名稱/成立日期/注冊地址） |
| Connected Party | TU 查詢返回的相關人士（角色/ID Type/ID Number/姓名/持股%） |
| Related Documents | TU 查詢返回的文件列表（BR / CI / NAR1 / M&A / Form1(a) / Form1(c) / Other）+ Preview 按鈕 |

---

## 9. Customer Risk 詳細評估

### 9.1 表格列定義（6列）

```
評估維度 | Result | Detail | Initial Score | Assessment Score | Action
```

### 9.2 19 行完整清單

| 行 | 評估維度 | Action 控件 |
|----|---------|------------|
| 1 | Main nature of business/industry | — |
| 2 | Date of incorporation | — |
| 3 | Entity type/Ownership structure | — |
| 4 | Bearer Shares Issued | — |
| 5 | Complex Structure | Radio（Yes / No） |
| 6 | Country of incorporation | — |
| 7 | High Tax Evasion Risk | — |
| 8 | Countries of operation | — |
| 9 | Usage of high risk products | — |
| 10 | Tax Risk | — |
| 11 | Onboarding channel | — |
| 12 | High Risk Remarks (LYOD) | `Remarks ▾`（展開 inline panel）+ `Detail`（彈窗） |
| 13 | HI Company Secretary Address | Radio（True Hit / False Hit） |
| 14 | Sanctions Exposure (LYOD) | `Detail`（彈窗） |
| 15 | PEP/Individual/SOG/Entity (LYOD) | `Detail`（彈窗） |
| 16 | Special Interest Subject (LYOD) | `Detail`（彈窗） |
| 17 | Factiva (Internal List) | `Detail`（彈窗） |
| 18 | Special List | — |
| 19 | AML Black List | `Detail`（彈窗） |

### 9.3 High Risk Remarks inline panel（行 12 展開）

選項：
- `AML Reference`（disabled，系統自動命中）
- `AML Reference - Grey List`（disabled）
- `Closely Monitoring (User Input)`（可勾選）
- `Others`（可勾選，勾選後展開 textarea，maxlength 60）

### 9.4 Detail 彈窗（命中名單詳情）

觸發：Sanctions / PEP / SIS / Factiva / AML 行點擊 `Detail`

彈窗表格共 22 列：NO / Original Name / Name / Address / Origin / ID / Type / Additional Info / Date of Birth / Place of Birth / Places of Birth / Hyperlinks / Nationality / Request Date / ID Type / ID No. / English Name / Chinese Name / DoB / Case Reference / **Result** / **Action**

**Result 列（5 選 1 Radio）：**
1. Non Material Hit
2. True Hit + Hong Kong PEP
3. True Hit + Non Hong Kong PEP
4. True Hit + International Organization PEP
5. False Hit

---

## 10. Bank Supplement Attachment — 附件管理

分為兩個獨立區塊：

### 10.1 客戶檔案原有（綠色標籤）

- 顯示客戶 CIF 中的歷史文件
- 操作：Download / Preview（**無 Delete**）
- 示例文件：BR / CI / NAR1 / M&A / Individual Background / Risk Assessment

### 10.2 本次觸發事件審核（藍色標籤）

- 本次審核流程中需要補充上傳的文件
- 操作：已上傳行有 Delete / Download / Preview；未上傳行有 Upload 按鈕
- 文件類型：Updated BR Certificate / Director Change Notice / Updated NAR1 / New ID Document / Proof of Address / Board Resolution / Other

---

## 11. Case Content / Flow Chart

兩個 Tab 切換：

| Tab | 說明 |
|-----|------|
| Case Content | 案件詳情記錄（文字展示） |
| Flow Chart | SVG 繪製的 Trigger Event Review 審批流程圖（點擊 Tab 時動態注入） |

**Flow Chart 節點：**
```
[RM Maker] → [RM Checker] → [AML Review] → [完成]
                  ↓ Escalation（可選）
             [RM Head / BU Head] → 返回 [RM Checker]
任意節點 → Return → [RM Maker]（虛線箭頭）
```

---

## 12. 右側審批操作面板

**固定在右側（sticky），寬度 360px。**

### 12.1 面板結構

| # | 區塊 | 顯示條件 |
|---|------|---------|
| Stats | Changed 數 / NS 數 / SLA 剩餘 | 始終顯示 |
| Check Status | Name Screening 狀態 / Risk Scoring 狀態 | 始終顯示 |
| 1. 審批結果 | Recommend Approval / Recommend Reject / Return / RFI | 始終顯示 |
| 2. Select RFI Category | 4格 2×2（上傳证明材料 / 补充信息填写 / 负责人重认证 / 客户联系确认） | 審批結果=RFI 時顯示 |
| 3. Review Overview | Transaction Limit / Full Review Flag / Comment / Trigger Event Reason / Review Reason / Comment | 始終顯示 |
| 4. 審批意見 | textarea（maxlength 20000）+ 展開編輯按鈕 | 始終顯示 |
| 5. 附件（可選） | 上傳按鈕 | 始終顯示 |
| 按鈕組（2×2） | Save Draft / Risk Scoring / Rerun Name / Generate RSS | 始終顯示 |
| 提交按鈕 | 全寬，顏色按審批結果變化 | 始終顯示 |

### 12.2 Review Overview 字段枚舉值

| 字段 | 類型 | 枚舉值 | 必輸 |
|------|------|--------|------|
| Transaction Limit | 下拉 | 01-Standard / 02-Enhanced (VIP) / 03-Restricted / 04-No Change | — |
| Full Review Flag | 下拉 | Y-Yes (Full Review Required) / N-No (Partial Review) | — |
| Full Review Comment | 文字輸入 | — | — |
| Trigger Event Reason | 下拉 | 01-Trigger By Customer / 02-Trigger By System / 03-Trigger By Regulator / 04-Trigger By Bank (Internal) / 05-Periodic Review | ✅ |
| Trigger Event Review Reason | 下拉 | 01-Change of Company Name / 02-Change of Director / 03-Change of Shareholding / 04-Change of Registered Address / 05-Change of Business Address / 06-Change of Industry/Business Nature / 07-Change of Beneficial Owner / 08-Hit AML/Sanctions/PEP List / 09-Adverse Media / 10-Customer Request / 11-Periodic Review Triggered / 12-Regulator Requirement / 13-Other | — |
| Trigger Event Comment | 文字輸入 | — | — |

### 12.3 提交按鈕顏色聯動

| 審批結果選擇 | 按鈕文字 | 按鈕顏色 |
|------------|---------|---------|
| Recommend Approval | 提交 — Recommend Approval | 綠色 `#1d9e75` |
| Recommend Reject | 提交 — Recommend Reject | 紅色 `#a32d2d` |
| Return | 提交 — Return | 橙色 `#854f0b` |
| RFI | 提交 — Send RFI | 藍色 `#185fa5` |

### 12.4 審批意見展開 Modal

點擊「⤢ 展開編輯」按鈕 → 彈出 860px 寬 Modal，textarea 最小 420px，內容雙向同步。

- 字符上限：**20,000**（注意：與 RM 開戶頁面三的 16,383 不同）
- 確認 → 同步回右側面板 textarea
- 取消 → 關閉不保存

---

## 13. 審批流程節點說明

### 13.1 Trigger Event Review 審批流程

```
[觸發事件創建] → [RM Maker 審核] → [Name Screening] → [Risk Scoring] → [RM Checker 審核] → [AML Review] → [完成]
```

### 13.2 各節點可執行操作

| 節點 | 可執行操作 | 限制 |
|------|-----------|------|
| RM Maker | Recommend Approval / Recommend Reject / Return / RFI / Pass on | — |
| RM Checker | Recommend Approval / Recommend Reject / Return / RFI / Pass on / Proceed with Escalation | 不能直接 Reject（需先 Return） |
| RM Head / BU Head | Return | **不能直接 Reject**，必須先 Return 給 RM Maker |
| AML Maker | Recommend Approval / Recommend Reject / Return / Pass on | — |
| AML Checker | Return / Pass on | **不能直接 Reject** |

### 13.3 Timeline 時間軸記錄格式

每條記錄：`角色徽章 → 操作標籤 → 操作對象 | 狀態描述 | 時間（耗時）`

---

## 14. 各彈窗交互規範

### 14.1 Name Screening 結果彈窗（`#modal-ns`）

- 觸發：右側面板「↩ Rerun Name」按鈕
- 顯示 NS 掃描命中記錄表格

### 14.2 Risk Scoring 結果彈窗（`#modal-rs`）

- 觸發：右側面板「⚡ Risk Scoring」按鈕
- 顯示風險評分（94.75 HIGH RISK）+ Score Breakdown 明細

### 14.3 Submit 確認彈窗（`#modal-submit`）

- 觸發：提交按鈕
- 顯示：確認提交至下一節點的說明
- 按鈕：確認提交（綠色）/ 取消（灰色）
- 確認後：toast「案件已成功提交至 RM Checker」

### 14.4 Generate RSS 彈窗（`#rss-modal-wrap`）

- 觸發：右側面板「⚡ Generate RSS」按鈕
- 表格列：Submission Time / Generated Time / RSS Document / Action / Assessment / Remarks
- 操作按鈕：⚡ Generate RSS（生成後 1.5s loading → 插入記錄行）/ ↺ Refresh / Cancel

### 14.5 Detail 彈窗（`#cr-detail-modal-wrap`）

- 觸發：Customer Risk 表格中各命中行的 `Detail` 按鈕
- 命中類型：High Risk Remarks / Sanctions / PEP / Special Interest / Factiva / AML
- 表格 22 列（見第 9 節）
- 每行操作：Result 5 選 1 Radio + 意見文字框（4000 字）+ Save 按鈕

### 14.6 審批意見展開 Modal

- 觸發：右側面板審批意見右上角「⤢ 展開編輯」
- 規格：860px 寬，textarea min-height 420px，maxlength 20000
- 確認 / 取消 按鈕，確認後同步回原 textarea

---

*文檔結束。如有更新，版本號遞增（V1.1, V1.2...），在文件頭部更新日期和更新內容記錄。*
