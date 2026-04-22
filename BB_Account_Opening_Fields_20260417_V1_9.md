# Business Banking Account Opening — 页面字段说明文档

> **文档版本：** `BB_Account_Opening_Fields_20260417_V1_9.md`
> **更新内容（V1.9）：** ✏️ 同步 HTML 原型新增字段：Section 2 新增 Group CIF Number / Group CIF Name（CHAR(12)/CHAR(120)）；Section 8 新增 Company Change flag 總標誌字段（CHAR(1)，Y 展開子字段）；Section 10.1 Individual 新增 FATCA 個人字段（US Tax ID CHAR(30) / Substantial US owner flag CHAR(1)）及 CRS 個人字段（Entity controlling flag / CRS Tax Residency / CRS TIN / CRS Reason for No TIN / Type of Controlling Person CHAR(2)，枚舉 01-08）
> **更新日期：** 2026-04-17
> **更新内容（V1.8）：** ✏️ 補充 Name Screening 完整送審字段清單（來源：PRD 字段定義文件）；修正 V1.7 § 4.3 中錯誤描述（地址字段的 Name Screening 觸發規則）；新增 § 4.4 Name Screening 完整字段清單（公司層面 + Connected Parties 自然人 + Connected Parties 公司）
>
> **更新内容（V1.7）：** ✏️ 補充 Risk Scoring 三個缺失說明：① 禁止風險（Terminate Risk）前端攔截規則；② 頁面三重跑 Risk Scoring 行為；③ Name Screening 與 Risk Scoring 聯動邏輯
>
> **更新内容（V1.6）：** ✏️ 新增「附錄二：三頁面完整交互說明」，補充 V1.5 未覆蓋的12項交互細節：頁面一（列表默認排序/click跳轉邏輯/delete確認彈窗/Refresh行為）；頁面二（Section折疊展開/Section 0反顯映射/Save Draft暫存/Risk Scoring校驗/多值字段增刪）；頁面三（Section 1-16 可編輯模式/Score Breakdown展開收起/High Risk Remarks多選面板/Complex Structure Radio/HI Company Secretary Radio/命中名單Detail彈窗22列/Generate RSS彈窗/提交按鈕文字聯動/Timeline數據格式/Flow Chart節點結構/審批意見字符計數/附件上傳規則）
>
> **更新内容（V1.5）：** ✏️ 補充前端組件架構規格（BBApprovalProgress / BBRiskBadge / BBCompanyIdentityBlock / BBApprovalActionCard）；修正進度條 Escalation 節點邏輯（RM Head 完成後保留顯示而非消失）；補充 Pass on、Proceed with Escalation 操作規格；補充 Score Breakdown 展開面板說明；明確 BBCompanyIdentityBlock 位置為 Case Overview 卡片頂部；明確操作角色限制（RM Head / BU Head / AML Checker 不能直接 Reject）
>
> **更新内容（V1.4）：** ✏️ 補充全部字段長度定義，来源：BB PRD（ZA_Business_Banking_RM開戶產品說明書_v1.0）、核心eonboarding接口api、企业网银开户api。具体更新：① Usage_of_products 枚举值改为 1/2/3；② Contact Person Job Title → CHAR(3)；③ Other Contact Person Job Title → CHAR(30)；④ Others Identity → CHAR(30)；⑤ Telephone Number Area Code → CHAR(5)；⑥ Please indicate the amount and rationale → CHAR(120)；⑦ Internal Client Type → CHAR(2)；⑧ Business Nature → CHAR(5)；⑨ Classify → CHAR(1)；⑩ Complex_Structure → CHAR(1)；⑪ customer_status → CHAR(3)；⑫ pep_flag → CHAR(1)；⑬ High Risk Type (With AML Referral) → CHAR(1)；⑭ High Risk Remarks (Remark For AML Referral) → CHAR(60)；⑮ Ownership_Structure_Level → CHAR(2)

> **文档说明**
> - `*` 红色星号：**必输字段**（Required）
> - `#` 红色井号：**参与 Name Screening 扫描**（Name Screening Required）
> - `[多值]`：字段后有绿色 **+** 按钮，支持输入多条记录
> - 字段顺序与页面截图一致，章节顺序按左侧导航栏排列

---

## 目录

0. [查询公司注册资料（HKCR / IRD）](#0-查询公司注册资料hkcr--ird)
1. [Company Information](#1-company-information)
2. [Contact Details](#2-contact-details)
3. [Company Business Background](#3-company-business-background)
4. [Financial Indicator of Latest Fiscal Year](#4-financial-indicator-of-latest-fiscal-year)
5. [Source of Wealth/Fund](#5-source-of-wealthfund)
6. [Anticipated Transaction & Purpose of Usage](#6-anticipated-transaction--purpose-of-usage)
7. [FATCA/CRS](#7-fatcacrs)
8. [Company Change](#8-company-change)
9. [Company Search - Company Secretary Information](#9-company-search---company-secretary-information)
10. [Details of Connected Parties](#10-details-of-connected-parties)
11. [DD Account Information](#11-dd-account-information)
12. [Signing Arrangement](#12-signing-arrangement)

---

## 开户列表页（RM Account Opening）

**功能说明：** 开户系统的入口主页面，展示所有已创建的对公开户订单，支持多条件搜索筛选及新建开户操作。

### 搜索筛选字段

| # | 字段名 | 说明 |
|---|--------|------|
| 1 | Start Date / End Date | 按提交时间范围筛选 |
| 2 | Case Number | 按开户案件编号搜索 |
| 3 | Company English Name | 按公司英文名称搜索 |
| 4 | Company Chinese Name | 按公司中文名称搜索 |
| 5 | ID Type | 下拉选择证件类型筛选 |
| 6 | ID Number | 按证件号码搜索 |
| 7 | Operator | 下拉选择经办人筛选 |
| 8 | Case Status | 下拉选择案件状态筛选 |

> 点击"**Search**"执行搜索，点击"**Reset**"重置所有筛选条件。点击"**Refresh**"刷新列表。

### 操作按钮

| 按钮 | 说明 |
|------|------|
| New Account Opening | 新建开户订单（手动流程，跳转至 Section 0 查询公司注册资料） |
| New Account Opening (Auto) | 一鍵填入模擬數據並新建開戶訂單（測試/演示用途）。點擊後系統自動創建一筆新草稿訂單，並將 Section 0-16 所有字段填入預設的完整模擬數據（含公司信息、聯絡人、財務指標、Connected Parties 等），然後跳轉至頁面二，RM 可直接點擊「Risk Scoring」觸發評估，無需手動填寫任何字段。此功能**僅供測試和演示使用**，不影響正式業務流程。 |

### 开户订单列表字段

| 列名 | 说明 |
|------|------|
| No. | 序号 |
| Case Number | 开户案件编号（系统自动生成） |
| ID Type | 证件类型（如 BUSINESS REGISTRATION CERTIFICATE） |
| ID Number | 证件号码 |
| Company English Name | 公司英文名称 |
| Company Chinese Name | 公司中文名称 |
| Submission Time | 提交时间（格式：YYYY-MM-DD HH:mm:ss） |
| Case Status | 案件状态（如 Approved、Pending 等） |
| Handler | 经办人（如 eob_rm_maker） |
| Action | 操作：click（查看/继续填写）/ delete（删除） |

---



## 开户页面说明

### 页面一：开户列表页（RM Account Opening）

在列表页点击"**New Account Opening**"，进入开户资料输入页（页面二）。

### 页面二：开户资料输入页（Section 0~16）

点击"New Account Opening"后，系统打开开户主页面，按 Section 0~16 顺序排列。页面顶部有"**Back**"按钮可返回列表页，页面底部固定显示以下三个操作按钮：

| 按钮 | 功能说明 |
|------|----------|
| Back | 返回开户列表页 |
| Save Draft | 保存当前填写内容为草稿，可随时继续填写 |
| Risk Scoring | 触发系统自动执行 Risk Scoring + Name Screening，执行后跳转至页面三 |

> **说明：** 页面二不包含 Case Overview 模块，也不包含 Customer Risk 详细评估。Section 0~16 均为可输入状态。

### 前端组件架构（页面三）

```
frontend/src/pages/bbCases/
├── detail.tsx                          # 主页面，API 调用 + 组装
└── components/
    ├── BBApprovalProgress.tsx          # 审批进度条
    ├── BBRiskBadge.tsx                 # 风险等级徽章（LOW绿/MEDIUM橙/HIGH红）
    ├── BBCompanyIdentityBlock.tsx      # 公司身份高亮区块（卡片顶部）
    └── BBApprovalActionCard.tsx        # 审批操作面板（从零售侧 copy 改造）
```

**detail.tsx API 调用：**
- 数据获取：`GET /api/bb-cases/:id`
- 审批提交：`POST /api/bb-cases/:id/action`
- 动态控制：根据后端返回的 `risk_level`、`current_role`、`approval_status` 控制进度条节点、操作按钮可见性及可点击状态

---

### 页面三：Risk Scoring 后页面

点击"Risk Scoring"后，系统跳转至审批详情页（页面三）。该页面采用**左宽右窄两栏布局**：

- **左侧主内容区**：包含 Case Overview 卡片、Section 1~16 折叠预览、Customer Risk 详细评估、Timeline 审批过程
- **右侧固定审批操作面板（sticky）**：审批结果 / RFI Category / RFI Template / 审批意见 / 附件 / 提交按钮

页面顶部结构：
- 全局导航栏（系统名称 + 用户信息 + 退出）
- 返回审批列表链接
- 两栏布局起始（左侧第一张卡片即为 Case Overview + 审批进度条）

**页面三底部四个操作按钮（位于 Customer Risk 区域下方）：**

| 按钮 | 功能说明 | 右侧面板对应 |
|------|----------|-------------|
| Back | 返回上一页 | — |
| Save Draft | 暂存草稿 | — |
| Risk Scoring | 重新执行 Risk Scoring | — |
| Submit | 提交审批结论 | 对应右侧面板"提交"按钮 |

> **审批结论对应关系：**
> - No Objection → 右侧面板 Recommend Approval
> - Reject → 右侧面板 Recommend Reject
> - Pass CIU → 右侧面板 转办（⇄）

---

## Case Overview（案件概览，页面三展示）

**功能说明：** 页面三左侧主内容区第一张卡片，与 Case 基本信息合并为统一的 Case Overview 卡片，字段名称全部使用英文。卡片底部以高亮区块突出显示公司身份标识字段。

### Case Overview 字段

| 字段名 | 说明 |
|--------|------|
| Case No | 案件编号 |
| Case Type | 案件类型（如 BB Corporate Account Opening） |
| Case Content | 案件内容（如 Hit High Risk） |
| Case Stage | 审核阶段（如 OPS 审核阶段） |
| Case Status | 案件状态（如 Pending OPS Maker Claim） |
| Risk Level | 风险等级（如 HIGH） |
| Current Role | 当前操作角色（如 OPS MAKER） |
| Handler | 处理人 |
| Queue Code | 队列编码 |
| Created By | 创建人 |
| Created Time | 创建时间 |
| Submission Time | 提交时间 |
| Last Updated Time | 最近操作时间 |
| Completion Time | 完成时间 |
| SLA Expiry | SLA 到期时间 |
| Pending Name Screening | 是否待 Name Screening（Y / N），普通字段样式 |

**顶部公司身份高亮区块（`BBCompanyIdentityBlock.tsx`，位于 Case Overview 卡片顶部）：**

> 📌 位置为卡片**顶部**（非底部），参考 bb_page3_post_risk.html 的 `id-highlight-row-main` 样式。

| 字段名 | 说明 |
|--------|------|
| ID Type | 证件类型（如 BUSINESS REGISTRATION CERTIFICATE） |
| ID Number | 证件号码 |
| Company English Name | 公司英文名称 |

**样式规则：**
- 三列同行，响应式适配
- 左边框 4px 实线，颜色跟风险等级：LOW → 绿色 / MEDIUM → 橙色 / HIGH → 红色
- 底色固定浅灰 `#f5f5f5`

### 审批进度条（Progress Bar）

位于 Case Overview 卡片内，显示当前案件所处的审批节点，与卡片内容一起收在左侧主内容区，与下方各章节左对齐：

| 步骤 | 角色 | 状态 |
|------|------|------|
| 1 | RM Maker | 已完成（绿色对勾） |
| 2 | RM Checker | 审批中（蓝色高亮） |
| 3 | RM Head | 待处理 |
| 4 | AML Maker | 待处理 |
| 5 | AML Checker | 待处理 |
| ✓ | 完成 | 待处理 |

### 风险评估（Risk Assessment 卡片，独立卡片位于 Case Overview 下方）

| 模块 | 字段名 | 说明 |
|------|--------|------|
| Initial Risk Assessment (System) | Initial rating | 系统初始评级（如 High Risk） |
| Initial Risk Assessment (System) | Initial score | 系统初始评分（如 94.75），点击"Score breakdown ▾"可展开各维度得分明细 |
| Final Risk Assessment | Latest rating | 最终评级（如 High Risk） |
| Final Risk Assessment | Latest score | 最终评分（如 94.75），点击"Score breakdown ▾"可展开各维度得分明细 |
| Review Indicator | — | 风险触发原因（如：Total supplier(s) countries % < 50%；Hit CRA List） |

**Score breakdown 展开面板说明：**

点击 Initial score / Latest score 旁的"Score breakdown ▾"按钮，展开各 Customer Risk 维度的得分明细列表：

| 行样式 | 说明 |
|--------|------|
| 🔴 红底行 | 高风险维度，有实际得分（如 20、15、13...） |
| 🟡 黄底行 | 中风险维度（如 0.25、0.5） |
| ⬜ 白底灰字行 | 0 分维度 |
| 🔵 蓝底合计行 | 底部 Total 总分汇总 |

再次点击收起，按钮箭头切换 ▾ / ▴。

> 本功能在 detail.tsx 内直接实现（`BBRiskScoreBreakdown` 内联组件），**与 detail.tsx 第二项改造一起实现，不留到后续迭代**。

### 右侧审批操作面板（sticky）

**对应组件：** `BBApprovalActionCard.tsx`（从零售侧 copy 改造），右侧面板在整个页面三中保持固定（sticky）。

#### 支持的操作及角色限制

| 操作 | 可执行角色 | 限制规则 |
|------|-----------|---------|
| Recommend Approval（No Objection） | RM Maker、RM Checker、AML Maker | RM Checker 在 Reject 路径下**不能直接点击**，需先 Return 给 RM Maker 改为 No Objection 后再提交 |
| Recommend Reject（Reject） | RM Maker、RM Checker、AML Maker | RM Head / BU Head / AML Checker **不能直接 Reject**，必须先 Return 给 RM Maker |
| Return | 所有节点 | 退回至 RM Maker，RM Maker 修改后重新提交 |
| RFI - Request for Information | RM Maker、RM Checker | 触发显示 RFI Category + RFI Template 选择区块 |
| Pass on | RM Maker、RM Checker、AML Maker、AML Checker | 同角色之间转派，下拉选择同角色可用人员 |
| Proceed with Escalation | RM Checker（仅限） | 下拉选择转派对象：`RM Head` 或 `BU Head` |

#### 面板区块结构

| # | 区块 | 显示条件 | 说明 |
|---|------|---------|------|
| 1 | 审批结果选择 | 始终显示 | 根据当前角色动态显示可用操作按钮 |
| 2 | Proceed with Escalation 下拉 | 仅 RM Checker 可见 | 选择 RM Head 或 BU Head |
| 3 | Pass on 下拉 | 点击 Pass on 时显示 | 同角色可转派人员列表 |
| 4 | Select RFI Category | 审批结果 = RFI 时显示 | 四选一：上传证明材料 / 补充信息填写 / 身份重新认证 / 客户联系确认 |
| 5 | Select RFI Template | RFI Category 选定后显示 | 根据 Category 显示对应模板列表 |
| 6 | 审批意见 | 始终显示 | 文本框，最多 16383 字 |
| 7 | 附件上传 | 始终显示 | 支持上传附件 |
| 8 | 提交按钮 | 始终显示 | 提交审批结论 |

> ⚠️ 操作按钮的**可见性与可点击状态**均由后端返回的 `current_role` + `approval_status` 动态控制，前端不做硬编码。

| 5. 附件（可选） | 上传附件，最多 60 个，支持 PDF、ZIP、EML、PNG、JPG、DOC、DOCX、XLS、XLSX、CSV，单个最大 50MB |
| 转办 | 面板右上角"⇄ 转办"按钮，对应 Pass CIU |
| 暂存 Save Draft | 暂存当前填写内容 |
| 提交按钮 | 根据所选审批结果显示对应文字（如 Send RFI） |

---



### 0.1 查询输入字段

| # | 字段名 | 必填 | 说明 |
|---|--------|:----:|------|
| 1 | 证书类型（Document Type） | ✅ | 下拉选择，枚举值：CI（公司注册证）/ BR（商业登记证） |
| 2 | 证书号码（Document Number） | ✅ | 输入公司注册证书号码（CI No. 或 BR No.），点击"搜索"发起查询 |

### 0.2 查询来源说明

| 证书类型 | 查询来源 | TU 接口 |
|----------|----------|---------|
| CI（Certificate of Incorporation） | HKCR（香港公司注册处） | Search Companies On Registry → Get Company Details → List Company Members |
| BR（Business Registration） | IRD（税务局商业登记署） | Search Companies On Registry → Get Company Details → List Company Members |

---

## 附录：TU 返回字段 → 开户资料字段映射说明

**说明：** 以下为 TU 接口返回字段与开户页面各 Section 字段的对应关系。查询成功后，系统自动将 TU 返回值反显到对应字段，反显值可由 CDD-OPS 人工核对并修改。

### 公司基本信息映射（Get Company Details）

| TU 字段编号 | TU 字段名 | TU API 路径 | 反显至开户字段 | 所在章节 |
|------------|-----------|------------|--------------|---------|
| D2 | Document number 公司注册证书号码 | company/properties/Registration Number | ID Number（证件号码） | Section 1 |
| D3 | Document type 公司注册证书类型 | company/dataSource | ID Type（证件类型） | Section 1 |
| D6 | Company name 公司英文名称 | company/entityName | Company English Name（公司英文名称） | Section 1 |
| D1 | Company type 公司类型 | company/properties/Company Type | Company Type（公司类型） | Section 1 |
| D5 | Company status 公司状态 | company/properties/Company Status | 仅供参考，不反显至表单（Active / Dissolved） | — |
| D7 | Incorporation date 成立日期 | company/properties/Incorporation Date | 仅供参考，不反显至表单 | — |
| D18 | 公司注册地址（第一行） | caseAddress/rawAddress | Registered Address（注册地址子字段） | Section 1 |
| D19 | 公司注册地址（第二行） | — | Registered Address（注册地址子字段） | Section 1 |
| D20 | 公司注册地址（第三行） | — | Registered Address（注册地址子字段） | Section 1 |

### 相关人士信息映射（List Company Members）

| TU 字段编号 | TU 字段名 | TU API 路径 | 反显至开户字段 | 所在章节 |
|------------|-----------|------------|--------------|---------|
| D41 | 姓氏 Last Name | member/lastName | Last Name（英文姓氏） | Section 10.1 |
| D42 | 名字 First Name | member/firstName | First Name（英文名字） | Section 10.1 |
| D62 | Chinese name 中文姓名 | member/properties/Other Names | Chinese Name（中文姓名） | Section 10.1 |
| D63 | Identity card number 身份证号码 | member/properties/National Country ID | Identity Card Number（证件号码） | Section 10.1 |
| D66 | Identity card type 证件类型 | 若 National Country ID 不为空则为 HKID | Identity Card Type（证件类型） | Section 10.1 |
| D46 | 相关人士类别 | shareholdersAndBeneficialOwners/role 或 controllingEntitiesAndIndividuals/role | Capacity of Connected Party（相关人士角色） | Section 10.1 |
| D80 | Percentage of share 持股比例 | shareholdersAndBeneficialOwners/percentage | Ownership（股份占比%） | Section 10.1 |
| — | Type of member 成员类型 | memberType（Individual / Company） | 用于区分自然人（Section 10.1）或公司相关人士（Section 10.2） | Section 10.1 / 10.2 |
| — | Name of company member 公司股东名称 | member/entityName | Registered Full Name in English | Section 10.2 |

### 公司文件映射（List Company Documents）

| 文件类型 | TU 来源 | 说明 |
|----------|---------|------|
| 公司注册证（CI） | Company Identity Documents / Certificate of Incorporation（source = HKCR） | 系统自动获取，供 CDD-OPS 下载核验 |
| 商业登记证（BR） | Company Identity Documents / DUPREGCERT（source = IRD，24小时后返回） | 系统自动获取，供 CDD-OPS 下载核验 |
| 最新年度申报表（NAR1） | Other Company Documents / FNAR1（source = HKCR） | 系统自动获取，供 CDD-OPS 下载核验 |
| 公司组织章程（M&A） | Other Company Documents / Articles of Association（source = HKCR） | 系统自动获取，供 CDD-OPS 下载核验 |
| 合夥经营商号 Form1(c) | Other Company Documents / ELEXTRACT（source = IRD） | 仅合夥公司适用 |
| 独资经营商号 Form1(a) | Other Company Documents / ELEXTRACT（source = IRD） | 仅独资公司适用 |

---

## 1. Company Information

对应数据模型：`party_organization_profile` + `party_identity`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Company Type | ✅ | | | CHAR(2) | 公司类型。枚举值参考数据字典：CO-LIMITED COMPANY(UNLISTED)、CS-COOPERATIVE SOC、EM-EMBASSY、FD-FOUNDATION、FI-FINANCIAL INSTITUTION、FU-FUND、GO-GOVERNMENT DEPARTMENT、HC-HOLDING COMPANY、JC-JOINT CORP、LC-LIMITED COMPANY(LISTED)、MS-MONEY SERVICE BUSINESS、NG-NATIONAL OR LOCAL BODY GOVERNMENT、PC-PRIVATE INVESTMENT COMPANY、PF-PRIVATE INVESTMENT FUND、PT-PRIVATE HOLDING TRUST、SB-STATUTORY BODY、SO-SOCIETY、SP-SOLE PROPRIETORSHIP、TR-TRUST、TU-TRADE UNION、VS-VOLUNTARY SECTOR、JP-JOINT PERSON、PA-PARTNERSHIP |
| 2 | ID Type | ✅ | ✅ | | ENUM | 证件类型。枚举值：BUSINESS REGISTRATION CERTIFICATE、CERTIFICATE OF INCORPORATION、Others |
| 3 | ID Number | ✅ | ✅ | | CHAR(30) | 公司证书号码。主证件号码，唯一标识 |
| 4 | Other IDs | | ✅ | ✅ | CHAR(30) | 其他证件类型及号码（如同时持有 BR 和 CI），支持多条 |
| 5 | Company English Name | ✅ | ✅ | | CHAR(60) | 公司英文名称，作为 Golden Record，参与 Name Screening |
| 6 | Company Chinese Name | | ✅ | | CHAR(60) | 公司中文名称，选填 |
| 7 | Trading Name (English) | | ✅ | ✅ | CHAR(60) | 英文营业名称，最多支持 10 个 |
| 8 | Trading Name (Chinese) | | ✅ | ✅ | CHAR(60) | 中文营业名称，最多支持 10 个 |
| 9 | Company Establish Date | ✅ | ✅ | | NUM(8) | 公司成立日期，格式 YYYYMMDD |
| 10 | Country of Registration | ✅ | | | CHAR(3) | 注册国家，枚举值：国家列表（ISO 3166-1 alpha-3） |
| 11 | Listing Company | | | | CHAR(30) | 是否上市公司。枚举值：Y-Yes、N-No |
| 12 | High Tax Evasion Risk | ✅ | | | CHAR(1) | 是否高税务逃避风险。枚举值：Y-Yes、N-No |
| 13 | Name(s) of Stock Exchange | | | | CHAR(120) | 证券交易所名称（Listing Company = Y 时填写） |
| 14 | Bearer Shares Issued | ✅ | | | CHAR(1) | 是否可以发行/已经发行不记名股票。枚举值：Y-Yes、N-No |
| 15 | Are any of the Intermediate owners of your company capable of issuing bearer shares | | | | CHAR(1) | 公司的中介控股公司是否能发行不记名股票。枚举值：Y-Yes、N-No |
| 16 | Channel | ✅ | | | CHAR(1) | 开户渠道。枚举值（按截图顺序）：A-Approved Business Partner Referral、B-RM Referral、W-Walk-in customer、I-Internal customer、O-Solely E-Onboarding Channel |
| 17 | Usage of products | ✅ | | | CHAR(1) | 高风险产品类型。枚举值：1-Low Risk Product Only、2-At Least One Medium Risk Product、3-At Least One High Risk Product。**高风险产品包括：VAS Account、Solar House Account、Solar Client Account、Commercial Card (VPA)** |
| 18 | On PEC | ✅ | | | CHAR(1) | 是否政府标志。枚举值：Y-Yes、N-No |
| 19 | Number of layers of ownership structure | ✅ | | | CHAR(2) | 公司股权结构层数。枚举值：1、2、3、4、5、6、7、8、9、10 |
| 20 | Internal Client Type | | | | CHAR(2) | 内部客户类型（选填）。枚举值：PUBLIC SECTOR、OTHER |
| 21 | Account Officer | | | | CHAR(10) | 客户经理代码（选填） |
| 22 | Business Nature | | | | CHAR(5) | 业务性质（选填）。枚举值：SMALL BUSINESS、SECURITIES FIRM、BANK、PUBLIC SECTOR、OTHER COMPANY、Other、INSURANCE COMPANY |
| 23 | Country Of Risk | | | | CHAR(3) | 风险国家（选填） |
| 24 | Country Of Origin | | | | CHAR(3) | 总部注册国家（选填） |
| 25 | Classify | ✅ | | | CHAR(1) | 分类（必输）。枚举值：S-SME、C-COPR、Z-ZA GROUP、N-NBFI |
| 26 | Whether the board resolution document has been obtained | ✅ | | | CHAR(1) | 是否已取得董事会决议文件。枚举值：Y-Yes、N-No |

---

## 2. Contact Details

对应数据模型：`party_organization_profile`（地址字段）+ `party_address`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Company Registered Address | ✅ | ✅ | | 见下方子字段 | **公司注册地址**。地址格式因 Country 而异（格式A支持中英文，格式B为中文），子字段详见下方说明。填写完成后系统自动将各子字段拼接为完整地址展示于输入框下方供核对。 |
| 2 | Company Operation Address | ✅ | ✅ | | 见下方子字段 | **公司运营地址**。格式规则同上。可切换"与注册地址相同"快捷填入；切换后地址格式随 Country 实时刷新，不触发整页刷新。 |
| 3 | Company Mailing Address | ✅ | ✅ | | 见下方子字段 | **公司邮寄地址**。格式规则同上。可切换"与注册地址相同"快捷填入。 |
| 4 | Declaration of registered address and business address | | | | CHAR(1) | 声明注册/营业地址是否为秘书公司地址。枚举值：1-注册或营业地址为秘书公司地址、2-注册及营业地址均为秘书公司地址、3-注册或营业地址为住宅地址、4-注册及营业地址均为住宅地址 |

> **📌 公司地址子字段规格说明（适用于 Company Registered / Operation / Mailing Address 及 Company Secretary Address）**
>
> 开户及 RM Account Opening 场景下，须先选择 Country 以确定地址格式（香港/海外 或 中国内地）；修改场景按已存数据格式显示，修改 Country 后实时刷新格式，改回原 Country 时自动恢复原数据。
>
> **格式 A：香港及其他海外国家/地区地址（支持中英文输入）**
> 格式顺序：Room → Floor → Building → Street → City → Province → Country
>
> | 子字段 | 类型 | 长度 | 必填规则 | 特殊交互 |
> |--------|------|------|----------|----------|
> | Room | 文本输入（含前置关键词下拉） | CHAR(35) | 选填 | 前置下拉框选择关键词：ROOM、FLAT、UNIT、NO.、SHOP、PORTION、OTHER；选 OTHER 时新增自定义关键词输入框，关键词存入 Room 字段最前方 |
> | Floor | 文本输入 | CHAR(35) | 选填 | 提交时系统自动在 Floor 值末尾追加 "/F"；若用户已输入 "/F" 或 "Floor"，输入框变红并提示"请勿重复输入关键词"，须删除后方可提交 |
> | Building | 文本输入 | CHAR(70) | 选填 | — |
> | Street | 文本输入 | CHAR(70) | **Country = HONG KONG 时必填**；其他海外国家必填 | — |
> | City | 下拉选择 | CHAR(70) | 选填 | — |
> | Province（区域） | 下拉选择 | CHAR(70) | **Country = HONG KONG 时必填**（禁止自填）；其他海外国家选填（自填） | HK：先选大区再选区，最终按"区-大区"格式显示；其他国家：自填 |
> | Country | 下拉选择 | CHAR(2) | 必填 | 决定下方格式切换 |
>
> **格式 B：中国内地地址（中文地址）**
> 格式顺序：国家 → 省 → 市 → 区 → 详细地址
>
> | 子字段 | 类型 | 长度 | 必填规则 | 特殊交互 |
> |--------|------|------|----------|----------|
> | 国家（Country） | 文本输入 | CHAR(2) | 必填 | — |
> | 省（Province） | 下拉选择 | CHAR(70) | 选填 | 联动：选省后自动筛选对应市 |
> | 市（City） | 下拉选择 | CHAR(70) | 选填 | 联动：选市后自动筛选对应区 |
> | 区（District） | 下拉选择 | CHAR(36) | 选填 | — |
> | 详细地址（Detail） | 文本输入 | CHAR(255) | 选填 | — |
>
> **地址完整拼接展示：** 填写完成后，系统自动将所有子字段（去除首尾空格）拼接为一条完整地址，显示于输入区域下方供填写人核对。

| 5 | Reason | | | | CHAR(500) | 注册/营业地址为秘书公司地址的原因（Declaration 有值时填写） |
| 6 | Contact Person First Name | ✅ | | | CHAR(30) | 联系人名字 |
| 7 | Contact Person Last Name | ✅ | | | CHAR(30) | 联系人姓氏 |
| 8 | Contact Person Email Address | ✅ | | | CHAR(34) | 联系人电子邮件地址 |
| 9 | Contact Person Telephone Number Area Code | ✅ | | | CHAR(5) | 联系人手机区号，**下拉选择**。枚举值：852（香港）、86（中国大陆）、84（越南）、66（泰国）。**注意：BIB 目前仅支持发送 SMS 至以上四个区号** |
| 10 | Contact Person Telephone Number | ✅ | | | CHAR(18) | 联系人电话号码 |
| 11 | Contact Person Job Title | ✅ | | | CHAR(3) | 联系人职衔，下拉选择。枚举值（按截图顺序）：董事、商人、东主、总经理、行政人员、高级行政人员、财务总监、秘书、其他 |
| 12 | Other Contact Person Job Title | | | | CHAR(30) | 其他联系人职衔（Job Title = 其他 时填写） |
| 13 | Office Telephone Number | | | | CHAR(18) | 办事处电话（选填） |
| 14 | Company Website | | | | CHAR(120) | 公司网页（选填） |
| N+1 | Group CIF Number | | | | CHAR(12) | 集團客戶號碼（選填），填寫後關聯集團客戶檔案 |
| N+2 | Group CIF Name | | | | CHAR(120) | 集團名稱（選填），與 Group CIF Number 配套使用 |

---

## 3. Company Business Background

对应数据模型：`party_organization_profile`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Office Location(s) | | | | CHAR(3) | 办公室地点，枚举值：国家列表 |
| 2 | Principal Place of Operations | ✅ | ✅ | | CHAR(3) | 主要运营地，枚举值：国家列表 |
| 3 | Countries of Foreign Branch(es)/Subsidiary(ies)/Factory(ies) | | | ✅ | CHAR(3) | 海外分支/子公司/工厂所在国家/地区，最多支持 10 个，枚举值：国家列表 |
| 4 | Country of Key Counterparties | | | ✅ | CHAR(3) | 主要交易对手所在国家，最多支持 5 个，枚举值：国家列表 |
| 5 | Industry Category | ✅ | | | CHAR(6) | 行业大类（第一行业） |
| 6 | Key Products/Services | ✅ | | | CHAR(6) | 行业小类/主要产品与服务（第一行业对应） |
| 7 | Industry Category2 | | | | CHAR(6) | 行业大类（第二行业，选填） |
| 8 | Key Products/Services2 | | | | CHAR(6) | 行业小类（第二行业，选填） |
| 9 | Name of Regulatory Body | | | | CHAR(120) | 监管机构名称（选填） |
| 10 | Regulatory Reference Number | | | | CHAR(30) | 监管机构参考编号（选填） |
| 11 | Country of Regulatory Body | | | | CHAR(3) | 监管机构所在国家（选填） |
| 12 | Employees (Country of Employee(s) + Number of Employee(s)) | | | ✅ | CHAR(3) + CHAR(5) | 雇员所在国家及人数，一组国家+人数，最多支持 5 组 |
| 13 | Whether WEB3 Company | ✅ | | | CHAR(1) | 是否 WEB3 公司。枚举值：Y-Yes、N-No |
| 14 | Whether Greater China Company | ✅ | | | CHAR(1) | 是否大中华区公司。枚举值：Y-Yes、N-No |
| 15 | Financial Background | | | | CHAR(5) | 公司经济类型（选填）。枚举值：1110-STATE-OWNED(国有)、1120-COLLECTIVELY-OWNED(集体)、1140-JOINT OPERATION(联营)、1160-JOINT STOCK(股份)、1170-PRIVATE-OWNED(私营)、1175-INDIVIDUAL-OWNED(个体)、1200-FOREIGN CAPITAL(外资)、1330-HONG KONG,MACAU,OR TAIWAN CAPITAL(港澳台资)、9999-OTHERS(其他) |
| 16 | Is your Company a Holding Company | | | | CHAR(1) | 是否为控股公司。枚举值：Y-Yes、N-No |
| 17 | Referral Party | ✅ | | | CHAR(60) | 开户推荐人名称（Channel = RM Referral 时必输） |

---

## 4. Financial Indicator of Latest Fiscal Year

对应数据模型：`party_organization_profile` + `party_organization_counterparty_profile`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Annual Business Turnover (HKD) | ✅ | | | NUM(16,2) | 全年营业额（等值港币），默认 0.00 |
| 2 | Net Profit (HKD) | | | | NUM(16,2) | 净利润（等值港币），选填，默认 0.00 |
| 3 | Asset Size (HKD) | | | | NUM(16,2) | 资产规模（等值港币），选填，默认 0.00 |
| 4 | Others (Please Specify) | | | | CHAR(200) | 其他补充说明（选填） |
| 5 | Supplier（Countries Of Supplier + Company Name Of Supplier + 占比%） | ✅ | | ✅ | CHAR(3)+CHAR(120)+DECIMAL | 供应商国家、公司名称及占总采购百分比，支持多条 |
| 6 | Buyer（Countries of Buyer + Company Name of Buyer + 占比%） | ✅ | | ✅ | CHAR(3)+CHAR(120)+DECIMAL | 买方国家、公司名称及占总销售百分比，支持多条 |
| 7 | Countries of Which Assets Held（Country + 占比%） | ✅ | | ✅ | CHAR(3)+DECIMAL | 资产持有国家及占总资产百分比，支持多条 |
| 8 | Name of the jurisdiction（Country + Purpose of business in the jurisdiction） | | | ✅ | CHAR(3)+CHAR(120) | 在制裁国家开展商业活动时填写，包含国家及在该司法管辖区经营目的，最多 5 个 |
| 9 | SEQ 制裁合规问卷（Sanctions Exposure Questionnaire） | ✅ | | | 见下方子字段 | 根据 SEQ 最新要求重新设计，触发逻辑及问题内容按国家类型分类展开，详见下方说明块。 |

> **📌 SEQ 制裁合规问卷设计规格（基于 SEQ 需求分析 v2 + Standard Script v2）**
>
> **检查对象（适用范围）：**
> 相关人士（自然人）的出生地、国籍、居住地址；公司的注册地址、运营地址、邮寄地址、主要运营国家/地区、买方国家/地区、资产国家/地区、主要交易对手国家/地区。
>
> **国家分类（系统后台可配置，非页面硬编码）：**
>
> | 类型 | 当前国家/地区列表（系统配置初始值，可后台更新） | 处理结果 |
> |------|--------------|----------|
> | **Type 1**（实施定制制裁的国家） | 阿富汗、白俄罗斯、中非共和国、刚果民主共和国、厄立特里亚、海地、伊拉克、黎巴嫩、利比亚、缅甸、俄罗斯联邦、南苏丹、苏丹、委内瑞拉、也门、津巴布韦、乌克兰 | 不涉及相关行业 → 填写 SEQ 后可继续开户；涉及相关行业 → 填写 SEQ 后拒绝开户 |
> | **Type 2**（实施全面制裁的国家） | 伊朗、朝鲜、叙利亚、古巴、乌克兰的非乌克兰政府控制区（含克里米亚地区、塞瓦斯托波尔、顿涅茨克人民共和国DNR、卢甘斯克人民共和国LNR、扎波罗热地区） | 只要命中该类国家 → 填写 SEQ 后拒绝开户 |
>
> **SEQ 问题结构：**
>
> ⚠️ **国家动态化原则：** SEQ 四个问题中涉及的国家/地区名称，页面**不硬编码**具体国名，而是从系统后台 SEQ 国家配置表中动态读取 Type 1 / Type 2 清单实时渲染。合规团队可在后台调整国家清单，无需发版即可生效。
>
> | 问题编号 | 问题内容（页面动态渲染，国家名称由系统配置填入） | 类型 | 枚举值 | 命中处理 |
> |----------|----------------------|------|--------|----------|
> | Q1 | Does the company have any current or planned exposure to **{Type 1 Russia-specific}** in the energy / oil and gas sector, the military or defence sector? | Type 1 俄罗斯行业专项 | Y / N | Y → 拒绝开户 |
> | Q2 | Does the company or, to the best of your knowledge, any of its connected or other related parties have any current or planned business activity in **{Type 2 Crimea/Sevastopol region config}**? | Type 2 区域专项 | Y / N | Y → 拒绝开户 |
> | Q3 | To the best of your knowledge, does the company or any of the company's connected or other related parties exist in **{Type 2 country list}**, and/or are currently targeted by sanctions administrated by the following bodies: UN, EU, UKHMT, HKMA, OFAC, or as part of local sanctions law? | Type 2 制裁目标检查 | Y / N | Y → 拒绝开户 |
> | Q4 | Does the Company currently have or plan to have business activity in the following countries/regions: **{Type 1 + Type 2 country list}**? | Type 1 & Type 2 综合业务检查 | Y / N | Type 2 命中 → 拒绝；Type 1 命中 → 视行业决定 |
>
> **实施方案：**
>
> **临时方案（当前）：OPS 手工工作流**
> - PWEB 前端：要求客户在开户流程中回答上述四个问题，任一命中即系统直接拒绝开户
> - RM 开户（EOB 后台）：OPS 在 KYC CALL 中向客户询问相关 SEQ 问题，人工判断是否命中并在系统中记录结果；如涉及，则在 KYC CALL 中调查客户相关 SEQ 问题
>
> **长期方案（系统化）：EOB/PWEB 流程嵌入 SEQ**
> - **触发机制：** 系统在开户信息录入完成后，自动将以下字段中的国家/地区与系统 SEQ 国家配置表进行实时比对：买方国家、供应商国家、资产持有国家、主要运营地、注册地址、运营地址、相关人士居住地址、国籍、出生地
> - **比对规则：** 命中 Type 1 或 Type 2 任一国家/地区，系统自动弹出 SEQ 问卷页面，要求 CDD-OPS / 客户回答四个问题
> - **国家清单维护：** Type 1 / Type 2 国家清单存储于系统后台配置表，合规团队可独立维护更新，页面问题文案动态读取，无需发版
> - **自动拦截：** Type 2 国家命中且问卷答案为 Y，系统直接拒绝开户并记录拒绝原因；Type 1 命中则进入人工复核流程
> - **审计留存：** SEQ 问卷结果及相关国家命中记录写入客户档案，供 FCC 审计及监管报送使用
> - **下游系统：** SEQ 填写结果上传至 KYC 系统，并同步给 FCC 进行审批
| 13 | Purpose for Account Opening | ✅ | | ✅ | CHAR(3) | 开户目的，支持多选，最多 5 个。枚举值：BO-BUSINESS OPERATIONS、IS-PAYMENT OF HIRE、IV-INVESTMENT、SF-SAVING/FIXED DEPOSIT、PR-PAYROLL、LO-LOAN、OT-OTHER |
| 14 | Others (Please Specify) | | | | CHAR(200) | 其他开户目的（Purpose = OT 时填写） |
| 15 | Whether the annual turnover of the group exceeds 1.5 billion | | | | CHAR(1) | 集团年营业额是否超过 15 亿。枚举值：Y-Yes、N-No |
| 16 | Non-SME flag | | | | CHAR(1) | 是否非中小企业标志。枚举值：Y-Yes、N-No |

---

## 5. Source of Wealth/Fund

对应数据模型：`party_organization_asset_profile`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Initial Source of Fund | ✅ | | ✅ | CHAR(3) | 初始资金来源，支持多选，最多 5 个。枚举值：BI-BUSINESS INCOME、IF-INTRA-GROUP FINANCING、IP-INVESTMENT BY UBO/PARTNER、OT-OTHER、SA-SALE OF PROPERTY/ASSET |
| 2 | Other Initial Source of Fund | | | | CHAR(60) | 其他初始资金来源说明（Initial Source of Fund = OT 时填写） |
| 3 | Ongoing Source of Fund | ✅ | | ✅ | CHAR(3) | 持续资金来源，支持多选，最多 5 个。枚举值同上 |
| 4 | Other Ongoing Source of Fund | | | | CHAR(60) | 其他持续资金来源说明（Ongoing Source of Fund = OT 时填写） |
| 5 | Initial Source of Wealth | ✅ | | ✅ | CHAR(3) | 初始财富来源，支持多选，最多 5 个。枚举值：BI-BUSINESS INCOME、IF-INTRA-GROUP FINANCING、IP-INVESTMENT BY UBO/PARTNER、OT-OTHER、SA-SALE OF PROPERTY/ASSET |
| 6 | Other Initial Source of Wealth | | | | CHAR(60) | 其他初始财富来源说明 |
| 7 | Ongoing Source of Wealth | ✅ | | ✅ | CHAR(3) | 持续财富来源，支持多选，最多 5 个。枚举值同上 |
| 8 | Other Ongoing Source of Wealth | | | | CHAR(60) | 其他持续财富来源说明 |

---

## 6. Anticipated Transaction & Purpose of Usage

对应数据模型：`party_organization_anticipated_txn_profile` + `party_organization_anticipated_txn_country_profile`

### 6.1 Cross Border Transaction（跨境交易）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Domestic / International Transaction | ✅ | | | CHAR(1) | 本地/国际交易类型。枚举值：A-本地电汇(Domestic Payment)、B-国际电汇(International Payment)、C-本地和国际电汇(Domestic and International Payment) |
| 2 | Does the Company intent to make Cross Border Transaction | ✅ | | | CHAR(1) | 公司是否进行跨境交易。枚举值：Y-Yes、N-No |
| 3 | Countries (to) of Cross Border Transaction | | | ✅ | CHAR(3) | 跨境交易汇出目标国家（Cross Border = Y 时填写），支持多个，最多 10 个，枚举值：国家列表 |
| 4 | Countries (from) of Cross Border Transaction | | | ✅ | CHAR(3) | 跨境交易汇入来源国家（Cross Border = Y 时填写），支持多个，最多 10 个，枚举值：国家列表 |
| 5 | Monthly Number of Cross Border Transaction (Inward) | | | | CHAR(1) | 每月跨境转账交易量（汇入）。枚举值：1至20宗、21至50宗、50宗以上 |
| 6 | Monthly Inward Amount of Cross Border Transaction（Currency + 金额） | | | ✅ | CHAR(3)+NUM(16,2) | 每月跨境转账汇入金额及货币，支持多种货币，最多 10 组，默认 0.00 |
| 6a | Monthly Number of Cross Border Transaction (Outward) | | | | CHAR(1) | 每月跨境转账交易量（汇出）。枚举值：1至20宗、21至50宗、50宗以上。**仅当客户风险级别 = High Risk 时显示并填写** |
| 6b | Monthly Outward Amount of Cross Border Transaction（Currency + 金额） | | | ✅ | CHAR(3)+NUM(16,2) | 每月跨境转账汇出金额及货币，支持多种货币，最多 10 组，默认 0.00。**仅当客户风险级别 = High Risk 时显示并填写** |
| 7 | Purpose for Cross-Border Transaction | | | ✅ | CHAR(3) | 跨境交易目的，支持多选，最多 10 个（Cross Border = Y 时必输）。枚举值：Trading-贸易、Investment-投资、Rental Payment/Collection-支付/收取租金、Payroll-支付薪金、Others-其他 |
| 8 | Other Purpose for Cross-Border Transaction | | | | CHAR(200) | 其他跨境交易目的（Purpose = Others 时填写） |

### 6.2 Regular Domestic Transaction（常规本地交易）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 9 | Does the Company intend to make Regular Domestic Transaction | ✅ | | | CHAR(1) | 公司是否进行常规本地交易。枚举值：Y-Yes、N-No |
| 10 | Monthly Number of Regular Transaction (Inward) | | | | CHAR(1) | 每月本地转账交易量（汇入）。枚举值：1至20宗、21至50宗、50宗以上 |
| 11 | Monthly Inward Amount of Regular Transaction（Currency + 金额） | | | ✅ | CHAR(3)+NUM(16,2) | 每月本地转账汇入金额及货币，支持多种货币，最多 10 组 |
| 11a | Monthly Number of Regular Transaction (Outward) | | | | CHAR(1) | 每月本地转账交易量（汇出）。枚举值：1至20宗、21至50宗、50宗以上。**仅当客户风险级别 = High Risk 时显示并填写** |
| 11b | Monthly Outward Amount of Regular Transaction（Currency + 金额） | | | ✅ | CHAR(3)+NUM(16,2) | 每月本地转账汇出金额及货币，支持多种货币，最多 10 组，默认 0.00。**仅当客户风险级别 = High Risk 时显示并填写** |
| 12 | Purpose for Regular Transaction | | | ✅ | CHAR(1) | 本地交易目的，支持多选，最多 10 个（Regular Domestic = Y 时必输）。枚举值：Trading-贸易、Investment-投资、Rental Payment/Collection-支付/收取租金、Payroll-支付薪金、Others-其他 |
| 13 | Other Purpose for Regular Transaction | | | | CHAR(200) | 其他本地交易目的（Purpose = Others 时填写） |
| 14 | Does the Company intend to hold balances in the account with infrequent activity | | | | CHAR(1) | 公司是否预期在账户持有结余而不进行交易。枚举值：Y-Yes、N-No |
| 15 | Please indicate the amount and rationale | | | | CHAR(120) | 请提供金额及理由（Hold Balances = Y 时填写） |

---

## 7. FATCA/CRS

对应数据模型：`party_fatca_profile` + `party_crs_profile` + `party_tax_residency`

> **⚠️ 公司类型差异说明：** FATCA Documentation、FATCA Classification、Non-consenting U.S. account Sub-type 三个字段根据公司类型是否为 **Sole Proprietorship** 显示不同枚举值。CRS Entity Classification 和 Sign Date of CRS Documentation 仅当公司类型**不为** Sole Proprietorship 时显示。

### 7.1 公司层面 FATCA

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | FATCA Documentation | ✅ | | | ENUM | FATCA 文档类型（原字段名"W-8BEN-E/W-9/Self-Certification"，本次改名）。枚举值：01-Form W-8BEN-E、02-Form W-9、03-Self-certification、04-Others – Please specify、05-Form W-8ECI、06-Form W-8EXP、07-Form W-8BEN、08-Form W-8IMY and Withholding Statement、09-Substitute Form W-8BEN-E。**注意：根据公司类型是否为 Sole Proprietorship 显示不同枚举值** |
| 2 | Others – Please specify | | | | VARCHAR(120) | 新增字段。FATCA Documentation = 04 时必输 |
| 3 | FATCA Classification | | | | ENUM | FATCA 实体分类（原字段名"W-8BEN-E"，本次改名）。枚举值（39项）：01-Active NFFE-501(c) organization、02-Active NFFE-Active NFFE、03-Active NFFE-Direct reporting NFFE、04-Active NFFE-Excepted nonfinancial entity in liquidation/bank、05-Active NFFE-Excepted nonfinancial start-up company、06-Active NFFE-Excepted territory NFFE、07-Active NFFE-Foreign gov/gov of U.S. possession/non-MO/US cen、08-Active NFFE-International organization(non-MO)、09-Active NFFE-Nonfinancial group entity、10-Active NFFE-Nonprofit organization、11-Active NFFE-Publicly traded NFFE or its NFFE affiliate、12-Active NFFE-Sponsored direct reporting NFFE、13~18-CDCFFI系列、19~23-EBO系列、24-Excepted inter-affiliate FFI、25~28-FFI系列、29~30-ODFFI系列、31-Passive NFFE with no substantial U.S. owners、32-Passive NFFE with substantial U.S. owner(s)、33~34-RDCFFI系列、35-Restricted distributor、36-Specified U.S. Person、37-Non-consenting U.S. account、38-U.S. Person other than Specified U.S. Person、39-Non-U.S. Person。**根据公司类型是否为 Sole Proprietorship 显示不同枚举值** |
| 4 | Non-consenting U.S. account Sub-type | | | | ENUM | 当 FATCA Classification = 37-Non-consenting U.S. account 时必输。枚举值：01-USP、02-Passive NFFE、03-Dormant、04-With US indicia、05-Without US indicia。**根据公司类型是否为 Sole Proprietorship 显示不同枚举值** |
| 5 | GIIN Code（Code1 + Code2 + Code3 + Code4） | | | | VARCHAR(16) | GIIN（全球中介机构识别码），分四段填写 |
| 6 | GIIN Status | | | | ENUM | GIIN 状态。枚举值：ACTIVE、INACTIVE、REVOKED、EXPIRED |
| 7 | Other types of self-certification | | | | ENUM | 其他自我声明类型。枚举值（35项）：01-Active NFFE-501(c) organization 至 35-Restricted distributor（与 FATCA Classification 01~35 枚举值相同） |
| 8 | Disregarded entity | | | | ENUM | 是否为非独立实体。枚举值：Y-Yes、N-No |
| 9 | Sign Date of FATCA Documentation | ✅ | | | DATE | FATCA 文档签署日期（原字段名"Sign Date of W-8BEN-E"，本次改名） |
| 10 | FATCA Due Diligence Status | ✅ | | | ENUM | FATCA 尽调状态。枚举值：01-Completed - Normal FATCA、02-Completed - Enhanced FATCA Due Diligence、03-Incomplete、04-N/A |
| 11 | FATCA pre-existing account | ✅ | | | CHAR(1) | **新增字段。** 是否为 FATCA 既有账户。枚举值：Y-Yes、N-No。**存量数据处理：** 系统根据账户开立时间及 FATCA 规则自动判断并赋值，存量客户由核心系统 SQL 批量更新 |

### 7.2 公司层面 CRS

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 12 | CRS Entity Classification | | | | ENUM | 原字段名"Entity Type"，本次改名。**仅公司类型不为 Sole Proprietorship 时显示。** 枚举值：01-FINANCIAL INSTITUTION-1、02-FINANCIAL INSTITUTION-2、03-ACTIVE NON-FINANCIAL ENTITY(NFE)-1、04-ACTIVE NON-FINANCIAL ENTITY(NFE)-2、05-ACTIVE NON-FINANCIAL ENTITY(NFE)-3、06-ACTIVE NON-FINANCIAL ENTITY(NFE)-4、07-PASSIVE NON-FINANCIAL ENTITY(PNFE)-1、08-PASSIVE NON-FINANCIAL ENTITY(PNFE)-2 |
| 13 | Sign Date of CRS Documentation | | | | DATE | **新增字段。** CRS 自我声明签署日期。当 CRS Entity Classification 不为空时必输。**仅公司类型不为 Sole Proprietorship 时显示** |
| 14 | Tax（Tax Residency + TIN + Reason for No TIN） | ✅ | | ✅ | 组合字段 | 税务信息，最多 4 条。Tax Residency CHAR(3)（必输）；Tax Identifying Number(TIN) CHAR(30)（TIN 与 Reason for No TIN 二者必输其一）；Reason for No TIN（枚举值：Reason A-所在税务居住地不向居民签发 TIN、Reason B-控制人无法取得 TIN、Reason C-TIN 不适用） |
| 15 | CRS self-certification valid | ✅ | | | CHAR(1) | **新增字段。** CRS 自我声明是否有效。枚举值：Y-Yes、N-No。**存量数据处理：** 存量客户默认赋值 Y（假设既有 CRS 声明有效），由核心系统 SQL 批量更新；后续如有客户更新声明则系统实时更新 |
| 16 | CRS pre-existing account | ✅ | | | CHAR(1) | **新增字段。** 是否为 CRS 既有账户。枚举值：Y-Yes、N-No。**存量数据处理：** 系统根据账户开立时间及 CRS 规则自动判断并赋值，存量客户由核心系统 SQL 批量更新 |

### 7.3 相关人士层面 Controlling Person（新增子模块）

> 当 Connected Party（Details of Connected Parties - Individuals）中某个相关人士的 **Capacity of Connected Party 选择 118-Controlling Person** 时，FATCA/CRS 模块中自动显示该相关人士的 Controlling Person 信息子模块。

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 17 | CP role（Controlling Person Type） | ✅ | | | CHAR(6) | **对应需求中的 CP role 字段，由 Connected Parties 中 Controlling Person Type 字段承载，无需新增独立字段。** 当 Capacity of Connected Party = 118-Controlling Person 时必输。枚举值：CRS801-LEGAL PERSON-OWNERSHIP、CRS802-LEGAL PERSON-OTHER MEANS、CRS803-LEGAL PERSON-SENIOR MANAGING OFFICIAL、CRS804-LEGAL ARRANGEMENT-TRUST-SETTLOR、CRS805-LEGAL ARRANGEMENT-TRUST-TRUSTEE、CRS806-LEGAL ARRANGEMENT-TRUST-PROTECTOR、CRS807-LEGAL ARRANGEMENT-TRUST-BENEFICIARY、CRS808-LEGAL ARRANGEMENT-TRUST-OTHER、CRS809-LEGAL ARRANGEMENT-OTHER-SETTLOR-EQUIVALENT、CRS810-LEGAL ARRANGEMENT-OTHER-TRUSTEE-EQUIVALENT、CRS811-LEGAL ARRANGEMENT-OTHER-PROTECTOR-EQUIVALENT、CRS812-LEGAL ARRANGEMENT-OTHER-BENEFICIARY-EQUIVALENT、CRS813-LEGAL ARRANGEMENT-OTHER-OTHER-EQUIVALENT |
| 18 | Tax Residency（Controlling Person） | ✅ | | ✅ | CHAR(3) | 新增字段，相关人士层面税务居住国家/地区。枚举值：国家列表（HK、CN 排在最前，其余按字母排序），最多 4 条 |
| 19 | Tax Identifying Number / TIN（Controlling Person） | | | ✅ | CHAR(30) | 新增字段，相关人士税务号码。TIN 与 Reason for No TIN 二者必输其一，最多 4 条 |
| 20 | Reason for No TIN（Controlling Person） | | | | ENUM | 新增字段，无 TIN 原因。枚举值：Reason A-所在税务居住地不向居民签发 TIN、Reason B-控制人无法取得 TIN、Reason C-TIN 不适用 |
| 21 | Sign Date of CRS Documentation（Controlling Person） | ✅ | | | DATE | 新增字段，CRS 表格签署日期。当 Capacity of Connected Party = 118-Controlling Person 时必输 |



---

## 8. Company Change

对应数据模型：`party_organization_change_history_profile`

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Have there been Material Changes to your Business in the last 5 years? (eg changes to ownership, company name, parent company, countries of operation, nature of business, products/services) | ✅ | | | CHAR(1) | 过去 5 年是否发生实质性变化。枚举值：Y-Yes、N-No |
| 2 | Company Name Change（Origin + Start Date + End Date） | | ✅ | ✅ | CHAR(120)+DATE+DATE | 公司曾用名及生效/终止日期（Material Change = Y 时展示），支持多条，最多 10 组 |
| 3 | Company Industry Change（Industry Category + Key Products/Services + Reason + Start Date + End Date） | | | ✅ | CHAR(6)+CHAR(6)+CHAR(500)+DATE+DATE | 行业变更记录，包括旧行业大类/小类、变更原因及日期，支持多条，最多 10 组 |
| 4 | Company Ownership Structure Change（Reason + Start Date） | | | | CHAR(500)+DATE | 股权结构变更记录，包括变更原因及起始日期 |
| 5 | Company Beneficial Owners Change（Reason + Start Date） | | | | CHAR(500)+DATE | UBO 变更记录，包括变更原因及起始日期 |

---

## 9. Company Search - Company Secretary Information

对应数据模型：`party_organization_profile`（秘书信息部分）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | ID Type | | | | ENUM | 秘书公司证件类型。枚举值：BR、CI、LEI、OTHERS |
| 2 | ID Number | | | | CHAR(30) | 秘书公司证件号码 |
| 3 | Company English Name | | ✅ | | CHAR(60) | 秘书公司英文名称 |
| 4 | Company Chinese Name | | | | CHAR(60) | 秘书公司中文名称（选填） |
| 5 | Company Secretary Address | | | | 见地址子字段规格 | 秘书公司地址。格式与子字段规格与 Section 2 公司地址相同（格式 A 香港/海外 或 格式 B 内地，依 Country 选择切换）。填写后自动拼接完整地址展示。 |

> **【後台控制規則】CRA Rerun 強制觸發：**
> 當 RM Maker 修改 `Company English Name`（秘書公司英文名稱）後，系統後台自動強制 Maker 重新觸發 CRA（Risk Scoring）Rerun。
> - **觸發條件：** Company English Name 字段值發生變更並保存
> - **執行方式：** 後台自動標記該案件 `pending_cra_rerun = true`，Maker 下次提交時系統攔截並要求先執行 Risk Scoring
> - **前端不顯示任何提示**，純後台控制邏輯

---

## 10. Details of Connected Parties

对应数据模型：`party_relationship` + `party_individual_employment_profile`（SOW 部分）

### 10.1 Individuals（相关人士 - 自然人）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Identity Card Type | ✅ | ✅ | | ENUM | 证件类型。枚举值（来源：PersonIdType 数据字典）：ID-HK Identity Card（香港居民身份证）、PP-Passport（护照）、CD-Chinese Resident Identity Card（中国居民身份证）、PT-Others（其他） |
| 2 | Identity Card Number | ✅ | ✅ | | CHAR(30) | 证件号码 |
| 3 | Others Identity | | | | CHAR(30) | 其他证件类型说明（Identity Card Type = OTHERS 时填写） |
| 4 | First Name | ✅ | ✅ | | CHAR(30) | 英文名字 |
| 5 | Last Name | ✅ | ✅ | | CHAR(30) | 英文姓氏 |
| 6 | Chinese Name | | ✅ | | CHAR(60) | 中文姓名 |
| 7 | Capacity of Connected Party | ✅ | | ✅ | CHAR(2) | 相关人士角色，支持多选，最多 10 个。枚举值（来源：CompanyRole 数据字典）：085-Beneficial Owner、034-Director、065-Partner、114-Sole Proprietor、084-Beneficiary、069-Shareholder ≥25%、103-Senior Officer、088-Authorised Representative、019-BIB Representative & Checker、045-Guarantor (For Loan)、060-Trustee、104-Settlor、105-Protector、117-Project S Contact Person、118-Controlling Person、119-Contact Person、120-BIB Maker、121-BIB Administrator、1199-BIB TRADER |
| 8 | Controlling Person Type | | | | CHAR(6) | 实际控制人类型（Capacity 含 Controlling Person 时必输）。枚举值：CRS801-LEGAL PERSON-OWNERSHIP、CRS802-LEGAL PERSON-OTHER MEANS、CRS803-LEGAL PERSON-SENIOR MANAGING OFFICIAL、CRS804~CRS813（各类信托及其他安排角色） |
| 9 | Sign Date of CRS Documentation (for Controlling Person) | | | | DATE | 控制人 CRS 文件签署日期 |
| 10 | Tax（Tax Residency + Tax Identifying Number(TIN) + Reason for No TIN） | | | ✅ | CHAR(3)+CHAR(30)+CHAR | 税务居民国家及税务号码，最多 4 组 |
| 11 | Ownership | | | | DECIMAL(5,2) | 股份占比（%） |
| 12 | Job Title | | | | CHAR(30) | 职衔 |
| 13 | Permanent HKID | | | | CHAR(1) | 是否永久香港身份证（Identity Card Type = HKID 时填写）。枚举值：Yes、No |
| 14 | ID Issue Date | | | | NUM(8) | 身份证明文件签发日期 |
| 15 | Gender | ✅ | ✅ | | CHAR(3) | 性别。枚举值：M-Male、F-Female |
| 16 | Date of Birth | ✅ | ✅ | | NUM(8) | 出生日期，格式 YYYYMMDD |
| 17 | Place of Birth | ✅ | ✅ | | CHAR(3) | 出生地，枚举值：国家列表 |
| 18 | Nationality | | ✅ | ✅ | CHAR(3) | 国籍，最多 5 个，枚举值：国家列表 |
| 19 | Email Address | ✅ | | | CHAR(34) | 电子邮件地址 |
| 20 | Telephone Number Area Code | | | | CHAR(5) | 电话区号。**注意：BIB 目前仅支持 852、86、84、66 区号发送 SMS** |
| 21 | Telephone Number | ✅ | | | CHAR(18) | 电话号码 |
| 22 | Residential Address | ✅ | ✅ | | 见下方子字段 | **相关人士居住地址**。格式与子字段规格同公司地址（格式 A 香港/海外 或 格式 B 内地）。**Room 和 Floor 自动关键词逻辑（居住地址专属）**：提交时系统自动检测 Room / Floor 字段内容，若缺少关键词则自动补充；若已包含则不处理。规则：Room 字段不以 ROOM / UNIT / FLAT / FLT / FT / RM / NO. / SHOP / PORTION / SUITE / SECTION / OFFICE / West Wing / APARTMENT 开头或结尾，则自动在最前方加 "Room"；Floor 字段不以 floor / /F / level 开头或结尾，则自动在最末尾加 "/F"。填写后自动拼接完整地址展示。 |
| 23 | Beneficial Owner Source of Wealth (Only for Beneficial Owner) | | | ✅ | CHAR(3) | 实益拥有人的财富来源（仅适用于实益拥有人），最多 5 个。枚举值：BI-BUSINESS INCOME、IF-INTRA-GROUP FINANCING、IP-INVESTMENT BY UBO/PARTNER、OT-OTHER、SA-SALE OF PROPERTY/ASSET |
| 24 | Others (Please Specify) | | | | CHAR(200) | 其他财富来源（Beneficial Owner SOW = OT 时必输） |
| 25 | Country(ies) in which Beneficial Owner attained his/her Initial Source of Wealth (Only for Beneficial Owner) | | | ✅ | CHAR(3) | 实益拥有人获得初始财富来源的国家（仅适用于实益拥有人），支持多选，最多 5 个 |
| 26 | Country(ies) in which Beneficial Owner attained his/her Ongoing Source of Wealth (Only for Beneficial Owner) | | | ✅ | CHAR(3) | 实益拥有人获得持续财富来源的国家（仅适用于实益拥有人），支持多选，最多 5 个 |
| 27 | Related Client On PEP | | | ✅ | CHAR(1) | PEP 标志，支持多选，最多 2 个。枚举值：Hong Kong PEP、Non Hong Kong PEP、International Organization PEP、No |
| 28 | Common Customer Alignment Information | | | | 表格 | 关联客户对齐信息，只读展示，列字段包括：Action、Chinese Name、Gender、Date of Birth、Place of Birth、Nationality、PEP Classification、PEP Link、Tax Info、Industry 1、Industry 2、Residence Address、Related Company Chinese Name、Relation Code、Percentage of Holding Shares、Latest Risk Rating (Client Risk)、Update Time |
| 29 | Authorised Representative's Signature style | | | | 附件 | 授权代表签名式样。以文件列表形式展示，列字段包含：File Type（文件类型）、File Name（文件名）、操作（Delete / Download / Preview / Upload）。File Type 固定为 "Authorised Representative"。上传方式：1）点击"Upload"按钮选择本地图片文件上传；2）截图后在上传区域直接 Ctrl+V / Cmd+V 粘贴，系统自动识别剪贴板图片内容完成上传。已上传文件支持预览（Preview）、下载（Download）和删除（Delete）。支持格式：PNG、JPG、JPEG。 |

**FATCA（個人）**

| # | 字段名 | 必輸 | NS | 多值 | 類型/長度 | 說明/枚舉值 |
|---|--------|:----:|:--:|:----:|-----------|-------------|
| F1 | US Tax ID (FATCA TIN) | | | | CHAR(30) | 美國稅務識別號碼（如有），適用於 US Person |
| F2 | Substantial US owner flag | | | | CHAR(1) | 是否為實體的重大美國股東。枚舉：Y-Yes、N-No |

**CRS（個人）**

| # | 字段名 | 必輸 | NS | 多值 | 類型/長度 | 說明/枚舉值 |
|---|--------|:----:|:--:|:----:|-----------|-------------|
| C1 | Entity controlling person flag | | | | CHAR(1) | 是否為實體控制人。枚舉：Y-Yes、N-No |
| C2 | CRS Tax Residency Country | | | ✅ | CHAR(3) | CRS 稅務居留國，最多 4 個，ISO 3166-1 alpha-3 |
| C3 | CRS TIN | | | ✅ | CHAR(30) | CRS 稅務識別號碼，與 Tax Residency 一一對應 |
| C4 | CRS Reason for No TIN | | | ✅ | CHAR(200) | 無 TIN 原因，與 Tax Residency 一一對應 |
| C5 | Type of Controlling Person | | | | CHAR(2) | 控制人類型。枚舉：01-Controlling ownership interest / 02-Control through other means / 03-Senior managing official / 04-Settlor / 05-Trustee / 06-Protector/Enforcer / 07-Beneficiary / 08-Other |

> 支持新增多名 Individual（点击 + 按钮），每名 Individual 均可独立删除。

### 10.2 Entities（相关人士 - 公司）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | ID Type | ✅ | ✅ | | ENUM | 证件类型。枚举值：BR、CI、LEI、OTHERS |
| 2 | ID Number | ✅ | ✅ | | CHAR(30) | 证件号码 |
| 3 | Others | | | | CHAR(30) | 其他证件号码（ID Type = OTHERS 时填写） |
| 4 | Registered Office Address (Only for Senior Office, Director, Authorised Representative) | | ✅ | | 见地址子字段规格 | 公司注册地址（仅适用于高级管理人员、董事、获授权代表）。格式与子字段规格同 Section 2 公司地址（格式 A 香港/海外 或 格式 B 内地）。Floor 字段自动追加 "/F" 关键词；Room 字段含前置关键词下拉（ROOM/FLAT/UNIT/NO./SHOP/PORTION/OTHER）。填写后自动拼接完整地址展示。 |
| 5 | Principal Business Address (Only for Senior Office) | | | | 见地址子字段规格 | 主要营业地址（仅适用于高级管理人员）。格式与子字段规格同上。 |
| 6 | Registered Full Name in English | ✅ | ✅ | | CHAR(60) | 公司英文注册全名 |
| 7 | Registered Full Name in Chinese (if applicable) | | ✅ | | CHAR(60) | 公司中文注册全名（如适用） |
| 8 | Entity Type of Connected Entity | ✅ | | | CHAR(2) | 相关人士实体类型（公司类型），参考 Company Type 数据字典 |
| 9 | Capacity of Connected Entity | ✅ | | ✅ | CHAR(3) | 相关人士角色，支持多选 |
| 10 | Ownership | | | | DECIMAL(5,2) | 股份占比（%） |
| 11 | Listing on Stock Exchange | | | | CHAR(30) | 是否上市公司。枚举值：Y-Yes、N-No |
| 12 | Name(s) of Stock Exchange | | | | CHAR(120) | 证券交易所名称（Listing = Y 时填写） |

> 支持新增多家 Entity（点击 + 按钮），每家 Entity 均可独立删除。

---

## 11. DD Account Information

对应数据模型：账户开立信息（对公储蓄账户）

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Product Code | ✅ | | | CHAR | 产品代码，默认值：CORP - SAVING ACCOUNT |
| 2 | Currency Type | ✅ | | | CHAR | 货币类型，默认值：Multiple Currency |
| 3 | Account Type | ✅ | | | CHAR | 账户类型，下拉选择。枚举值：Client Account、House Account、SFGS Account、Payroll Account、Solar House Account、Solar Client Account、Reserve Account |
| 4 | Account English Name | ✅ | | | CHAR | 账户英文名称 |
| 5 | Account Local Name | | | | CHAR | 账户本地名称（选填） |
| 6 | Booking Centre | ✅ | | | CHAR | 账本中心，下拉选择 |
| 7 | Create Commercial Card (VPA) Flag | | | | CHAR(1) | 是否创建商业卡（VPA），默认：No |
| 8 | Commercial Card (VPA) Number | | | | CHAR | 商业卡（VPA）号码（Create VPA Flag = Yes 时填写） |

> 支持新增多个账户（点击 + 按钮）。

---

## 12. Signing Arrangement

对应数据模型：授权签署安排

| # | 字段名 | 必输 | Name Screening | 多值 | 类型/长度 | 说明/枚举值 |
|---|--------|:----:|:--------------:|:----:|-----------|-------------|
| 1 | Signing Instruction | ✅ | | | CHAR(1) | 签署指示，下拉选择。枚举值：Other、Any one to sign、Any two to sign、Any three to sign、Any four to sign、Any five to sign、Any six to sign |
| 2 | Please specify | | | | CHAR | 其他签署指示说明（Signing Instruction = Others 时填写） |

---

## 附录：Company Type 数据字典

| 代码 | 说明 |
|------|------|
| 0 | Company Change flag | ✅ | | | CHAR(1) | 過去 5 年是否發生實質性變化的總標誌。枚舉：Y-Yes（展開子字段）、N-No。前端：選 Y 時顯示 Name/Industry/Other Change 子字段；選 N 時隱藏。 |
| CO | LIMITED COMPANY (UNLISTED) |
| CS | COOPERATIVE SOC |
| EM | EMBASSY |
| FD | FOUNDATION |
| FI | FINANCIAL INSTITUTION |
| FU | FUND |
| GO | GOVERNMENT DEPARTMENT |
| HC | HOLDING COMPANY |
| JC | JOINT CORP |
| LC | LIMITED COMPANY (LISTED) |
| MS | MONEY SERVICE BUSINESS |
| NG | NATIONAL OR LOCAL BODY GOVERNMENT |
| PC | PRIVATE INVESTMENT COMPANY |
| PF | PRIVATE INVESTMENT FUND |
| PT | PRIVATE HOLDING TRUST |
| SB | STATUTORY BODY |
| SO | SOCIETY |
| SP | SOLE PROPRIETORSHIP |
| TR | TRUST |
| TU | TRADE UNION |
| VS | VOLUNTARY SECTOR |
| JP | JOINT PERSON |
| PA | PARTNERSHIP |

---

## 附录：关键业务规则汇总

| 规则 | 说明 |
|------|------|
| BIB SMS 区号限制 | 目前仅支持向 852（香港）、86（大陆）、84（越南）、66（泰国）区号发送短信 |
| Greater China 判定 | 满足以下任一条件：1. 任何 UBO 为中国人；2. 供应商中国占比超过 50%；3. 买方中国占比超过 50% |
| 高风险产品 | VAS Account、Solar House Account、Solar Client Account、Commercial Card (VPA) |
| 秘书公司英文名称变更 | 如修改秘书公司英文名称，需手动触发 Risk Scoring |
| CRS Controlling Person 类型 | 当 Capacity of Connected Party 包含 Controlling Person 角色时，Controlling Person Type 为必输 |
| Name Screening 触发 | 带 # 标记的字段内容变更时，系统自动触发 Name Screening 扫描 |
| Beneficial Owner SOW | Beneficial Owner Source of Wealth 及相关国家字段仅适用于 Capacity = Beneficial Owner 的相关人士 |

---

# BIB（对公网银）信息录入 — 页面字段说明文档

> **文档说明**
> - `*` 红色星号：**必填字段**（Required）
> - 🔄 **系统自动反显**：字段值由前序开户信息（CIF/KYC 系统）自动带入，CDD-OPS 核认即可，BIB 不另行保存
> - 🔕 **系统自动赋值（不反显）**：字段值系统后台自动赋值传入 BIB，页面不展示，CDD-OPS 无需操作
> - ✏️ **CDD-OPS 手工录入**：CIF 系统无该字段，需由 CDD-OPS 在此页面手工填写，BIB 保存
> - ⚠️ **待IT确认**：该字段的使用方式或是否启用，尚待 IT 进一步确认
> - 字段顺序与页面截图一致，章节顺序按 BIB 系统操作流程排列

**整体流程说明：** 完成前序开戶信息（Business Banking Account Opening，共 12 步）录入并提交后，系统跳转至 BIB 信息录入页面，依次完成以下三个模块的操作：
1. Register New Company（注册公司网银档案）
2. New Company User（新增公司网银用户）
3. Set Transaction Limits → Set Authorization Preferences

---

## 附录：CIF 系统 → BIB 系统接口传值说明

**说明：** 开户流程完成提交后，系统自动将 CIF 中的以下字段通过接口传入 BIB，BIB 页面不展示这些字段，用户无需手动操作。

### Register New Company — CIF → BIB 传值映射

| # | BIB 字段名 | 数据类型 | CIF 来源字段 | 传值说明 |
|---|------------|----------|-------------|----------|
| 1 | CIF NO | VARCHAR | 系统生成 CIF NO. | 公司 CIF 编号，系统自动传入 |
| 2 | Full Name Eng | VARCHAR | Company English Name | 公司英文名称 |
| 3 | Full Name Chn | VARCHAR | Company Chinese Name | 公司中文名称 |
| 4 | Commercial Registry No. | VARCHAR | ID Number（Section 1） | 营业执照/注册证书编号 |
| 5 | Country of Registry | VARCHAR | Country of Registration | 注册国家/地区 |
| 6 | Address | VARCHAR | Company Registered Address | 公司注册地址（拼接后完整地址） |
| 7 | Telephone | VARCHAR | Contact Person Telephone Number | 联系人电话号码 |
| 8 | Fax No. | VARCHAR | 无对应字段 | 开户流程无传真字段，默认传空值 |
| 9 | E-mail | VARCHAR | Contact Person Email Address | 联系人电子邮件 |
| 10 | Foreign City | VARCHAR | 无对应字段 | 开户流程无此字段，默认传空值 |

### New Company User — BIB 页面字段完整说明（接口传值 + 操作方式）

| # | BIB 字段名 | 必填 | 数据类型 | CIF 来源字段 | 操作方式 | 说明 |
|---|------------|:----:|----------|-------------|----------|------|
| 1 | User ID | ✅ | VARCHAR | 无 | ✏️ CDD-OPS 手工录入 | BIB 用户登录 ID，BIB 保存 |
| 2 | Role | | LIST | 无 | ✏️ CDD-OPS 手工录入 | Operator / Approver / Administrator，可多选 |
| 3 | Investment Flag | ✅ | ENUM | 无 | ✏️ CDD-OPS 手工录入 | No / Yes，默认 No，BIB 保存 |
| 4 | Short Name | ✅ | VARCHAR | First Name + Last Name | 🔄 反显（供确认） | BIB 不保存 |
| 5 | English Name | ✅ | VARCHAR | First Name + Last Name | 🔄 反显（供确认） | BIB 不保存 |
| 6 | First Name | | VARCHAR | First Name | 🔄 反显（供确认） | BIB 不保存 |
| 7 | Last Name | ✅ | VARCHAR | Last Name | 🔄 反显（供确认） | BIB 不保存 |
| 8 | Chinese Full Name | | VARCHAR | Chinese Name | 🔄 反显（供确认） | BIB 不保存 |
| 9 | Date of Birth | ✅ | DATE | Date of Birth | 🔄 反显（供确认） | BIB 不保存 |
| 10 | Gender | | ENUM | Gender | 🔄 反显（供确认） | Female / Male，BIB 不保存 |
| 11 | Title | ✅ | VARCHAR | Job Title | 🔄 反显（供确认） | BIB 不保存 |
| 12 | Full Name | | VARCHAR | 待 IT 确认 | ⚠️ 待确认 | 若无实际用途则废弃 |
| 13 | ID Type | | ENUM | Identity Card Type | 🔄 反显（供确认） | HKID / Passport，BIB 不保存 |
| 14 | Permanent HKID | | ENUM | Permanent HKID | 🔄 反显（供确认） | BIB 不保存 |
| 15 | ID No. | ✅ | VARCHAR | Identity Card Number | 🔄 反显（供确认） | BIB 不保存 |
| 16 | ID Issue Date | | DATE | ID Issue Date | 🔄 反显（供确认） | BIB 不保存 |
| 17 | ID Issuer | | VARCHAR | 无（CIF 无此字段） | ⚠️ 待 IT 确认 | 待确认 BIB 是否使用 |
| 18 | Telephone | ✅ | VARCHAR | Telephone Number Area Code + Telephone Number | 🔄 反显（供确认） | BIB 不保存 |
| 19 | Mobile Telephone | ✅ | VARCHAR | Telephone Number Area Code + Telephone Number | 🔄 反显（供确认） | 区号下拉 + 号码，BIB 不保存 |
| 20 | E-mail | ✅ | VARCHAR | Email Address | 🔄 反显（供确认） | BIB 不保存 |
| 21 | Fax No. | | VARCHAR | 无 | 🔄 反显（空值） | 默认留空 |
| 22 | Country | ✅ | ENUM | Residential Address → Country | 🔄 反显（供确认） | 国家下拉，BIB 不保存 |
| 23 | User Group | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | Default，仅 Operator / Approver 可见 |
| 24 | Authorization Level | ✅ | ENUM | 无 | ✏️ CDD-OPS 手工录入 | A/B/C/D/E，仅 Approver 必填 |
| 25 | View All Approvers' Approval Records | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | NO / YES，仅 Approver 可见 |
| 26 | FX Flag | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | No / Yes，仅 Approver 可见 |
| 27 | Receive Notification | ✅ | ENUM | 无 | ✏️ CDD-OPS 手工录入 | Yes / No，所有角色 |
| 28 | Notification Accounts | | LIST | 无 | ✏️ CDD-OPS 手工录入 | All Account + 账户列表，Receive Notification = Yes 时展示 |
| 29 | Revolving Loan Drawdown Menu | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | No / Yes，所有角色 |
| 30 | Transaction Authority | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | 默认 All Transfer，所有角色 |
| 31 | Download encrypted Security Token file | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | No / Yes，所有角色 |
| 32 | Receive the decryption code | | ENUM | 无 | ✏️ CDD-OPS 手工录入 | No / Yes，所有角色 |
| 33 | Internal Transfer（BAU） | | DECIMAL | 无 | ✏️ CDD-OPS 手工录入 | User's BAU Account Transaction Limit，仅 Operator 可见 |
| 34 | Local Fund Transfer（BAU） | | DECIMAL | 无 | ✏️ CDD-OPS 手工录入 | User's BAU Account Transaction Limit，仅 Operator 可见 |
| 35 | Overseas Transfer（BAU） | | DECIMAL | 无 | ✏️ CDD-OPS 手工录入 | User's BAU Account Transaction Limit，仅 Operator 可见 |
| 36 | Payroll（BAU） | | DECIMAL | 无 | ✏️ CDD-OPS 手工录入 | User's BAU Account Transaction Limit，仅 Operator 可见 |
| 37 | Internal Transfer（Solar） | | DECIMAL | 无 | ✏️ CDD-OPS 手工录入 | User's Solar Account Transaction Limit，仅 Operator 可见 |

---

## 13. Register New Company（注册公司网银档案）

**功能说明：** 在 BIB 系统中为公司创建网银档案。公司基本信息由 CIF 系统通过接口自动传入（详见上方接口传值说明），页面仅展示以下需要 CDD-OPS 手工录入的网银专有配置项。

| # | 字段名 | 必填 | 数据类型 | 枚举值 |
|---|--------|:----:|----------|--------|
| 1 | Allow Time Deposit Operation | ✅ | CHAR(1) | YES / NO |
| 2 | Allow Approver Selection | ✅ | CHAR(1) | YES / NO |
| 3 | Allow First Approver | ✅ | CHAR(1) | YES / NO |
| 4 | Authorization Preference Currency | ✅ | LIST（多选） | HKD / USD / CNY |
| 5 | Virtual Account Menu | ✅ | CHAR(1) | YES / NO |
| 6 | Monthly Limit Flag | ✅ | CHAR(1) | YES / NO |
| 7 | FX Consent Flag | ✅ | CHAR(1) | YES / NO |



---

## 14. New Company User（新增公司网银用户）

**功能说明：** 为公司网银创建用户（Operator / Approver / Administrator）。先从开户相关人士中选择对应的自然人，系统自动反显其基本信息（只读，不可修改）；再由 CDD-OPS 手工录入 BIB 专属权限配置。可新增多名用户，每名用户完成一次完整流程。

### 14.1 公司信息（系统只读展示）

| 字段名 | 说明 |
|--------|------|
| Company ID | 系统自动展示（如 C8000000333） |

### 14.2 相关人士选择 + 反显信息（只读）

| # | 字段名 | 必填 | 数据类型 | 说明 |
|---|--------|:----:|----------|------|
| 1 | 相关人士名字 | ✅ | 下拉选择 | 从开户页面 Section 10 已维护的自然人相关人士中选择，下拉列表显示各相关人士的 First Name + Last Name。支持新增多名（点击 + 按钮），最多可新增的数量等于 Section 10 中已维护的自然人相关人士总数。每名用户对应一位自然人相关人士，选择后系统自动反显以下字段（只读，不可在此修改；如需修改请返回 Section 10 相关人士章节；Section 10 修改后此处自动刷新）。**仅限自然人相关人士，不包括公司相关人士（Entities）。** |
| 2 | Identity Card Type | | ENUM | 反显所选相关人士的证件类型 |
| 3 | Identity Card Number | | VARCHAR | 反显所选相关人士的证件号码 |
| 4 | First Name | | VARCHAR | 反显所选相关人士的英文名字 |
| 5 | Last Name | | VARCHAR | 反显所选相关人士的英文姓氏 |
| 6 | Chinese Name | | VARCHAR | 反显所选相关人士的中文姓名 |
| 7 | Capacity of Connected Party | | LIST | 反显所选相关人士的角色 |
| 8 | Email Address | | VARCHAR | 反显所选相关人士的电子邮件 |
| 9 | Telephone Number Area Code | | ENUM | 反显所选相关人士的电话区号 |
| 10 | Telephone Number | | VARCHAR | 反显所选相关人士的电话号码 |

> **注意：** 以上反显字段均为只读，不可在 BIB 页面直接修改。如需更改，请返回开户页面 Section 10 Details of Connected Parties 进行修改，修改后系统将自动刷新此处显示的信息。

### 14.3 BIB 用户配置（CDD-OPS 手工录入，BIB 保存）

| # | 字段名 | 必填 | 数据类型 | 枚举值/说明 | 可见条件 |
|---|--------|:----:|----------|-------------|----------|
| 11 | User ID | ✅ | VARCHAR | BIB 用户登录 ID | 所有角色 |
| 12 | Investment Flag | ✅ | ENUM | No（默认）/ Yes | 所有角色 |
| 13 | User Group | | ENUM | Default | 仅 Operator / Approver |
| 14 | Authorization Level | ✅ | ENUM | A / B / C / D / E | 仅 Approver 必填 |
| 15 | View All Approvers' Approval Records | | ENUM | NO / YES | 仅 Approver |
| 16 | FX Flag | | ENUM | No / Yes | 仅 Approver |
| 17 | Receive Notification | ✅ | ENUM | Yes / No | 所有角色 |
| 18 | Notification Accounts | | LIST（多选） | All Account / DD Account1 + Account English Name（开户时 DD Account 号码尚未生成，以"DD Account1 + Account English Name"显示）；Receive Notification = Yes 时展示 | Receive Notification = Yes |
| 19 | Revolving Loan Drawdown Menu | | ENUM | No / Yes | 所有角色 |
| 20 | Transaction Authority | | ENUM | All Transfer（默认） | 所有角色 |
| 21 | Download encrypted Security Token file | | ENUM | No / Yes | 所有角色 |
| 22 | Receive the decryption code | | ENUM | No / Yes | 所有角色 |

### 14.4 用户交易限额（仅 Operator 可见，CDD-OPS 手工录入，BIB 保存）

| # | 字段名 | 数据类型 | 所属分类 |
|---|--------|----------|----------|
| 23 | Internal Transfer | DECIMAL | User's BAU Account Transaction Limit |
| 24 | Local Fund Transfer | DECIMAL | User's BAU Account Transaction Limit |
| 25 | Overseas Transfer | DECIMAL | User's BAU Account Transaction Limit |
| 26 | Payroll | DECIMAL | User's BAU Account Transaction Limit |
| 27 | Internal Transfer | DECIMAL | User's Solar Account Transaction Limit |

---

## 15. Set Transaction Limits（设置客户交易限额）

**功能说明：** 在开户流程中，账户尚未正式开立，此阶段仅设置客户层面（Customer Level）的交易限额。CIF 系统调用 BIB 系统时，账户层面限额默认与客户层面限额保持一致。所有字段均由 **CDD-OPS 手工录入**，CIF 无该字段，BIB 保存。

### 15.1 客户层面限额设置（Customer Level Transaction Limit）

> **注意：** 账户层面的交易限额将默认与此处设置的客户层面限额保持一致，无需单独设置。

| # | 字段名 | 数据类型 | 所属分类 | 说明 |
|---|--------|----------|----------|------|
| 1 | Daily Limit | DECIMAL | ZA Bank Same-Name Account Transfer | 分类总日限额，placeholder: Unlimited |
| 2 | ZA Same-Name Account Transfer (Daily) | DECIMAL | ZA Bank Same-Name Account Transfer | 子项限额，placeholder: Unlimited |
| 3 | Time Deposit (Daily) | DECIMAL | ZA Bank Same-Name Account Transfer | 子项限额，placeholder: Unlimited |
| 4 | Daily Limit | DECIMAL | Third Party Account Transfer | 分类总日限额，placeholder: Max limit |
| 5 | Local Third Party Account Transfer (Daily) | DECIMAL | Third Party Account Transfer | 子项限额，placeholder: Max limit |
| 6 | Overseas Bank Account Transfer (Daily) | DECIMAL | Third Party Account Transfer | 子项限额，placeholder: Max limit |
| 7 | Batch Payment (Daily) | DECIMAL | Third Party Account Transfer | 子项限额，placeholder: Max limit |
| 8 | Daily Limit | DECIMAL | Payroll Service | 分类总日限额，placeholder: Max limit |
| 9 | Payroll (Daily) | DECIMAL | Payroll Service | 子项限额，placeholder: Max limit |

> **注：** 开户时账户尚未开立，页面不展示账户列表。CIF 系统调用 BIB 系统时，账户层面限额默认与此处设置的客户层面限额一致。

---

## 16. Set Authorization Preferences（设置授权偏好）

**功能说明：** 为公司设置交易授权规则，分两个交易类型分别配置（All Transaction Types 和 Solar Account Transaction Type），每种类型均可选择 Single Class 或 Multi Class 授权方式。所有字段均由 **CDD-OPS 手工录入**，CIF 无该字段，BIB 保存。

### 16.1 授权类型选择（第一级）

> 页面展示两个交易类型供选择，每个类型右侧显示当前设置状态（No Setting / Normal），点击进入对应类型的授权设置页面。

| # | 交易类型 | 说明 |
|---|----------|------|
| 1 | All Transaction Types | 适用于所有交易类型，右侧显示当前状态（No Setting / Normal） |
| 2 | Solar Account Transaction Type | 仅适用于 Solar 账户交易，右侧显示当前状态（No Setting / Normal） |

### 16.2 授权规则设置（第二级，按交易类型配置）

> 点击某个交易类型后进入，页面展示以下信息：

| 字段名 | 必填 | 说明 |
|--------|:----:|------|
| Transaction Type | | 只读，显示所选交易类型（All Transaction Type / Solar Account Transaction Type） |
| Currency Type | | 只读，显示 HKD |
| Authorization Type | ✅ | 单选：Single Class Authorization（选择后从下拉选择单一 Level A~E）；Multi Class Authorization（选择后配置授权矩阵） |

**Single Class Authorization：**
- 选择后显示 Level 下拉：A / B / C / D / E，选择一个审批级别即可

**Multi Class Authorization — 授权矩阵：**

| 字段名 | 说明 |
|--------|------|
| Amount Range From（HKD） | 金额区间起始值，第一行默认 0.00 |
| Amount Range To（HKD） | 金额区间结束值（最后一行可为空，表示不设上限） |
| Number of Approver(s) — Level A | 该金额区间需要 Level A 审批人数量 |
| Number of Approver(s) — Level B | 该金额区间需要 Level B 审批人数量 |
| Number of Approver(s) — Level C | 该金额区间需要 Level C 审批人数量 |
| Number of Approver(s) — Level D | 该金额区间需要 Level D 审批人数量 |
| Number of Approver(s) — Level E | 该金额区间需要 Level E 审批人数量 |
| Add a New Rule | 按钮，新增一行金额区间规则 |
| Remove a Rule | 按钮，删除对应行规则 |

### 16.3 开户完成后 — Welcome Letter 自动发送

> 完成全部 BIB 设置（Register New Company → New Company User → Set Transaction Limits → Set Authorization Preferences）后，**系统默认自动为每名 BIB 用户发送 Welcome Letter**，无需 CDD-OPS 手动触发。

---

## 附录：RM 开户审批流程说明

**说明：** 开户申请提交后，系统根据 Risk Scoring 结果自动路由至对应审批流程。审批流程分为 **Low/Medium Risk** 和 **High Risk** 两条路径。

---

### Low/Medium Risk 审批流程

#### 路径一：No Objection 路径（正常通过）

| 步骤 | 角色 | 操作 | 说明 |
|------|------|------|------|
| 1 | RM Maker | 提交 No Objection | 提交开户申请 |
| 2 | RM Checker | 审批 | 收到案件进行审核 |
| 2a | RM Checker → BU Head/RM Head | Escalation（上呈） | RM Checker 可选择上呈至 BU Head/RM Head |
| 2b | BU Head/RM Head | 审批 | 上呈后由 BU Head/RM Head 处理 |
| 3 | RM Checker | No Objection | 审批通过 → **审批结束** |
| — | RM Checker | Return | 退回至 RM Maker 修改后重新提交 |

> ⚠️ **限制规则：** BU Head/RM Head **不能直接点击 Reject**，需先 Return 给 RM Maker，由 RM Maker 先 Reject 后再处理。

#### 路径二：Reject 路径（拒绝开户）

| 步骤 | 角色 | 操作 | 说明 |
|------|------|------|------|
| 1 | RM Maker | 提交 Reject | 提交拒绝开户申请 |
| 2 | RM Checker | 审批 | 收到案件进行审核 |
| 3 | RM Checker | Reject | 确认拒绝 → **审批结束** |
| — | RM Checker | Return | 退回至 RM Maker 修改 |

> ⚠️ **限制规则：** RM Checker **不能直接点击 No Objection**，需先 Return 给 RM Maker，由 RM Maker 先改为 No Objection 后再提交。

---

### High Risk 审批流程

#### 路径一：No Objection 路径（正常通过）

| 步骤 | 角色 | 操作 | 说明 |
|------|------|------|------|
| 1 | RM Maker | No Objection | 提交开户申请 |
| 2 | RM Checker | No Objection | 初审通过 |
| 3 | RM Head | No Objection | 复审通过 |
| 4 | AML Maker | No Objection | AML 初审通过 |
| 5 | AML Checker | No Objection | AML 终审通过 → **审批结束** |
| — | RM Checker / RM Head / AML Maker / AML Checker | Return | 任一节点均可 Return 至 RM Maker 重新处理 |

> **Return 路径说明：**
> - RM Checker → Return → RM Maker
> - RM Head → Return → RM Maker
> - AML Maker → Return → RM Maker
> - AML Checker → Return → RM Maker（跨多级退回）

#### 路径二：Reject 路径（拒绝开户）

| 步骤 | 角色 | 操作 | 说明 |
|------|------|------|------|
| 1 | RM Maker | Reject | 提交拒绝开户申请 |
| 2 | RM Checker | 审批 | 收到案件进行审核 |
| 3 | RM Checker | Reject | 确认拒绝 → **审批结束** |
| — | RM Checker | Return | 退回至 RM Maker 修改 |

> ⚠️ **限制规则：** RM Checker **不能直接点击 No Objection**，需先 Return 给 RM Maker，由 RM Maker 先改为 No Objection 后再提交。

#### Reject 跨节点路径

| 发起节点 | 操作 | 说明 |
|----------|------|------|
| RM Checker | Reject | 直接拒绝，流程结束 |
| RM Head | Reject | 直接拒绝，流程结束 |
| AML Maker | Reject | 直接拒绝，流程结束 |

> ⚠️ **限制规则：** AML Checker **不能直接点击 Reject**，需先 Return 给 RM Maker，由 RM Maker 先 Reject 后再处理。

---

### 审批流程汇总对比

| 风险级别 | 审批节点 | 最短通过路径 |
|----------|----------|-------------|
| Low/Medium Risk | RM Maker → RM Checker（→ 可选 BU Head/RM Head） | RM Maker → RM Checker No Objection → 审批结束 |
| High Risk | RM Maker → RM Checker → RM Head → AML Maker → AML Checker | RM Maker → RM Checker → RM Head → AML Maker → AML Checker No Objection → 审批结束 |

### 通用规则

| 规则 | 说明 |
|------|------|
| Return 退回 | 任何节点均可 Return 至 RM Maker，RM Maker 修改后重新提交 |
| Reject 发起限制 | BU Head / RM Head 及 AML Checker 不能直接点击 Reject，必须先 Return 给 RM Maker 由 RM Maker 先 Reject |
| No Objection 发起限制 | RM Checker 在 Reject 路径下不能直接点击 No Objection，需先 Return 给 RM Maker 由 RM Maker 先改为 No Objection |
| Pass on 同角色转派 | RM Maker / RM Checker / AML Maker / AML Checker 均可将案件转派给同角色其他人员，不跨角色 |
| Proceed with Escalation | 仅 RM Checker 可操作，可将案件上呈至 RM Head 或 BU Head；上呈节点处理完毕后返回 RM Checker 继续流转，RM Head 节点保留显示并标记 Completed |


---

## Customer Risk 详细评估（Risk Scoring 结果页，Section 16 之后）

**功能说明：** 点击底部"Risk Scoring"按钮后，系统自动执行风险评分并刷新页面，在 Section 16 之后展示 Customer Risk 详细评估结果。

**整体页面布局：** 标题栏左侧显示"Customer Risk 详细评估"，右侧显示当前综合风险评级徽章（如"High Risk · 94.75"）。

**列表结构：** 评估结果以表格形式呈现，包含以下 6 列：

| 列名 | 说明 |
|------|------|
| 评估维度 | 风险评估项目名称 |
| Result | 评级结果（如 High Risk、Medium Risk、No Hit、Yes 等） |
| Detail | 评估详细说明 |
| Initial Score | 系统初始评分（数字，0 分显示为灰色） |
| Assessment Score | 人工评估分数（可与 Initial Score 不同） |
| Action | 操作控件（因评估维度不同而异，见下方各项说明） |

---

### 审批进度条（Progress Bar）

**对应组件：** `BBApprovalProgress.tsx`，位于 Case Overview 卡片内，铺满一行展示当前审批节点状态。

#### 节点显示规则

| 场景 | 显示节点 |
|------|---------|
| High Risk（正常流程） | `RM Maker → RM Checker → RM Head → AML Maker → AML Checker` 共 5 个 |
| Low / Medium Risk（正常流程） | `RM Maker → RM Checker` 共 2 个 |
| 任意风险等级 + Proceed with Escalation 期间 | 动态插入 `RM Head`，显示 `RM Maker → RM Checker → RM Head`，RM Head 蓝色高亮 |
| Escalation 完成，返回 RM Checker 后 | RM Head 节点**保留并标记 Completed（绿色对勾）**，不能消失；RM Checker 再次蓝色高亮 |

#### 每个节点显示内容

| 字段 | 说明 |
|------|------|
| 角色名称 | 如 RM Maker、RM Checker 等 |
| 状态 | `Pending`（灰色）/ `In Progress`（蓝色高亮）/ `Completed`（绿色对勾） |
| 处理人 | 认领该节点的人员姓名 |
| 完成时间 | 仅 Completed 状态显示 |

#### 连接线规则

- 已完成节点之间的连接线：**绿色**
- 未完成节点之间的连接线：**灰色**

---

### 风险评估各维度字段说明

| # | 评估维度 | Action 类型 | Action 交互说明 |
|---|----------|-------------|----------------|
| 1 | Main nature of business/industry | — | 无操作 |
| 2 | Date of incorporation | — | 无操作 |
| 3 | Entity type/Ownership structure | — | 无操作 |
| 4 | Bearer Shares Issued | — | 无操作 |
| 5 | Complex Structure | Radio（Yes / No） | 当 Result 为 YES 时，Action 列显示单选按钮组：**Yes**（默认选中）/ **No**，供人工确认或修改是否为复杂结构 |
| 6 | Country of incorporation/registration | — | 无操作 |
| 7 | High Tax Evasion Risk | — | 无操作 |
| 8 | Countries of operation/business | — | 无操作 |
| 9 | Countries of supplier | — | 无操作 |
| 10 | Countries of buyer | — | 无操作 |
| 11 | Usage of high risk products | — | 无操作 |
| 12 | Tax Risk | — | 无操作 |
| 13 | Onboarding channel | — | 无操作 |
| 14 | High Risk Remarks (LYOD) | Remarks 展开按钮 + Detail 按钮（True Hit 时） | 点击"Remarks ▾"按钮展开高风险备注复选框面板（见下方说明）；当 Result 为 True Hit 时，额外显示"Detail"按钮，点击弹出命中名单详情弹窗 |
| 15 | HI Company Secretary Address | Radio（True Hit / False Hit） | Action 列显示单选按钮组：**True Hit**（默认选中）/ **False Hit**，供人工确认秘书公司地址命中结果 |
| 16 | Sanctions Exposure (LYOD) | Detail 按钮（True Hit 时） | 当 Result 为 True Hit 时，显示"Detail"按钮，点击弹出命中名单详情弹窗 |
| 17 | PEP/Individual/SOG/Entity (LYOD) | Detail 按钮（True Hit 时） | 当 Result 为 True Hit/Hit PEP 时，显示"Detail"按钮，点击弹出命中名单详情弹窗 |
| 18 | Special Interest Subject (LYOD) | Detail 按钮（True Hit 时） | 当 Result 为 True Hit 时，显示"Detail"按钮，点击弹出命中名单详情弹窗 |
| 19 | Factiva (Internal List) | Detail 按钮（True Hit 时） | 当 Result 为 True Hit 时，显示"Detail"按钮，点击弹出命中名单详情弹窗 |
| 20 | Special List | — | 无操作 |
| 21 | Company Secretary Blacklist | — | 无操作 |
| 22 | AML Black List | Detail 按钮（True Hit 时） | 当 Result 为 True Hit 时，显示"Detail"按钮，点击弹出命中名单详情弹窗 |

---

### Action 交互详细说明

#### High Risk Remarks (LYOD) — Remarks 展开面板

点击"Remarks ▾"按钮，在该行下方展开高风险备注复选框面板，包含以下选项（可多选）：

| 选项 | 说明 |
|------|------|
| AML Reference | AML 参考标记 |
| AML Reference - Grey List | AML 灰色名单标记 |
| Closely Monitoring (User Input) | 密切监控（用户输入） |
| Others | 其他 |

再次点击按钮（变为"Remarks ▴"）可收起面板。

#### Complex Structure — Radio 操作

Action 列显示单选按钮组，选项：**Yes**（默认）/ **No**，供处理人确认或修改系统自动判断的复杂结构结果。

#### HI Company Secretary Address — Radio 操作

Action 列显示单选按钮组，选项：**True Hit**（默认）/ **False Hit**，供处理人确认秘书公司地址的命中属性。

#### 命中名单 Detail 弹窗（适用于 High Risk Remarks、Sanctions Exposure、PEP/Individual/SOG/Entity、Special Interest Subject、Factiva、AML Black List）

当上述评估维度的 Result 为 True Hit 时，Action 列出现"Detail"按钮。点击后弹出命中名单详情弹窗，弹窗结构如下：

**弹窗标题：** 对应评估维度名称（如"PEP(Individual)/SOC(Entity) (LYODS)"）

**弹窗内容表格列：**

| 列名 | 说明 |
|------|------|
| NO | 序号 |
| Original name | 原始姓名 |
| Names | 姓名（可能含别名） |
| Addresses | 地址 |
| Origin | 来源国家/地区 |
| ID | 证件编号 |
| Type | 名单类型（如 PEP、Sanctions、AML Blacklist 等） |
| Additional Info | 附加信息 |
| Dates Of Birth | 出生日期 |
| Place Of Birth | 出生地点 |
| Places Of Birth | 其他出生地点 |
| Hyperlinks | 参考链接 |
| Nationality | 国籍 |
| Request Date | 查询日期 |
| ID Type | 证件类型 |
| ID No. | 证件号码 |
| English Name | 英文姓名 |
| Chinese Name | 中文姓名 |
| DoB | 出生日期（简写） |
| Case Reference | 案件参考编号 |
| Result | 人工评估结果（操作控件，见下方说明） |
| Action | 保存操作（Save 按钮） |

**Result 列交互（人工评估）：**

每条命中记录的 Result 列包含以下操作控件：

单选按钮组（五选一）：
- Non Material Hit（非实质性命中）
- True Hit + Hong Kong PEP（真实命中 + 香港 PEP）
- True Hit + Non Hong Kong PEP（真实命中 + 非香港 PEP）
- True Hit + International Organization PEP（真实命中 + 国际组织 PEP）
- False Hit（误命中）

文本输入框：最多输入 4000 字，右下角显示字数计数（0/4000）。

Preview 链接：点击可预览所填写的评估备注内容。

**Action 列：** 每条记录对应一个"Save"按钮，点击保存该条记录的人工评估结果。

**弹窗关闭：** 点击右上角 ✕ 按钮，或点击弹窗外部遮罩区域，关闭弹窗。

### 银行补充附件（Bank Supplement Attachment）

| 字段名 | 说明 |
|--------|------|
| Document name | 附件文件名 |
| Upload by | 上传人 |
| Upload time | 上传时间 |
| Action | 操作（删除/下载） |

### 相关文件（Related Documents）

> 支持"Download Selected"批量下载已勾选文件。

| 文件类型 | 说明 | 操作 |
|----------|------|------|
| Business registration certificate | 商业登记证 | Delete / Download / Preview / Upload |
| NAR1 | 最新年度申报表 | Upload |
| M&A | 公司章程 | Upload |
| Other | 其他文件 | Upload |
| Certificate of Incorporation | 公司注册证 | Upload |
| Individual Background | 个人背景资料 | Upload |
| Form1(a) | 独资商号申报表 | Upload |
| Form1(c) | 合夥商号申报表 | Upload |
| Summary of Risk Assessment | 风险评估摘要 | Upload |

### Case Result（案件结论）

| 选项 | 说明 |
|------|------|
| No Objection | 无异议，批准开户 |
| Reject | 拒绝开户 |
| Pass CIU | 转交 CIU 处理 |

### Case Comment

> 文本输入区，供处理人填写案件备注说明。

### 页面底部操作按钮

| 按钮 | 说明 |
|------|------|
| Submit | 提交案件结论 |
| Risk Scoring | 重新执行 Risk Scoring |
| Reset Manual Risk Scoring | 重置手动风险评分 |
| Back | 返回上一页 |
| Generate NSL | 生成 NSL |



---

## 附錄：數據庫字段長度完整對照表（V1.4 已確認）

> 來源優先級：BB PRD（最高）> 核心 eonboarding 接口 api > 企業網銀開戶 api > 業務確認
> 標記說明：🟢 PRD確認 | 🔵 核心API確認 | 🟡 BIB API確認 | 🔴 業務確認

### A. 公司基本信息（party_organization_profile）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| case_number | CHAR(30) | 🟢 PRD | 系統自動生成 |
| company_english_name | CHAR(60) | 🟢 PRD | 英文字符60，中文只能30個 |
| company_chinese_name | CHAR(60) | 🟢 PRD | |
| trading_name_en | CHAR(60) | 🟢 PRD | 最多10個 |
| trading_name_cn | CHAR(60) | 🟢 PRD | 最多10個 |
| company_establish_date | NUM(8) | 🟢 PRD | 格式 YYYYMMDD |
| company_type | CHAR(2) | 🟢 PRD | CO/SP/PA 等 |
| id_type | CHAR(3) | 🟢 PRD | CI/BR |
| id_number | CHAR(30) | 🟢 PRD | |
| country_of_registration | CHAR(3) | 🟢 PRD | ISO 3166 國家碼 |
| listing_company | CHAR(30) | 🟢 PRD | 枚舉 Y/N |
| stock_exchange_name | CHAR(120) | 🟢 PRD | |
| bearer_shares_issued | CHAR(1) | 🟢 PRD | Y/N |
| intermediate_bearer_shares | CHAR(1) | 🟢 PRD | Y/N |
| channel | CHAR(1) | 🟢 PRD | O/W/A/I/B |
| account_type | CHAR(2) | 🟢 PRD | 01/02 |
| usage_of_products | CHAR(1) | 🟢 PRD + 🔴業務確認 | 枚舉 1/2/3 |
| complex_structure | CHAR(1) | 🔴 業務確認 | Y/N，系統計算結果 |
| ownership_structure_level | CHAR(2) | 🟢 PRD | 股權層級 |
| industry_category | CHAR(6) | 🟢 PRD | |
| key_products_services | CHAR(6) | 🟢 PRD | |
| number_of_employees | CHAR(5) | 🟢 PRD | |
| whether_web3_company | CHAR(1) | 🟢 PRD | Y/N |
| principal_place_of_operations | CHAR(3) | 🟢 PRD | 國家碼 |
| company_website | CHAR(120) | 🟢 PRD | |
| name_of_regulatory_body | CHAR(120) | 🟢 PRD | |
| regulatory_reference_number | CHAR(30) | 🟢 PRD | |
| customer_status | CHAR(3) | 🔴 業務確認 | C00-C36 BlockCode |
| on_pep | CHAR(1) | 🔵 核心API | Y/N，pep_flag1 |
| on_pec | CHAR(1) | 🔵 核心API | Y/N，pep_flag2 |
| client_risk | CHAR(1) | 🔵 核心API | H/M/L |
| with_aml_referral | CHAR(1) | 🟢 PRD | 高風險類型，多選存儲 |
| remark_for_aml_referral | CHAR(60) | 🟢 PRD | High Risk Remarks |

### B. 聯絡資料（party_address / party_contact）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| company_registered_address | CHAR(210) | 🟢 PRD | 拼接後總長度 |
| company_operation_address | CHAR(210) | 🟢 PRD | |
| company_mailing_address | CHAR(210) | 🟢 PRD | |
| address_line_1_6 (English) | CHAR(35) each | 🔵 核心API | 每行35 |
| address_building_street | CHAR(70) each | 🔵 核心API | Building/Street 各70 |
| declaration_of_address | CHAR(1) | 🟢 PRD | 枚舉 1/2/3/4 |
| reason | CHAR(500) | 🟢 PRD | 地址聲明原因 |
| contact_person_first_name | CHAR(30) | 🟢 PRD | |
| contact_person_last_name | CHAR(30) | 🟢 PRD | |
| contact_person_email | CHAR(34) | 🟢 PRD | |
| contact_person_telephone | CHAR(18) | 🟢 PRD | |
| contact_person_telephone_area_code | CHAR(5) | 🔵 核心API | 國際區號 |
| contact_person_job_title | CHAR(3) | 🟢 PRD | 枚舉 01-09 |
| other_contact_person_job_title | CHAR(30) | 🟢 PRD | Job Title=09時填寫 |
| office_telephone_number | CHAR(18) | 🟢 PRD | |

### C. 財務 / 業務背景（party_additional_info）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| annual_business_turnover | NUM(16,2) | 🟢 PRD | |
| net_profit | NUM(16,2) | 🟢 PRD | |
| asset_size | NUM(16,2) | 🟢 PRD | |
| company_name_of_supplier | CHAR(120) | 🟢 PRD | 最多10個 |
| countries_of_supplier | CHAR(3) | 🟢 PRD | |
| percentage_of_total_purchase | CHAR(4) | 🟢 PRD | |
| company_name_of_buyer | CHAR(120) | 🟢 PRD | 最多10個 |
| countries_of_buyer | CHAR(3) | 🟢 PRD | |
| percentage_of_total_sales | CHAR(4) | 🟢 PRD | |
| countries_of_assets_held | CHAR(3) | 🟢 PRD | |
| percentage_of_total_assets | CHAR(4) | 🟢 PRD | |
| others_please_specify | CHAR(200) | 🟢 PRD | |
| purpose_for_account_opening | CHAR(3) | 🟢 PRD | |

### D. Source of Wealth / Fund（party_wealth）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| initial_source_of_wealth | CHAR(3) | 🟢 PRD | 最多5個 |
| other_initial_source_of_wealth | CHAR(60) | 🟢 PRD | SOW=OT時填寫 |
| ongoing_source_of_wealth | CHAR(3) | 🟢 PRD | |
| other_ongoing_source_of_wealth | CHAR(60) | 🟢 PRD | |
| initial_source_of_fund | CHAR(3) | 🟢 PRD | |
| other_initial_source_of_fund | CHAR(60) | 🟢 PRD | |
| ongoing_source_of_fund | CHAR(3) | 🟢 PRD | |
| other_ongoing_source_of_fund | CHAR(60) | 🟢 PRD | |

### E. FATCA / CRS（party_fatca_profile / party_crs_profile）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| fatca_documentation | CHAR(2) | 🟢 PRD | W-8BEN-E/W-9 類型 |
| fatca_status | CHAR(2) | 🟢 PRD | |
| tax_residency | CHAR(3) | 🟢 PRD | 國家碼 |
| tax_identifying_number_tin | CHAR(22) | 🟢 PRD | |
| reason_for_no_tin | CHAR(1) | 🟢 PRD | |
| sign_date_of_fatca | NUM(8) | 🟢 PRD | |
| crs_status | CHAR(2) | 🟢 PRD | |
| fatca_due_diligence_status | CHAR(2) | 🟢 PRD | |
| sign_date_of_crs | NUM(8) | 🟢 PRD | |
| controlling_person_type | CHAR(6) | 🔵 核心API | CRS801-CRS813 |

### F. 相關人士 Individual（party_relationship / party_individual）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| ownership_structure_level | CHAR(2) | 🟢 PRD | 股權層級 |
| first_name | CHAR(30) | 🟢 PRD | |
| last_name | CHAR(30) | 🟢 PRD | |
| chinese_name | CHAR(60) | 🟢 PRD | |
| capacity_of_connected_party | CHAR(2) | 🟢 PRD | |
| ownership | CHAR(3) | 🟢 PRD | 股份佔比 |
| job_title | CHAR(30) | 🟢 PRD | |
| identity_card_type | CHAR(3) | 🟢 PRD | |
| others_identity_card_type | CHAR(60) | 🟢 PRD | ID Type=OTHERS時 |
| others_identity | CHAR(30) | 🔴 業務確認 | 其他證件說明 |
| identity_card_number | CHAR(30) | 🟢 PRD | |
| id_issue_date | NUM(8) | 🟢 PRD | |
| gender | CHAR(3) | 🟢 PRD | |
| date_of_birth | NUM(8) | 🟢 PRD | |
| place_of_birth | NUM(8) | 🟢 PRD | |
| nationality | CHAR(3) | 🟢 PRD | |
| email_address | CHAR(34) | 🟢 PRD | |
| telephone_number | CHAR(18) | 🟢 PRD | |
| telephone_area_code | CHAR(5) | 🔵 核心API | |
| residential_address | CHAR(210) | 🟢 PRD | |
| beneficial_owner_source_of_wealth | CHAR(3) | 🟢 PRD | |
| fatca_us_tin | CHAR(30) | 🟢 PRD | |
| tax_residency_individual | CHAR(3) | 🟢 PRD | |
| tin_individual | CHAR(30) | 🟢 PRD | |
| reason_for_no_tin_individual | CHAR(200) | 🟢 PRD | |
| type_of_controlling_person | CHAR(2) | 🟢 PRD | |
| please_indicate_amount_rationale | CHAR(120) | 🟢 PRD | |

### G. 相關人士 Entity（party_entity）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| registered_full_name_en | CHAR(60) | 🟢 PRD | |
| registered_full_name_cn | CHAR(60) | 🟢 PRD | |
| entity_type | CHAR(2) | 🟢 PRD | |
| capacity_of_connected_entity | CHAR(3) | 🟢 PRD | |
| others_capacity | CHAR(30) | 🟢 PRD | |
| registered_office_address | CHAR(210) | 🟢 PRD | |
| principal_business_address | CHAR(210) | 🟢 PRD | |
| listing_on_stock_exchange | CHAR(30) | 🟢 PRD | |

### H. BIB 企業網銀（bib_company / bib_user）

| 字段名 | 數據類型 | 來源 | 備注 |
|--------|---------|------|------|
| corp_id | CHAR(20) | 🟡 BIB API | 固定格式 C+cifNo |
| cif_no | CHAR(10) | 🔵 核心API | 核心客戶號 |
| corp_name | CHAR(120) | 🟡 BIB API | 公司中文短名 |
| full_name_bib | CHAR(100) | 🟡 BIB API | 公司英文全名 |
| commercial_registry_no | CHAR(20) | 🟡 BIB API | BR/CI號碼 |
| bib_address_1_4 | CHAR(40) each | 🟡 BIB API | 4行地址各40 |
| bib_user_id | CHAR(12) | 🟡 BIB API | 網銀登錄名 |
| bib_user_name | CHAR(60) | 🟡 BIB API | Short Name |
| bib_english_name | CHAR(100) | 🟡 BIB API | |
| bib_id_no | CHAR(20) | 🟡 BIB API | |
| bib_id_issuer | CHAR(40) | 🟡 BIB API | |
| bib_mobile | CHAR(20) | 🟡 BIB API | |
| bib_email | CHAR(60) | 🟡 BIB API | |
| bib_limit_1_10 | DECIMAL(15,2) | 🟡 BIB API | 交易限額 |


---

## 附錄二：三頁面完整交互說明（V1.6 補充）

> **說明：** 本附錄補充 V1.5 未覆蓋的交互細節，供前端開發直接參考實現。

---

### 一、頁面一：開戶列表頁 — 補充交互

#### 1.1 列表默認排序
- 進入列表時默認按 **Submission Time 降序**排列（最新的在第一條）
- 每個表格列均支持排序，點擊列頭切換升序/降序，箭頭圖標跟隨變化

#### 1.2 click（View Detail）跳轉邏輯
```
點擊 Action 列的「click」：
  if (訂單狀態 === '草稿') {
    → 跳轉到 /rm-account-opening/new?id={caseNo}
    → 恢復到上次退出時的 Section 位置
  } else {
    → 跳轉到 /bb-cases/{caseNo}（頁面三審批詳情頁）
  }
```

#### 1.3 delete 按鈕交互

| 步驟 | 說明 |
|------|------|
| 點擊「delete」 | 彈出確認框，**不直接刪除** |
| 確認框標題 | 「確認刪除」 |
| 確認框內容 | 「確認刪除該開戶草稿訂單嗎？刪除後無法恢復。」 |
| 確認框按鈕 | 「確認刪除」（紅色）/ 「返回」（灰色） |
| 點擊確認刪除 | 調用 `DELETE /api/bb-cases/{caseNo}`，成功後刷新列表，顯示 toast「刪除成功」|
| 點擊返回 | 關閉確認框，不執行任何操作 |
| 已提交狀態 | delete 按鈕置灰不可點擊，hover 顯示 tooltip「已提交訂單不可刪除」|

#### 1.4 Refresh 按鈕
- 點擊後重新調用列表接口，保留當前篩選條件不清空
- 刷新期間按鈕顯示旋轉加載動畫

---

### 二、頁面二：開戶資料錄入頁 — 補充交互

#### 2.1 Section 折疊/展開交互

```
點擊 Section Header（.sec-hd）：
  if (Section 已展開) {
    → 收起 .sec-body，chevron 圖標改為 ▼
    → 展示 .sec-preview 摘要行（關鍵字段預覽）
  } else {
    → 展開 .sec-body，chevron 圖標改為 ▲
    → 隱藏 .sec-preview
  }
```

**Section 預覽摘要規則（.sec-preview）：**

| Section | 預覽顯示字段 |
|---------|------------|
| 1 | 公司類型 / 英文名 / 證件號 / 注冊地 |
| 2 | 聯絡人 / 電話 / Email |
| 3 | 業務性質 / 年營業額範圍 |
| 4 | 年營業額金額 |
| 5 | SOF 來源 |
| 6 | Cross Border 條數 / Domestic 條數 |
| 7 | FATCA 類型 / CRS 類型 |
| 8 | 變更記錄條數 |
| 9 | 秘書公司名稱 |
| 10 | 自然人數量 / 公司數量 |
| 11 | 賬戶類型 |
| 12 | 簽署方式 |
| 13-16 | 已完成 / 未完成 狀態標籤 |

- 預覽字段只顯示**已填寫的關鍵字段**，未填寫則不顯示對應條目
- 預覽行最多顯示 **4 個字段**，超出省略

#### 2.2 Section 0：HKCR/IRD 查詢後自動反顯

```
用戶輸入 Document Type + Document Number，點擊「Search」：
  → 調用 TU 接口查詢
  → 查詢成功：將以下字段自動反顯到 Section 1：
```

| TU 返回字段 | 反顯至 Section 1 字段 |
|------------|---------------------|
| Registration Number | ID Number |
| dataSource（HKCR/IRD） | ID Type（CI / BR） |
| entityName | Company English Name |
| Company Type | Company Type |
| Incorporation Date | Company Establish Date |
| caseAddress / rawAddress | Registered Address（第1-4行） |

**反顯後的字段狀態：**
- 反顯值顯示為**淺灰底色只讀樣式**（`background:#f5f5f0; color:#888`）
- RM 可點擊「編輯」圖標解鎖並手動修改
- 解鎖後底色恢復白色，可正常輸入

**查詢失敗處理：**
- 查詢無結果：顯示 `alert-warn`「未查詢到對應公司信息，請確認證書類型和號碼後重試」
- 接口報錯：顯示 `alert-error`「查詢失敗，請稍後重試」

#### 2.3 Save Draft 暫存交互

```
點擊「Save Draft」：
  → 調用 POST /api/bb-cases/draft（或 PUT /api/bb-cases/{id}/draft）
  → 成功：
    - Sticky topbar 右側顯示「草稿已保存 HH:MM」（自動更新時間戳）
    - 頂部顯示綠色 toast「草稿已保存」，3秒後自動消失
  → 失敗：
    - 顯示紅色 toast「保存失敗，請重試」
```

**自動暫存：** 用戶在任意輸入框失焦後 **30秒無操作**，系統自動觸發一次 Save Draft（靜默保存，不顯示 toast，只更新頂部時間戳）

#### 2.4 Risk Scoring 按鈕交互

**前置校驗（點擊前檢查）：**
- Section 1 的必填字段（`*` 標注）全部填寫完畢，否則高亮未填字段並提示「請完善必填字段後再提交」
- Section 0 至少執行過一次 HKCR/IRD 查詢

**完整執行流程：**

```
點擊「Risk Scoring」按鈕：

Step 1：前端
  - 按鈕變為 loading 狀態（旋轉圖標 + 文字「評估中...」）
  - 自動觸發 Save Draft，保存當前填寫內容

Step 2：後端自動執行 Name Screening
  - 按 §4.3 掃描字段清單，調用 LYODS 名單管理系統
  - 同步執行 Special List 檢查（名單管理系統）
  - 同步執行 AML Black List 檢查（名單管理系統）
  - 各名單命中結果記錄到數據庫，供頁面三展示

Step 3：後端自動執行 Risk Scoring 打分
  - Name Screening 完成後，根據命中結果 + 開戶表單字段
  - 按 CRA 打分表（附錄三）逐 Pillar 計算得分
  - 判定風險等級（LOW / MEDIUM / HIGH / PROHIBITED）
  - 生成 Score Breakdown 明細

Step 4：後端返回結果，前端跳轉
  - 成功：navigate(`/bb-cases/${caseNo}`)，即頁面三
  - 失敗：按鈕恢復可點擊，顯示紅色 toast「風險評估失敗：{error}」
```

> **注意：** Name Screening 和 Risk Scoring 均為後端**同步串行執行**，前端只需維持 loading 狀態等待一次接口返回，無需分步調用。

---

**頁面三展示的 Risk Scoring 結果（對應截圖）：**

點擊 Risk Scoring 跳轉到頁面三後，頁面左側主欄展示以下內容：

**① Customer Risk 詳細評估表格**（Section 16 之後）

表格標題右側顯示當前風險等級徽章，如 `High Risk · 94.75`

| 評估維度 | Result 示例 | Detail 示例 | Initial Score | Assessment Score | Action |
|---------|-----------|-----------|:------------:|:---------------:|--------|
| Main nature of business/industry | High Risk | Activities of charity organisations (non-religious) | 20 | 20 | — |
| Date of incorporation | Medium Risk | Date of corpus limit 1 to 1.5 years · 2022-07-16 | 0.25 | 0.25 | — |
| Entity type/Ownership structure | High Risk | Trust | 15 | 15 | — |
| Bearer Shares Issued | Yes | Bearer shares issued in the ownership structure | 15 | 15 | — |
| Complex Structure | Yes | Company has a complex structure | 10 | 10 | ⭕ Yes ○ No |
| Country of incorporation/registration | High Risk Country | BURUNDI | 13 | 13 | — |
| High Tax Evasion Risk | No | > 4 responses | 0 | 0 | — |
| Countries of operation/business | High Risk Country | BELARUS | 15 | 15 | — |
| Countries of supplier | Medium Risk | Medium-CHAD 20.0% | 0.5 | 0.5 | — |
| Countries of buyer | High Risk | High-IRAQ 20.0% | 0 | 0 | — |
| Usage of high risk products | High Risk | At Least One High Risk Product | 10 | 10 | — |
| Tax Risk | More than one Tax Residency | Multiple tax residency countries | 10 | 10 | — |
| Onboarding channel | RM Referral | RM Referral | 0 | 0 | — |
| High Risk Remarks (LYOD) | True Hit | AML Reference | 10 | 10 | Remarks ▾ / Detail |
| HI Company Secretary Address | Hi Sec. Address | Score: 1 / Assessment: 1 | True HI | True HI | ⭕ True Hit ○ False Hit |
| Sanctions Exposure (LYOD) | True Hit | 1 / 2 | 15 | 15 | Detail |
| PEP/Individual/SOG/Entity (LYOD) | Hit PEP | 1 | 10 | 10 | Detail |
| Special Interest Subject (LYOD) | True Hit | 1 | 10 | 10 | Detail |
| Factiva (Internal List) | True Hit | 1 | 10 | 10 | Detail |
| Special List | No Hit | Not Hit | — | — | — |
| Company Secretary Blacklist | No Hit | Not Hit | — | — | — |
| AML Black List | Hit AML Black List | True Hit | — | — | Detail |

**② High Risk Remarks 展開面板**（命中時自動展開）

```
☑ AML Reference（系統自動勾選，disabled）
☐ AML Reference - Grey List
☐ Closely Monitoring (User Input)
☐ Others
```

**③ 右側審批操作面板（頁面三 sticky）**

| 區塊 | 截圖顯示內容 |
|------|------------|
| 1. 審批結果 | Recommend Approval / Recommend Reject / Return / **RFI**（已選中）|
| 2. Proceed with Escalation | RM Checker only，下拉選 RM Head / BU Head |
| 3. Pass on | 下拉選轉派人員 |
| 4. Select RFI Category | 上傳證明材料（已選）/ 補充信息填寫 / 身份重新認證 / 客戶聯系確認 |
| 5. Select RFI Template | 地址證明文件上傳 / 收入證明文件上傳 / 銀行流水文件上傳 |
| 6. 審批意見 | 文本框，最多 16383 字 |
| 7. 附件（可選） | 上傳（最多 60 個）|
| 操作按鈕 | 暫存 Save Draft / Risk Scoring / Rerun Name Screening / Generate RSS |
| 提交按鈕 | ▶ 提交 — Send RFI |

#### 2.5 Topbar 右側輔助功能按鈕

頁面二 Topbar 右側（草稿時間戳左側）固定顯示三個輔助按鈕：

| 按鈕 | 樣式 | 功能 |
|------|------|------|
| 填入測試數據 | 橙黃色邊框（#e9a825）、淡黃底（#fffbf0）、棕色文字（#854f0b）、帶刷新圖標 | 一鍵將 Section 0-16 所有字段填入預設模擬數據，填完後自動展開全部 Section，彈出提示「測試數據已填入！」**僅供測試和演示使用，不影響正式業務流程** |
| 展開全部 | 白底灰邊框 | 展開頁面所有 Section 的 sec-body |
| 折疊全部 | 白底灰邊框 | 折疊頁面所有 Section 的 sec-body |

**「填入測試數據」預填的模擬數據規格：**

```
Section 0：Document Type = CI，Document Number = 7546205
Section 1：
  - Company Type = CO（Limited Company Unlisted）
  - ID Type = BUSINESS REGISTRATION CERTIFICATE
  - ID Number = 7546205
  - Company English Name = AUTOTEST&RmCompany46053
  - Company Chinese Name = 自动化测试RM公司6122
  - Company Establish Date = 20200101
  - Country of Registration = HKG - Hong Kong
  - Channel = W（Walk-in）
  - Usage of products = 1（Low Risk Product Only）
  - High Tax Evasion Risk = N，Bearer Shares Issued = N
  - Classify = Normal，Whether board resolution obtained = Y
  - Trading Name #1 English = AUTOTEST Trading HK
  - Registered Address Street = IT STREET，Country = HKG

Section 2：
  - Contact Person First Name = Tai Man，Last Name = CHAN
  - Email = taiman.chan@test.com
  - Telephone = 852-98765432
  - Job Title = 01（董事）
  - Company Website = www.autotest-rmcompany.com

Section 3：
  - Principal Place of Operations = HKG
  - Industry Category = Trading，Key Products = Import and Export Trading
  - Whether WEB3 = N，Is Holding Company = N
  - Supplier Country #1 = CHN，Buyer Country #1 = HKG

Section 4：
  - Annual Business Turnover = 5,000,000（HKD）
  - Asset Size = 10,000,000，Net Profit = 500,000
  - Fiscal Year End = 12/31，Currency = HKD
  - SEQ Q1/Q2/Q3/Q4 = N

Section 5：SOF Initial/Ongoing = BI（BUSINESS INCOME），SOW Initial/Ongoing = BI

Section 6：
  - Purpose for Account Opening = BO（BUSINESS OPERATIONS）
  - Cross Border #1：Trade Payment / CHN / HKD / Monthly / 100,000

Section 7：FATCA Documentation = 04（N/A），CRS Entity = Active NFE

Section 8：Company Change flag = N

Section 9：
  - Secretary Company English Name = ABC SECRETARIAL SERVICES LTD
  - Secretary Company Chinese Name = 美興秘書服務有限公司

Section 10 Individual #1：
  - First Name = Tai Man，Last Name = CHAN，Chinese Name = 陈大文
  - Identity Card Type = ID（HK Identity Card），ID Number = A123456（7）
  - Date of Birth = 19800101，Place of Birth = HKG，Nationality = HKG
  - Capacity = 085（Beneficial Owner）+ 034（Director）
  - Ownership = 60%
  - Email = taiman.chan@test.com
  - Residential Address Country = HKG

Section 11：Account Type = Client Account，Account English Name = AUTOTEST&RMCOMPANY46053
Section 12：Signing Arrangement = Any One
Section 13-16：BIB 相關字段使用對應的測試預設值
```

**實現要求：**
- 填入後自動調用 `expandAll()` 展開所有 Section
- 彈出成功提示（alert 或 toast 均可）
- 此功能為純前端操作，不調用任何接口，不保存到後端
- 按鈕位置：topbar 右側，草稿時間戳左側，始終可見（不受 Section 狀態影響）
- **新增開發時必須保留此功能，不得刪除或覆蓋**

#### 2.6 多值字段（[多值] 標注）的 + / 刪除 交互

```
點擊「+」按鈕：
  → 在當前記錄下方新增一條空白記錄卡片
  → 每條記錄卡片右上角顯示「刪除」按鈕（紅色）

點擊「刪除」：
  → 直接移除該條記錄（無需確認彈窗，因為草稿狀態）
  → 若只剩1條記錄，「刪除」按鈕置灰不可點
```

---

### 三、頁面三：審批詳情頁 — 補充交互

#### 3.1 Section 1-16 折疊可編輯表單（頁面三**可編輯**模式）

與頁面二的折疊邏輯和字段交互完全相同，頁面三的 Section **可編輯**（非只讀）：
- Section Header 可點擊展開，展開後所有字段保持可輸入 / 可選擇狀態，與頁面二完全一致
- RM 在審批過程中可直接修改字段內容
- 修改字段後，若涉及 Risk Scoring 相關字段，需重新點擊「Rerun Risk Scoring」按鈕重新計算分數
- 默認全部折疊，只顯示摘要預覽行（摘要規則與頁面二相同，參考 §2.1）

#### 3.2 Score Breakdown 展開面板

```
點擊「Score breakdown ▾」按鈕：
  → 面板從按鈕下方展開（max-height 動畫）
  → 按鈕箭頭變為 ▴
  → 面板內容：
```

| 行樣式 | 觸發條件 | 背景色 | 分數顏色 |
|--------|---------|--------|---------|
| 🔴 高風險行 | Score > 0 且維度為高風險 | `#fff5f5` | `#a32d2d` 紅色 |
| 🟡 中風險行 | Score > 0 且維度為中風險 | `#fffbf0` | `#854f0b` 橙色 |
| ⬜ 0分行 | Score = 0 | `#fff` | `#bbb` 灰色 |
| 🔵 合計行 | 固定在面板底部 | `#f0f6fc` | `#0c447c` 藍色，加粗 |

```
再次點擊「Score breakdown ▴」：
  → 面板收起
  → 按鈕箭頭恢復 ▾
```

**Initial Score 和 Latest Score 各有獨立的展開面板，互不影響。**

#### 3.3 High Risk Remarks 展開面板（Customer Risk 表格第14行）

Action 列有兩個按鈕：「Remarks ▾」和「Detail」（Result 為 True Hit 時才顯示 Detail）

**Remarks ▾ 展開面板交互：**

```
點擊「Remarks ▾」：
  → 在該行下方插入展開行（行內展開，不是彈窗）
  → 按鈕文字變為「Remarks ▴」
  → 展開面板包含以下選項（2列網格佈局）：
```

| 選項 | 狀態 | 說明 |
|------|------|------|
| AML Reference | ☑ disabled（不可更改） | 系統自動勾選，只讀 |
| AML Reference - Grey List | ☑ disabled（不可更改） | 系統自動勾選，只讀 |
| Closely Monitoring (User Input) | ☐ 可勾選 | RM 手工勾選 |
| Others | ☐ 可勾選 | RM 手工勾選 |

```
勾選「Others」：
  → 面板底部顯示 Remark For AML Referral 文本框
  → 輸入限制：CHAR(60)，右下角顯示字符計數「x/60」
  
取消勾選「Others」：
  → 文本框收起隱藏
  
再次點擊「Remarks ▴」：
  → 展開行收起
  → 已填寫的備注值保留
```

#### 3.4 Complex Structure — Radio 交互

- 當系統評估 Result = YES 時，Action 列顯示 Radio 按鈕組
- 默認選中「Yes」（與系統結果一致）
- RM 可手動改為「No」（表示人工認為不是複雜結構）
- 選擇「No」後 Assessment Score 對應分值清零，Final Risk Score 重新計算

#### 3.5 HI Company Secretary Address — Radio 交互

- Action 列顯示：`○ True Hit` / `○ False Hit`，默認選中「True Hit」
- 選擇「False Hit」：表示人工確認為誤命中，Assessment Score 對應分值清零

#### 3.6 命中名單 Detail 彈窗

觸發條件：Result 為 True Hit 的評估維度（High Risk Remarks / Sanctions Exposure / PEP / Special Interest Subject / Factiva / AML Black List）

**彈窗結構：**

| 元素 | 說明 |
|------|------|
| 標題 | 對應評估維度名稱（如「PEP(Individual)/SOC(Entity) (LYODS)」） |
| 關閉按鈕 | 右上角 ✕，點擊關閉彈窗 |
| 遮罩層 | 半透明黑色，點擊遮罩也可關閉彈窗 |
| 表格 | 橫向滾動，共 **22 列** |

**彈窗表格 22 列：**

| # | 列名 |
|---|------|
| 1 | NO |
| 2 | Original name |
| 3 | Names |
| 4 | Addresses |
| 5 | Origin |
| 6 | ID |
| 7 | Type |
| 8 | Additional Info |
| 9 | Dates Of Birth |
| 10 | Place Of Birth |
| 11 | Places Of Birth |
| 12 | Hyperlinks |
| 13 | Nationality |
| 14 | Request Date |
| 15 | ID Type |
| 16 | ID No. |
| 17 | English Name |
| 18 | Chinese Name |
| 19 | DoB |
| 20 | Case Reference |
| 21 | Result（5選1 Radio） |
| 22 | Action（Save 按鈕） |

**Result 列 5 個選項（Radio，每行獨立）：**
1. Non Material Hit
2. True Hit + Hong Kong PEP
3. True Hit + Non Hong Kong PEP
4. True Hit + International Organization PEP
5. False Hit

**彈窗操作流程：**
```
1. 查看命中詳情
2. 在 Result 列選擇對應結果
3. 可在 Action 列的文本框輸入備注（最多 4000 字）
4. 點擊「Save」保存該行的 Result 選擇
5. 所有行保存完畢後關閉彈窗
```

#### 3.7 Generate RSS 彈窗

觸發：右側面板底部「Generate RSS」按鈕

```
點擊「Generate RSS」：
  → 彈出確認框
  → 標題：「Generate RSS」
  → 內容：「確認為本案件生成 RSS 報告？」
  → 按鈕：「確認」（綠色）/ 「取消」（灰色）
  → 確認後：調用接口生成報告，完成後顯示「RSS 已生成，可在附件區下載」
```

#### 3.8 右側面板提交按鈕文字聯動

| 審批結果選擇 | 提交按鈕文字 | 按鈕顏色 |
|------------|-----------|---------|
| Recommend Approval | ▶ 提交 — Recommend Approval | 綠色 `#1d9e75` |
| Recommend Reject | ▶ 提交 — Recommend Reject | 紅色 `#a32d2d` |
| Return | ▶ 提交 — Return | 橙色 `#854f0b` |
| RFI | ▶ 提交 — Send RFI | 藍色 `#185fa5` |

#### 3.9 Timeline Tab — 時間軸數據格式

每條時間軸記錄包含：

| 字段 | 說明 |
|------|------|
| 角色徽標 | 藍紫色圓角標籤，如「OPS Admin: Admin」 |
| 操作箭頭 | → |
| 操作標籤 | 藍色標籤，如「Assign」/ 「Submit」/ 「No Objection」/ 「Return」 |
| 操作對象 | 如「→ RM Maker: eob_rm_maker」 |
| 狀態描述 | 如「Pending OPS Maker Claim → Pending OPS Maker Claim」 |
| 時間 | 格式 YYYY/M/DD HH:MM:SS |
| 耗時 | 如「(< 1 min)」/ 「(2 hrs 15 min)」 |

**時間軸圓點顏色：**
- 灰色圓點：系統自動操作（分配/認領）
- 紫色圓點：人工審批操作（Submit / No Objection / Reject / Return）
- 綠色圓點：流程完成節點

#### 3.10 Flow Chart Tab — 流程圖結構

SVG 繪製，觸發條件：點擊 Flow Chart tab 時調用 `drawFlowChart()` 函數動態注入。

**流程圖節點（按風險等級顯示不同路徑）：**

```
High Risk 路徑：
[RM Maker] → [RM Checker] → [RM Head / BU Head（可選）] → [AML Maker] → [AML Checker] → [完成]
                ↓ Escalation
           [RM Head / BU Head] → 返回 [RM Checker]

Low/Medium Risk 路徑：
[RM Maker] → [RM Checker] → [完成]
                ↓ Escalation（可選）
           [RM Head / BU Head] → 返回 [RM Checker]
```

**Return 路徑（任意節點均可）：**
```
任意節點 → Return → [RM Maker]（虛線箭頭表示）
```

#### 3.11 審批意見字符計數

- 右側面板審批意見 textarea 右下角實時顯示 `x / 16383`
- 超過 16383 字後禁止繼續輸入
- 字符計數格式：`0 / 16383` → `100 / 16383`

#### 3.12 附件上傳規則

| 規則 | 說明 |
|------|------|
| 最大數量 | 60 個 |
| 單個大小 | 最大 50MB |
| 支持格式 | PDF / ZIP / EML / PNG / JPG / DOC / DOCX / XLS / XLSX / CSV |
| 超出限制 | 顯示 toast 提示「文件大小超過 50MB 限制」/ 「最多上傳 60 個附件」 |
| 上傳成功 | 顯示文件名 + 大小 + 刪除按鈕 |


---

### 四、Risk Scoring 補充說明（V1.7）

#### 4.1 禁止風險（Terminate Risk）前端攔截規則

**觸發條件：** Risk Scoring 結果 `Case Content = Hit Terminate Risk`

**頁面三右側審批面板（BBApprovalActionCard）行為：**

| 元素 | 正常狀態 | Terminate Risk 狀態 |
|------|---------|-------------------|
| Recommend Approval | 可選 | **隱藏或 disabled**，不允許選擇 |
| Recommend Reject | 可選 | 可選（唯一可用的審批結論） |
| Return | 可選 | 可選 |
| RFI | 可選 | **隱藏或 disabled** |
| 提交按鈕 | 正常 | 只能提交 Reject 或 Return |

**頁面三頂部提示：**
- 在 Case Overview 卡片頂部顯示紅色警告橫條：
  `⛔ 本案件風險評估結果為「禁止（Terminate Risk）」，系統不允許通過開戶申請，只能拒絕。`
- 警告條樣式：`background:#fff5f5; border:1px solid #f09595; color:#a32d2d; padding:8px 16px`

**Customer Risk 表格中的禁止項：**
- 當某評估維度 Result = `Prohibited Business` 時：
  - Initial Score 列不顯示具體分數，顯示紅色標籤 `Prohibited`
  - Assessment Score 列同樣顯示 `Prohibited`，不可修改

---

#### 4.2 頁面三重跑 Risk Scoring

**觸發位置：** 右側審批操作面板底部按鈕組中的「Risk Scoring」按鈕

**執行流程：**

```
點擊「Risk Scoring」（頁面三）：
  1. 按鈕進入 loading 狀態（旋轉圖標 + 文字「重新評估中...」）
  2. 調用 POST /api/bb-cases/{caseNo}/risk-scoring
  3. 成功：
     - 頁面三數據整體刷新（重新 GET /api/bb-cases/{caseNo}）
     - Risk Assessment 卡片組更新（Initial/Final Rating + Score）
     - Customer Risk 詳細評估表格更新（各維度 Result/Score 重新渲染）
     - Review Indicator 更新
     - 進度條節點保持不變（不重置審批流程）
     - 顯示 message.success('風險評估已重新執行', 3)
  4. 失敗：
     - 按鈕恢復可點擊狀態
     - 顯示 message.error('重新評估失敗：{error message}')
```

**與頁面二 Risk Scoring 的區別：**

| 維度 | 頁面二（首次） | 頁面三（重跑） |
|------|-------------|-------------|
| 觸發位置 | 底部 Sticky footer | 右側面板按鈕組 |
| 前置動作 | 先 Save Draft | 無需 Save Draft（數據已提交） |
| 成功後跳轉 | 跳轉到頁面三 | 原地刷新頁面三 |
| 審批流程影響 | 創建審批訂單 | 不影響，只更新風險評分 |

---

#### 4.3 Name Screening 與 Risk Scoring 聯動邏輯

**觸發時機：** 點擊 Risk Scoring 按鈕時，系統**同步**執行 Name Screening 掃描

**Name Screening 掃描範圍：**

> ⚠️ **修正說明（V1.8）：** 地址字段的觸發規則比 V1.7 描述更複雜，詳見 § 4.4 完整字段清單。

| 層面 | 掃描字段 |
|------|---------|
| 公司層面 | Company English Name / Company Chinese Name / Trading Name（英+中）/ Company Establish Date / ID Number / Company Registered Address / Company Operation Address / Company Mailing Address / Principal Place of Operations / Old Company Name |
| Connected Parties 自然人 | First Name / Last Name / Chinese Name / Capacity / Identity Card Type / Others Identity Card Type / Identity Card Number / ID Issue Date / Gender / Date of Birth / Place of Birth / Nationality；Residential Address（僅修改了**國家/地區**時觸發）|
| Connected Parties 公司 | Registered Full Name in English / Registered Full Name in Chinese / ID Type / Others / ID Number；Registered Office Address / Principal Business Address（僅修改了**國家/地區**時觸發）|

**掃描結果影響 Customer Risk 表格：**

| 掃描命中類型 | 對應 Customer Risk 表格行 | Result 顯示 | Action |
|------------|------------------------|-----------|--------|
| Sanctions Exposure | 第15行：Sanctions Exposure (LYOD) | `True Hit` | Detail 按鈕彈窗 |
| PEP 命中 | 第16行：PEP/Individual/SOG/Entity (LYOD) | `Hit PEP` | Detail 按鈕彈窗 |
| Special Interest Subject | 第17行：Special Interest Subject (LYOD) | `True Hit` | Detail 按鈕彈窗 |
| Factiva | 第18行：Factiva (Internal List) | `True Hit` | Detail 按鈕彈窗 |
| AML Black List | 第20行：AML Black List | `Hit AML Black List` | Detail 按鈕彈窗 |
| 無命中 | 所有掃描行 | `No Hit` | 無 Action |

**`Pending Name Screening` 字段說明（Case Overview）：**
- `Y`：Name Screening 尚未執行或有字段修改後未重新掃描
- `N`：Name Screening 已執行且結果為最新

**觸發重新掃描的條件：**
- 任意 `NS` 標注字段的值被修改後，`Pending Name Screening` 自動變為 `Y`
- 再次點擊 Risk Scoring 執行後，變回 `N`

**掃描執行順序：**
```
點擊 Risk Scoring
  → Step 1: Name Screening（Accuity / Special List / AML Black List）
  → Step 2: Risk Scoring（CRA 打分，依賴 Name Screening 結果）
  → Step 3: 返回完整評估結果（Rating + Score + 各維度明細）
```

> ⚠️ **注意：** Name Screening 和 Risk Scoring 均為後端同步執行，
> 前端只需展示 loading 狀態直到接口返回，無需分兩步調用。

---

#### 4.4 Name Screening 完整送審字段清單（來源：PRD 字段定義）

> **掃描名單類型：** PEP / Special Interest Subject / Sanctions / SOC（以下簡稱「NS 四類名單」）
> **觸發時機：** 首次 Risk Scoring 提交時，或以下任一字段被修改後重新提交

---

##### A. 公司層面（Company Information）

| 字段名 | 字段長度 | 觸發規則 |
|--------|---------|---------|
| Company English Name | CHAR(60) | 修改後必須重新做 NS |
| Company Chinese Name | CHAR(60) | 修改後必須重新做 NS |
| Trading Name（English） | CHAR(60) | 修改後必須重新做 NS（最多 10 個）|
| Trading Name（Chinese） | CHAR(60) | 修改後必須重新做 NS（最多 10 個）|
| Company Establish Date | NUM(8) | 修改後必須重新做 NS |
| ID Number | CHAR(30) | 修改後必須重新做 NS |
| Company Registered Address | CHAR(210) | 修改後必須重新做 NS |
| Company Operation Address | CHAR(210) | 修改後必須重新做 NS |
| Company Mailing Address | CHAR(210) | 修改後必須重新做 NS |
| Principal Place of Operations | CHAR(3) | 修改後必須重新做 NS |
| Old Company Name（過去5年曾用名）| CHAR(90) | 修改後必須重新做 NS |

---

##### B. Connected Parties — 自然人（Individuals）

| 字段名 | 字段長度 | 觸發規則 |
|--------|---------|---------|
| First Name | CHAR(30) | 修改後必須重新做 NS |
| Last Name | CHAR(30) | 修改後必須重新做 NS |
| Chinese Name | CHAR(60) | 修改後必須重新做 NS |
| Capacity of Connected Party | CHAR(2) | 修改後必須重新做 NS |
| Identity Card Type | CHAR(3) | 修改後必須重新做 NS |
| Others Identity Card Type | CHAR(60) | 修改後必須重新做 NS |
| Identity Card Number | CHAR(30) | 修改後必須重新做 NS |
| ID Issue Date | NUM(8) | 修改後必須重新做 NS |
| Gender | CHAR(3) | 修改後必須重新做 NS |
| Date of Birth | NUM(8) | 修改後必須重新做 NS |
| Place of Birth | NUM(8) | 修改後必須重新做 NS |
| Nationality | CHAR(3) | 修改後必須重新做 NS |
| Residential Address | CHAR(210) | **僅修改了地址中的「國家/地區」時**才需要重新做 NS |

---

##### C. Connected Parties — 公司（Entities）

| 字段名 | 字段長度 | 觸發規則 |
|--------|---------|---------|
| Registered Full Name in English | CHAR(60) | 修改後必須重新做 NS |
| Registered Full Name in Chinese | CHAR(60) | 修改後必須重新做 NS |
| ID Type | CHAR(3) | 修改後必須重新做 NS |
| Others（其他證書號碼）| CHAR(30) | 修改後必須重新做 NS |
| ID Number | CHAR(30) | 修改後必須重新做 NS |
| Registered Office Address（Senior Officer / Director / Authorised Rep 適用）| CHAR(210) | **僅修改了地址中的「國家/地區」時**才需要重新做 NS |
| Principal Business Address（Senior Officer 適用）| CHAR(210) | **僅修改了地址中的「國家/地區」時**才需要重新做 NS |

---

> **注意：**
> - 地址字段的全量 NS 觸發（公司層面的三個地址）與自然人/公司的地址觸發規則不同：
>   - **公司的三個地址**（Registered / Operation / Mailing）：**任何修改**都觸發重新 NS
>   - **Connected Parties 的地址**（Residential / Registered Office / Principal Business）：**只有修改「國家/地區」**才觸發重新 NS
> - 首次提交時，所有上述字段無論是否修改，均全量送審一次
> - Pending Name Screening = `Y` 表示有 NS 字段被修改但尚未重新執行

---

## 附錄三：Risk Scoring 規則引擎設計（CRA 打分表）

> **來源：** CRA 打分表（與 EOB 相同版本）
> **觸發時機：** Name Screening 通過後系統自動執行
> **計分規則：** 各 Pillar 得分累加為總分；命中 Prohibited 項則直接判定禁止，不計入總分
> **風險等級判定：** 依據總分對應風險等級（Low / Medium / High / Prohibited）

---

### 一、完整 CRA 打分表

#### Pillar 1 — Customer（客戶）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Main nature of business/industry** | Low Risk Business | 0 |
| | Medium Risk Business | 1 |
| | High Risk Business | 20 |
| | Prohibited Business | ⛔ Prohibited |
| **Date of incorporation** | > 5 years | 0 |
| | 1–5 years | 0.25 |
| | Less than 1 year | 1 |
| **Entity type/Ownership structure** | Partnership | 0 |
| | Sole Prop | 0 |
| | Private Company | 0 |
| | Unincorporated Company | 0 |
| | Limited Company | 0 |
| | Trust | 10 |
| | Nominee | 10 |
| **Bearer Shares Issued** | Yes | 10 |
| | No | 0 |
| **Complex Structure** | Yes | 10 |
| | No | 0 |

#### Pillar 2 — Country（國家）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Country of incorporation/registration** | Low Risk Country | 0 |
| | Medium Risk Country | 0.65 |
| | High Risk Country | 13 |
| | Prohibited Country | ⛔ Prohibited |
| **Countries of operation/business** | Low Risk Country | 0 |
| | Medium Risk Country | 0.75 |
| | High Risk Country | 15 |
| | Prohibited Country | ⛔ Prohibited |
| **Countries of supplier (>20%)** | Low Risk Country | 0 |
| | Medium Risk Country | 0.5 |
| | High Risk Country | 5 |
| | Prohibited Country | ⛔ Prohibited |
| **Countries of buyer (>20%)** | Low Risk Country | 0 |
| | Medium Risk Country | 0.5 |
| | High Risk Country | 5 |
| | Prohibited Country | ⛔ Prohibited |

#### Pillar 3 — Product（產品）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Usage of high risk products** | Low Risk Product Only | 0 |
| | At Least One Medium Risk Product | 0.5 |
| | At Least One High Risk Product | 10 |

#### Pillar 4 — Tax（稅務）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Tax Residency** | One Tax Residency | 0 |
| | More Than One Tax Residency | 10 |

#### Pillar 5 — Channel（渠道）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Onboarding channel** | Solely E-Onboarding Channel | 6 |
| | Walk-in customer | 6 |
| | Approved Business Partner Referral | 0.6 |
| | Internal customer | 0 |
| | RM Referral | 0 |

#### Pillar 6 — Others（其他）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **STR or other AML concern** | No | 0 |
| | With AML Reference | 10 |
| | Closely Monitoring | 10 |
| | Others | 10 |
| **Sanctions Exposure** | No Hit | 0 |
| | False Hit | 0 |
| | True Hit | ⛔ Prohibited |
| **Foreign PEP / PEP with power to exercise / PEP with adverse news** | No Hit | 0 |
| | False Hit | 0 |
| | True Hit（但 PEP 不是受益人且無實質控制）| 0 |
| | True Hit | 10 |
| **Adverse News** | No Hit | 0 |
| | False Hit / Non-Material Hit | 0 |
| | True Hit | 10 |
| **Factiva List** | No Hit | 0 |
| | False Hit / Non-Material Hit | 0 |
| | True Hit | 10 |

#### Pillar 7 — Red Flag（紅旗指標）

| 評估維度 | 選項 | 分數 |
|---------|------|------|
| **Hit company secretary address** | No | 0 |
| | Yes | 1 |

---

### 二、規則引擎設計

#### 2.1 整體架構

```
輸入：開戶表單字段值（formData）+ Name Screening 結果
  ↓
Step 1: 禁止項檢查（任一命中直接返回 Prohibited）
  ↓
Step 2: 各 Pillar 逐項計分
  ↓
Step 3: 累加總分
  ↓
Step 4: 對照風險等級閾值判定 Low / Medium / High
  ↓
輸出：{ rating, score, breakdown[], isProhibited }
```

#### 2.2 禁止項清單（Prohibited Rules）

以下任一條件命中，立即判定 **Prohibited**，不計算總分，不允許開戶通過：

| # | 條件 | 來源 Pillar |
|---|------|------------|
| 1 | Main nature of business/industry = Prohibited Business | Pillar 1 |
| 2 | Country of incorporation/registration = Prohibited Country | Pillar 2 |
| 3 | Countries of operation/business = Prohibited Country | Pillar 2 |
| 4 | Countries of supplier (>20%) = Prohibited Country | Pillar 2 |
| 5 | Countries of buyer (>20%) = Prohibited Country | Pillar 2 |
| 6 | Sanctions Exposure = True Hit | Pillar 6 |

> ⚠️ Prohibited 項不計入總分；命中即終止評估，系統只允許 Reject，不允許 Recommend Approval。

#### 2.3 計分規則說明

**Step 1：禁止項優先攔截**
以下任一條件命中，立即終止評估，輸出 PROHIBITED，不進行後續計分：
- 行業 = Prohibited Business
- 注冊國 / 運營國 / 供應商國 / 買方國（>20%）= Prohibited Country
- Sanctions Exposure = True Hit

**Step 2：各 Pillar 逐項取分**

| Pillar | 計分規則 |
|--------|---------|
| Pillar 1 Customer | 按選項直接查表取分；Entity Type 只取一個值（最高 10 分）；Date of incorporation 按成立年限分段取分（<1年=1，1-5年=0.25，>5年=0）|
| Pillar 2 Country | 多選國家字段（Countries of operation / supplier / buyer）取所有選中國家中**分值最高的一個**，不累加 |
| Pillar 3 Product | 按 Usage of products 枚舉值直接取分 |
| Pillar 4 Tax | 稅務居民國數量 > 1 則得 10 分，否則 0 分 |
| Pillar 5 Channel | 按開戶渠道直接查表取分 |
| Pillar 6 Others | STR/AML Concern：任一選項勾選均計 10 分，不累加；Sanctions 已被 Step 1 攔截不重複計分；PEP True Hit 但無實質控制 = 0 分，其餘 True Hit = 10 分 |
| Pillar 7 Red Flag | 命中秘書公司地址 = 1 分，否則 0 分 |

**Step 3：累加總分**
將所有 Pillar 得分相加，得到 Total Score。

**Step 4：對照閾值判定風險等級**

| 總分範圍 | 風險等級 |
|---------|---------|
| < 1 | 🟢 LOW |
| ≥ 1 且 < 10 | 🟡 MEDIUM |
| ≥ 10 | 🔴 HIGH |

#### 2.3.1 規則引擎示意圖

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Risk Scoring 規則引擎                             │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
              ┌───────────────────────────┐
              │    輸入：開戶表單字段值     │
              │  + Name Screening 結果    │
              └───────────────────────────┘
                              │
                              ▼
         ┌────────────────────────────────────────┐
         │         Step 1：禁止項檢查              │
         │                                        │
         │  • 行業 = Prohibited Business?         │
         │  • 注冊/運營/供應商/買方國 = Prohibited? │
         │  • Sanctions Exposure = True Hit?      │
         └────────────────────────────────────────┘
                    │                  │
               任一命中              全部通過
                    │                  │
                    ▼                  ▼
         ┌──────────────────┐   ┌──────────────────────────────────────────┐
         │  ⛔ PROHIBITED   │   │         Step 2：各 Pillar 逐項計分        │
         │  不允許通過開戶   │   │                                          │
         │  只能 Reject     │   │  P1 Customer  →  查表取分（最高 50）      │
         └──────────────────┘   │  P2 Country   →  多國取 MAX（最高 38）   │
                                │  P3 Product   →  查表取分（最高 10）      │
                                │  P4 Tax       →  >1個稅居 = 10（最高 10）│
                                │  P5 Channel   →  查表取分（最高  6）      │
                                │  P6 Others    →  各項命中取分（最高 40）  │
                                │  P7 Red Flag  →  秘書地址命中 = 1         │
                                └──────────────────────────────────────────┘
                                                   │
                                                   ▼
                                ┌──────────────────────────────┐
                                │     Step 3：累加 Total Score  │
                                │  Score = P1+P2+P3+P4+P5+P6+P7│
                                └──────────────────────────────┘
                                                   │
                                                   ▼
                                ┌──────────────────────────────┐
                                │   Step 4：對照閾值判定等級    │
                                └──────────────────────────────┘
                                         │
                  ┌──────────────────────┼──────────────────────┐
                  ▼                      ▼                      ▼
          ┌──────────────┐      ┌──────────────┐      ┌──────────────┐
          │  Score < 1   │      │ 1 ≤ Score<10 │      │  Score ≥ 10  │
          │  🟢 LOW      │      │  🟡 MEDIUM   │      │  🔴 HIGH     │
          │              │      │              │      │              │
          │ 2節點審批流  │      │ 2節點審批流  │      │ 5節點審批流  │
          │ RM Maker     │      │ RM Maker     │      │ RM Maker     │
          │  → Checker   │      │  → Checker   │      │  → Checker   │
          └──────────────┘      └──────────────┘      │  → RM Head   │
                                                       │  → AML Maker │
                                                       │  → AML Check │
                                                       └──────────────┘
```

> **注意：** 各 Pillar 最高分為理論值，實際分數視具體字段填寫情況而定。

#### 2.4 多國評分規則

**⚠️ 重要：** `Countries of supplier` 和 `Countries of buyer` 的計分邏輯**不是簡單取 MAX**，而是按各國百分比累計計算。`Countries of operation/business` 同樣適用此邏輯。

---

##### 2.4.1 Countries of supplier / Countries of buyer 計分規則

計分前先計算：
- **高風險國家百分比累計** = 所有 High Risk 國家的百分比之和
- **中風險國家百分比累計** = 所有 Medium Risk 國家的百分比之和
- **中風險 + 高風險百分比累計** = 兩者之和

然後按以下優先順序判定風險等級（從規則 1 開始逐條判斷，命中即停止）：

| 檢查順序 | 高風險國家百分比累計 | 中風險國家百分比累計 | 中風險+高風險累計 | 判定結果 |
|:-------:|:-----------------:|:-----------------:|:---------------:|:-------:|
| 規則 1 | **≥ 20%** | 不看 | 不看 | 🔴 **高風險** → 計 5 分（Supplier）/ 5 分（Buyer）|
| 規則 2 | **10% < 且 < 20%** | 不看 | 不看 | 🟡 **中風險** → 計 0.5 分 |
| 規則 3 | **≤ 10%** | **≥ 20%** | 不看 | 🟡 **中風險** → 計 0.5 分 |
| 規則 4 | **≤ 10%** | **10% < 且 < 20%** | **≥ 20%** | 🟡 **中風險** → 計 0.5 分 |
| 規則 5 | **≤ 10%** | **10% < 且 < 20%** | **< 20%** | 🟢 **低風險** → 計 0 分 |
| （其他） | ≤ 10% | < 10% | 不限 | 🟢 **低風險** → 計 0 分 |

> `Countries of buyer` 使用與 `Countries of supplier` 完全相同的判定邏輯和分值。

---

##### 2.4.2 計算示例

**示例 1 → 規則 1（高風險）**

| 國家 | Risk | 百分比 |
|------|------|--------|
| JAPAN | Low | 10% |
| INDIA | Medium | 20% |
| VIET NAM | Medium | 30% |
| IRAQ | High | 15% |
| SOUTH SUDAN | High | 7% |

高風險累計 = 15% + 7% = **22% ≥ 20%** → 命中規則 1 → **高風險，得 5 分**

---

**示例 2 → 規則 2（中風險）**

| 國家 | Risk | 百分比 |
|------|------|--------|
| JAPAN | Low | 10% |
| INDIA | Medium | 20% |
| VIET NAM | Medium | 30% |
| IRAQ | High | 15% |
| SOUTH SUDAN | High | 3% |

高風險累計 = 15% + 3% = **18%，10% < 18% < 20%** → 命中規則 2 → **中風險，得 0.5 分**

---

**示例 3 → 規則 3（中風險）**

| 國家 | Risk | 百分比 |
|------|------|--------|
| JAPAN | Low | 10% |
| INDIA | Medium | 20% |
| VIET NAM | Medium | 30% |
| IRAQ | High | 5% |
| SOUTH SUDAN | High | 3% |

高風險累計 = 5% + 3% = **8% ≤ 10%**
中風險累計 = 20% + 30% = **50% ≥ 20%** → 命中規則 3 → **中風險，得 0.5 分**

---

**示例 4 → 規則 4（中風險）**

| 國家 | Risk | 百分比 |
|------|------|--------|
| JAPAN | Low | 10% |
| INDIA | Medium | 8% |
| VIET NAM | Medium | 5% |
| IRAQ | High | 5% |
| SOUTH SUDAN | High | 3% |

高風險累計 = **8% ≤ 10%**
中風險累計 = 8% + 5% = **13%，10% < 13% < 20%**
中風險 + 高風險 = 13% + 8% = **21% ≥ 20%** → 命中規則 4 → **中風險，得 0.5 分**

---

**示例 5 → 規則 5（低風險）**

| 國家 | Risk | 百分比 |
|------|------|--------|
| JAPAN | Low | 10% |
| INDIA | Medium | 8% |
| VIET NAM | Medium | 5% |
| IRAQ | High | 2% |
| SOUTH SUDAN | High | 3% |

高風險累計 = **5% ≤ 10%**
中風險累計 = 8% + 5% = **13%，10% < 13% < 20%**
中風險 + 高風險 = 13% + 5% = **18% < 20%** → 命中規則 5 → **低風險，得 0 分**

---

##### 2.4.3 Countries of operation/business 計分規則

`Countries of operation/business` 使用**相同的百分比累計邏輯**，但對應的分值不同：

| 判定結果 | 分值 |
|---------|------|
| 高風險 | **15 分** |
| 中風險 | **0.75 分** |
| 低風險 | **0 分** |

---

##### 2.4.4 規則引擎實現邏輯

```
function calcCountryRiskLevel(countries):
  highPct  = sum(c.percentage for c in countries if c.risk == 'High')
  medPct   = sum(c.percentage for c in countries if c.risk == 'Medium')
  combPct  = highPct + medPct

  if any(c.risk == 'Prohibited' for c in countries):
    return 'Prohibited'               // 禁止項優先

  if highPct >= 20:   return 'High'   // 規則 1
  if highPct > 10:    return 'Medium' // 規則 2
  if medPct >= 20:    return 'Medium' // 規則 3
  if medPct > 10 and combPct >= 20:
                      return 'Medium' // 規則 4
                      return 'Low'    // 規則 5（其餘）

function getScore(riskLevel, dimension):
  scoreTable = {
    'supplier': {'High': 5,  'Medium': 0.5, 'Low': 0},
    'buyer':    {'High': 5,  'Medium': 0.5, 'Low': 0},
    'operation':{'High': 15, 'Medium': 0.75,'Low': 0},
  }
  return scoreTable[dimension][riskLevel]
```

#### 2.5 PEP 特殊判定規則

| Result | 分數 | 說明 |
|--------|------|------|
| No Hit | 0 | 無命中 |
| False Hit | 0 | 人工確認為誤判 |
| True Hit（PEP 不是受益人且無實質控制）| 0 | 特殊豁免情況 |
| True Hit | 10 | 命中且有實質影響 |

#### 2.6 STR / AML Concern 計分規則

```
以下任一選項勾選，均計 10 分（不累加，取一次）：
- With AML Reference
- Closely Monitoring
- Others

即：該維度最高得分為 10 分，無論勾選幾項
```

#### 2.7 Score Breakdown 展示對應

| Breakdown 行顯示顏色 | 觸發條件 |
|--------------------|---------|
| 🔴 紅底（高風險） | score > 0 且分值較高（通常 ≥ 5）|
| 🟡 黃底（中風險） | score > 0 且分值較低（通常 < 5）|
| ⬜ 白底灰字（0分） | score = 0 |
| 🔵 藍底（合計） | Total 行，固定在面板底部 |

---

### 三、各 Pillar 最高分匯總

| Pillar | 評估維度數 | 最高可得分 | 備注 |
|--------|----------|----------|------|
| Pillar 1 Customer | 5 | 50 | Trust+Nominee 最高各10，但Entity Type只取一個 |
| Pillar 2 Country | 4 | 38 | 各維度最高分相加（13+15+5+5）|
| Pillar 3 Product | 1 | 10 | |
| Pillar 4 Tax | 1 | 10 | |
| Pillar 5 Channel | 1 | 6 | |
| Pillar 6 Others | 5 | 40 | AML(10)+PEP(10)+Adverse(10)+Factiva(10)+Red Flag(1) 不含 Sanctions（Prohibited）|
| Pillar 7 Red Flag | 1 | 1 | |
| **合計** | **18** | **理論最高約 ~155** | 實際分布視具體組合而定 |

---

### 四、風險等級閾值（Risk Rating Range）

| 風險等級 | 總分範圍 | 說明 |
|---------|---------|------|
| 🟢 LOW | 總分 < 1 | 低風險，進入 Low/Medium Risk 審批流程（RM Maker → RM Checker）|
| 🟡 MEDIUM | 總分 ≥ 1 且 < 10 | 中風險，進入 Low/Medium Risk 審批流程（RM Maker → RM Checker）|
| 🔴 HIGH | 總分 ≥ 10 | 高風險，進入 High Risk 審批流程（RM Maker → RM Checker → RM Head → AML Maker → AML Checker）|
| ⛔ PROHIBITED | 命中任一禁止項 | 直接判定禁止，不計算總分，只允許 Reject |

> **注意：** LOW 和 MEDIUM 使用同一審批路徑（2節點），HIGH 使用 5節點完整審批路徑。


---

## 附錄四：各名單 Detail 頁面字段及 Mock 數據（來源：PRD 截圖）

> **說明：** 以下各名單的字段結構及 Result 選項均來自 PRD 截圖，Mock 數據取自截圖中的真實示例數據。

---

### 一、PEP(Individual)/SOC(Entity) (LYODS)

**來源系統：** LYODS 名單管理系統（已替換舊 LYODS 名單管理系統）

#### 一級頁面字段

| 字段 | 屬性 | Result 枚舉值 |
|------|------|-------------|
| Result | 顯示 | `Hit PEP` / `No Hit` |
| Initial Score | 顯示 | Risk Scoring 分數 |
| Assessment Score | 顯示 | 調整後分數 |
| Action | 按鈕 | Detail 鏈接 |

#### 二級 Detail 頁面字段

| # | 字段名 | 說明 |
|---|--------|------|
| 1 | NO | 序號 |
| 2 | Original Name | 原始名稱 |
| 3 | Names | 所有別名（分號分隔） |
| 4 | Addresses | 地址 |
| 5 | Origin | 來源（如 REF、FACTIVA）|
| 6 | ID | 名單系統 ID |
| 7 | Type | `I`（Individual）/ `C`（Corporate）|
| 8 | Additional Info | 詳細信息（含 Title、Gender、Create Date 等）|
| 9 | Dates Of Birth | 出生日期 |
| 10 | Places Of Birth | 出生地 |
| 11 | Hyperlinks | 參考鏈接 |
| 12 | Nationality | 國籍 |
| 13 | Request Date | 查詢日期 |
| 14 | ID Type | 證件類型 |
| 15 | ID No. | 證件號碼 |
| 16 | English Name | 英文名 |
| 17 | Chinese Name | 中文名 |
| 18 | DoB | 出生日期 |
| 19 | Case Reference | 案件參考號 |
| 20 | **Result**（Discounting） | Radio 5選1（見下）|
| 21 | **Action** | Save 按鈕 |

**Result 選項（Radio，5選1）：**
1. Non-Material Hit
2. True Hit + Hong Kong PEP
3. True Hit + Non Hong Kong PEP
4. True Hit + International Organization PEP
5. False Hit ← 選中時顯示 Remark 文本框

**Batch Discounting 彈窗選項（Checkbox 多選）：**
同上 5 項 + Remark 文本框

#### Mock 數據（來自截圖）

```json
{
  "no": 1,
  "originalName": "KONG PO",
  "names": "PO, KONG;3184 3068;P'U UI;PO KONG;PU, CHIANG;PU, JIANG;浦江",
  "addresses": "HENG YA CHUEN, NOT PROVIDED, HONG KONG",
  "origin": "REF",
  "id": "225064295",
  "type": "I",
  "additionalInfo": "List ID: 1020 / Create Date: 09/19/2017 12:58:45 / Last Update Date: 11/18/2021 18:33:43 / Title: SANCTIONED ENTITY, FORMER MEMBER OF CHINESE PEOPLE'S POLITICAL CONSULTATIVE CONFERENCE (MARCH 03, 1998 - MARCH 02, 2018) / Gender: MALE / OriginalID: 225064295",
  "datesOfBirth": "09/01/1964",
  "placesOfBirth": "",
  "hyperlinks": "https://acuity.worldcompliance.com/signin.aspx?ent=2836cca-d5d4-4886-9be0-9b9-6d76210f",
  "nationality": "",
  "requestDate": "",
  "idType": "",
  "idNo": "",
  "englishName": "",
  "chineseName": "",
  "dob": "",
  "caseReference": "",
  "result": "Non-Material Hit",
  "remark": ""
}
```

---

### 二、Sanctions Exposure (LYODS)

**來源系統：** LYODS 名單管理系統（已替換舊 LYODS 名單管理系統）

#### 一級頁面字段

| 字段 | 屬性 | Result 枚舉值 |
|------|------|-------------|
| Result | 顯示 | `Hit Sanctions Exposure` / `No Hit` |
| Initial Score | 顯示 | Risk Scoring 分數（命中為 Prohibited）|
| Assessment Score | 顯示 | 調整後分數 |
| Action | 按鈕 | Detail 鏈接 |

#### 二級 Detail 頁面字段

與 PEP(Individual)/SOC(Entity) 結構相同（同為 LYODS 名單），共 21 列。

**Result 選項（Radio，2選1）：**
1. True Hit ← 命中為 Prohibited，不允許繼續提交
2. False Hit ← 選中時顯示 Remark 文本框（請輸入內容）

#### Mock 數據（來自截圖 img6）

```json
{
  "no": 1,
  "originalName": "IVAN PETROV SOKOLOV",
  "names": "Ivan Petrov Sokolov",
  "addresses": "ul. Lenina 45, Minsk, Belarus",
  "origin": "FACTIVA",
  "id": "BY-002",
  "type": "I",
  "additionalInfo": "Sanctions / OFAC SDN List",
  "datesOfBirth": "1975-07-22",
  "placesOfBirth": "Minsk",
  "hyperlinks": "https://www.mep.gob.c./sites/defa...",
  "nationality": "Belarusian",
  "requestDate": "2024-02-14",
  "idType": "",
  "idNo": "",
  "englishName": "",
  "chineseName": "",
  "dob": "",
  "caseReference": "CR-2024-002",
  "result": "False Hit",
  "remark": ""
}
```

---

### 三、Special Interest Subject (LYODS)

**來源系統：** LYODS 名單管理系統（已替換舊 LYODS 名單管理系統）

#### 一級頁面字段

| 字段 | 屬性 | Result 枚舉值 |
|------|------|-------------|
| Result | 顯示 | `Hit Special Interest Subject` / `No Hit` |
| Initial Score | 顯示 | Risk Scoring 分數 |
| Assessment Score | 顯示 | 調整後分數 |
| Action | 按鈕 | Detail 鏈接 |

#### 二級 Detail 頁面字段

與 PEP(Individual)/SOC(Entity) 結構相同（同為 LYODS 名單），共 21 列。

**Result 選項（Radio，3選1）：**
1. True Hit
2. Non Material Hit
3. False Hit ← 選中時顯示 Remark 文本框（請輸入內容）

#### Mock 數據（來自截圖 img7、img8）

```json
{
  "no": 1,
  "originalName": "Malladi Drugs & Pharmaceuticals",
  "names": "Malladi Drugs & Pharmaceuticals;Malladi Drugs & Pharmaceuticals Limited;Malladi Drugs & Pharmaceuticals Ltd.",
  "addresses": "9 G.S.T Road, Saint Thomas Mount, Chennai, Tamil Nadu, 600 016, India",
  "origin": "FACTIVA_AME",
  "id": "FE11393775",
  "type": "C",
  "additionalInfo": "Categories:ENVIRONMENT/PRODUCTI Date of Registration:1980/08/21 Description1:Special Interest Entity (SIE) Description2:Regulatory,Environment/Production Description3:Product/Service Issues,Regulatory Issues DUNS Number:650076243 LastUpdateDate:2023-02-06",
  "datesOfBirth": "",
  "placesOfBirth": "",
  "hyperlinks": "http://www.malladi.co.in",
  "nationality": "",
  "requestDate": "",
  "result": "Non Material Hit",
  "remark": ""
}
```

---

### 四、Factiva (Internal List)

**來源系統：** Factiva 內部名單

#### 一級頁面字段

| 字段 | 屬性 | Result 枚舉值 |
|------|------|-------------|
| Result | 顯示 | `Hit Factiva` / `No Hit` |
| Initial Score | 顯示 | Risk Scoring 分數 |
| Assessment Score | 顯示 | 調整後分數 |
| Action | 按鈕 | Detail 鏈接 |

#### 二級 Detail 頁面字段（與 LYODS 不同，為精簡結構）

| # | 字段名 | 說明 |
|---|--------|------|
| 1 | NO | 序號 |
| 2 | Original Name | 原始名稱 |
| 3 | Origin | 來源：`Internal List` |
| 4 | Type | 名單類型：`Factiva` |
| 5 | Case Reference | 名單系統 ID |
| 6 | Customer Type | `Individual / Corporate` |
| 7 | English Name | 英文名 |
| 8 | Chinese Name | 中文名 |
| 9 | DOB / Date of incorporation | 出生日期 / 成立日期 |
| 10 | BR / CR number | BR / CR 號碼 |
| 11 | Gender | 性別 |
| 12 | Occupation | 職位 |
| 13 | Hyperlinks | 新聞鏈接 |
| 14 | **Result**（Discounting） | Radio 3選1（見下）|

**Result 選項（Radio，3選1）：**
1. True Hit
2. Non-Material Hit
3. False Hit
   - └─ English Name Not Match（Checkbox）
   - └─ Chinese Name Not Match（Checkbox）
   - └─ Gender Not Match（Checkbox）
   - └─ Date of Birth/Age Not Match（Checkbox）
   - └─ Other（Checkbox）→ 勾選時 Remark 必填

#### Mock 數據（來自截圖 img17）

```json
{
  "no": 1,
  "originalName": "KONG PO",
  "origin": "Internal List",
  "type": "Factiva",
  "caseReference": "12121212442",
  "customerType": "Individual / Corporate",
  "englishName": "Test Name",
  "chineseName": "測試名稱",
  "dobOrEstDate": "2024/9/12",
  "brCrNumber": "31212111",
  "gender": "男",
  "occupation": "董事",
  "hyperlinks": "https://acuity.worldcompliance.com/signin.aspx?ent=283fdcca-cfc4-4866-99e8-9b96d76210ff",
  "result": "False Hit",
  "falseHitSubOptions": ["English Name Not Match"],
  "remark": ""
}
```

---

### 五、Special List

**來源系統：** 名單管理系統（BP/BO 破產名單）

#### 二級 Detail 頁面字段（結構完全不同）

| # | 字段名 | 說明 |
|---|--------|------|
| 1 | NO | 序號 |
| 2 | Original Name | 原始名稱 |
| 3 | CW_NUM | 名單編號 |
| 4 | CW_TYPE | 名單類型（WP / BP / BO 等）|
| 5 | SBJ_NAME | 英文主體名稱 |
| 6 | SBJ_CHINAME | 中文主體名稱 |
| 7 | SBJ_ID | 主體 ID |
| 8 | REMARK_CODE | 備注代碼 |
| 9 | ADDRESS_A | 地址 A |
| 10 | ADDRESS_B | 地址 B |
| 11 | PLTF_NAME1 | 平台名稱 |
| 12 | **Result**（Discounting） | Radio 9選1（見下）|

**Result 選項（Radio，9選1）：**
1. True Hit_Bankruptcy Order-Fail
2. True Hit_Bankruptcy Petition-Fail
3. True Hit With LoA-Pass
4. False Hit_ID not match-Pass
5. False Hit_Address not match (refer ATT) - Pass
6. False Hit_CName not match
7. False Hit_DisCharge Bankruptcy Order
8. False Hit_Petition Dismissal
9. False Hit_Other ← 選中時顯示 Remark 文本框

#### Mock 數據（來自截圖 img11）

```json
[
  {
    "no": 1,
    "originalName": "真愛4號有限公司",
    "cwNum": "0",
    "cwType": "WP",
    "sbjName": "",
    "sbjChiname": "真愛4號有限公司",
    "sbjId": "",
    "remarkCode": "WP",
    "addressA": "",
    "addressB": "",
    "pltfName1": "",
    "result": "False Hit_ID not match-Pass",
    "remark": ""
  },
  {
    "no": 2,
    "originalName": "ZA02 Limited",
    "cwNum": "0",
    "cwType": "WP",
    "sbjName": "ZA02 LTD",
    "sbjChiname": "",
    "sbjId": "",
    "remarkCode": "WP",
    "addressA": "",
    "addressB": "",
    "pltfName1": "",
    "result": "True Hit_Bankruptcy Petition-Fail",
    "remark": ""
  }
]
```

---

### 六、Grey List（AML 灰名單）

**來源系統：** AML 內部灰名單

#### 二級 Detail 頁面字段

| # | 字段名 | 說明 |
|---|--------|------|
| 1 | NO | 序號 |
| 2 | Original Name | 原始名稱 |
| 3 | Names | 別名列表 |
| 4 | Addresses | 地址 |
| 5 | Origin | 來源 |
| 6 | Original ID | 原始 ID |
| 7 | Type | `AML灰名單` |
| 8 | Additional Info | 附加信息 |
| 9 | Dates Of Birth | 出生日期 |
| 10 | Places Of Birth | 出生地 |
| 11 | Hyperlinks | 參考鏈接 |
| 12 | Nationality | 國籍 |
| 13 | Request Date | 查詢日期 |
| 14 | ID Type | 證件類型 |
| 15 | ID No. | 證件號碼（紅色高亮標注命中字段）|
| 16 | English Name | 英文名 |
| 17 | Chinese Name | 中文名 |
| 18 | DoB | 出生日期 |
| 19 | Case Reference | 案件參考號 |
| 20 | **Result**（Discounting） | Radio 5選1（見下）|
| 21 | **Action** | Save 按鈕 |

**Result 選項（Radio，5選1）：**
1. True Hit
2. Non Material Hit
3. True Hit - Name+DOB match; confirmed same identity using different ID type by ID photo
4. False Hit - Name+DOB match but it is a different customer by ID photo
5. False Hit ← 選中時顯示 Remark 文本框

#### Mock 數據（來自截圖 img13、img14）

```json
{
  "no": 1,
  "originalName": "CHI WANG POON",
  "names": "",
  "addresses": "",
  "origin": "",
  "originalId": "",
  "type": "AML灰名單",
  "additionalInfo": "",
  "datesOfBirth": "2002-12-16",
  "placesOfBirth": "",
  "hyperlinks": "",
  "nationality": "",
  "requestDate": "2023-08-15 13:56:34",
  "idType": "HKID",
  "idNo": "Y9114023",
  "englishName": "POON CHI WANG",
  "chineseName": "",
  "dob": "2002-12-16",
  "caseReference": "12312",
  "result": "True Hit",
  "remark": ""
}
```

---

### 七、各名單 Detail 字段對照總覽

| 字段 | PEP/SOC | Sanctions | Special Interest | Factiva | Special List | Grey List |
|------|:-------:|:---------:|:----------------:|:-------:|:------------:|:---------:|
| NO | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Original Name | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Names/CW_NUM | ✅ Names | ✅ Names | ✅ Names | — | CW_NUM | ✅ Names |
| Addresses | ✅ | ✅ | ✅ | — | ADDRESS_A/B | ✅ |
| Origin | ✅ | ✅ | ✅ | ✅ | — | ✅ |
| ID/CW_TYPE | ✅ ID | ✅ ID | ✅ ID | — | CW_TYPE | Original ID |
| Type | ✅ | ✅ | ✅ | ✅ Type=Factiva | CW_TYPE | ✅ AML灰名單 |
| Additional Info | ✅ | ✅ | ✅ | — | REMARK_CODE | ✅ |
| Dates Of Birth | ✅ | ✅ | ✅ | DOB/EstDate | — | ✅ |
| Places Of Birth | ✅ | ✅ | ✅ | — | — | ✅ |
| Hyperlinks | ✅ | ✅ | ✅ | ✅ | — | ✅ |
| Nationality | ✅ | ✅ | ✅ | — | — | ✅ |
| Request Date | ✅ | ✅ | ✅ | — | — | ✅ |
| ID Type | ✅ | ✅ | ✅ | — | — | ✅ |
| ID No. | ✅ | ✅ | ✅ | BR/CR number | SBJ_ID | ✅ |
| English Name | ✅ | ✅ | ✅ | ✅ | SBJ_NAME | ✅ |
| Chinese Name | ✅ | ✅ | ✅ | ✅ | SBJ_CHINAME | ✅ |
| DoB | ✅ | ✅ | ✅ | — | — | ✅ |
| Case Reference | ✅ | ✅ | ✅ | ✅ | — | ✅ |
| Gender | — | — | — | ✅ | — | — |
| Occupation | — | — | — | ✅ | — | — |
| Customer Type | — | — | — | ✅ | — | — |
| PLTF_NAME1 | — | — | — | — | ✅ | — |
| **Result 選項數** | 5 | 2 | 3 | 3+子選項 | 9 | 5 |
| **Remark** | False Hit時 | False Hit時 | False Hit時 | False Hit-Other時 | False Hit_Other時 | False Hit時 |

