# CIF 客戶查詢功能 — Claude API 啟動命令

## 使用方法

將以下內容作為一條完整的 User Prompt 發送給 Claude API（使用 claude-opus-4-6 模型）。
System Prompt 使用現有的 `CLAUDE_API_SYSTEM_PROMPT.md` 內容（已有的 BB 開戶系統指令）。

---

## User Prompt（直接複製發送）

```
請先讀取以下所有文件（按順序，逐一完整讀取，不要跳過）：

【規格文件】
1. C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\CIF_Customer_Query_UI_Fields_v4.md
2. C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\cif_customer_query.html
3. C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\BB_Account_Opening_Fields_20260417_V1_9.md
4. C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\CLAUDE_API_SYSTEM_PROMPT.md

【項目代碼庫根目錄】
C:\Users\zekai.wang\zabank-cif-demo\

---

讀取完成後，按以下順序執行 CIF 客戶查詢功能（View Customer），一步一步完成，遇到任何問題自行解決，不要停下來問我：

## Step 1：掃描現有代碼庫

執行以下命令，了解現有代碼結構：
```
find C:/Users/zekai.wang/zabank-cif-demo/src -name "*.ts" -o -name "*.tsx" -o -name "*.css" | grep -v node_modules | sort
```
輸出：已實現的功能清單、需要新增的功能清單。

## Step 2：檢查數據庫現有表結構

連接 MySQL，執行：
```sql
SHOW TABLES;
-- 對每個相關表執行：
DESCRIBE party;
DESCRIBE party_organization_profile;
-- 以此類推，確認字段名和類型
```
根據實際表結構調整後續 SQL，不要假設表名。

## Step 3：新增後端 API（不修改現有路由）

在 `C:\Users\zekai.wang\zabank-cif-demo\server\` 下新增：

**新文件：** `routes/cif.ts`
實現以下端點（所有查詢必須覆蓋 `CIF_Customer_Query_UI_Fields_v4.md` 中定義的所有字段）：

```
GET  /api/cif/customers                        客戶列表（支持 clientNo、companyName、idType、idNumber、clientRisk、nextReviewDate 篩選）
GET  /api/cif/customers/:cifId                 客戶 Header 數據（公司名、Client No、ID、Client Risk、CDD dates、Account Officer）
GET  /api/cif/customers/:cifId/company-info    Company Info Tab（9個Section全部字段）
GET  /api/cif/customers/:cifId/connected-party Connected Party Tab（Individual+Entity，含FATCA/CRS個人字段）
GET  /api/cif/customers/:cifId/assets          Asset Overview Tab（DD/TD/Funds/Loan/Fund）
GET  /api/cif/customers/:cifId/cards           Card Information Tab
GET  /api/cif/customers/:cifId/company-search  Company Search Tab（TU結果）
GET  /api/cif/customers/:cifId/ekyc            eKYC Records Tab
GET  /api/cif/customers/:cifId/block-codes     Block Code Tab（含Checker字段）
GET  /api/cif/customers/:cifId/auth-arrangement Authorization Arrangement Tab（Signing+Modification History）
GET  /api/cif/customers/:cifId/attachments     Attachment Tab
GET  /api/cif/customers/:cifId/audit-log       Audit Log Tab
GET  /api/cif/customers/:cifId/addr-history    地址變更歷史彈窗數據
GET  /api/cif/customers/:cifId/ind-history/:indId   Individual變更歷史彈窗
GET  /api/cif/customers/:cifId/entity-history/:entityId Entity變更歷史彈窗

POST /api/cif/customers/:cifId/trigger-event         創建Trigger Event（返回訂單號）
POST /api/cif/customers/:cifId/periodic-review        創建Periodic Review
POST /api/cif/customers/:cifId/periodic-review-eob    創建EOB Periodic Review
POST /api/cif/customers/:cifId/dd-account             新開DD Account
GET  /api/cif/customers/:cifId/audit-pdf              生成AuditCL PDF
POST /api/cif/customers/:cifId/company-update         發起Company Info Update訂單
```

**如果 MySQL 中沒有測試數據**，在 `server/seeds/cifSeedData.ts` 中生成至少 3 個完整的測試客戶數據並自動插入，數據必須覆蓋所有字段，包括 Individual 的 FATCA/CRS 個人字段、制裁問卷 SEQ、Supplier/Buyer/Assets 多值字段。

在 `server/app.ts` 或 `server/index.ts` 中掛載：
```typescript
import cifRouter from './routes/cif';
app.use('/api/cif', cifRouter);
```

## Step 4：新增前端頁面（必須復用 cif_customer_query.html 的所有 CSS）

**核心原則：不能重寫 CSS，直接把 cif_customer_query.html 的 `<style>` 完整遷移。**

### 4.1 提取 CSS

創建 `src/styles/cif-customer-query.module.css`，把 `cif_customer_query.html` 的 `<style>` 標籤內所有 CSS 規則完整複製進去（class 名原樣保留：`.cust-hd`、`.fg`、`.fc`、`.fl`、`.fv`、`.sec`、`.tab-bar`、`.tab`、`.badge`、`.yn`、`.pill`、`.addr-box`、`.dt`、`.cp-block` 等所有 class）。

### 4.2 路由配置

在 `src/App.tsx` 或路由文件中新增：
```tsx
<Route path="/cif/customers" element={<CIFCustomerList />} />
<Route path="/cif/customers/:cifId" element={<CIFCustomerDetail />} />
```

### 4.3 客戶列表頁（CIFCustomerList.tsx）

創建 `src/pages/cif/CIFCustomerList.tsx`，實現：
- 頂部搜索欄（clientNo、companyName、idType/idNumber、clientRisk、nextReviewDate 篩選條件）
- 客戶列表表格，字段：Client No / Company English Name / Company Chinese Name / ID Type / ID Number / Client Risk（badge）/ Account Officer / Client Open Date / CDD Next Review Date / 操作（View）
- 點擊 View 跳轉到 `/cif/customers/:cifId`
- 使用 `cif-customer-query.module.css` 的樣式（主色調、邊框、badge 顏色）

### 4.4 客戶詳情頁（CIFCustomerDetail.tsx）

創建 `src/pages/cif/CIFCustomerDetail.tsx`，**UI 結構完全照抄 `cif_customer_query.html`**：

**Header 區域（嚴格按 HTML 原型）：**
- ← 返回 按鈕（上方）
- cust-hd 卡片：公司名稱 + 三行 3 欄字段 grid
  - 行1：Client No + Client Status badge / ID Type / ID Number  
  - 行2：Client Risk badge / CDD Next Review Date / CDD Last Review Date
  - 行3：Account Officer / Client Open Date / Client Close Date
- Header 下方 6 個綠色功能按鈕（btn-action 樣式）：
  Create Trigger Event / Create Periodic Review / Create Periodic Review for EOB - Manual / New DD Account / AuditCL PDF / Company Info Update

**Tab Bar（10 個 Tab）：**
Company Info / Connected Party / Asset Overview / Card Information / Company Search / eKYC Records / Block Code / Authorization Arrangement / Attachment / Audit Log

**每個 Tab 的 Panel（動態數據）：**
按 `CIF_Customer_Query_UI_Fields_v4.md` 中各 Tab 的字段定義，從對應 API 獲取數據並渲染，HTML 結構和 class 名與 `cif_customer_query.html` 完全一致。

**3 個變更歷史彈窗（Modal）：**
- 地址變更歷史（Contact Details 右上角 🕐 按鈕觸發）
- Individual 變更歷史（每個 Individual 塊右上角觸發）
- Entity 變更歷史（每個 Entity 塊右上角觸發）

**功能按鈕邏輯：**
```tsx
// 點擊功能按鈕 → 確認彈窗 → 調用 API → 顯示成功/失敗提示
const handleAction = async (action: string, api: string) => {
  Modal.confirm({
    title: `確認執行：${action}`,
    onOk: async () => {
      const res = await fetch(`/api/cif/customers/${cifId}/${api}`, { method: 'POST' });
      const data = await res.json();
      if (data.success) message.success(`${action} 已提交，訂單號：${data.orderId}`);
      else message.error(data.message);
    }
  });
};
```

## Step 5：自動執行測試（兩輪）

### 第一輪：功能測試

啟動後端：`npm run dev`（或 `node server/index.js`）
啟動前端：`npm start`（或 `npm run dev`）

逐一驗證每個 Tab 的數據是否從 API 正確返回並渲染：
- Company Info 9個Section（含Supplier/Buyer/Assets多值字段、SEQ問卷4條）
- Connected Party（Individual的FATCA/CRS個人字段）
- Asset Overview（DD含Last Financial Transaction Date、TD含Maturity Instruction）
- Block Code（含Checker字段）
- Authorization Arrangement（Signing + Modification History 兩個 Section）
- 3個變更歷史彈窗能正常打開和關閉

記錄所有問題和解決方案。

### 第二輪：審批流程測試

驗證 Header 下方 6 個功能按鈕：
1. Create Trigger Event → 確認 → POST API → 返回訂單號
2. Create Periodic Review → 確認 → POST API → 返回訂單號
3. Create Periodic Review for EOB - Manual → 同上
4. New DD Account → 確認 → POST API → 成功提示
5. AuditCL PDF → GET API → 下載或顯示 PDF（如無 PDF 生成庫，返回 JSON 格式的審計摘要）
6. Company Info Update → POST API → 返回維護訂單號

## Step 6：輸出測試報告

完成兩輪測試後，輸出以下格式的測試報告：

```
# CIF 客戶查詢功能測試報告

## 測試環境
- 測試日期：[自動填入]
- 後端地址：http://localhost:3001
- 前端地址：http://localhost:3000
- 數據庫：MySQL localhost:3306/zabank_cif

## 新增文件清單
| 文件路徑 | 功能說明 |
|----------|----------|
| server/routes/cif.ts | CIF客戶查詢API路由 |
| server/seeds/cifSeedData.ts | 測試數據種子文件 |
| src/pages/cif/CIFCustomerList.tsx | 客戶列表頁 |
| src/pages/cif/CIFCustomerDetail.tsx | 客戶詳情頁 |
| src/styles/cif-customer-query.module.css | 復用HTML原型的CSS |

## 第一輪測試（功能測試）
| 測試項目 | 結果 | 問題 | 解決方案 |
|----------|------|------|----------|
| Company Info - 9個Section | ✅/❌ | ... | ... |
| Supplier/Buyer/Assets 多值字段 | ✅/❌ | ... | ... |
| SEQ 制裁問卷 4條 | ✅/❌ | ... | ... |
| Individual FATCA/CRS個人字段 | ✅/❌ | ... | ... |
| DD Last Financial Transaction Date | ✅/❌ | ... | ... |
| TD Maturity Instruction | ✅/❌ | ... | ... |
| Block Code Checker字段 | ✅/❌ | ... | ... |
| Signing Arrangement | ✅/❌ | ... | ... |
| Modification History | ✅/❌ | ... | ... |
| 地址變更歷史彈窗 | ✅/❌ | ... | ... |
| Individual變更歷史彈窗 | ✅/❌ | ... | ... |
| Entity變更歷史彈窗 | ✅/❌ | ... | ... |

## 第二輪測試（審批流程）
| 功能按鈕 | API響應 | 結果 | 問題 | 解決方案 |
|----------|---------|------|------|----------|
| Create Trigger Event | 200 orderId:... | ✅/❌ | ... | ... |
| Create Periodic Review | ... | ... | ... | ... |
| Create Periodic Review for EOB | ... | ... | ... | ... |
| New DD Account | ... | ... | ... | ... |
| AuditCL PDF | ... | ... | ... | ... |
| Company Info Update | ... | ... | ... | ... |

## 問題匯總與解決方案
1. 問題：[描述] → 解決：[方案]
2. ...

## 測試結論
- 第一輪功能測試：通過 X / 共 Y 項
- 第二輪審批流程：通過 X / 共 6 項
- 整體結論：[通過/部分通過/需要修復]
- 需要人工確認項：[如有]
```

---

遇到任何技術問題（數據庫表結構與預期不符、端口衝突、TypeScript 類型錯誤、CORS、依賴缺失等），自行診斷和修復，不要停下來問我。
如果 MySQL 沒有數據，自動生成並插入測試數據。
完成所有步驟後輸出完整測試報告。
```

---

## 需要提前準備的文件（放到以下路徑）

在執行前，請把以下文件複製到 `C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\`：

| 文件 | 來源 |
|------|------|
| `CIF_Customer_Query_UI_Fields_v4.md` | 本次輸出（outputs 目錄） |
| `cif_customer_query.html` | 本次輸出（outputs 目錄） |
| `CIF_Claude_API_System_Prompt.md` | 本次輸出（outputs 目錄，參考用） |

以下文件已在 `prd\V2版本\` 目錄中存在，無需複製：
- `BB_Account_Opening_Fields_20260417_V1_9.md` ✅
- `CLAUDE_API_SYSTEM_PROMPT.md` ✅

---

## Claude Code 命令行方式（如使用 Claude Code CLI）

```bash
cd C:\Users\zekai.wang\zabank-cif-demo

claude "請先讀取以下文件：
1. prd/V2版本/CIF_Customer_Query_UI_Fields_v4.md
2. prd/V2版本/cif_customer_query.html
3. prd/V2版本/BB_Account_Opening_Fields_20260417_V1_9.md
4. prd/V2版本/CLAUDE_API_SYSTEM_PROMPT.md

然後實現 CIF 對公客戶信息查詢功能（View Customer 頁面）。

UI 必須直接復用 cif_customer_query.html 的 CSS，不重寫。
後端新增 /api/cif 路由，不修改現有路由。
數據庫查詢必須覆蓋 CIF_Customer_Query_UI_Fields_v4.md 中所有字段。
遇到問題自行解決，完成兩輪測試後輸出測試報告。"
```

---

## 注意事項

1. **System Prompt** 繼續使用現有 `CLAUDE_API_SYSTEM_PROMPT.md` 的內容（BB 開戶系統的完整開發指令），CIF 查詢功能作為增量任務通過 User Prompt 指定
2. **不要刪除或修改現有的 BB 開戶功能**（Phase 0-8 已完成的部分），只做增量開發
3. Claude API 模型推薦使用 `claude-opus-4-6`，max_tokens 設置 `8192` 以上
4. 如果單次 token 不夠，可以分多次調用，每次以「繼續執行 Step X」開頭
