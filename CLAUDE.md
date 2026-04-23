# CLAUDE.md — ZA Bank Business Banking CIF Demo 项目指南

> **适用对象：** Claude Code / Claude API（`claude-sonnet-4-20250514`）  
> **项目路径：** `C:\Users\zekai.wang\zabank-cif-demo\`  
> **规格文件路径：** `C:\Users\zekai.wang\zabank-cif-demo\prd\V2版本\`  
> **最后更新：** 2026-04-23

---

## 目录

1. [项目概览](#1-项目概览)
2. [技术栈与目录结构](#2-技术栈与目录结构)
3. [规格文件权威性排序](#3-规格文件权威性排序)
4. [开发工作流程（强制）](#4-开发工作流程强制)
5. [UI 实现规范（强制）](#5-ui-实现规范强制)
6. [三页面功能说明](#6-三页面功能说明)
7. [后端业务逻辑规范](#7-后端业务逻辑规范)
8. [数据库规范](#8-数据库规范)
9. [审批流程状态机](#9-审批流程状态机)
10. [前端组件架构（页面三）](#10-前端组件架构页面三)
11. [已知陷阱与解决方案](#11-已知陷阱与解决方案)
12. [测试要求](#12-测试要求)
13. [完整开发阶段（Phase 0–8）](#13-完整开发阶段phase-08)
14. [错误处理策略](#14-错误处理策略)
15. [字段关键规则速查](#15-字段关键规则速查)
16. [进度状态](#16-进度状态)

---

## 1. 项目概览

**项目名称：** `zabank-cif-demo`  
**业务背景：** ZA Bank（众安银行）Business Banking（对公）账户开户系统 Demo，面向产品评审和开发参考。

### 核心功能三页面

| 页面 | 路由 | 功能 | 对应 HTML 原型 |
|------|------|------|---------------|
| 页面一 | `/bb-cases` | 开户案件列表、搜索、筛选 | `bb_page1_list.html` |
| 页面二 | `/bb-cases/new` 或 `/bb-cases/:id/edit` | 开户资料录入（Section 0–16） | `bb_page2_data_entry.html` |
| 页面三 | `/bb-cases/:id/detail` | Risk Scoring 后审批详情 | `bb_page3_post_risk.html` |

### 外部系统交互

| 系统 | 交互说明 |
|------|---------|
| TU 接口 | Section 0 查询香港公司注册信息，自动反显 |
| BIB 系统 | Section 13–16 企业网银开户，字段映射后传值 |
| LYODS 名单 | Name Screening，命中后显示 Detail 弹窗 |

---

## 2. 技术栈与目录结构

### 技术栈

```
前端：  React 18 + TypeScript + Ant Design 5.x
后端：  Node.js + Express（REST API）
数据库：MySQL 8.x
测试：  Jest（后端）+ React Testing Library（前端）
```

### 推荐目录结构

```
zabank-cif-demo/
├── frontend/
│   └── src/
│       ├── styles/
│       │   ├── variables.css          # 所有 CSS 自定义属性（色值）
│       │   ├── bb-page1.module.css    # 页面一样式（class 名与 HTML 原型一致）
│       │   ├── bb-page2.module.css    # 页面二样式
│       │   ├── bb-page3.module.css    # 页面三样式
│       │   └── bb-common.module.css   # 公共样式（.sec/.field/.btn/.badge 等）
│       ├── types/
│       │   └── index.ts               # 所有业务实体 TypeScript 类型定义
│       ├── constants/
│       │   └── fields.ts              # 枚举值、字段名、接口路径常量
│       ├── api/                       # 接口调用函数
│       ├── hooks/
│       │   ├── useBBCase.ts           # 开户数据管理
│       │   └── useDraftAutoSave.ts    # 30秒自动保存
│       ├── components/
│       │   ├── common/                # 地址7字段、多值增删、pill多选等通用组件
│       │   └── bbCases/
│       │       ├── BBApprovalProgress.tsx     # 审批进度条
│       │       ├── BBRiskBadge.tsx            # 风险等级徽章
│       │       ├── BBCompanyIdentityBlock.tsx  # 公司身份高亮块（卡片顶部）
│       │       └── BBApprovalActionCard.tsx    # 右侧审批操作面板
│       └── pages/
│           └── bbCases/
│               ├── list.tsx           # 页面一
│               ├── form.tsx           # 页面二
│               └── detail.tsx         # 页面三
├── backend/
│   └── src/
│       ├── services/
│       │   ├── craRuleEngine.ts       # CRA 规则引擎
│       │   ├── seqValidator.ts        # SEQ 制裁问卷校验
│       │   ├── nameScreeningService.ts # Name Screening 字段组装
│       │   ├── approvalStateMachine.ts # 审批流程状态机
│       │   └── bibMappingService.ts   # BIB 接口传值映射
│       ├── routes/
│       │   └── bbOpening.ts           # /api/bb-opening 路由
│       └── db/
│           └── schema.sql             # 完整建表脚本
└── prd/
    └── V2版本/                        # 规格文件目录（只读参考）
        ├── BB_Account_Opening_Fields_20260417_V1_9.md  ← 业务规格权威来源
        ├── bb_page1_list.html
        ├── bb_page2_data_entry.html
        ├── bb_page3_post_risk.html
        └── CLAUDE_API_SYSTEM_PROMPT.md
```

---

## 3. 规格文件权威性排序

**冲突时，优先级从高到低：**

```
1. BB_Account_Opening_Fields_20260417_V1_9.md  ← 唯一业务规格权威
2. bb_page1/2/3_*.html                         ← UI 交互的精确参考
3. 核心eonboarding接口api_20220913.xlsx        ← 字段长度/类型的补充参考
4. 企业网银开户api20220518.xlsx               ← BIB 接口字段参考
5. CLAUDE_API_SYSTEM_PROMPT.md                 ← 开发方式和阶段规范
```

**处理规则：**
- MD 与 HTML 不一致 → **以 MD 为准**，代码注释标注 `// HTML 原型差异：...`
- MD 有歧义 → 选最合理的解释，注释标注 `// 业务假设：...`
- 缺少字段规格 → 按同类字段规律推断，注释标注 `// 推断规格：...`

---

## 4. 开发工作流程（强制）

**每个任务必须按此流程完整执行，不得跳过任何步骤：**

```
步骤 1：读取现有代码库结构
  → find . -type f -name "*.ts" -o -name "*.tsx" | head -80
  → 识别已有的组件、hooks、services、types、constants
  → 识别「已有」vs「需要新增」vs「需要更新」的清单

步骤 2：从底层到顶层依次实现（强制顺序）
  types 类型定义
  → constants 枚举和常量
  → api/services 接口调用层
  → hooks 业务逻辑层
  → components UI 组件层
  → pages 页面层

步骤 3：自动执行测试
  后端：npm test 或 jest --coverage
  前端：npm test 或 npx react-scripts test --watchAll=false
  → 循环直到所有测试通过，再进入下一个任务

步骤 4：输出完成摘要
  → 新增文件清单（路径 + 功能说明）
  → 修改文件清单（路径 + 改动说明）
  → 测试结果（通过 N / 失败 0）
  → 剩余 TODO 清单（如有）
```

### 自主开发原则

- **不等待确认**，遇到问题自己解决
- **增量开发**：只开发尚未实现或需要更新的部分，不重写已有的正确实现
- **保留现有功能**：如「填入测试数据」按钮、「展开全部」等，读取现有代码确认后原样保留
- 接口尚未实现 → 先写 mock，标注 `// TODO: 替换真实接口`

---

## 5. UI 实现规范（强制）

### 核心原则：三个 HTML 文件是 UI 的唯一标准，必须一比一还原

1. **像素级还原**：颜色值、字体大小、间距、圆角、边框、阴影均以 HTML 原型为准，直接提取原型 CSS 数值，**不得使用估计值或 Ant Design 默认值替代**

2. **CSS 直接迁移**：把 HTML 文件 `<style>` 标签内的 CSS 类整体迁移到前端项目样式文件（CSS Modules），不重新设计

3. **class 名原样保留**：`sec`、`sec-hd`、`sec-body`、`g3`、`field`、`multi-item`、`party-card` 等 class 名在 React 中以相同名称的 CSS Module class 保留

4. **Ant Design 仅用于功能逻辑**：表单校验、数据管理等，视觉样式必须用 CSS 覆蓋到与原型完全一致

5. **DOM 结构一致**：JSX 结构必须与 HTML 中的 div 嵌套层次一致

6. **开始任何页面组件前**，必须先从对应 HTML 原型提取 CSS，建立样式常量文件

### CSS 提取顺序（Phase 0 强制完成）

```
0.1 提取 bb_page1_list.html 全部 <style> 内容
0.2 提取 bb_page2_data_entry.html 全部 <style> 内容
0.3 提取 bb_page3_post_risk.html 全部 <style> 内容
0.4 建立 src/styles/variables.css（所有色值定义为 CSS 自定义属性 --color-*）
0.5 建立 src/styles/bb-page1.module.css（class 名原样保留）
0.6 建立 src/styles/bb-page2.module.css（class 名原样保留）
0.7 建立 src/styles/bb-page3.module.css（class 名原样保留）
0.8 建立 src/styles/bb-common.module.css（三页面共用 CSS）
```

---

## 6. 三页面功能说明

### 页面一 — 开户列表页

- 搜索筛选（8 字段）、分页列表、默认 Submission Time 降序
- 点击跳转逻辑：**草稿** → 页面二；**已提交** → 页面三
- Delete 确认弹窗 + API 调用 + toast 反馈
- Refresh 保留筛选条件刷新

### 页面二 — 开户资料录入页

#### Section 0 — TU 查询反显

输入 BR/CR 号码后查询 HKCR/IRD，自动反显公司注册信息。按 MD 附录中的 TU 字段映射表进行字段对应。

#### Section 1–16 完整实现要求

| Section | 关键交互 |
|---------|---------|
| 1 | 三个地址 7 字段格式、Trading Name / Old Company Name 多值、Declaration 枚举 |
| 2 | First/Last Name 拆分、Job Title 带编号（CHAR(3)）、Group CIF Number / Group CIF Name |
| 3 | Supplier/Buyer 含百分比 + 风险等级联动、所有多值字段 |
| 4 | SEQ 问卷 Q1–Q4（Q4=Y 展开 Jurisdiction 子表单）、Countries of Assets Held |
| 5 | Initial/Ongoing SOF+SOW 各自独立、pill 多选（**最多 5 个**）、OT 展开 Other 输入框 |
| 6 | Purpose checkbox（最多 5 个）、Hold Balances flag → 展开子字段 |
| 8 | Company Change flag（总标志 CHAR(1)）→ Y 展开所有子字段 |
| 10 | Individual：含 Ownership Structure Level / Beneficial Owner SOW / FATCA 个人字段 / CRS 个人字段（Type of Controlling Person CHAR(2)，枚举 01–08）；Entity：含两个 7 字段地址 |
| 13–16 | BIB 企业网银完整实现，字段映射后传值 |

#### Topbar 三个辅助按钮（必须实现且永久保留）

| 按钮 | 样式 | 功能 |
|------|------|------|
| 填入测试数据 | 橙黄色 | 一键预填 Section 0–16 完整模拟数据 + 自动展开全部 |
| 展开全部 | — | 展开所有 sec-body |
| 折叠全部 | — | 折叠所有 sec-body |

#### 底部操作

- **Save Draft**：手动暂存 + **30秒自动静默保存**（`useDraftAutoSave.ts`）
- **Risk Scoring**：必填校验 → 自动 Save Draft → 调用接口 → 跳转页面三

### 页面三 — 审批详情页

#### 布局结构

```
┌──────────────────────────────────────────────┬─────────────────┐
│  左侧主内容区（滚动）                           │ 右侧 sticky 面板 │
│  ├── Case Overview 卡片                        │                 │
│  │   ├── BBCompanyIdentityBlock（卡片顶部）     │  BBApproval     │
│  │   ├── BBApprovalProgress（进度条）           │  ActionCard     │
│  │   └── Case 信息 grid（16 个字段）            │                 │
│  ├── Risk Assessment 卡片（Score Breakdown）   │                 │
│  ├── Section 1–16 折叠可编辑表单              │                 │
│  ├── Customer Risk 详细评估表格（21行×6列）    │                 │
│  └── 相关文档 / 附件上传                       │                 │
└──────────────────────────────────────────────┴─────────────────┘
```

#### BBCompanyIdentityBlock 样式规则

```
位置：Case Overview 卡片顶部（非底部）
字段：ID Type / ID Number / Company English Name（三列同行）
左边框：4px 实线，颜色跟风险等级：
  LOW    → 绿色 (#1d9e75)
  MEDIUM → 橙色
  HIGH   → 红色 (#a32d2d)
底色：固定浅灰 #f5f5f5
参考 HTML：id="id-highlight-row-main"
```

#### Customer Risk 评估表格（6列，21行）

| 列 | 说明 |
|----|------|
| 评估维度 | 风险评估项目名称 |
| Result | 评级结果 |
| Detail | 评估详细说明 |
| Initial Score | 系统初始评分 |
| Assessment Score | 人工评估分数 |
| Action | 操作控件（因维度而异） |

**Action 控件类型：**

| 维度编号 | 维度名称 | Action 类型 |
|---------|---------|------------|
| 5 | Complex Structure | Radio（Yes / No），Result=YES 时显示 |
| 14 | HI Company Secretary | Radio（Yes / No） |
| 15 | High Risk Remarks | 展开面板（disabled checkbox 列表 + Others 展开输入框） |
| 命中名单行 | PEP / Sanctions / Factiva / Special / Grey | Detail 弹窗（22列命中详情 + Result 5选1 + Save） |

#### 命中名单 Detail 弹窗（Result 5 个选项）

1. Non Material Hit
2. True Hit + Hong Kong PEP
3. True Hit + Non Hong Kong PEP
4. True Hit + International Organization PEP
5. False Hit

#### Terminate Risk 攔截（Case Content = Hit Terminate Risk）

```
- Case Overview 顶部红色警告横条
- 右侧面板：Recommend Approval 和 RFI 隐藏或 disabled
- 只允许选择 Recommend Reject 或 Return
- Customer Risk 表格中 Prohibited Business 行：显示红色标签「Prohibited」，不显示分数
```

#### 右侧审批操作面板（Sticky）

| 区块 | 说明 |
|------|------|
| 审批结果 | 三选一：Recommend Approval / Recommend Reject / RFI |
| Select RFI Category | 2×2 格，仅 RFI 时显示 |
| Select RFI Template | 根据 Category 显示模板列表 |
| 审批意见 | textarea，最多 16383 字，右下角实时显示 `x / 16383` |
| 附件上传 | 最多 60 个，单个最大 50MB，支持 PDF/ZIP/EML/PNG/JPG/DOC/DOCX/XLS/XLSX/CSV |
| 提交按钮 | 文字和颜色跟随审批结果联动（见下表） |

**提交按钮联动：**

| 审批结果选择 | 按钮文字 | 按钮颜色 |
|------------|---------|---------|
| Recommend Approval | ▶ 提交 — Recommend Approval | `#1d9e75`（绿） |
| Recommend Reject | ▶ 提交 — Recommend Reject | `#a32d2d`（红） |
| Return | ▶ 提交 — Return | `#854f0b`（橙） |
| RFI | ▶ 提交 — Send RFI | `#185fa5`（蓝） |

---

## 7. 后端业务逻辑规范

### API 端点（/api/bb-opening）

| 方法 | 路径 | 功能 |
|------|------|------|
| GET | `/api/bb-opening` | 列表查询（含筛选、分页、排序） |
| POST | `/api/bb-opening/draft` | 创建草稿 |
| PUT | `/api/bb-opening/:id/draft` | 更新草稿 |
| POST | `/api/bb-opening/:id/risk-scoring` | 执行 Risk Scoring |
| POST | `/api/bb-opening/:id/action` | 提交审批操作 |

### CRA 规则引擎（`craRuleEngine.ts`）

```
输入：所有 Section 字段值
输出：
  { riskLevel: 'LOW' | 'MEDIUM' | 'HIGH' | 'TERMINATE', totalScore: number, breakdown: Pillar[] }

7 Pillar 评分逻辑：
  Pillar 1：主要业务性质/行业（高风险行业加分）
  Pillar 2：公司注册日期（新成立公司加分）
  Pillar 3：实体类型/持股结构（复杂结构加分）
  Pillar 4：持票人股份（Bearer Shares 加分）
  Pillar 5：注册/运营国家（高风险国家加分）
  Pillar 6：供应商/买家国家分布（高风险国家占比 >= 20% 触发 High）
  Pillar 7：交易产品（高风险产品加分）

禁止项（任一触发 → 返回 TERMINATE）：
  SEQ Q1 = Y（受制裁实体）
  SEQ Q2 = Y（受制裁国家交易 > 10%）
  SEQ Q3 = Y（与制裁名单相关方交易）

风险等级阈值（具体数值见 V1.9.md 附录三）
```

### SEQ 制裁问卷（`seqValidator.ts`）

```
Q1 = Y → 拒绝（Terminate）
Q2 = Y → 拒绝（Terminate）
Q3 = Y → 拒绝（Terminate）
Q4 = Y → 记录 Jurisdiction（允许继续开户，需填写辖区）
```

### Name Screening 联动（`nameScreeningService.ts`）

**触发时机：**
- Risk Scoring 执行时
- 以下字段修改时：Registered Full Name (EN/ZH)、ID Type、ID Number、Registered Office Address 国家/地区、Connected Parties 的相关字段

**送审字段清单（来源：MD 附录二 §4.4）：**
- 公司层面：公司英文/中文注册名、ID Type、ID Number 等
- Connected Parties 自然人：First/Last Name、DoB、国籍、地址国家 等
- Connected Parties 公司：英文/中文注册名、ID Type、ID Number 等

### 审批流程状态机（`approvalStateMachine.ts`）

详见第 9 节。

### BIB 接口传值映射（`bibMappingService.ts`）

Section 13–16 字段按 `企业网银开户api20220518.xlsx` 中的映射关系转换后传值。

---

## 8. 数据库规范

### 字段长度规则（严格按 MD V1.9 附录一执行）

**关键字段长度（常见易错项）：**

| 字段名 | 正确长度 | 错误示例 |
|--------|---------|---------|
| `Complex_Structure` | `CHAR(1)` | CHAR(3) |
| `customer_status` | `CHAR(3)` | VARCHAR(10) |
| `pep_flag1` / `pep_flag2` | `CHAR(1)` | CHAR(3) |
| `Usage_of_products` | `CHAR(1)` 枚举 `1/2/3` | CHAR(2) 或枚举 `01/02/03` |
| `Contact_Person_Job_Title` | `CHAR(3)` | CHAR(30) |
| `Telephone_Number_Area_Code` | `CHAR(5)` | CHAR(10) |
| `Business_Nature` | `CHAR(5)` | CHAR(10) |
| `Ownership_Structure_Level` | `CHAR(2)` | CHAR(1) |
| `High_Risk_Type` | `CHAR(1)` | CHAR(3) |
| `High_Risk_Remarks` | `CHAR(60)` | VARCHAR(255) |
| `Internal_Client_Type` | `CHAR(2)` | CHAR(3) |
| `Group_CIF_Number` | `CHAR(12)` | CHAR(20) |
| `Group_CIF_Name` | `CHAR(120)` | VARCHAR(255) |
| `US_Tax_ID` | `CHAR(30)` | VARCHAR(50) |
| `Type_of_Controlling_Person` | `CHAR(2)` 枚举 `01–08` | CHAR(1) |

### 字段长度来源确认原则

**每个字段在最终确定长度前，必须交叉核对三个来源：**
1. BB PRD（`ZA_Business_Banking_RM開戶產品說明書_v1.0`）
2. 核心 eonboarding 接口 API（`核心eonboarding接口api_20220913.xlsx`）
3. BIB API（`企业网银开户api20220518.xlsx`）

三者不一致时，以 MD 附录一为最终准。

---

## 9. 审批流程状态机

### 三条风险等级路径

```
Low / Medium Risk（正常流程）：
  RM Maker → RM Checker → 审批结束

High Risk（正常流程）：
  RM Maker → RM Checker → RM Head → AML Maker → AML Checker → 审批结束

任意风险等级 + Escalation：
  RM Checker → [Proceed with Escalation] → RM Head / BU Head → 返回 RM Checker
  RM Head 节点处理完毕后保留显示（标记 Completed），不消失
```

### 角色权限严格限制

| 角色 | 可执行操作 | 禁止操作 |
|------|-----------|---------|
| RM Maker | 提交、Save Draft | — |
| RM Checker | No Objection、Return、Pass on、**Proceed with Escalation**（唯一可执行角色） | 不能直接 Reject（需 Return 给 RM Maker） |
| RM Head | Return | **不能直接 Reject**（需 Return 给 RM Maker） |
| BU Head | Return | **不能直接 Reject**（需 Return 给 RM Maker） |
| AML Maker | 提交、Return、Pass on | — |
| AML Checker | No Objection、Return | **不能直接 Reject**（需 Return 给 RM Maker） |

### Pass on（同角色转派）

适用角色：RM Maker / RM Checker / AML Maker / AML Checker  
规则：只能转派给**同角色**其他人员，不跨角色

### Proceed with Escalation（上呈）

- **仅 RM Checker 可操作**
- 上呈目标：RM Head 或 BU Head（二选一）
- 上呈完成后：RM Head 节点**保留并标记 Completed（绿色对勾）**，不消失
- RM Checker 节点再次变为 In Progress（蓝色高亮）

### 进度条节点显示规则

| 场景 | 显示节点 |
|------|---------|
| High Risk | RM Maker → RM Checker → RM Head → AML Maker → AML Checker（共 5 个） |
| Low / Medium Risk | RM Maker → RM Checker（共 2 个） |
| Escalation 期间 | 动态插入 RM Head，显示前三节点，RM Head 蓝色高亮 |
| Escalation 完成后 | RM Head 节点**保留且标记 Completed**，RM Checker 再次高亮 |

---

## 10. 前端组件架构（页面三）

### 四个独立组件

```typescript
// 1. BBApprovalProgress.tsx
// 功能：审批进度条，铺满一行，6节点（High Risk）或2节点（Low/Med Risk）
// Props：riskLevel, currentRole, nodes: ApprovalNode[], escalationNode?

// 2. BBRiskBadge.tsx
// 功能：风险等级徽章（HIGH 红色 / MEDIUM 橙色 / LOW 绿色）+ 风险分数
// Props：level: 'HIGH' | 'MEDIUM' | 'LOW', score: number

// 3. BBCompanyIdentityBlock.tsx
// 功能：公司身份高亮块，位于 Case Overview 卡片顶部
// Props：idType, idNumber, companyEnName, riskLevel
// 样式：左边框颜色跟随 riskLevel，底色 #f5f5f5

// 4. BBApprovalActionCard.tsx
// 功能：右侧 sticky 审批操作面板，包含全部审批操作
// Props：caseId, currentRole, riskLevel, caseContent, onSubmit
```

### Score Breakdown 展开面板

- 点击 "Score breakdown ▾" 展开各 Customer Risk 维度得分明细
- 红底行：高风险维度（有实际得分）
- 黄底行：中风险维度
- 白底灰字行：0 分维度
- 蓝底合计行：显示 Total 总分
- 再次点击收起，箭头切换 ▾ / ▴

---

## 11. 已知陷阱与解决方案

### 陷阱 1：路由隔离问题

**问题：** React Router 页面三和页面二共用同名路由导致状态串联  
**解决：** 页面三路由必须独立（如 `/bb-cases/:id/detail`），不使用 query param 区分

### 陷阱 2：TypeScript 执行路径问题

**问题：** 后端 TypeScript 文件直接 `node xxx.ts` 会报错  
**解决：** 必须用 `ts-node` 或先 `tsc` 编译后执行，不得直接 `node` 运行 `.ts` 文件

### 陷阱 3：tradingNames 映射问题

**问题：** Section 1 的 Trading Names（多值字段）在 payload 中使用了错误的字段名  
**解决：** 字段名统一用 `tradingNames`（camelCase），数据库存储为 JSON 数组或关联表

### 陷阱 4：Qwen API 内容块类型问题

**问题：** `qwen-3-6-plus` 在对话历史中包含不支持的 content block type 或 null `stop_reason` 的 assistant 消息时，抛出 `InvalidParameter: Unexpected item type in content`  
**解决：** 在构造对话历史时，用 JavaScript 过滤掉 content block type 不为 `text` 的消息，以及 stop_reason 为 null 的 assistant 消息

### 陷阱 5：枚举值零填充问题

**问题：** `Usage_of_products` 枚举值误用 `01/02/03`（零填充格式）  
**正确：** 枚举值为 `1/2/3`（单字符，无零填充）  
同类字段也适用：所有 `CHAR(1)` 枚举字段不使用零填充格式

### 陷阱 6：Ant Design 默认样式覆盖不足

**问题：** Ant Design Table / Modal / Form 组件有大量默认样式，导致与 HTML 原型视觉差异大  
**解决：** 对每个 Ant Design 组件，必须在对应 CSS Module 中添加 `:global(.ant-*)` 覆盖规则；如果无法覆盖到位，改用原生 HTML 元素自行实现

### 陷阱 7：RM Head 节点 Escalation 后的消失问题

**问题：** Escalation 完成后，有实现将 RM Head 节点从进度条中隐藏  
**正确：** RM Head 节点**必须保留并标记 Completed（绿色对勾）**，不消失

### 陷阱 8：div 结构不平衡（页面二）

**问题：** 修改 HTML 原型的 JS 后，`two-col` 内的 `main-col` depth 不等于 1  
**解决：** 修改 HTML 后必须用 `node --check <file>` 验证语法，并手动确认 div 层级平衡

---

## 12. 测试要求

### 后端单元测试（每个服务必须有）

```typescript
// CRA 规则引擎测试
describe('CRA 规则引擎', () => {
  test('Pillar 1: 高风险行业应得 20 分')
  test('禁止项: SEQ Q1=Y 应返回 Terminate')
  test('SEQ Q2=Y 应返回 Terminate')
  test('SEQ Q3=Y 应返回 Terminate')
  test('SEQ Q4=Y 应记录 Jurisdiction，不终止流程')
  test('阈值计算: 总分 > 20 应为 High Risk')
  test('高风险国家占比 >= 20% 触发 High Risk')
  test('多国累计供应商/买家分布逻辑')
})
```

### 前端组件测试

```typescript
// Section 5 SOF/SOW pill 多选
describe('SOF/SOW pill 多选', () => {
  test('最多选 5 个，超过时不能继续选取')
  test('选中 OT 后展开 Other 输入框')
  test('取消 OT 后收起 Other 输入框')
})

// Section 6 Purpose
describe('Purpose checkbox', () => {
  test('最多选 5 个，超过时不能继续选取')
  test('选中 Hold Balances 后展开子字段')
})

// 审批进度条
describe('BBApprovalProgress', () => {
  test('High Risk 显示 5 个节点')
  test('Low Risk 显示 2 个节点')
  test('Escalation 期间动态插入 RM Head')
  test('Escalation 完成后 RM Head 保留 Completed 状态')
})
```

### 集成测试（页面级别）

```typescript
describe('页面二 Risk Scoring 流程', () => {
  test('未填必填字段时点击 Risk Scoring 应显示校验错误')
  test('填完所有必填字段后点击 Risk Scoring 应触发接口调用')
  test('接口成功后应跳轉到页面三')
})

describe('审批操作权限', () => {
  test('RM Checker 是唯一可执行 Proceed with Escalation 的角色')
  test('RM Head 不能直接 Reject')
  test('AML Checker 不能直接 Reject')
})
```

### 测试覆盖率要求

- 后端（规则引擎、状态机）：> 80%
- 前端（核心组件）：> 70%

---

## 13. 完整开发阶段（Phase 0–8）

```
Phase 0 — CSS 完整提取（所有前端开发前必须先完成）
  0.1-0.8 提取三个 HTML 原型的全部 CSS，建立 4 个样式文件
  验收：所有 CSS class 名与 HTML 原型一一对应，色值完全匹配

Phase 1 — 数据基础层
  1.1 读取现有代码库结构，输出增量范围分析
  1.2 types/index.ts — 所有业务实体的 TypeScript 类型定义
  1.3 constants/fields.ts — 所有枚举值、字段名、接口路径常量
  1.4 SQL 建表脚本（按附录一完整建表）
  → 验收：TypeScript 编译通过，无类型错误

Phase 2 — 后端核心逻辑
  2.1 CRA 规则引擎服务（craRuleEngine.ts）
  2.2 SEQ 制裁问卷校验服务（seqValidator.ts）
  2.3 Name Screening 字段组装服务（nameScreeningService.ts）
  2.4 审批流程状态机（approvalStateMachine.ts）
  2.5 BIB 接口传值映射服务（bibMappingService.ts）
  → 验收：jest --coverage，覆盖率 > 80%

Phase 3 — API 接口层
  3.1 开户列表接口（GET /api/bb-opening）
  3.2 草稿保存接口（POST/PUT /api/bb-opening/draft）
  3.3 Risk Scoring 接口（POST /api/bb-opening/:id/risk-scoring）
  3.4 审批操作接口（POST /api/bb-opening/:id/action）
  3.5 TU 查询接口 proxy（GET /api/tu/company）
  → 验收：接口单元测试 + mock 数据验证

Phase 4 — 前端共用层
  4.1 api/ 接口调用函数
  4.2 hooks/useBBCase.ts
  4.3 hooks/useDraftAutoSave.ts（30秒自动保存）
  4.4 components/common/（地址7字段、多值增删、pill多选等）
  → 验收：组件渲染测试 + 交互测试

Phase 5 — 页面一（列表页）✅
  5.1 BBCaseList 页面组件
  5.2 搜索筛选、分页、排序
  5.3 Delete 确认弹窗
  → 视觉验收：与 bb_page1_list.html 逐元素对比

Phase 6 — 页面二（资料录入页，Section 逐个实现）✅
  6.0 Topbar 三个辅助按钮（必须实现且保留）
  6.1-6.16 Section 0–16 全部字段
  → 视觉验收：与 bb_page2_data_entry.html 逐元素对比

Phase 7 — 页面三（审批详情页）🔄 进行中
  7.1 BBApprovalProgress.tsx
  7.2 BBRiskBadge.tsx
  7.3 BBCompanyIdentityBlock.tsx
  7.4 BBApprovalActionCard.tsx
  7.5 detail.tsx 页面集成
  7.6 Customer Risk 详细评估表格（21行×6列，全部 Action 交互）
  7.7 Score Breakdown 展开面板
  7.8 Generate RSS 弹窗
  7.9 Flow Chart Tab（SVG，drawFlowChart() 函数）
  7.10 Timeline Tab
  → 视觉验收：与 bb_page3_post_risk.html 逐元素对比

Phase 8 — E2E 测试 ⏳ 待完成
  8.1 完整开户流程 E2E（页面一 → 页面二 → Risk Scoring → 页面三 → 审批）
  8.2 审批流程各路径测试（Low / Medium / High / Escalation）
  8.3 Terminate Risk 拦截测试
  8.4 Name Screening 联动测试
```

---

## 14. 错误处理策略

| 问题类型 | 自主解决方式 |
|---------|-------------|
| 类型错误（TS） | 补充类型定义或使用类型断言（附注释说明） |
| 接口不存在 | 创建 mock 实现，标注 `// TODO: 接入真实 API` |
| 依赖缺失 | `npm install <package>` 或寻找等价替代包 |
| 测试失败（逻辑错误） | 阅读报错 → 修复代码 → 重测，循环直到通过 |
| 测试失败（环境问题） | 调整测试配置或 mock 环境依赖 |
| 业务规则歧义 | 选最保守（最严格）的解释，注释标注假设 |
| 现有功能已实现 | 读取现有代码确认后，**原样保留，不删除不重写** |
| 文件路径模块找不到 | 搜索项目确认正确路径后引用 |

---

## 15. 字段关键规则速查

### Usage_of_products 枚举值

```
✅ 正确：1 / 2 / 3
❌ 错误：01 / 02 / 03
```

### Section 5 SOF/SOW pill 选择

```
最多选 5 个（Initial SOF / Initial SOW / Ongoing SOF / Ongoing SOW 各自独立，每个最多 5 个）
选中 OT（Others）→ 展开 Other 文本输入框
```

### Section 4 SEQ 问卷

```
Q1 / Q2 / Q3 = Y → 触发 Terminate，拒绝开户
Q4 = Y → 展开 Jurisdiction 子表单（可继续开户）
```

### Company Change（Section 8）

```
总标志字段 Company Change flag CHAR(1)：Y → 展开所有子字段
```

### 地址格式（7 字段）

```
Format A：Floor、Flat、Building、Street No、Street Name、District、Country
Format B：Address Line 1、Address Line 2、Address Line 3、District、Country
```

### Name Screening 触发字段

修改以下字段时触发 NS 重跑：
- 公司英文/中文注册名
- ID Type / ID Number
- Registered Office Address 中的国家/地区
- Connected Parties 中的姓名、证件、国家字段

---

## 16. 进度状态

```
✅ Phase 0：CSS 提取完成
✅ Phase 1：数据基础层完成
✅ Phase 2：后端核心逻辑完成
✅ Phase 3：API 接口层完成
✅ Phase 4：前端共用层完成
✅ Phase 5：页面一完成
✅ Phase 6：页面二完成
🔄 Phase 7：页面三（detail.tsx）进行中
⏳ Phase 8：E2E 测试待完成
```

### 当前任务（Phase 7）

实现 `detail.tsx` 页面，使用四组件架构：
1. `BBApprovalProgress.tsx` — 审批进度条
2. `BBRiskBadge.tsx` — 风险等级徽章
3. `BBCompanyIdentityBlock.tsx` — 公司身份高亮块（置于 Case Overview 卡片**顶部**）
4. `BBApprovalActionCard.tsx` — 右侧 sticky 审批操作面板

**开始 Phase 7 前必须先确认：**
- [ ] 阅读 `bb_page3_post_risk.html` 全部 CSS 和 DOM 结构
- [ ] 阅读 `BB_Account_Opening_Fields_20260417_V1_9.md` 附录二 §3（页面三交互说明）
- [ ] 确认现有代码库中 detail.tsx 的已有实现（如有）

---

*本文件由 Claude 根据项目规格文件和对话历史自动生成，版本对应 BB_Account_Opening_Fields_20260417_V1_9.md。*
