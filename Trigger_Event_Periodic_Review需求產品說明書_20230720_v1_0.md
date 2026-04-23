產品說明書_E-onboarding 對公KYC系統

                                                                                                                產品說明書_E-onboarding對公KYC系統

特別提示：

——本文檔傳遞過程必須遵守“必需知道、最小授權”原則，不得向任何非授權人員提供本文檔的全部或部分內容。

——若本文檔不屬於您知悉範圍，應立即銷毀，不得保存、傳輸、複製、印刷或使用本文檔之任何內容。

**Trigger Event**** ****&**** Periodic Review**

**產品說明書**

**202****3****年****7****月**

			**修 訂 頁**	

| **編號** | **章節名稱** | **修訂內容簡述** | **修訂日期** | **修訂前版本號** | **修訂後版本號** | **修訂人** | **批准人** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 全部 | 新建 | 2023/7/18 | V1.0 |  | 王澤楷 |  |
| 2 | 修改 | 修改2.5.1章節 1、增加字段：Number of layers of ownership structure、High Risk Date、High Risk Type、High Risk Date、Internal Client Type、Account Officer、Business Nature、Country Of Risk、Business Nature、Country Of Registration、Country Of Origin、Secretary Address、四個sanction的問題、Non-SME flag 、Controlling Person Type、Entity Type、Country of Key Counterparties、Financial Background、Is your Company a Holding Company，Estimated the monthly payment amount、Estimated the amount of monthly payments、Estimated monthly payee amount、Estimated the amount of monthly payee、Disregarded entity、工作經驗、Whether the annual turnover of the group exceeds 1.5 billion 2、刪除字段：Industry Category、Ownership Structure Level 3、相關人士的Residential Address可以只輸入國家/地區，室/房，層，大廈，街道可以不輸入。 4、Entity Type of Connected Entity的枚舉值修改成跟Company Type一致。 | 2023/9/5 | V1.0 |  | 王澤楷 |  |
| 3 | 新增 | 2.4章節增加處理規則：如果存在多个进行中的单子，要按创建顺序进行审批，否則提示“The customer has unapproved trigger event orders or periodic review orders{case number}”。 | 2023/9/5 | V1.0 |  | 王澤楷 |  |
| 4 | 新增 | 第3章節增加模板說明 | 2023/9/5 | V1.0 |  | 王澤楷 |  |

**目  錄**

1	需求概述	5

1.1	業務背景	5

2	Trigger Event & Periodic Review	6

2.1	頁面交互說明	6

2.2	審批流程說明	6

2.2.1	系統權限控制表	7

2.3	Risk Scoring處理說明	7

2.3.1	Name Screening檢查	7

2.3.2	Special List 檢查	8

2.3.3	AML Black List 檢查	10

2.3.4	MSO名單 檢查	11

2.3.5	CRA打分處理	11

2.4	其他處理說明	12

2.5	新增TE/PR審批單	16

2.5.1	創建TE/PR單	16

2.5.2	TE/PR Risk Scoring頁面	41

2.5.3	顯示客戶的交易歷史頁面	51

2.5.4	TE/PR查詢列表	52

3	Periodic Review批量處理	55

4	Trigger Event上線後生產數據處理	58

需求概述

業務背景

目前Trigger Event和 Periodic Review無系統功能，RM做Trigger Event和 Periodic Review都是通過手工review的方式，效率較低，因此需要在對公KYC系統上開發Trigger Event和 Periodic Review功能，實現系統自動檢查檢查Name Screening，AML Black List，Special List等名單，自動計算CRA分數以及客戶風險級別，並生成Trigger Event和 Periodic Review審批訂單給到RM和AML Team審批。

Trigger Event & Periodic Review

頁面交互說明

**交互說明：**

- RM Maker在View Customer列表中選擇需要發起Trigger Event或者Periodic Review的公司

- 系統根據舊的資料先重跑一次risk scoring，然後顯示risk scoring的結果給RM maker review。

- RM Maker可以修改該公司資料后重跑risk scoring。

- 對於有hit的記錄，RM需要做dicounting，如果為True Hit，則需要上傳EDD form/PEP Assessment Form。

- RM Maker在Risk Scoring頁面確認完點擊Submit按鈕，系統生成待審批訂單給RM Checker

說明：Trigger Event場景說明：當公司的資料發生變更且涉及風險評估要素時，例如公司行業或者公司董事，股東等相關人士發生變更時，RM需要對該公司發起Trigger Event  Review，具體涉及風險評估的要素請查閱2.3.5章節中的CRA打分表。

審批流程說明

**審批流程說明：**

- RM Maker發起Trigger Event Review或者Periodic Review後，審批單自動流轉到RM Checker節點進行審批；

- RM Checker可以將審批單Return給RM Maker進行修改，也可以審批通過；

- RM Checker審批之後，系統根據客戶的風險級別判斷是否需要流轉給AML Team審批，如果客戶新舊兩個風險級別存在有一個是高風險的情況，則系統自動流轉給AML Maker審批，如果客戶新舊兩個風險級別中不存在有高風險的情況，則RM Checker審批之後流程結束，不需要轉派給AML Team審批；

- AML Maker審批時，可以選擇將審批單Return給 RM Maker，如果審批通過，則審批單自動流轉到AML Checker進行審批；

- AML Checker審批時，可以選擇將審批單Return給 RM Maker或者AML Maker；AML Checker審批通過之後，審批結束。

系統權限控制表

Risk Scoring處理說明

Name Screening檢查

功能說明 

當RM Maker提交成功之後，系統繼續進行Name Screening檢查；檢查的對象包含公司和公司相關人士的信息

Name Screening會調用FT系統。FT系統會檢查的名單類型包括Sanctions Exposure（GWL）、Adverse News(EDD)、 Foreign PEP/PEP with power to exercise/PEP with adverse news（PEP）和AML Internal Watchlist (PRI)。

- **檢查規則：**

- 公司層面的檢查：將Company English Name，Company Chinese Name，Trading Name（English），Trading Name（Chinese），Old Company Name，送到FT系統做檢查；

- 相關人士層面的檢查（自然人）：English Name，Chinese Name，Gender，Date of Birth，Identity Card Number送到FT系統做檢查；

- 相關人士層面的檢查（公司股東）：Registered Full Name in English，Registered Full Name in Chinese送到FT系統做檢查；

- **檢查結果處理：**如果有命中，則由RM Maker做Discounting

- ** ****Dis****counting****流程****：**

- RM maker對命中的記錄結合相關人士的資料進行判斷，如果是True Hit，

- 則需要填寫EDD  Form並上傳到系統中； 

- 如果是命中PEP，則還需要填寫PEP Assessment Form並上傳到系統中；

- 提交給RM Checker和AML Team審核；

- 如果RM 無法做discounting，則需要線下咨詢AML team的意見，然後線下得出結論之後，再在系統中做出True Hit或者False Hit的判斷。

其他說明：

- 對於Sanctions Exposure（GWL），Adverse News(EDD)、 Foreign PEP/PEP with power to exercise/PEP with adverse news（PEP）和AML Internal Watchlist (PRI)有Hit的，且RM Maker Discounting的結果是True Hit，則系統允許繼續提交，RM maker提交之後，系統根據2.2章節審批流程的控制走相應的流程。

Special List 檢查

Special List 的cw_type为BP或BO

BP：Bankruptcy Petition(BP) 破产呈请

BO：Bankruptcy Order(BO)破产令

- 公司層面的檢查規則：

- 將公司的ID Type，ID Number送到名單管理系統進行匹配，如果有命中，則系統不允許提交申請；

- 將公司的Company English Name，Trading Name（English），Old Company Name（English）送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（自然人）的檢查規則：

- 將相關人士的Identity Card Type ，Identity Card Number送到名單管理系統進行匹配，如果有命中，則系統不允許提交申請；

- 將相關人士的English Name送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（公司股東）的檢查規則：

- 將相關人士（公司）的ID Type，ID Number送到名單管理系統進行匹配，如果有命中，則系統不允許提交申請；

- 將相關人士（公司）的Registered Full Name in English送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

Special List 的cw_type为非BP或BO

- 公司層面的檢查規則：

- 將公司的ID Type，ID Number送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 將公司的Company English Name送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（自然人）的檢查規則：

- 將相關人士的Identity Card Type ，Identity Card Number送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 將相關人士的English Name送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（公司股東）的檢查規則：

- 將相關人士（公司）的ID Type，ID Number送到名單管理系統進行匹配，如果有命中，則系統不允許提交申請；

- 將相關人士（公司）的Registered Full Name in English送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

其他說明：

- 對於Special List有Hit的，且RM Maker Discounting的結果是True Hit，系統也允許繼續提交。 RM maker提交之後，系統根據2.2章節審批流程的控制走相應的流程。

AML Black List 檢查

AML Black List單維度名單檢查

- 公司層面的檢查規則：

- 將公司的ID Number送到名單管理系統進行匹配，如果有命中，則系統顯示命中詳情，RM 不需要做Discounting；

- 相關人士層面（自然人）的檢查規則（股份佔比>=25%）：

- 將相關人士的Identity Card Number，Email Address，Telephone Number送到名單管理系統分別進行匹配，如果三者有任一命中，則系統顯示命中詳情，RM 不需要做Discounting；

- 相關人士層面（公司股東）的檢查規則（股份佔比>=25%）：

- 將相關人士（公司）的ID Number送到名單管理系統進行匹配，如果有命中，則系統顯示命中詳情，RM 不需要做Discounting；

AML Black List多維度名單檢查

- 公司層面的檢查規則：

- 將公司的ID Type，ID Number送到名單管理系統進行匹配，如果有命中，則系統顯示命中詳情，RM 不需要做Discounting；

- 將公司的的Company English Name，Company Establish Date送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（自然人）的檢查規則（股份佔比>=25%）：

- 將相關人士的Identity Card Type ，Identity Card Number送到名單管理系統進行匹配，如果有命中，則系統顯示命中詳情，RM 不需要做Discounting；

- 將相關人士的English Name， Date of Birth送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

- 相關人士層面（公司股東）的檢查規則（股份佔比>=25%）：

- 將相關人士（公司）的ID Type，ID Number送到名單管理系統進行匹配如果有命中，則系統顯示命中詳情，RM 不需要做Discounting；；

- 將相關人士（公司）的Registered Full Name in English，送到名單管理系統進行匹配，如果有命中，則系統落RM Maker人工做Discounting。

其他說明：

- 對於AML Black List有Hit的，如果RM Maker Discounting的結果是True Hit，則系統也允許繼續提交。

MSO名單 檢查

- **檢查規則**：將公司的Company English Name送到名單管理系統進行匹配；

- **檢查結果處理：**如果有命中，則系統也允許提交。

CRA打分處理

功能說明 

檢查規則說明 

- 根據打分表計算出CRA的分數.

- 禁止風險項不計入總分。

- CRA打分表使用跟EOB相同的處理以及版本，具體版本如下(該版本更新系統時間2013/4/4)：

- Industry list’s rating也是使用跟EOB相同的版本，具體版本如下（該版本更新系統的時間為2023/6/7）

- country risk rating也是使用跟EOB相同的版本，具體版本如下（該版本更新系統的時間為2023/4/25）

其他處理說明

- Next Review Date計算方法：每個對公客戶根據上述的參數，系統生成每個客戶的“Next Review Date”，具體計算方法為，新開賬戶從賬戶開戶日期開始計算“Next Review Date”，存在 “Last Review Date”的， 則使用 “Last Review Date”計算下一個 “Next Review Date”， 具體處理邏輯複用現有核心關於Next Review Date的處理；

例子說明：

- **場景一**

Next Review Date: 22/11/2022

1

2

3

抽取1. CDD Last Review Date: 23/11/2021，可忽略2.Client Open Date。直接對應3. Client Risk: **HIGH (+12月 -1天)**，得出Next Review Date: **22/11/2022**。

**B) 場景二**

Next Review Date: 19/07/2023

1

2

3

抽取1. CDD Last Review Date: 20/07/2021，(因沒有1. CDD Last Review Date資料，故抽取2. Client Open Date: 20/07/2021)；並對應3. Client Risk: **Medium**** ****(****+****24****月 -1天****)**，得出Next Review Date: **19****/07/202****4**。

- 當Trigger Event Review審批完成之後，由RM選擇是否為Full Review，

- 如果為Full Review，則以Trigger Event Review審批完成的時間更新“Last Review Day”，並使用 “Last Review Day”計算下一個 “Next Review Date”； 

- 如果不是Full Review，則不需要用Trigger Event Review審批完成的時間更新“Last Review Day”，也不需要重新計算“Next Review Date”。

例子： Trigger Event的審批完成日期為2021/12/17，且RM選擇了Full Review，當前的Next Review Date為2021/12/31。

- 系統先將Last Review Day更新為2021/12/17，

- 並計算下一次的Next Review Date為2022/12/16.

- 高風險轉中低風險，或者中低風險轉高風險都要經過AML審核。

- Trigger Event订单在生成后必須在60天內審批完; 当所有maker或者checker打开未完成订单时, 系统需置頂提示該筆訂單已經處理了多少天了，提示效果如下所示：

如果該Trigger Event訂單已經審批完成，則不需要再顯示該訂單的超時天數提示。

- 每30天要求重做Risk Scoring

- 当RM Maker第一次點擊 “Risk Scoring” 之后，系统开始累计天数。之后每30天，系统必须強制要求RM Maker 重新點擊 “Risk Scoring”  系统重做 “Name Screening” 和相關的名單檢查，並重新計算CRA分數和風險級別。

- 如果RM Maker 没有重新點擊 “Risk Scoring”:

- 則不允許點擊proceed按鈕提交到下一步。

- 彈窗提示“Reminder: The previous “Name Screening” has exceeded 30 days, please proceed with “Risk Scoring”.

- 如果流程停留在RM Checker / AML Maker /AML Checker而且 “Risk Scoring” 过了30天，則要求RM Checker / AML Maker /AML Checker把審批單退回給RM Maker重跑Risk Scoring。

- 當用戶一進入審批單頁面則在Overview頂部提示“The previous “Name Screening” has exceeded 30 days, please return to RM Maker redo “Risk Scoring””

- 當RM Checker / AML Maker /AML Checker點擊proceed按鈕時，系統再彈窗提示““The previous “Name Screening” has exceeded 30 days, please return to RM Maker redo “Risk Scoring”””

- 創建Trigger Event時，如果已經存在Periodic Review的訂單，則當Maker點擊Create Trigger Event按鈕時，系統彈窗提示“The customer already has a Periodic Review order, please complete the approval first”；

- 創建Trigger Event時，如果已經存在Trigger Event的訂單，則當Maker點擊Create Trigger Event按鈕時，系統彈窗提示“The customer already has a Trigger Event order, please complete the approval first”；

- 如果先有Trigger Event訂單，則系統允許批量繼續生成Periodic Review訂單，但不能手工創建Periodic Review訂單，如果手工創建Periodic Review訂單，則系統彈窗提示“The customer already has a Trigger Event order, please complete the approval first”；

- 如果存在多个进行中的单子，要按创建顺序进行审批，否則提示“The customer has unapproved trigger event orders or periodic review orders{case number}”。

- 當前面的Trigger Event訂單審批完成後，系統後台自動更新已經存在的Periodic Review訂單資料並重跑Risk Scoring。

- Trigger Event需要檢查秘書公司地址，由RM手工錄入秘書公司地址之後，再跟公司註冊地址和公司運營地址比較。

- 審批完成之後，才將修改後的信息更新到核心系統。

- 支持將E-onboarding客戶限額由第二層調整為第三層。

- 對於有修改的字段，系統都需要紅色高亮顯示，（改了空格的都算修改）

- 重跑Risk  Scoring 之後，如果Hit沒變的話，需要保存RM之前Discounting的結果；

- Legal status 與CRA打分表中Entity type/Ownership structure的映射關係如下；

| **Legal status（Hisun）** | **　** | **CRA打分表 Mapping** |
| --- | --- | --- |
| CO: LIMITED COMPANY (UNLISTED) | 公司:有限公司(非上市) | Limited Company |
| CS: COOPERATIVE SOC | CS:合作SOC | Unincorporated Company |
| EM: EMBASSY | 新兴市场:大使馆 | SOE |
| FD: FOUNDATION | FD:基础 | Unincorporated Company |
| FI: FINANCIAL INSTITUTION | FI:金融机构 | ERFI |
| FU: FUND | 傅:基金 | Trust |
| GO: GOVERNMENT DEPARTMENT | :政府部门 | SOE |
| HC: HOLDING COMPANY | HC:控股公司 | Limited Company |
| JC: JOINT CORP | JC:联合公司 | Unincorporated Company |
| LC: LIMITED COMPANY (LISTED) | 立法会:有限公司(上市公司) | Unincorporated Company |
| MS: MONEY SERVICE BUSINESS | Ms:货币服务业务 | Limited Company |
| NG: NATIONAL OR LOCAL BODY GOVERNMENT | 国家或地方机构政府 | SOE |
| PC: PRIVATE INVESTMENT COMPANY | Pc:私人投资公司 | Limited Company |
| PF: PRIVATE INVESTMENT FUND | 私人投资基金 | Trust |
| PT: PRIVATE HOLDING TRUST | 私人控股信托 | Trust |
| SB: STATUTORY BODY | 某人:法定机构 | SOE |
| SO: SOCIETY | 所以:社会 | Unincorporated Company |
| SP: SOLE PROPRIETORSHIP | SP:个人独资 | Sole Prop |
| TR: TRUST | TR:信託 | Trust |
| TU: TRADE UNION | 你:工会 | Unincorporated Company |
| VS: VOLUNTARY SECTOR | VS:志愿部门 | Unincorporated Company |
| JP: JOINT PERSON | JP:联合人 | Unincorporated Company |
| PA: Partnership | PA:伙伴关系 | Partnership |

- RM Maker對於還未提交給RM checker的Trigger Event或者手工創建的Periodic Review草稿訂單可以刪除。對於系統自動生成的Periodic Review訂單或者已經提交給RM Checker的訂單則不能刪除。

操作說明：當RM Maker勾選“Delete”選項，並點擊“Submit”按鈕時，系統彈窗提示“confirm delete draft order？”，當RM Maker點擊確定按鈕時，系統刪除該筆草稿，點擊返回按鈕時，則不刪除草稿，返回當前頁面。

新增TE/PR審批單

創建TE/PR單

| **字段** | **字段長度** | **屬性** | **概述** | **EOB****字段** |
| --- | --- | --- | --- | --- |
| **A. Company Information****公司資料** |  |
| Company Type | CHAR(2) | 必輸 不可修改 | CO: LIMITED COMPANY (UNLISTED) CS: COOPERATIVE SOC EM: EMBASSY FD: FOUNDATION FI: FINANCIAL INSTITUTION FU: FUND GO: GOVERNMENT DEPARTMENT HC: HOLDING COMPANY JC: JOINT CORP LC: LIMITED COMPANY (LISTED) MS: MONEY SERVICE BUSINESS NG: NATIONAL OR LOCAL BODY GOVERNMENT PC: PRIVATE INVESTMENT COMPANY PF: PRIVATE INVESTMENT FUND PT: PRIVATE HOLDING TRUST SB: STATUTORY BODY SO: SOCIETY SP: SOLE PROPRIETORSHIP TR: TRUST TU: TRADE UNION VS: VOLUNTARY SECTOR JP: JOINT PERSON PA: Partnership | 是 Company Type 必輸 |
| Company English Name | CHAR(60) | 可修改 必輸 | CHAR（60）核心英文字符可以輸入60個，但是中文只能輸入30個字； 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 是 Company English Name 必輸 |
| Company Chinese Name | CHAR(60) | 可修改 選輸 | 公司中文名稱 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 是 Company Chinese Name 選輸 |
| Trading Name（English） | CHAR(60) | 可修改 選輸 | 英文營業名稱 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring 該字段最多支持10個Trading Name | 是 Trading Name 選輸 |
| Trading Name（Chinese） | CHAR(60) | 選輸 可修改 | 中文營業名稱 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring 該字段最多支持10個Trading Name | 否 |
| Company Establish Date | NUM(8) | 必輸 可修改 | 公司成立日期 如果修改，需要重新做Risk Scoring | 是 Company Establish Date 必輸 |
| ID Type | CHAR(3) | 必輸 可修改 | 證書類型 枚舉值： CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE | 是 ID Type 必輸 |
| ID Number | CHAR(30) | 必輸 可修改 | 證書號碼 | 是 ID Number 必輸 |
| Country of Registration | CHAR(3) | 必輸 可修改 | 註冊國家 如果修改，需要重新做Risk Scoring | 是，必輸 Country of Incorporation/Registration |
| Listing Company | CHAR(30) | 選輸 可修改 | 是否上市公司 枚舉值：Y:Yes，N:No | 否 |
| Name(s) of Stock Exchange | CHAR(120) | 選輸 可修改 | 交易所名稱 | 否 |
| Bearer Shares Issued | CHAR(1) | 必輸 可修改 | 是否可以發行/已經發行不記名股票 枚舉值：Y:Yes，N:No | 是，必輸 Bearer Shares Issued |
| Are any of the Intermediate owners of your company capable of issuing bearer shares | CHAR(1) | 選輸 可修改 | 公司的中介控股公司是否能夠發行不記名股票 枚舉值：Y:Yes，N:No | 否 |
| Channel | CHAR(1) | 必輸 可修改 | 開戶渠道 枚舉值： O：Solely E-Onboarding Channel W:Walk-in customer A:Approved Business Partner Referral I:Internal customer B:RM Referral | 是，必輸 Channel |
| Account Type | CHAR(2) | 必輸 不可修改 | 賬戶類型 枚舉值： 01：Client Account 02：House Account 03：SFGS Account | 否，必輸 |
| Usage of products | CHAR(1) | 必輸 可修改 | 使用的產品 枚舉值： 01：Low Risk Product Only 02：At Least One Medium Risk Product 03：At Least One High Risk Product | 否 |
| On PEC | CHAR(1) | 必輸 可修改 | 是否政府標誌 | 是，必輸 On PEC |
| Number of layers of ownership structure | CHAR(2) | 顯示 | 公司股權結構層數 |  |
| High Risk Date | NUM(8) | 選輸 可修改 | 高風險日期 | 否 |
| High Risk Type | CHAR(2) | 顯示 | 01：No AML Referral 02：AML Reference 03：Closely Monitoring: 04：Others | High Risk Type |
| High Risk Date | NUM(8) | 顯示 | 高風險日期 | High Risk Date |
| Internal Client Type | CHAR(2) | 選輸 | 内部客户类别 02:PUBLIC SECTOR 03:OTHER | 是，選輸 |
| Account Officer | CHAR(10) | 選輸 | 客戶經理 | 是，選輸 |
| Business Nature | CHAR(5) | 選輸 | 1:SMALL BUSINESS 2:SECURITIES FIRM 3:BANK 4:PUBLIC SECTOR 5:OTHER COMPANY 6:Other 7:INSURANCE COMPANY | 是，選輸 |
| Country Of Risk | CHAR(3) | 選輸 | 以核心的國家列表為準 | 是，選輸 |
| Business Nature | CHAR(5) | 選輸 | 1:SMALL BUSINESS 2:SECURITIES FIRM 3:BANK 4:PUBLIC SECTOR 5:OTHER COMPANY 6:Other 7:INSURANCE COMPANY | 是，選輸 |
| Country Of Registration | CHAR(3) | 選輸 | 注册国家 | 是，選輸 |
| Country Of Origin | CHAR(3) | 選輸 | 总部注册国家 | 是，選輸 |
| **B. Contact Details**** ****聯絡資料** |
| Company Registered Address | CHAR(210) | 必輸 可修改 | 註冊地址 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 是，必輸 Company Registered Address |
| Company Operation Address | CHAR(210) | 必輸 可修改 | 營業地址 可選擇Same as Registered Address 與註冊地址相同 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 是，必輸 Company Operation Address |
| Company Mailing Address | CHAR(210) | 必輸 可修改 | 通訊地址 可選擇Same as Registered Address 與註冊地址相同/ Same as Operation Address 與營業地址相同 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 是，必輸 Company Mailing Address |
| Secretary Address | CHAR(210) | 選輸 可修改 | 秘書公司地址 RM有輸入則做秘書公司地址比較，沒有輸入則不做比較。 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 是，選輸 |
| Contact Person First Name | CHAR(30) | 必輸 可修改 | 聯絡人名字 | 是，必輸 Contact person First Name |
| Contact Person Last name | CHAR(30) | 必輸 可修改 | 聯絡人姓氏 | 是，必輸 Contact Person Last name |
| Contact Person Email Address | CHAR(34) | 必輸 可修改 | 聯絡人電郵地址 | 是，必輸 Contact Person Email Address |
| Contact Person Telephone Number | CHAR(18) | 必輸 可修改 | 聯絡人手機號碼 | 是，必輸 Contact Person Telephone Number |
| Contact Person Job Title | CHAR(3) | 必輸 可修改 | 聯絡人頭銜 枚舉值： 01：董事 02：商人 03：東主 04：總經理 05：行政人員 06：高級行政人員 07：財務總監 08：秘書 09：其他 | 是，必輸 Contact Person Job Title |
| Other Contact Person Job Title | CHAR(30) | 選輸 可修改 | 當字段“Contact Person Job Title”選擇“09：其他”時，該字段必輸； | 否 |
| Office Telephone Number | CHAR(18) | 選輸 可修改 | 辦事處電話 | 否 |
| Company Website | CHAR(120) | 選輸 可修改 | 公司網頁 | 否 |
| **C. Commercial Details**** ****商業資料** |
| **1. Company Business Background ****公司業務背景** |
| Office Location(s)􉳵􂰖􃹔􃖑􋟊 | CHAR(3) | 選輸 可修改 | 辦公室地點 枚舉值：國家列表，第一個默認香港。 | 否 |
| Principal Place of Operations | CHAR(3) | 必輸 可修改 | 主要運營地 枚舉值：國家列表，第一個默認香港。 | 是，必輸 Countries of Operation/Business |
| Countries of Foreign Branch(es)/ Subsidiary(ies) / Factory(ies) | CHAR(3) | 選輸 可修改 | 海外分支/子公司/工廠所在的國家/地區 枚舉值：國家列表，第一個默認香港。 支持最多增加10個 | 否 |
| Country of Key Counterparties | CHAR(3) | 必輸 可修改 | 主要交易國家 | 是，必輸 Country of Key Counterparties |
| Industry Category | CHAR(6) | 必輸 可修改 | 行業大類 如果修改，需要重新做Risk Scoring | 是，必輸 Industry Category |
| Key Products/ Services | CHAR(6) | 必輸 可修改 | 行業小類 如果修改，需要重新做Risk Scoring | 是，必輸 Key Products/ Services |
| Name of Regulatory Body | CHAR(120) | 選輸 可修改 | 監管機構名稱 | 否 |
| Regulatory Reference Number | CHAR(30) | 選輸 可修改 | 監管機構參考編號 | 否 |
| Country of Regulatory Body | CHAR(3) | 選輸 可修改 | 監管機構所在國家 枚舉值：國家列表，第一個默認香港。 | 否 |
| Country of Employee(s) | CHAR(3) | 選輸 可修改 | 僱員所在國家 枚舉值：國家列表，第一個默認香港。 跟僱員數目為一組，最多支持5組 | 否 |
| Number of Employee(s) | CHAR(5) | 選輸 可修改 | 僱員數目 跟僱員所在國家為一組，最多支持5組 | 否 |
| Whether WEB3 Company | CHAR(1) | 選輸 可修改 | 是否WEB3公司 枚舉值：Y:Yes，N:No | 否 |
| Financial Background | CHAR(5) | 選輸 可修改 | 1110:STATE-OWNED(国有) 1120:COLLECTIVELY-OWNED(集体) 1140:JOINT OPERATION(联营) 1160:JOINT STOCK(股份) 1170:PRIVATE-OWNED(私营) 1175:INDIVIDUAL-OWNED(个体) 1200:FOREIGN CAPITAL(外资) 1330:HONG KONG,MACAU,OR TAIWAN CAPITAL(港澳台资) 9999:OTHERS(其他) | 是，選輸 Financial Background |
| Is your Company a Holding Company | CHAR(1) | 選輸 可修改 | Y:Yes N:No | 是，選輸 Is your Company a Holding Company |
| **Financial Indicator of Latest**** ****Fiscal Year ****最新財政年度的財務數據** |
| Annual Business Turnover | NUM(16,2) | 必輸 可修改 | 全年營業額 | 是，必輸 Company Annual Turnover |
| Net Profit | NUM(16,2) | 選輸 可修改 | 淨利潤 | 否 |
| Asset Size | NUM(16,2) | 選輸 可修改 | 資產規模 | 否 |
| Others(Please Specify) | CHAR(200) | 選輸 可修改 | 其他說明 | 否 |
| Company Name of Supplier | CHAR(120) | 選輸 可修改 | 供應商公司名稱 最多支持10個 如果修改，需要重新做Risk Scoring | 否 |
| Countries of Supplier | CHAR(3) | 必輸 可修改 | 供應商所在國家/地區 枚舉值：國家列表，第一個默認香港。 多支持10個 如果修改，需要重新做Risk Scoring | 是，至少輸入一個 Countries of Supplier |
| Percentage of Total Purchase | CHAR(4) | 必輸 可修改 | 佔總採購之百分比 最多支持10個 | 是，至少輸入一個 Percentage of Total Purchase |
| Company Name of Buyer | CHAR(120) | 選輸 可修改 | 買方公司名稱 最多支持10個 如果修改，需要重新做Risk Scoring | 否 |
| Countries of Buyer | CHAR(3) | 必輸 可修改 | 買方所在國家/地區 枚舉值：國家列表，第一個默認香港。 最多支持10個 如果修改，需要重新做Risk Scoring | 是，至少輸入一個 Countries of Buyer |
| Percentage of Total Sales | CHAR(4) | 必輸 可修改 | 佔總銷售量之百分比 最多支持10個 | 是，至少輸入一個 Percentage of Total Sales |
| Countries of Which Assets Held | CHAR(3) | 選輸 可修改 | 資產所在國家/地區 枚舉值：國家列表，第一個默認香港。 最多支持10個 | 否 |
| Percentage of Total Assets Held | CHAR(4) | 選輸 可修改 | 佔總資產之百分比 最多支持10個 | 否 |
| Does the Company conduct any business activity(ies) in any sanctioned country(ies), e.g. North Korea, South Sudan, Libya, Iran? | CHAR(1) | 必輸 可修改 | 貴公司是否在受制裁之國家進行商業行為？ 枚舉值：Y:Yes，N:No | 否 |
| Name of the jurisdiction | CHAR(3) | 選輸 可修改 | 司法管轄權 枚舉值：國家列表，第一個默認香港。 最多支持5個 | 否 |
| Purpose of business in the jurisdiction | CHAR(120) | 選輸 可修改 | 於該地進行商業行為之目的 最多支持5個 | 否 |
| Does the Company, to the best of your knowledge, have any current or planned business activity in the following countries / regions (Iran, Sudan, North Korea, Syria, Cuba, South Sudan, Crimea or Zimbabwe | CHAR(1) | 必輸 可修改 | 公司目前或计划在伊朗、苏丹、朝鲜、叙利亚、古巴、南苏丹、克里米亞或津巴布韦等国家/地区开展业务活动吗？ 枚舉值：是/否 | 是，必輸 Does the Company, to the best of your knowledge, have any current or planned business activity in the following countries / regions (Iran, Sudan, North Korea, Syria, Cuba, South Sudan, Crimea or Zimbabwe |
| To the best of your knowledge, does the company or any of the company's connected or other related parties have a presence in Iran, Sudan, North Korea, Syria, Crimea or Cuba, and/or are currently targeted by sanctions administrated by the folliwing bodies: UN, EU, UKHMT, HKMA, OFAC, or as part of local sanctions law | CHAR(1) | 必輸 可修改 | 据你所知，貴公司或貴公司的任何关联方或其他相关方是否存在在伊朗、苏丹、朝鲜、叙利亚，克里米亞或古巴，以及/或目前是以下国家实施制裁的目标 机构:联合国、欧盟、UKHMT、金管局、OFAC，或作为当地制裁法律的一部分 枚舉值：是/否 | 是，必輸 To the best of your knowledge, does the company or any of the company's connected or other related parties have a presence in Iran, Sudan, North Korea, Syria, Crimea or Cuba, and/or are currently targeted by sanctions administrated by the folliwing bodies: UN, EU, UKHMT, HKMA, OFAC, or as part of local sanctions law |
| Does the company or, to the best of your knowledge, any of its connected or other related parties have any current or planned business activity in Crimea / Sevastopol regions | CHAR(1) | 必輸 可修改 | 据您所知，貴公司或其任何关联方或其他关联方是否有任何现有或 计划在克里米亚/塞瓦斯托波尔地区进行的商业活动 枚舉值：是/否 | 是，必輸 Does the company or, to the best of your knowledge, any of its connected or other related parties have any current or planned business activity in Crimea / Sevastopol regions |
| Does the company have any current or planned exposure to Russia in the energy / oil and gas sector, the military or defence | CHAR(1) | 必輸 可修改 | 公司目前或计划在俄罗斯的能源/石油和天然气部门、军事或国防领域有任何业务 枚舉值：是/否 | 是，必輸 Does the company have any current or planned exposure to Russia in the energy / oil and gas sector, the military or defence |
| Purpose for Account Opening | CHAR(3) | 可修改 必輸 | 修改不需要重做檢查 枚舉值： BO:BUSINESS OPERATIONS IS:PAYMENT OF HIRE IV:INVESTMENT SF:SAVING / FIXED DEPOSIT PR:PAYROLL LO:LOAN OT:OTHER | 是，必輸 Purpose for Account Opening |
| Others(Please Specify): | CHAR(200) | 選輸 可修改 | 其他開戶目的 | 否 |
| Whether the annual turnover of the group exceeds 1.5 billion | CHAR(1) | 選輸 可修改 | 所屬集團年營業是否超過15億 枚舉值：Y:Yes，N:No | 否 |
| Non-SME flag | CHAR(1) | 選輸 | 是否SME標誌 枚舉值：Y:Yes，N:No | 否 |
| **2****.****Source of Wealth **** ****財富來源** |
| Initial Source of Wealth | CHAR(3) | 必輸 可修改 | 初始财富来源 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET 如果修改，不需要重新做company search, Name Screening,Risk Scoring 最多支持輸入5個 | 否 |
| Other Initial Source of Wealth | CHAR(60) | 選輸 可修改 | 其他初始财富来源 如果修改，不需要重新做company search, Name Screening,Risk Scoring | 否 |
| Ongoing Source of Wealth | CHAR(3) | 必輸 可修改 | 持续财富来源 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET 如果修改，不需要重新做company search, Name Screening,Risk Scoring 支持多選 | 否 |
| Other Ongoing Source of Wealth | CHAR(60) | 選輸 可修改 | 其他持续财富来源 如果修改，不需要重新做company search, Name Screening,Risk Scoring | 否 |
| **3****.****Source of ****Fund**** ****資金****來源** |
| Initial Source of Fund | CHAR(3) | 必輸 可修改 | 初始資金来源 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET 如果修改，不需要重新做company search, Name Screening,Risk Scoring 支持多選 | 是，選輸 Source of funds to establish the company |
| Other Initial Source of Fund | CHAR(60) | 選輸 可修改 | 其他初始資金来源 如果修改，不需要重新做company search, Name Screening,Risk Scoring | 是，選輸 Other Source of funds to establish the company |
| Ongoing Source of Fund | CHAR(3) | 必輸 可修改 | 持续資金来源 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET 如果修改，不需要重新做company search, Name Screening,Risk Scoring 支持多選 | 是，必輸 Ongoing Source of Fund |
| Other Ongoing Source of Fund | CHAR(60) | 選輸 可修改 | 其他持续資金来源 如果修改，不需要重新做company search, Name Screening,Risk Scoring | 是，選輸 Other Ongoing Source of Fund |
| **3. Anticipated Transaction ****&**** Purpose of Usage ****預期交易及使用目的** |
| Domestic / International Transaction | CHAR(1) | 必輸 可修改 | 本地/國際交易 修改不需要重做檢查 枚舉值： A：本地電匯(Domestic Payment) B：國際電匯(International Payment) C：本地和國際電匯(Domestic and International Payment) | 是，必輸 Domestic / International Transaction |
| Does the Company intent to make Cross Border Transaction | CHAR(1) | 必輸 可修改 | 公司會否進行跨境交易 枚舉值：Y:Yes，N:No | 是，必輸 Does the Company intent to make Cross Border Transaction |
| Countries (to) of Cross Border Transaction | CHAR(3) | 選輸 可修改 | 匯出國家 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：國家列表，第一個默認香港。 最多支持10個 | 否 |
| Countries (from) of Cross Border Transaction | CHAR(3) | 選輸 可修改 | 匯入國家 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：國家列表，第一個默認香港。 最多支持10個 | 否 |
| Monthly Transaction Pattern | CHAR(1) | 選輸 可修改 | 每月交易量 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： 1至20宗 21至50宗 50宗以上 | 是 |
| Total Amount of Cross Border Transaction | NUM(16,2) | 選輸 可修改 | 跨境交易總金額 當問題“公司會否進行跨境交易”為“Y時必輸” | 否 |
| Currency for Total Amount of Cross Border Transaction | CHAR(3) | 選輸 可修改 | 跨境交易總金額貨幣 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： CNY:YUAN RENMINBI HKD:HONG KONG DOLLAR USD:US DOLLAR | 否 |
| Purpose for Cross-Border Transaction | CHAR(3) | 選輸 可修改 | 跨境交易目的，支持多選 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） | 否 |
| Other Purpose for Cross-Border Transaction | CHAR(200) | 選輸 可修改 | 其他跨境交易目的 | 否 |
| Does the Company intend to make Regular Domestic Transaction | CHAR(1) | 必輸 可修改 | 公司會否進行常規本地交易 枚舉值：Y:Yes，N:No | 否 |
| Monthly Transaction Pattern | CHAR(1) | 選輸 可修改 | 每月交易量 當問題“公司會否進行常規本地交易”為“Y時必輸” 枚舉值： 1至20宗 21至50宗 50宗以上 | 是 |
| Total Amount of Cross Border Transaction | NUM(16,2) | 選輸 可修改 | 本地交易總金額 當問題“公司會否進行常規本地交易”為“Y時必輸” | 否 |
| Currency for Total Amount of Cross Border Transaction | CHAR(3) | 選輸 可修改 | 跨境交易總金額貨幣 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： CNY:YUAN RENMINBI HKD:HONG KONG DOLLAR USD:US DOLLAR | 否 |
| Estimated the monthly payment amount | CHAR(1) | 選輸 可修改 | 估計每月付款金額 0至60,000港元 60,001至600,000港元 600,001至3000,000港元 3000,001至6,000,000港元 6,000,000港元以上 | 是，選輸 Estimated the monthly payment amount |
| Estimated the amount of monthly payments | CHAR(1) | 選輸 可修改 | 估計每月付款數量 1至20宗 21至50宗 50宗以上 | 是，選輸 Estimated the amount of monthly payments |
| Estimated monthly payee amount | CHAR(1) | 選輸 可修改 | 估計每月收款金額 0至60,000港元 60,001至600,000港元 600,001至3000,000港元 3000,001至6,000,000港元 6,000,000港元以上 | 是，選輸 Estimated monthly payee amount |
| Estimated the amount of monthly payee | CHAR(1) | 選輸 可修改 | 估計每月收款數量 1至20宗 21至50宗 50宗以上 | 是，選輸 Estimated the amount of monthly payee |
| Purpose for Cross-Border Transaction | CHAR(3) | 選輸 可修改 | 本地交易目的，支持多選 當問題“公司會否進行常規本地交易”為“Y時必輸” **枚舉值：** Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） | 否 |
| Other Purpose for Cross-Border Transaction | CHAR(200) | 選輸 可修改 | 本地其他交易目的 | 否 |
| Does the Company intend to hold balances in the account with infrequent activity | CHAR(1) | 選輸 可修改 | 公司是否預期在賬戶持有結餘而不進行交易 枚舉值：Y:Yes，N:No | 否 |
| Please indicate the amount and rationale | CHAR(120) | 選輸 可修改 | 請提供金額及理由 | 否 |
| **FATCA/CRS 問題** |
| W-8BEN-E/W-9/Self-Certification | CHAR(2) | 必輸 可修改 | 01:W-8BEN-E 02:W-9 03:SELF-CERTIFICATION 04:N/A 05:W-8ECI 06:W-8EXP 07:W-8BEN 08:W-8IMY | 是，必輸 W-8BEN-E/W-9/Self-Certification |
| W-9 | CHAR(2) | 選輸 可修改 | 01:Specified U.S. Person 02:U.S. Person other than Specified U.S | 是，選輸 W-9 |
| W-8BEN-E | CHAR(2) | 選輸 可修改 | 01:Active NFFE-501(c) organization 02:Active NFFE-Active NFFE 03:Active NFFE-Direct reporting NFFE 04:Active NFFE-Excepted nonfinancial entity in liquidation/bank 05:Active NFFE-Excepted nonfinancial start-up company 06:Active NFFE-Excepted territory NFFE 07:Active NFFE-Foreign gov/gov of US possession/non-MO/US centr 08:Active NFFE-International orgainzation(non-MO) 09:Active NFFE-Nonfinancial group entity 10:Active NFFE- Nonprofit organization 11:Active NFFE-Publicly traded NFFE or its NFFE affiliate 12:Active NFFE-Sponsored direct reporting NFFE 13:CDCFFI-CDCFFI with only low-value accounts 14:CDCFFI-CDC investment advisors and investment managers 15:CDCFFI-CDC limited life debt investment entity 16:CDCFFI-Certified deemed-compliant nonregistering local bank 17:CDCFFI-CDC sponsored, closely held investment vehicle 18:CDCFFI-Nonreporting IGA FFI 19:EBO-Entity wholly owned by  exempt beneficial owners 20:EBO-Exempt  retirement plans 21:MO government and subdivisions,MO central bank of issue 22:EBO-International organization with MO presence 23:EBO-Territory financial institution 24:Excepted inter-affiliate FFI 25:FFI-Nonparticipating FFI,including limited FFI/branch 26:FFI-Participating FFI 27:FFI-Reporting Model 2 FFI 28:FFI-Reporting Model 1 FFI 29:ODFFI-Owner-documented FFI with no specified U.S. Owner(s) 30:ODFFI-Owner-documented FFI with specified U.S. Owner(s) 31:Passive NFFE with no substantial U.S. owners 32:Passive NFFE with substantial U.S. owner(s) 33:RDCFFI-Registered deemed-compliant FFI 34:RDCFFI-Sponsored FFI that has not obtained a GIIN 35:Restricted distributor | 是，選輸 W-8BEN-E |
| Sign Date of W-8BEN-E | NUM(8) | 選輸 可修改 |  | 是，選輸 Sign Date of W-8BEN-E |
| Other types of self-certification | CHAR(2) | 選輸 可修改 | 01:Active NFFE-501(c) organization 02:Active NFFE-Active NFFE 03:Active NFFE-Direct reporting NFFE 04:Active NFFE-Excepted nonfinancial entity in liquidation/bank 05:Active NFFE-Excepted nonfinancial start-up company 06:Active NFFE-Excepted territory NFFE 07:Active NFFE-Foreign gov/gov of U.S. possession/non-MO/US cen 08:Active NFFE-International orgainzation(non-MO) 09:Active NFFE-Nonfinancial group entity 10:Active NFFE- Nonprofit organization 11:Active NFFE-Publicly traded NFFE or its NFFE affiliate 12:Active NFFE-Sponsored direct reporting NFFE 13:CDCFFI-CDCFFI with only low-value accounts 14:CDCFFI-CDC investment adviscus and investment managers 15:CDCFFI-CDC limited life debt investment entity 16:CDCFFI-Certified deemed-compliant nonregistering local bank 17:CDCFFI-CDC sponsored, closely held investment vehicle 18:CDCFFI-Nonreporting IGA FFI 19:EBO-Entity wholly owned by exempt beneficial owners 20:EBO-Exempt retirement plans 21:EBO-MO government and subdivisions,MO central bank of issue 22:EBO-International organization with MO presence 23:EBO-Territory financial institution 24:Excepted inter-affiliate FFI 25:FFI-Nonparticipating FFI,including limited FFI/branch 26:FFI-Participating FFI 27:FFI-Reporting Model 2 FFI 28:FFI-Reporting Mdoel 1 FFI 29:ODFFI-Owner-documented FFI with no specified U.S. Owner(s) 30:ODFFI-Owner-documented FFI with specified U.S. Owner(s) 31:Passive NFFE with no substantial U.S. owners 32:Passive NFFE with substantial U.S. owner(s) 33:RDCFFI-Registered deemed-compliant FFI 34:RDCFFI-Sponsored FFI that has not obtained a GIIN 35:Restricted distributor | 是，選輸 Other types of self-certification |
| FATCA Due Diligence Status | CHAR(2) | 必輸 可修改 | 01:Completed - Normal FATCA 02:Completed - Enhanced FATCA Due Diligence 03:Incomplete 04:N/A | 是，必輸 FATCA Due Diligence Status |
| Tax Residency | CHAR(3) | 必輸 可修改 | 稅務地 枚舉值：國家列表，第一個默認香港。 支持最多增加10個 | 是，必輸 Tax Residency |
| Tax identifying number（TIN） | CHAR(22) | 選輸 可修改 | 稅務編號 支持最多增加10個 | 是，必輸 Tax identifying number（TIN） |
| Reason for No TIN | CHAR(1) | 選輸 可修改 | 沒有稅務編號的原因 當稅務編號為空時，該字段必輸，支持最多增加10個 枚舉值 A – The jurisdiction where the account holder is a resident for tax purposes does not issue TINs to its residents. B – The account holder is unable to obtain a TIN.  Refer to CRS Self-certification. C – TIN is not required. Select this reason only if the authorities of the jurisdiction of residence do not require the TIN to be disclosed. | 否 |
| Controlling Person Type | CHAR(6) | 選輸 | 控制人類型 CRS801:LEGAL PERSON - OWNERSHIP CRS802:LEGAL PERSON - OTHER MEANS CRS803:LEGAL PERSON - SENIOR MANAGING OFFICIAL CRS804:LEGAL ARRANGEMENT - TRUST - SETTLOR CRS805:LEGAL ARRANGEMENT - TRUST - TRUSTEE CRS806:LEGAL ARRANGEMENT - TRUST - PROTECTOR CRS807:LEGAL ARRANGEMENT - TRUST - BENEFICIARY CRS808:LEGAL ARRANGEMENT - TRUST - OTHER CRS809:LEGAL ARRANGEMENT - OTHER - SETTLOR-EQUIVALENT CRS810:LEGAL ARRANGEMENT - OTHER - TRUSTEE-EQUIVALENT CRS811:LEGAL ARRANGEMENT - OTHER - PROTECTOR-EQUIVALENT CRS812:LEGAL ARRANGEMENT - OTHER - BENEFICIARY-EQUIVALENT CRS813:LEGAL ARRANGEMENT - OTHER - OTHER-EQUIVALENT | 是，選輸 |
| Entity Type | CHAR(2) | 選輸 | 實體類型 01:FINANCIAL INSTITUTION-1 02:FINANCIAL INSTITUTION-2 03:ACTIVE NON-FINANCIAL ENTITY (NFE)-1 04:ACTIVE NON-FINANCIAL ENTITY (NFE)-2 05:ACTIVE NON-FINANCIAL ENTITY (NFE)-3 06:ACTIVE NON-FINANCIAL ENTITY (NFE)-4 07:PASSIVE NON-FINANCIAL ENTITY (PNFE)-1 08:PASSIVE NON-FINANCIAL ENTITY (PNFE)-2 | 是，選輸 |
| Disregarded entity | CHAR(1) | 選輸 | 是否為非實體公司 Y:Yes N:No | Disregarded entity |
| Have there been Material Changes to your Business in the last 5 years? (eg chanes to ownership, company name, parent company, countries of operation, nature of business, products / services) | CHAR(1) | 必輸 可修改 | 在过去的5年里，貴公司有过实质性的变化吗?(如所有权、公司名称、母公司的变更、经营国家、业务性质、产品/服务) 枚舉值：Y:Yes，N:No 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，必輸 Have there been Material Changes to your Business in the last 5 years? (eg chanes to ownership,company name, parent company,countries of operation, nature of business, products / services) |
| Old Company Name | CHAR(90) | 選輸 可修改 | 原公司名稱 當“公司名稱”字段選擇“是”的時候顯示，必輸。 修改需要重做Name Screening、Risk Scoring的檢查； 支持中文和英文名稱 支持最多輸入8組 | 是，選輸 Old Company Name |
| Start date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 Start date |
| End date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 End date |
| Old Industry Category | CHAR(6) | 選輸 可修改 | 舊行業大類 當“產品與服務”字段選擇“是”的時候顯示，必輸。 下拉選項（行業大類） 提示語：“佔公司營業額比重最大的業務” 支持最多輸入十組 | 是，選輸 Old Industry Category |
| Old  Key Products/ Services | CHAR(6) | 選輸 可修改 | 舊行業小類 當“產品與服務”字段選擇“是”的時候顯示，必輸。 下拉選項（行業小類） 只顯示行業大類下對應的行業小類； 支持最多輸入十組 | 是，選輸 Old  Key Products/ Services |
| Start date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 Start date |
| End date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 End date |
| Reasons for changing  Products and Services | CHAR(500) | 選輸 可修改 | 改變產品與服務的原因 當“產品與服務”字段選擇“是”的時候顯示，必輸。 支持最多輸入十組 | 是，選輸 Reasons for changing  Products and Services |
| Reasons for changing Company Ownership structure | CHAR(500) | 選輸 可修改 | 改變公司股權結構的原因 當“公司股權結構”字段選擇“是”的時候顯示，必輸。 | 是，選輸 Reasons for changing Company Ownership structure |
| Start date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 Start date |
| End date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 End date |
| Reasons for changing Beneficial Owners | CHAR(500) | 選輸 可修改 | 改變收益所有人的原因 當“收益所有人”字段選擇“是”的時候顯示，必輸。 | 是，選輸 Reasons for changing Beneficial Owners |
| Start date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 Start date |
| End date | NUM(8) | 選輸 可修改 | 修改不需要重做Name Screening、Risk Scoring的檢查； | 是，選輸 End date |
| Please briefly describe your previous work experience and how it relates to the company's business.(If there are more than one connected parties, please also provide their relevant background) |  | 顯示 | 請簡單描述你以前的工作經驗是什麼，與公司業務有什麼聯繫?（如有多個相關人士也請提供其相關背景） | 是，顯示 |
| **D. Details of Connected Parties **** ****有關人士資料** |  |
| **1. Individuals ****個人** |  |
| First Name | CHAR(30) | 可修改 必輸 | 英文名稱 修改後需要重做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 是，必輸 First Name |
| Last Name | CHAR(30) | 可修改 必輸 | 英文姓氏 修改後需要重做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 是，必輸 Last Name |
| Chinese Name | CHAR(60) | 可修改 非必輸 | 中文全名 修改後需要重做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 是，必輸 Chinese Name |
| Capacity of Connected Party | CHAR(2) | 必輸 可修改 | 有關人士類別 Sole Proprietor 獨資經營者 Partner 合夥人 Director 董事 Beneficial Owner 實益擁有人 Senior Officer 高級管理人員 Authorised Representative 獲授權代表 | 是，必輸 Role |
| Ownership | CHAR(3) | 選輸 可修改 | 持股量百分比 如果有關人士類別為“實益擁有人”時，則該字段可輸 | 是，選輸 Percentage of Shareholdings |
| Job Title | CHAR(30) | 選輸 可修改 | 職銜 如果有關人士類別為“高級管理人員”時，該字段可輸 | 否 |
| Identity Card Type | CHAR(3) | 可修改 必輸 | 身份證明文件種類 HK Identity Card 香港身份證 Passport 護照 Others 其他 | 是，必輸 Identity Card Type |
| Permanent HKID | CHAR(1) | 選輸 可修改 | 是否永久香港身份證 枚舉值：Y:Yes，N:No | 否 |
| Others Identity Card Type | CHAR(60) | 選輸 可修改 | 其他證件類型 | 否 |
| Identity Card Number | CHAR(30) | 可修改 選輸 | 身份證明文件號碼 當身份證明文件種類為“香港身份證”或者“護照”時必輸 | 是，必輸 Identity Card Number |
| ID Issue Date | NUM(8) | 可修改 選輸 | 身份證明文件簽發日期 | 是，必輸 ID Issue Date |
| Gender | CHAR(3) | 可修改 必輸 | 性別 | 是，必輸 Gender |
| Date of Birth | NUM(8) | 可修改 必輸 | 出生日期 | 是，必輸 Date of Birth |
| Place of Birth | NUM(8) | 可修改 必輸 | 出生地 | 是，必輸 Place of Birth |
| Nationality | CHAR(3) | 選輸 | 國籍 枚舉值：國家列表，下拉選項中第一個顯示香港，其他按首字母排序。 支持最多5個 | 是，必輸一個 Nationality |
| Email Address | CHAR(34) | 必輸 可修改 | 電郵地址 | 是，必輸 Email Address |
| Telephone Number | CHAR(18) | 必輸 可修改 | 電話號碼 | 是，必輸 Telephone Number |
| Residential Address | CHAR(210) | 可修改 必輸 可修改 | 如果修改了地址中的國家/地區，則需要做Name Screening檢查。 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 是，至少輸入國家/地區 Residential Address |
| Beneficial Owner Source of Wealth (Only for Beneficial Owner) | CHAR(3) | 選輸 可修改 | 實益擁有人的財富來源（只適用於實益擁有人） 枚舉值： Inheritance 繼承財產 Loan / bank funding 借貸/銀行貸款 Personal Savings 個人儲蓄 Sale of Business 出售業務 Sale of Investment Asset 出售投資資產 Capital from Family Members or Close Associates 來自家庭成員或關連人士的資金 Others(Please Specify) 其他（請註明） 支持多選 | 否 |
| Others(Please Specify) | CHAR(200) | 選輸 可修改 | 其他（請註明） | 否 |
| Country(ies) in which Beneficial Owner attained his/her Initial Source of Wealth  (Only for Beneficial Owner) | CHAR(3) | 選輸 可修改 | 實益擁有人獲得初始財富來源的國家 （只適用於實益擁有人），支持多選 枚舉值：國家列表，下拉選項中第一個顯示香港，其他按首字母排序。 | 否 |
| Country(ies) in which Beneficial Owner attained his/her Ongoing Source of Wealth (Only for Beneficial Owner) | CHAR(3) | 選輸 可修改 | 實益擁有人獲得持續財富來源的國家 （只適用於實益擁有人），支持多選 枚舉值：國家列表，下拉選項中第一個顯示香港，其他按首字母排序。 | 否 |
| Related Client On PEP | CHAR(1) | 必輸 可修改 | 枚舉值 No Domestic PEP or International Organization PEP Foreign PEP | 是，必輸 Related Client On PEP |
| **2. Entities**** ****公司** |
| Registered Full Name in English | CHAR(60) | 可修改 必輸 | 英文註冊全名 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 否 |
| Registered Full Name in Chinese (if applicable) | CHAR(60) | 可修改 選輸 | 中文註冊全名（如適用） 如果修改，需要重新做Name Screening（包括Accuity、Special list和AML Black List）、Risk Scoring | 否 |
| Entity Type of Connected Entity | CHAR(2) | 必輸 可修改 | 關連公司的公司類別 枚舉值： CO: LIMITED COMPANY (UNLISTED) CS: COOPERATIVE SOC EM: EMBASSY FD: FOUNDATION FI: FINANCIAL INSTITUTION FU: FUND GO: GOVERNMENT DEPARTMENT HC: HOLDING COMPANY JC: JOINT CORP LC: LIMITED COMPANY (LISTED) MS: MONEY SERVICE BUSINESS NG: NATIONAL OR LOCAL BODY GOVERNMENT PC: PRIVATE INVESTMENT COMPANY PF: PRIVATE INVESTMENT FUND PT: PRIVATE HOLDING TRUST SB: STATUTORY BODY SO: SOCIETY SP: SOLE PROPRIETORSHIP TR: TRUST TU: TRADE UNION VS: VOLUNTARY SECTOR JP: JOINT PERSON PA: Partnership | 否 |
| Capacity of Connected Entity | CHAR(3) | 必輸 可修改 | 關連公司的身份 有關人士類別 Director 董事 Beneficial Owner 實益擁有人 Partner 合夥人 Senior Officer 高級管理人員 Authorised Representative 獲授權代表 | 否 |
| Ownership | CHAR(3) | 選輸 可修改 | 持股量百分比 如果有關人士類別為“實益擁有人”時，則該字段可輸 | 否 |
| ID Type | CHAR(3) | 必輸 可修改 | 證書類型 枚舉值： CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE Others | 否 |
| Others | CHAR(30) | 選輸 可修改 | 其他證書號碼 | 否 |
| ID Number | CHAR(30) | 必輸 可修改 | 證書號碼 | 否 |
| Registered Office Address (Only for Senior Office, Director, Authorised Representative) | CHAR(210) | 選輸 可修改 | 公司註冊地址 （只使用於高級管理人員、董事、獲授權代表） 如果修改了地址中的國家/地區，則需要做Name Screening檢查。 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 否 |
| Principal Business Address (Only for Senior Office) | CHAR(210) | 選輸 可修改 | 主要營業地址 （只使用於高級管理人員） 如果修改了地址中的國家/地區，則需要做Name Screening檢查。 第一行：室/房/層——對應核心English Address 6——長度char（35） 第二行：大廈——對應核心的English Address 4和English Address 5——長度char（70） 第三行：街道——對應核心的English Address 2和English Address 3——長度char（70） 第四行：國家——對應核心的English Address 1（對應上面的第六行）——長度char（35） | 否 |
| Listing on Stock Exchange | CHAR(30) | 選輸 可修改 | 是否上市公司 枚舉值：Y:Yes，N:No | 否 |
| Name(s) of Stock Exchange | CHAR(120) | 選輸 可修改 | 交易所名稱 | 否 |
| **Related Documents** |  |
| Business registration certificate |  | 上傳 | 上傳商業登記證書，可下載 | 是 |
| Certificate of incorporate |  | 上傳 | 上傳公司註冊證書，可下載 | 是 |
| NAR1 |  | 上傳 | 上傳最新的周年申报表，可下載 | 是 |
| M&A |  | 上傳 | 上傳公司组织章程，可下載 | 是 |
| Form1(a) |  | 上傳 | 上傳香港税务局商业登记署发出的独资经营商号，可下載 | 是 |
| Form1(c) |  | 上傳 | 上傳香港税务局商业登记署发出的合伙经营商号，可下載 | 是 |
| Other |  | 上傳 | 非上述類型的文件，則上傳在Other項中。 | 是 |
| **操作** |
| Save |  |  | 保存按鈕，點擊則生成草稿保存當前修改的信息 |  |
| Risk Scoring |  |  | 點擊則系統根據錄入的信息跑風險評估，並跳轉到risk scoring頁面。 |  |
| Back |  |  | 點擊則返回列表頁面。 |  |
|  |

TE/PR Risk Scoring頁面

| **字段** | **字段長度** | **屬性** | **概述** | **E****OB****字段？** |
| --- | --- | --- | --- | --- |
| **Overview** |  |
| Client Type | CHAR(1) | 顯示 | 客戶類型 P - PERSONAL CUSTOMER C - COMPANY CUSTOMER F - FINANCIAL INSTITUTION CUSTOMER |  |
| Client Number | CHAR(10) | 顯示 | 客戶號 |  |
| ID Type | CHAR(3) | 顯示 | 證件類型 枚舉值： CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |  |
| ID Number | CHAR(30) | 顯示 | 證件號碼 |  |
| Company English Name | CHAR(60) | 顯示 | 公司英文名稱 |  |
| Company Chinese Name | CHAR(60) | 顯示 | 公司中文名稱 |  |
| Client Status | CHAR(3) | 顯示 | 存在多個狀態，點擊“Client Status”時彈窗顯示具體狀態 |  |
| RM | CHAR(60) | 顯示 | 對應的客戶經理 |  |
| Risk Rating | CHAR(3) | 顯示 | Risk scoring的風險等級，枚舉值： High risk Medium risk Low risk |  |
| **Initial Risk Assessment (System)**** ** |  |
| Initial Rating | CHAR(30) | 顯示 | 系統第一次跑出來的風險等級 | 是 |
| Initial Score | CHAR(30) | 顯示 | 系統第一次計算出來的CRA分數 | 是 |
| ** ****Final Risk Assessment ** |  |
| Latest Rating | CHAR(30) | 顯示 | 系統調整後的風險等級 | 是 |
| Latest Score | CHAR(30) | 顯示 | 系統重新計算後的CRA分數 | 是 |
| **# E-onboarding**** Review Indicator** |  |
| Review Indicator | CHAR(30) | 顯示 | 顯示系統檢查結果，將命中的檢查項顯示在這裡 | 是 |
| **Company information** **頁面中間這部分位置顯示****2.5.1****章節****創建T****E****/****PR****單****頁面中的所有字段** |  |
| **Customer Risk****（該模塊可展開或者收起）** |  |
| **Main nature of business/industry** | 是 |
| Result | CHAR(200) | 顯示 | 显示文案 Low Risk Business Medium Risk Business High Risk Business Prohibited Business | 是 |
| Detail | CHAR(200) | 顯示 | 顯示公司的行業 例如：“TRDG477499 - Other miscellaneous retail sale of new goods n.e.c.” | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 如果Result字段為Prohibited Business，則不顯示具體分數，顯示為“Prohibited”。 | 是 |
| **Date of incorporation** | 是 |
| Result | CHAR(200) | 顯示 | 显示文案 >5 years >=1且<=5 years < 1 year | 是 |
| Detail | CHAR(200) | 顯示 | 顯示公司成立日期 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Entity type/Ownership structure** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： 1) Limited Company 2) Sole Proprietorship 3) Partnership 4) Private Company 5) Unincorporated Company 6) ERFI 7) SOE 8) Trust 9) Nominee | 是 |
| Detail | CHAR(200) | 顯示 | 顯示公司類型 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Bearer Shares Issued** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： Yes No | 是 |
| Detail | CHAR(200) | 顯示 | 當Result字段的只為YES時，顯示文案“Bearer shares issued in the ownership structure” 當Result字段的只為NO時，顯示文案“No bearer shares issued in the ownership structure” | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Complex Structure** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： Yes No | 是 |
| Detail | CHAR(200) | 顯示 | 當Result字段的只為YES時，顯示文案“Company has a complex structure” 當Result字段的只為NO時，顯示文案“Company does not have a complex structure” | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Country of incorporation/ registration** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容 Low Risk Country Medium Risk Country High Risk Country Prohibited Country | 是 |
| Detail | CHAR(200) | 顯示 | 根據註冊地址中的國家/地區字段顯示 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 如果Result字段為Prohibited Country，則不顯示具體分數，顯示為“Prohibited”。 | 是 |
| **Countries of operation/business** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容 Low Risk Country Medium Risk Country High Risk Country Prohibited Country | 是 |
| Detail | CHAR(200) | 顯示 | 顯示客戶在pweb輸入的主要運營國家顯示 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 如果Result字段為Prohibited Country，則不顯示具體分數，顯示為“Prohibited”。 | 是 |
| **Countries of supplier (****>****20%)** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容 Low Risk Country Medium Risk Country High Risk Country | 是 |
| Detail | CHAR(200) | 顯示 | 顯示客戶在pweb輸入的供應商國家，支持顯示多個國家，以及該國家對應的風險級別和百分比，例子： “high risk - Iran - 7% high risk - Iraq - 8% low risk - Hong Kong - 5%”。 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Countries of buyer (****>****20%)** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容 Low Risk Country Medium Risk Country High Risk Country | 是 |
| Detail | CHAR(200) | 顯示 | 顯示客戶在pweb輸入的買方國家，支持顯示多個國家，以及該國家對應的風險級別和百分比，例子： “high risk - Iran - 7% high risk - Iraq - 8% low risk - Hong Kong - 5%”。 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Usage of high risk products** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： Low Risk Product Only At Least One Medium Risk Product At Least One High Risk Product | 是 |
| Detail | CHAR(200) | 顯示 | 顯示內容： Low Risk Product Only At Least One Medium Risk Product At Least One High Risk Product | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Tax Residency** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： One Tax Residency More Than One Tax Residency | 是 |
| Detail | CHAR(200) | 顯示 | 顯示客戶在pweb選擇的納稅地區 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **Onboarding channel** | 是 |
| Result | CHAR(200) | 顯示 | 顯示內容： Solely E-Onboarding Channel Walk-in customer Approved Business Partner Referral Internal customer RM Referral | 是 |
| Detail | CHAR(200) | 顯示 | 顯示渠道名稱 | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示分數 | 是 |
| **High Risk Remarks** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果命中黑名單，則顯示“Hit Blacklist” 2、如果沒有命中，則顯示” No Hit” | 是 |
| Initial Score | CHAR(30) | 顯示 | 顯示第一次Risk Scoring的分數 | 是 |
| Assessment Score | CHAR(30) | 顯示 | 顯示調整後的分數 | 是 |
| Action |  |  | Detail鏈接，點擊彈窗查看詳情 | 是 |
| High Risk Remarks | CHAR(1) | 選項 | 1、枚舉值： AML Reference:如果存在相關人士命中AML Internal Watchlist (PRI) 名單，則系統會自動勾選這項。（系統自動勾選） Closely Monitoring (User Input)：如果人工審批過程中存在一些Closely Monitoring 事項，則RM可以手工勾選該項。（人工選輸） Others：有其他高風險事項，RM可以手工勾選該項。（人工選輸） 2、上述選項支持多選 3、勾選項，當RM勾選Others之後，則顯示下面的Remark For Other輸入框 | 是 |
| Remark For Other | CHAR(60) | 可編輯 | 輸入框 | 是 |
| **Hit Company Secretary Address** |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案“Hit Company Secretary Address”。 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Score | CHAR(200) | 顯示 | 顯示Risk Scoring的分數 | 是 |
| Action |  |  | RM可以做discounting TRUE Hit FALSE Hit | 是 |
| **Sanctions Exposure** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案“Hit Sanctions Exposure”。 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Initial Score | CHAR(200) | 顯示 | 顯示Risk Scoring的分數 | 是 |
| Assessment Score | CHAR(30) | 顯示 | 顯示調整後的分數 | 是 |
| Action |  |  | Detail鏈接，點擊彈窗查看詳情 | 是 |
| **PE****P** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案“Hit PEP”。 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Initial Score | CHAR(200) | 顯示 | 顯示Risk Scoring的分數 | 是 |
| Assessment Score | CHAR(30) | 顯示 | 顯示調整後的分數 | 是 |
| Action |  |  | Detail鏈接，點擊彈窗查看詳情 | 是 |
| **Adverse News** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案“Hit Adverse News”。 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Initial Score | CHAR(200) | 顯示 | 顯示Risk Scoring的分數 | 是 |
| Assessment Score | CHAR(30) | 顯示 | 顯示調整後的分數 | 是 |
| Action |  |  | Detail鏈接，點擊彈窗查看詳情 | 是 |
| **Special List** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案 如果命中BP名單，則命中結果顯示為“True Hit_Bankruptcy Petition-Fail” 如果命中BO名單，則命中結果顯示為“True Hit_Bankruptcy Order-Fail” 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Assessment Result | CHAR(30) | 顯示 | 顯示RM調整後的結果 | 是 |
| Action |  |  | 鏈接，點擊彈窗查看詳情 | 是 |
| **AML**** Black List** | 是 |
| Result | CHAR(200) | 顯示 | 1、如果有命中則顯示文案“Hit AML Black List”。 2、如果沒有命中則顯示文案“No Hit”。 | 是 |
| Assessment Result | CHAR(30) | 顯示 | 顯示RM調整後的結果 | 是 |
| Action |  |  | 鏈接，點擊彈窗查看詳情 | 是 |
|  |
| Trigger Event reason | CHAR(2) | 必輸 | 枚舉值 01-Trigger By Bank 02-Trigger By Customer |  |
| Trigger Event Review Reason | CHAR(2) | 必輸 | 枚舉值： 01-Open additional account or apply for additional products or services 02-Dormant/inactive account reactivation (by account); 03-Change of customer risk rating 04-Change of customer name 05-Change in nature of business 06-Change of jurisdiction of registered address, principal place of business or correspondence address 07-Change of beneficial owner(s) / ownership structure (applicable to beneficial ownership of at least 25% or more) 08-Change of Directors 09-Change of authorized signatories; <Ops trigger manually> 10-Requests to change the name of BO/ Director and Authorized Signor 11-Significant change in transaction behavior; <Ops trigger manually> 12-Changes in customer’s Tax risk 13-Transactions that are suspected to be involved with terrorist financing and money laundering crimes; <Ops trigger manually> 14-Transactions that are not commensurate / not making economic sense with KYC profile <Ops trigger manually> 15-Any suspicious transactions or unusual activities identified; <Ops trigger manually> 16-True Screening Hit (Sanctions-Related) 17-True Screening Hit (PEP-Related) 18-True Screening Hit (Material ML&TF Adverse News Related) 19-When the Bank doubts that the veracity or adequacy of any information previously obtained for the purpose of ID&V <Ops trigger manually> 20-At the request of the AML Compliance <Ops trigger manually> 21- Othera |  |
| Trigger Event Review Reason Comments | CHAR(200) | 選輸 | 當Trigger Event Review Reason為other時必輸 |  |
| Full Review Flag | CHAR(1) | 必輸 | 枚舉值：Y:Yes，N:No | 否 |
| Full Review Comments | CHAR(200) | 必輸 | Free text | 否 |
| Case  Comments | CHAR(4000) | 選輸 |  |  |
| **Customer Attachment** |
| Document name | CHAR(200) | 顯示 |  | 是 |
| Upload Time | NUM(14) | 顯示 |  | 是 |
| **Bank Supplement Attachment** |
| **Attachment（上傳文件列表）** | 是 |
| Document type | CHAR(30) | 顯示 |  | 是 |
| Document name | CHAR(200) | 顯示 |  | 是 |
| Upload By | CHAR(30) | 顯示 |  | 是 |
| Upload Time | NUM(14) | 顯示 |  | 是 |
| Download |  | 按鈕 | 操作按鈕，點擊下載文檔 | 是 |
| Preview |  | 按鈕 | 預覽 | 是 |
| **操作選項** |  |
| **No**** O****bjection** |  |  | 審批通過 | 是 |
| **R****eject** |  |  | 審批拒絕 | 是 |
| **P****ass**** ****on** |  |  | 同角色之間的轉派 | 是 |
| **操作按鈕** |
| Risk Scoring |  | 按鈕 | 重跑風險評估 | 是 |
| **S****ubmit** |  | 按鈕 | 當RM點擊Submit 按鈕，則提交該筆訂單 | 是 |
| Save |  | 按鈕 | 保存按鈕，點擊則生成草稿保存當前修改的信息 | 否 |
| Cancel |  | 按鈕 | 取消訂單 | 否 |
| Back |  | 按鈕 | 點擊則返回列表頁面。 | 否 |

顯示客戶的交易歷史頁面

輸入字段

| **字段** | **字段長度** | **屬性** | **概述** |
| --- | --- | --- | --- |
| Start Date | NUM(8) | 必輸 | 開始日期 查詢核心交易日期 |
| End Date | NUM(8) | 必輸 | 結束日期 查詢核心交易日期 |
| Account Number | CHAR（12） | 選輸 | 賬戶號碼 |
| Journal No. | NUM(9) | 選輸 | 銀行流水號 |
| DR/CR Flag | CHAR(1) | 選輸 | 借貸標誌 DR CR ALL |
| Sorting Flag | CHAR(1) | 必輸 | 排序標識，默認正序，可修改 正序，時間最新的在前面 倒序，時間最新的在後面 |

- 輸出字段 

| **字段** | **字段長度** | **屬性** | **概述** |
| --- | --- | --- | --- |
| Trade Date |  | 顯示 | 交易日期 |
| Journal No. |  | 顯示 | 銀行流水號 |
| Account Number |  | 顯示 | 賬戶號碼 |
| Account Name |  | 顯示 | 賬戶名稱 |
| Virtual Account No. |  | 顯示 | 子賬戶號 |
| Transfer Amount |  | 顯示 | 金額 |
| Currency |  | 顯示 | 幣種 |
| DR/CR Flag |  | 顯示 | 借貸標誌 1- DR 2- CR |
| Reciprocal Account |  | 顯示 | 對方帳號 |
| Reciprocal Account Name |  | 顯示 | 對方賬戶名稱 |
| Reciprocal Bank Code |  | 顯示 | 對方銀行代碼號 |
| Trade Time |  | 顯示 | 交易時間 |
| Transaction Type |  | 顯示 | 交易類型 通過該字段區分交易渠道 |
| Transaction Channel |  | 顯示 | 交易渠道 入金或者出金的渠道（本行、FPS、CHATS、SWIFT等） |
| Remark |  | 顯示 | 備註 |

TE/PR查詢列表

輸入字段

| **字段** | **字段長度** | **屬性** | **概述** |
| --- | --- | --- | --- |
| Start Date | NUM(8) | 必輸 | 開始日期 查詢訂單創建日期 |
| End Date | NUM(8) | 必輸 | 結束日期 查詢訂單創建日期 |
| Client No. | CHAR(10) | 選輸 | 客戶號 |
| ID Type | CHAR(3) | 選輸 | 公司證件類型，枚舉值如下 CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |
| ID Number | CHAR(30) | 選輸 | 公司證書號碼，支持模糊搜索 |
| Company Chinese Name | CHAR(60) | 選輸 | 支持通過公司中文名稱篩選，支持模糊搜索 |
| Task Name | CHAR(10) | 選輸 | 隊列名稱 |
| Case  Status | CHAR(1) | 選輸 | 訂單狀態 Active Open Approve Reject Cancel |
| Handler | CHAR(30) | 選輸 | 當前審核節點的處理人員名稱 手工輸入域賬號名稱 |

- 輸出字段 

| **字段** | **字段長度** | **屬性** | **概述** |
| --- | --- | --- | --- |
| Create Date | NUM(8) | 顯示 | 訂單創建日期 |
| Client No. | CHAR(10) | 顯示 | 客戶號 |
| ID Type | CHAR(3) | 顯示 | 公司證件類型，枚舉值如下 CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |
| ID Number | CHAR(30) | 顯示 | 公司證書號碼，支持模糊搜索 |
| Company Chinese Name | CHAR(60) | 顯示 | 支持通過公司中文名稱篩選，支持模糊搜索 |
| Case Due Date | NUM(8) | 顯示 | 訂單過期日期 |
| No. Days Remaining | NUM(3) | 顯示 | 距離過期日期天數 |
| Case  Status | CHAR(1) | 顯示 | 訂單狀態 Draft Active Open Complete |
| Handler | CHAR(30) | 顯示 | 當前審核節點的處理人員名稱 手工輸入域賬號名稱 |
| Last Handler | CHAR(30) | 顯示 | 上一節點的處理人員 |
| Task Name | CHAR(10) | 顯示 | 隊列名稱 |

Periodic Review批量處理

- 系統後台配置周期審核的參數，參數根據風險等級設置不同的審核周期，具體配置參數如下

| **Risk level** | **Periodic review time period** |
| --- | --- |
| High | +12月 -1天 |
| Medium | +24月 -1天 |
| Low | +36月 -1天 |

- 系統根據Periodic Review的週期自動生成待審批訂單，放在All Pending List和Periodic Review列表中，在All Pending List中可以進行Claim操作，在Periodic Review列表中可以查看詳情。

- 在每個客戶的“Next Review Date”的前3個月（按對年對月對日計算）的凌晨1點，系統自動產生Periodic Review訂單，並且系統自動通過郵件組通知RM maker池（產生訂單當天9:00才發送通知）和RM郵件組；

如果系統已經存在Trigger Event訂單，則系統允許繼續自動生成Periodic Review訂單，如果系統已經存在手工創建的Periodic Review訂單，則系統不再自動生成Periodic Review訂單。

**通知文案****1****：****(**模板編碼： PR_Approval_Remind_All**)**

**E****mail標題：****N****ew periodic review order ****pending**** ****approval**

“There is a new periodic review order to be approved, please claim it as soon as possible,thanks!

Case number: {Case_number}

Company name: {Company_name}

The order must be completed before {Next_review_date}”

- 對於後續的審批流程中，當流程流轉到RM Checker/AML Maker/AML Checker, 或者 return to RM Maker時，需要給相應的maker或者checker人員發送郵件提醒通知。（即時通知）

- 如果該節點還沒有人claim過，則發送給該節點用戶池中的所有用戶（使用通知文案1），包括RM郵件組。

- 如果該節點已經有claim過了，則只發送給之前claim過這筆訂單的用戶（使用通知文案2）。

**通知文案1：****(**模板編碼： PR_Approval_Remind_All**)**

**E****mail標題：****N****ew periodic review order ****pending**** ****approval**

“There is a new periodic review order to be approved, please claim it as soon as possible,thanks!

Case number: {Case_number}

Company name: {Company_name}

The order must be completed before {Next_review_date}”

**通知文案****2****：****(**模板編碼： PR_Approval_Remind_Claim_User**)**

**E****mail標題：****P****eriodic review order ****return ****t****o you for update**

“There is a periodic review order eturn to you for update, please complete the approval as soon as possible,thanks!

Case number: {Case_number}

Company name: {Company_name}

The order must be completed before {Next_review_date}”

- 同時在生成Periodic Review當天發送消息通知（SMS+EMAIL）給公司的聯絡人，發送時間為生成該筆Periodic Review訂單當天上午10點鐘。

**通知文案****3****：****(**模板編碼： PR_Remind_Contact_Person**)**

|  | **简体中文** | **繁体中文** | **英文** |
| --- | --- | --- | --- |
| SMS | 【众安银行】我行将对你的公司进行尽职审查年度重检，如果贵公司有任何资料需要改动，请联系你的客户经理或者客户服务热线提供相关资料。若有任何疑问，请随时致电客户服务热线3665 3665联络我们。 | 【ZA Bank】我行將對你的公司進行盡職審查年度重檢，如果貴公司有任何資料需要改動，請聯絡你的客戶經理或者客戶服務熱線提供相關資料。若有任何疑問，請隨時致電客戶服務熱線3665 3665聯絡我們。 | [ZA Bank] We will soon conduct the annual customer due diligence review of your company. If your company has any connected parties, industry or other changes, please contact your customer manager or customer service hotline and provide the corresponding change information. If you have any questions, please contact us at 3665 3665. |
| Email標題 | 客户尽职审查年度重检通知 | 客戶盡職審查年度重檢通知 | Annual customer due diligence review notification |
| Email | 亲爱的客户:     我行将对你的公司进行尽职审查年度重检，如果贵公司有任何资料需要改动，请联系你的客户经理或者客户服务热线提供相关资料。若有任何疑问，请随时致电客户服务热线3665 3665联络我们。  ZA Bank | 親愛的客戶: 我行將對你的公司進行盡職審查年度重檢，如果貴公司有任何資料需要改動，請聯繫你的客戶經理或者客戶服務熱線提供相關資料。若有任何疑問，請隨時致電客戶服務熱線3665 3665聯絡我們。 ZA Bank | Dear Valued Customer: We will conduct the annual customer due diligence review of your company soon. If your company has any connected parties, industry or other changes, please contact your customer manager or customer service hotline and provide the corresponding change information. If you have any questions, please contact us at 3665 3665. ZA Bank |

- 如果一筆“Periodic Review”訂單一直未被某個審批節點（RM Maker/RM Checker/AML Maker/AML Checker）Claim過，則從進入該審批節點的單日開始，每隔5天給該審批節點的用戶池（權限平台的所有用戶+RM郵件組）發送提醒通知；如果兩次通知的間隔跨越了“Next Review Date”，則需要在“Next Review Date”當天通知一次，過了“Next Review Date”還需要每日繼續通知該審批節點，每次通知時間為上午9:00發送通知（使用通知文案4）； 

**通知文案****4****：****(**模板編碼： PR_Approval_Remind_UnClaim_Timed_Task**)**

**E****mail標題：****N****ew periodic review order ****pending**** ****approval****(****more than {wating days} days****)**

“There is a periodic review order was never claimed more than {wating days} days, please claim it as soon as possible,thanks!

Case number: {Case_number}

Company name: {Company_name}

The order must be completed before {Next_review_date}”

- 如果一筆訂單被Claim之後未被審批完，則每隔5天發送定時通知提醒該節點的claim用戶進行審批（使用通知文案5）

**通知文案****5****：****(**模板編碼： PR_Approval_Remind_Claim_User_Timed_Task**)**

**E****mail標題：****P****eriodic review order ****pending**** ****approval****(****more than {wating days} days****)**

“There is a periodic review order waiting for your approval more than {wating days} days, please complete the approval as soon as possible,thanks!

Case number: {Case_number}

Company name: {Company_name}

The order must be completed before {Next_review_date}”

- Periodic review需要支持系統自動（Periodic review Batch）和手工發起（Periodic review）。

Trigger Event上線後生產數據處理

- 因為在4月4日系統更新了CRA分數，因此對於沙盒期間成功開戶的100個客戶以及0404之前RM開立的所有線下客戶，系統需要自動批量生成Trigger Event訂單，由RM對這些Case進行Review。 

- 對於6月7日的行業列表更新涉及的行業，系統需要自動對受影響的客戶，系統需要自動批量生成Trigger Event訂單，由RM對這些Case進行Review。

第 8 页 共 82 页

ZA Bank第 9 页 共 82 页

ZA Bank

ZA Bank 第 82 页 共 82 页