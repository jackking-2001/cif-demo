Customer Information File

客户信息管理系统

产品需求说明书

 [[BANK-80663](https://jira.in.za/browse/BANK-80663)]
客户信息查询模块

内部资料 注意保密
v1

文档修改历史记录

| 版本 | 修订时间 | 修订内容 | 修订人员 | 审核人员 |
| --- | --- | --- | --- | --- |
| 1.0 | 2025/08/20 | 客户信息查询模块 | Sam Guo | -- |

**目录：**

1	CIF - 客户信息管理系统	7

1.1	系统定位与范围	7

1.1.1	定位	7

1.1.2	范围	8

1.1.3	逻辑关系图	8

1.1.4	模型结构	9

1.1.4.1	- 主档模型（party）	9

1.1.4.2	- 证件模型（party_identity）	10

1.1.4.3	- 个人档案模型（party_individual_profile）	13

1.1.4.4	– 机构档案模型（party_organization_profile）	15

1.1.4.5	- 关系模型（party_relationship）	17

1.1.4.6	- 角色定义模型（party_role）	18

1.1.4.7	- 地址模型（party_address）	19

1.1.4.8	- 税务模型（party_tax_residency）	20

1.1.4.9	- 零售域专用信息模型（party_retail_specific_information）	20

1.1.4.10	– 对公域专用信息模型（party_business_specific_information）	21

2	零售客户管理	21

2.1	客户查询	21

2.1.1.1	客户查询条件	22

2.1.1.2	客户列表展示功能	23

2.1.2	客户详情页面功能	26

2.1.2.1	客户主要信息	27

2.1.2.1.1	Tab-基本信息	28

2.1.2.1.2	Tab-基本信息-国籍信息	31

2.1.2.1.3	Tab-基本信息-税务信息	33

2.1.2.1.4	Tab-基本信息-地址信息	36

2.1.2.1.5	Tab-基本信息-就业信息	39

2.1.2.2	Tab-资产概览	45

2.1.2.3	Tab-风险评估记录	45

2.1.2.4	Tab-人脸识别记录	45

2.1.2.5	Tab-eKYC记录	45

2.1.2.6	Tab-BlockCode记录	45

2.1.2.7	Tab-登录记录	45

3	对公客户管理	45

4	验收场景	46

5	大数据需求	46

6	客服需求	46

7	运营需求	46

8	数据埋点需求	47

9	版本兼容需求	47

10	历史数据迁移需求	47

11	其他附件	47

- CIF - 客户信息管理系统

CIF目的是建立一个可持续、可规模化的全行客户信息管理平台，提升客户生命周期管理的能力，并加强各部门的协同， 如零售客户和对公客户数据的统一等等。 项目关键点是客户信息从现有的核心系统（Hisun）抽离出来，并搭建涵盖客户信息管理、风险管理、产品和服务、账户管理、年检、休眠账户、销户、运营流程审批、合规与数据安全， 监管报告等多个核心功能模块的系统。包括：

- 建立统一的客户主档（Party Master），支持 个人客户（Individual） 与 机构客户（Organization） 的集中管理；

- 支持客户生命周期全程（开户、维护、年检、销户）信息统一；

- 建立客户关系与角色体系，支持跨业务系统调用；

- 支撑合规与反洗钱（AML/FATCA/CRS）风险评估；

- 支撑零售（Retail）与对公（Business Banking）两个域客户信息管理。

- 系统定位与范围

- 定位

CIF 作为全行级客户信息主档系统，提供客户基础信息、关系信息、风险信息等统一数据服务，成为：

- 客户身份与关系信息的权威来源；

- 为其他系统（开户Onboarding、账号Account（HiSun）、AML（CAS）、CRM（ARK）、贷款（Loan）、基金股（Invest）、ZA Card、BIB、對公KYC、AM系統等）提供统一数据；

- 支撑AML（CRA、Name Screening）、Trigger Event、Periodic Review、Dormant Account、CRS、FATCA、等业务流程。

- 范围

CIF（Customer Information File）作为全行的客户主数据平台，采用 Party 主档模型（Party + Profile + Attribute + Domain Extension） 的分层体系，以支持个人与机构客户的统一建档、持续维护及多业务域共享。该模型确保数据的一致性、可扩展性及监管合规性，覆盖开户、KYC、AML、风险管理及客户生命周期管理等核心业务。

- 逻辑关系图

Party 主档层

└─ party

- 统一客户主键 cif_id；

- 支持自然人、机构两类主体；

- 作为所有 Profile、Attribute、Relationship 的唯一外键来源

Party 主体关系层

├─ party_relationship

└─ party_role

用于描述“(主体)谁与(主体)谁存在什么关系”以及角色类型：

典型场景：

- 自然人与公司之间的 Connected Party / UBO 关系等

- 客户在产品中的角色

Profile 主体档案层

├─ party_individual_profile

└─ party_organization_profile

- 描述主体本身的核心档案信息，区分自然人与机构两类 Profile。

- 仅承载相对稳定的档案字段（如姓名、性别、出生日期、PEP 状态、客户类型、风险等级等）

- 可变、更易多条或需独立审计的信息下沉到 Attribute 层或业务域扩展层；

Attribute 信息层

├─ party_identity                    （证件信息：HKID / 护照 / BR 等）

├─ party_contact_information         （联系方式：电话、邮箱等）

├─ party_address                     （地址主表）

│   ├─ party_address_mainland_detail （大陆地址明细）

│   └─ party_address_overseas_detail （香港 / 海外地址明细）

├─ party_nationality                 （国籍信息，最多 4 个国籍）

├─ party_tax_residency               （税务居民国家 + TIN，最多 4 条）

├─ party_fatca_profile               （FATCA 视角 Profile：US indicia / W8/W9 等）

├─ party_crs_profile                 （CRS 视角 Profile：Self-Cert 声明、地址一致性等）

├─ party_individual_employment_profile （自然人就业 / SOF / SOW / 预期交易性质）

├─ party_organization_change_history_profile  （記錄公司名稱，公司行業，UBO以及股權結構等信息的變更歷史）

├─ party_organization_counterparty_profile    （記錄公司交易對手信息，包括買方賣方，資產所在國家/地區等）

├─ party_organization_asset_profile           （記錄客戶財富資金來源信息）

├─ party_organization_anticipated_txn_profile （記錄公司每月預期交易的轉賬類型，貨幣和金額）

└─ party_organization_anticipated_txn_country_profile （記錄公司預期做跨境交易的資金轉入轉出國家）

该层为 属性级 信息，以独立表维护可变更、可多条、需进行合规验证 / 风险规则评估的客户信息，是 CIF 最核心的数据细分结构。设计原则：

- 支持多条记录（如 4 个国籍、4 个税务居民国、多地址）

- 满足 AML/KYC/FATCA/CRS 监管要求

- 具备版本管理能力与渠道来源标记

- Attribute 层的数据直接影响客户风险等级、年审周期与账户风险策略

Domain Extension 业务域扩展层

└─ party_retail_specific_info      （零售域专用信息：App 用户、营销同意等）

└─ party_business_specific_info    （对公域专用信息：主要是对公网银部分的信息管理，本次需求暂不改造。）

零售域专用信息：

- App 登录名、昵称、头像、联系方式（加密）、渠道功能启用状态；

- 营销同意、语言与时区偏好、通知与安全配置等；

- 不承载 KYC / 身份证明 / CRS / FATCA / 风险评级等信

对公域专用信息 / 机构扩展信息，用于存放对公网银渠道的数据：

Domain Extension 层用于承载“业务域特有”的数据，避免污染核心 Party 主档及通用 Attribute 层，在不影响主档模型稳定性的前提下，支持零售域与对公域的灵活扩展。

- 模型结构

-  - 主档模型（party）

party 模型是 CIF 的 最小实体主档，代表全行最核心的“主体（Party）”抽象：

- 无论自然人（Individual）还是机构（Organization），在本表中均 有且仅有一条记录（一人一码 / 一企一码）；

- 所有下游对象（账户、KYC、AML、FATCA/CRS、年检、Dormant、对公 Connected Party 等）都通过 cif_id 与主体或主体的角色进行关联；

- party 仅负责 “主体存在 + 类型 + 主证件引用 + 生命周期状态”，不承载姓名、证件面信息、地址、风险、税务等业务属性。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 全行唯一主键。一人一码 / 一企一码，在所有系统中作为客户主键引用。由 CIF 系统统一生成，不允许修改。 |
| party_type | ENUM('INDIVIDUAL','ORGANIZATION') | 主体类型：自然人 / 机构。决定其对应的 Profile 模型（party_individual_profile 或 party_organization_profile）。 |
| primary_identity_id | BIGINT UNSIGNED | 当前主证件的 ID，引用 party_identity.id。仅做“指针引用”，不直接存储证件明细。必须与 party_identity.is_primary=1 的记录一致。 |
| status | ENUM('ACTIVE','CLOSED') | 主档业务状态：ACTIVE=正常；CLOSED=主体已关闭（例如全行销户后软关闭）。用于控制是否允许新开账户、是否继续进行 KYC/年检。 |
| closed_at | DATETIME NULL | 主体被标记为 CLOSED 的时间，用于满足 7 年留存及审计追溯。ACTIVE 状态可为空。 |
| created_at | DATETIME | 记录创建时间（首次建档时间）。由系统生成。 |
| updated_at | DATETIME | 最近一次主档记录更新时间（状态或主证件引用变更时更新）。 |

说明：

- 任何人在全行系统中只能生成一个 cif_id（无论多少个开户渠道、多少账户产品），机构亦同理，一个 BR/CI/LE 号对应一个主体。

- cif_id 不随姓名、证件、地址变更而变化，支持证件换发、多证件并存，均不影响主档记录。

- 下游 KYC / AML / FATCA / CRS / Dormant / 年检均以 cif_id 为唯一连接点。

主档必须保证：

- 唯一性检查（基于证件号、出生日期、姓名、机构注册号等）。

- 绑定主证件 identity。

- 销户关闭 Account，但 party 继续存在（满足7年记录保存要求）。

- party 不允许删除，只允许标记关闭。

-  - 证件模型（party_identity）

party_identity 属于 CIF 的 Attribute 信息层，用于管理主体（自然人 / 机构）的证件事实（Identity Facts），包括：

- 证件类型、签发国家、有效期等静态属性；

- 证件号、主证件标记；

- 证件面姓名 / DOB / 性别等“证件原文信息”；

- OCR / NFC / 第三方供应商的核验方式及结果；

- 证件影像与证件人像头像。

设计原则：

- 一主体多证件：同一 cif_id 下可存在多条证件记录（如 HKID + Passport、BR + CI）。

- 保留证件面事实：记录证件当时的原文信息（包含证件面姓名 / 性别 / DOB / 头像），用于审计与还原事实。

- 主档分工清晰：主档姓名、出生日期、性别等 Golden Record 信息由 party_individual_profile / party_organization_profile 维护，本表不作为主档，只作为事实来源与证据。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | BIGINT UNSIGNED | 主键，自增 ID |
| cif_id | BIGINT UNSIGNED | 外键，引用 party.cif_id；同一主体可有多条证件记录 |
| identity_category | ENUM('INDIVIDUAL','ORGANIZATION') | 证件归类：自然人 / 机构 |
| identity_type | ENUM('HKID' ,'PRCID', 'PASSPORT', 'BR','CI','LEI','OTHERS') | 证件类型（涵盖常见个人/机构证件） 当前众安银行零售客户基于主证件类型（party_identity）来定义客户类型，香港客户是指identity_type = 'HKID', 简称HKID， 大陆访客是指identity_type = 'PRCID', 简称MCV。主证件类型是指is_primary = Y。 |
| identity_subtype | VARCHAR(32) NULL | 证件子类型或版本（如“永久性居民身份证 2018”等） |
| is_primary | TINYINT(1) | 是否为主证件；同一主体最多允许 1 条记录为主证件 |
| name_on_id_local | VARCHAR(256) | 证件面本地语种姓名（如中文法定名） |
| name_on_id_english | VARCHAR(256) | 证件面英文姓名或英文法定名 |
| dob_on_id | DATE NULL | 证件面出生日期（可空） |
| gender_on_id | ENUM('M','F','OTHER','UNKNOWN') NPRCULL | 证件面性别（可空） |
| id_portrait_photo | VARCHAR(512) | 证件人像头像路径（证件照片上的人像） |
| id_photo | VARCHAR(2048) NULL | 多张证件图片路径（如正反面），以英文逗号分隔 |
| id_video | VARCHAR(2048) NULL | eKYC/OCR 采集视频路径，以英文逗号分隔 |
| chinese_commercial_code | VARCHAR(256) NULL | 香港身份证中文电码（CCC，姓与名电码合并） |
| issue_date | DATE NULL | 证件签发日期 |
| issuing_country_code | CHAR(2) | 签发国家/地区（ISO-3166 Alpha-2） |
| issuing_authority | VARCHAR(128) NULL | 签发机关（如 Immigration Dept、公安部 等） |
| expiry_date | DATE NULL | 证件到期日期（可空） |
| identity_number | VARCHAR(64) | 证件号码，用于展示与人工核对；存储与保护方式由技术团队在 TRD 中定义 |
| verification_method | ENUM('OCR','NFC','MANUAL','API_SUPPLIER') | 本证件记录的核验方式 |
| verification_result | ENUM('PASSED','FAILED','NOT_VERIFIED') | 核验结果 |
| verification_raw | JSON NULL | 第三方供应商返回的原始核验数据（结构化 JSON） |
| updated_by_case_no | VARCHAR(64) NULL | 记录最近一次新增或更新该证件信息所对应的申请单号 / 审批单号（如开户申请单、年检 Case 等） |
| status | ENUM('ACTIVE','INACTIVE','EXPIRED') | ACTIVE=当前使用；EXPIRED=已过期；INACTIVE=替换/停用但保留历史 |
| remarks | VARCHAR(256) NULL | 人工备注（异常、说明等） |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 最近更新时间 |

要求：

- 多证件支持：同一 cif_id 下可有多条记录，不同类型（如 HKID、PASSPORT、EEP、BR、CI）并存。

- 主证件机制：is_primary=1 的记录即主证件和 party.primary_identity_id 应指向该记录的 id；规则保证两者一致（唯一主证件约束）。

- 存储的是证件本身的信息“证件面原文”（事实数据，逐张证件）。

- 影像字段存储规范：

- id_portrait_photo：单个头像照路径（例 /cif/9001/identity/hkid_portrait.jpg）

- id_photo：多个证件图片路径，以英文逗号,分隔（例 /xxxxxx_front.jpg,/xxxxxxx_back.jpg）

- id_video：多个 eKYC 视频路径（例 /ocr_scan_xxx.mp4,/ocr_scan.mp4）

- 证件有效性：证件过期、换发需保留历史记录而非覆盖。

- 审计要求：所有证件新增/变更需保留审计记录，并可与 Case NO关联。

切换主证件时，系统必须同时：

- 更新旧证件 is_primary=0；

- 更新新证件 is_primary=1；

- 更新 party.primary_identity_id 指向新证件。

-  - 自然人档案模型（party_individual_profile）

用于管理自然人主体的基础档案信息，是零售开户、身份识别、KYC、风险评级、年检（Periodic Review）与 Dormant 激活流程的核心基础数据。该模型通过 cif_id 与 party 关联，每个自然人主体仅对应 一条档案记录。

该模型只存储较稳定且能够代表个人属性的内容（如出生日期、性别、国籍分类、PEP 状态、客户类型、风险等级）。所有可变、可多条或需独立审计的字段（如证件、就业信息、地址、税务居民身份）均独立分布在 Attribute 层。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 主键 & 外键，引用 party.cif_id，一人一档，一对一关系 |
| full_name_english | VARCHAR(256) | 英文全名（姓 + 名），当前有效的主档英文姓名 |
| full_name_local | VARCHAR(256) | 本地语种姓名（如中文姓名），当前有效的主档本地姓名 |
| given_name | VARCHAR(128) | 名（可选字段，用于拆分 full_name） |
| surname | VARCHAR(128) | 姓（可选字段，用于拆分 full_name） |
| gender | ENUM('M','F','OTHER','UNKNOWN') | 客户主档性别（Golden Record） |
| date_of_birth | DATE | 客户出生日期（Golden Record） |
| place_of_birth | VARCHAR(128) NULL | 客户出生地国家，在客户在开户时申报的、不可变的自然人事实属性。 |
| residency_status | ENUM('HK_RESIDENT','NON_HK_RESIDENT','UNKNOWN') | 是否香港居民（用于区分本地 / 非本地客群） |
| pep_flag1 | TINYINT(1) | 是否 PEP关系人（以 FCC 评估为准） |
| pep_flag2 | TINYINT(1) | 是否 PEP关系人（以 FCC 评估为准） |
| us_person_flag | TINYINT(1) | 是否 US Person（由 FATCA 评估得出） |
| za_bank_employee_flag | TINYINT(1) | 是否 ZA Bank 员工 / 董事等内部关联人士 |
| customer_status | 从BlockCode列表获取C状态，比如C00，C11，C01等 | 客户自然人状态：正常 / 已销户 / 已故等（与原来的BlockCode模块对接） |
| risk_level | ENUM('LOW','MEDIUM','HIGH') | AML 客户风险等级（高 / 中 / 低），由 FCC / KYC 决定 |
| last_review_date | DATE NULL | 最近一次 KYC 年检完成日期（基于风险等级 1 / 3 / 5 年） |
| next_review_date | DATE NULL | 下一次年检到期日（系统按规则计算） |
| review_frequency | ENUM('ANNUAL','THREE_YEARS','FIVE_YEARS') NULL | 年检周期：高风险=ANNUAL、中风险=THREE_YEARS、低风险=FIVE_YEARS（可选字段） |
| kyc_completed_date | DATE NULL | 初始 KYC 完成时间（首次开户 / 客户启用时） |
| updated_by_case_no | VARCHAR(64) NULL | 最近一次更新本档案信息所对应的申请单号 / 审批单号 / 年检 Case No |
| remarks | VARCHAR(256) NULL | 档案备注（如特别说明、异常情况等） |
| created_at | DATETIME | 记录创建时间（首次建档时间） |
| updated_at | DATETIME | 最近一次更新本记录的时间 |

定位上，它只承载 “相对稳定、不可多条、与自然人本体强相关” 的档案属性，例如：

- 姓名（英文 / 本地语种）、性别、出生日期、出生地

- 客户是否为 PEP、US Person、ZA Bank 员工

- AML 客户风险等级（高 / 中 / 低）

- 年检相关信息（最近一次年检时间、下一次年检到期时间）

而以下信息 不在本模型中维护：

- 证件事实 → party_identity

- 地址 → party_address + detail 模型

- 联系方式 → party_contact_information

- 国籍 → party_nationality

- 税务居民 / TIN → party_tax_residency

- FATCA / CRS → party_fatca_profile / party_crs_profile

- 就业 / 收入 / SOF / SOW / 预期交易性质 → party_individual_employment_profile

一句话总结：profile 负责“Golden Record 的自然人档案”，不负责多条、可变的属性维度。

-  – 机构档案模型（party_organization_profile）

管理机构 organization 的法定属性与合规关键字段（成立信息、公司类型、注册地、存续状态、行业等），作为“这是一家什么公司”的基准信息，与 party_identity 的公司证照（如 BR、CI 等）解耦：证照与影像仍在 party_identity，本表仅保存主值与引用（primary_identity_id），对外统一展示与供风控/报表使用。

| 字段名 | 类型 | 约束/默认 | 说明 |
| --- | --- | --- | --- |
| cif_id | CHAR(3) | party.cif_id | 与主体绑定 |
| Company_Type | CHAR(2) | 必輸 | 公司类型 枚舉值：参考12.1 Company Type数据字典 |
| Company_English_Name | CHAR(60) | 必輸 | 公司英文名称 |
| Company_Chinese_Name | CHAR(60) | 選輸 | 公司中文名称 |
| Trading_Name _(English)*10 | CHAR(60) | 選輸 | 英文營業名稱（最多支持10个） |
| Trading_Name_(Chinese)*10 | CHAR(60) | 選輸 | 中文营业名称（最多支持10个） |
| Channel | CHAR(1) | 必輸 | 開戶渠道 枚舉值： O: Solely E-Onboarding Channel W: Walk-in customer A: Approved Business Partner Referral I: Internal customer B: RM Referral |
| Account_Officer | CHAR(10) | 選輸 | 客戶經理代碼 |
| Referral_Party | CHAR(60) | 選輸 | 開戶推薦人名稱 开户渠道是RM开户的，才需要展示该字段且必输 |
| Risk_Level | CHAR(1) | 必輸 | 风险級別 枚举值：H-High Risk；M-Medium Risk；L-Low Risk |
| Last_review_date | NUM(8) | 必輸 | 上次审查日期 |
| Next_review_date | NUM(8) | 必輸 | 下次审查日期 |
| Company_Establish_Date | NUM(8) | 必輸 | 公司成立日期 |
| Country_of_Registration | CHAR(3) | 必輸 | 注册国家 枚举值：参考数据字典 |
| Listing_Company | CHAR(30) | 選輸 | 是否上市公司 枚舉值：Y:Yes，N:No |
| High_Tax_Evasion_Risk | CHAR(1) | 必輸 | 枚舉值：Y:Yes，N:No |
| Name(s)_of_Stock_Exchange | CHAR(120) | 選輸 | 证券交易所名称 |
| Bearer_Shares_Issued | CHAR(1) | 必輸 | 是否可以發行/已經發行不記名股票 枚舉值：Y:Yes，N:No |
| Bearer_Shares_Flag | CHAR(1) | 選輸 | 公司的中介控股公司是否能夠發行不記名股票 枚舉值：Y:Yes，N:No |
| Usage_of_products | CHAR(1) | 必輸 | 高風險產品類型 枚舉值： 01：Low Risk Product Only；03：At Least One High Risk Product |
| On_PEC | CHAR(1) | 必輸 | 是否政府標誌 枚舉值：Y:Yes，N:No |
| layers_of_ownership_structure | CHAR(2) | 必輸 | 公司股權結構層數 |
| Country_Of_Risk | CHAR(3) | 選輸 | 風險國家 |
| Country_Of_Origin | CHAR(3) | 選輸 | 总部注册国家 |
| Declaration | CHAR(1) | 選輸 | 聲明註冊地址/營業地址是否為秘書公司地址 1-The registered address or business address is an address of a company secretarial firm 2-Both the registered address and business address addresses of a company secretarial firm 3-The registered address or business address is a residential address 4-Both the registered address and business address are residential addresses |
| Reason | CHAR(500) | 選輸 | 註冊地址/營業地址為秘書公司地址的原因 |
| Office_Telephone_Number | CHAR(18) | 選輸 | 辦事處電話 |
| Company_Website | CHAR(120) | 選輸 | 公司網頁 |
| Office_Location(s)􉳵􂰖􃹔􃖑􋟊 | CHAR(3) | 選輸 | 辦公室地點 枚舉值：國家列表 |
| Principal_Place_of_Operations | CHAR(3) | 必輸 | 主要運營地 枚舉值：國家列表 |
| Countries_of_Foreign_Branch(es)/Subsidiary(ies) / Factory(ies) | CHAR(3) | 選輸 | 海外分支/子公司/工廠所在的國家/地區（支持最多增加10個） 枚舉值：國家列表 |
| Country_of_Key_Counterparties | CHAR(3) | 必輸 | 主要交易對手所在國家（支持最多增加5個） 枚舉值：國家列表 |
| Industry_Category | CHAR(6) | 必輸 | 行業大類 |
| Key_Products/ Services | CHAR(6) | 必輸 | 行業小類 |
| Name_of_Regulatory_Body | CHAR(120) | 選輸 | 監管機構名稱 |
| Regulatory_Reference_Number | CHAR(30) | 選輸 | 監管機構參考編號 |
| Country_of_Regulatory_Body | CHAR(3) | 選輸 | 監管機構所在國家 枚舉值：國家列表 |
| Country_of_Employee(s) | CHAR(3) | 選輸 | 僱員所在國家（跟僱員數目為一組，最多支持5組） 枚舉值：國家列表 |
| Number_of_Employee(s) | CHAR(5) | 選輸 | 僱員數目（跟僱員所在國家為一組，最多支持5組） |
| WEB3_Company_Flag | CHAR(1) | 選輸 | 是否WEB3公司 枚舉值：Y:Yes，N:No |
| Greater_China_Company_Flag | CHAR(1) | 必輸 | 是否Greater China公司 枚舉值：Y:Yes，N:No |
| Financial_Background | CHAR(5) | 選輸 | 公司经济类型 枚举值： 1110:STATE-OWNED(国有) 1120:COLLECTIVELY-OWNED(集体) 1140:JOINT OPERATION(联营) 1160:JOINT STOCK(股份) 1170:PRIVATE-OWNED(私营) 1175:INDIVIDUAL-OWNED(个体) 1200:FOREIGN CAPITAL(外资) 1330:HONG KONG,MACAU,OR TAIWAN CAPITAL(港澳台资) 9999:OTHERS(其他) |
| Holding_Company_Flag | CHAR(1) | 選輸 | 是否為控股公司 枚舉值：Y:Yes，N:No |
| Annual_Business_Turnover(HKD) | NUM(16,2) | 必输 | 全年營業額（等值港幣） |
| Net_Profit(HKD) | NUM(16,2) | 选输 | 淨利潤（等值港幣） |
| Asset_Size(HKD) | NUM(16,2) | 选输 | 資產規模（等值港幣） |
| Others(Please_Specify) | CHAR(200) | 选输 | 其他 |
| Name_of_the_Jurisdiction | CHAR(3) | 选输 | 司法管轄權（公司在制裁國家有進行商業活動的）（最多支持5個） 枚舉值：國家列表，第一個默認香港。 |
| Purpose_of_business_in_the_Jurisdiction | CHAR(120) | 选输 | 於制裁國家進行商業行為之目的（最多支持5個） |
| Sanction_question1 | CHAR(1) | 必输 | 公司目前或计划在伊朗、苏丹、朝鲜、叙利亚、古巴、南苏丹、克里米亞或津巴布韦等国家/地区开展业务活动吗？ 枚舉值：Y:Yes，N:No |
| Sanction_question2 | CHAR(1) | 必输 | 据你所知，貴公司或貴公司的任何关联方或其他相关方是否存在在伊朗、苏丹、朝鲜、叙利亚，克里米亞或古巴，以及/或目前是以下国家实施制裁的目标 机构:联合国、欧盟、UKHMT、金管局、OFAC，或作为当地制裁法律的一部分 枚舉值：是/否 |
| Sanction_question3 | CHAR(1) | 必输 | 据您所知，貴公司或其任何关联方或其他关联方是否有任何现有或计划在克里米亚/塞瓦斯托波尔地区进行的商业活动 枚舉值：是/否 |
| Sanction_question4 | CHAR(1) | 必输 | 公司目前或计划在俄罗斯的能源/石油和天然气部门、军事或国防领域有任何业务 枚舉值：是/否 |
| Purpose_for_Account_Opening | CHAR(3) | 必输 | 開戶目的（最多支持5個） 枚舉值： BO:BUSINESS OPERATIONS IS:PAYMENT OF HIRE IV:INVESTMENT SF:SAVING / FIXED DEPOSIT PR:PAYROLL LO:LOAN OT:OTHER |
| Others(Please_Specify): | CHAR(200) | 选输 | 其他開戶目的 |
| Annual_Turnover_Flag | CHAR(1) | 选输 | 该集团的年营业额是否超过 15 亿 枚舉值：Y:Yes，N:No |
| Non_SME_flag | CHAR(1) | 选输 | 是否SME標誌 枚舉值：Y:Yes，N:No |
| Domestic/International_Transaction | CHAR(1) | 必输 | 本地/國際交易 枚舉值： A：本地電匯(Domestic Payment) B：國際電匯(International Payment) C：本地和國際電匯(Domestic and International Payment) |
| Cross_Border_Transaction_flag | CHAR(1) | 必输 | 公司會否進行跨境交易 枚舉值：Y:Yes，N:No |
| Monthly_ Cross_Border_Transaction_ Amount (Inward) | CHAR(1) | 选输 | 每月跨境轉賬交易量（匯入） 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly_ Cross_Border_Transaction_ Amount (Outward) | CHAR(1) | 选输 | 每月跨境轉賬交易量（匯出） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Purpose_for_Cross_Border_Transaction | CHAR(3) | 选输 | 跨境交易目的，支持多選 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） |
| Other_Purpose_for_Cross_Border_Transaction | CHAR(200) | 选输 | 其他跨境交易目的 |
| Regular_Domestic_Transaction_Flag | CHAR(1) | 必输 | 公司會否進行常規本地交易 枚舉值：Y:Yes，N:No |
| Monthly_Number_of_Regular_Transaction (Inward) | CHAR(1) | 选输 | 每月本地轉賬交易量（匯入） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly_Number_of_Regular_Transaction (Outward) | CHAR(1) | 选输 | 每月本地轉賬交易量（匯出） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Purpose_for_Regular_Transaction | CHAR(1) | 选输 | 本地交易目的，支持多選 當問題“公司會否進行常規本地交易”為“Y時必輸” 枚舉值： Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） |
| Other_Purpose_for_Regular_Transaction | CHAR(3) | 选输 | 本地其他交易目的 |
| Hold_Balances_with_Infrequent_Activity_flag | NUM(16,2) | 选输 | 公司是否預期在賬戶持有結餘而不進行交易 枚舉值：Y:Yes，N:No |
| Amount_and_Rationale | CHAR(3) | 选输 | 請提供金額及理由 |
| Company_Change_flag | CHAR(1) | 必輸 | 在过去的5年里，貴公司有过实质性的变化吗?(如所有权、公司名称、母公司的变更、经营国家、业务性质、产品/服务) 枚舉值：Y:Yes，N:No |
| Created_at | NUM(8) | 必輸 | 建立时间 |
| Updated_at | NUM(8) | 必輸 | 更新时间 |

-  - 关系模型（party_relationship） 

用于记录任意两个主体（个人或机构）之间的正式业务或合规关系，是 CIF 系统中的核心关系表。每条记录代表一条有方向的关系边，即 “主体 A（src_cif_id）与主体 B（dst_cif_id）之间存在某种角色关系（role_code）”。所有关联关系（如董事、UBO、股东、关联人、Connected Party、零售客户等）均通过本表统一建模。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| rel_id | BIGINT UNSIGNED | 主键，全行唯一。代表一条关系边。 |
| src_cif_id | BIGINT UNSIGNED | 关系起点（Source Party）的 cif_id；例如「公司 → 董事」中的公司。 |
| dst_cif_id | BIGINT UNSIGNED | 关系终点（Destination Party）的 cif_id；例如「公司 → 董事」中的自然人。 |
| role_code | VARCHAR(64) | 角色类型编码（如 RETAIL_CUSTOMER, DIRECTOR, UBO, SHAREHOLDER, AUTHORIZED_SIGNER, CONNECTED_PARTY, CONTACT_POINT 等），引用自 party_role.role_code。 |
| status | ENUM('ACTIVE','INACTIVE','CLOSED') | 关系状态：ACTIVE=当前有效；INACTIVE=暂停/非当前；CLOSED=关系已终止（满足留存要求后仅供审计）。 |
| effective_from | DATETIME | 关系生效时间（如董事任命日期、股权生效日期）。 |
| effective_to | DATETIME NULL | 关系终止时间；当前仍生效的关系为空。用于期间管理、历史版本追溯。 |
| Connected_Party_Type | CHAR(60) | 相关人士类型 枚举值：NP – Natural Person 自然人；BC – Body Corporate 法人团体 |
| Role_of_Connected_Party | CHAR(60) | 支持同时具有多个角色 |
| Language | CHAR(60) | 用于发送消息通知时确定通知的语言 |
| Telephone_Number | CHAR(60) | 相关人士电话号码編號 |
| Email_Address | CHAR(34) | 相关人士电子邮箱地址編號 |
| Residential_Address_ID | CHAR(2) | 居住地址編號 |
| Registered_Address_ID | CHAR(2) | 注册地址編號 |
| Operation_Address_ID | CHAR(2) | 运营地址編號 |
| Controlling_Person_Type | CHAR(6) | 实际控制人类型，当Capacity of Connected Party的类型存在Controlling Person时，则该字段为必输 枚举值： CRS801:LEGAL PERSON - OWNERSHIP CRS802:LEGAL PERSON - OTHER MEANS CRS803:LEGAL PERSON - SENIOR MANAGING OFFICIAL CRS804:LEGAL ARRANGEMENT - TRUST - SETTLOR CRS805:LEGAL ARRANGEMENT - TRUST - TRUSTEE CRS806:LEGAL ARRANGEMENT - TRUST - PROTECTOR CRS807:LEGAL ARRANGEMENT - TRUST - BENEFICIARY CRS808:LEGAL ARRANGEMENT - TRUST - OTHER CRS809:LEGAL ARRANGEMENT - OTHER - SETTLOR-EQUIVALENT CRS810:LEGAL ARRANGEMENT - OTHER - TRUSTEE-EQUIVALENT CRS811:LEGAL ARRANGEMENT - OTHER - PROTECTOR-EQUIVALENT CRS812:LEGAL ARRANGEMENT - OTHER - BENEFICIARY-EQUIVALENT CRS813:LEGAL ARRANGEMENT - OTHER - OTHER-EQUIVALENT |
| Equity_Hierarchy | CHAR(3) | 股權層級 |
| ownership_pct | DECIMAL(5,2) NULL | 股份占比，0–100。仅当角色为 UBO/SHAREHOLDER 等需披露持股比例的角色时必输。 |
| voting_right_pct | DECIMAL(5,2) NULL | 表决权占比（如与持股比例不同的情况），需要时填写。 |
| control_basis_code | VARCHAR(64) NULL | 控制依据编码，例如：SHAREHOLDING、VOTING_RIGHT、OTHER_MEANS 等，用于支撑 AML / CRS 的控制人判断。可以与 CRS Controlling Person Type 映射。 |
| source_system | VARCHAR(64) | 关系数据来源系统/流程（如 ONBOARDING, CIF_MANUAL, CORPORATE_WORKFLOW 等），用于审计和问题追踪。 |
| evidence_ref | VARCHAR(256) NULL | 证据引用：可存放附件编号、KYC 表单编号、公司注册处文件编号等，用于审计追溯。 |
| remarks | VARCHAR(512) NULL | 备注说明，用于存放补充说明（如临时安排、特殊解释等）。 |
| created_at | DATETIME | 记录创建时间。 |
| updated_at | DATETIME | 最近一次更新时间。 |

说明:是全局的主体关系表，在全行维度建立统一的关系图谱，用于统一描述“谁与谁存在何种关系”， 关系的类型是什么（role_code），关系从什么时候开始、是否仍然有效。

- 方向性关系：
每条记录都有明确方向，例如：

- 公司 → 董事：src_cif_id = 公司，dst_cif_id = 自然人

- 公司 → UBO：src_cif_id = 公司，dst_cif_id = 自然人

- 公司 → 公司股东：src_cif_id = 公司，dst_cif_id = 公司股东

- 对于一个零售客户：src_cif_id = dst_cif_id，用于标识零售客户自身角色（RETAIL_CUSTOMER）

- 角色来源与控制：

- 角色类型由 party_role 定义，集中配置可维护性高。

- 系统在写入时根据 party_role 配置校验是否需要填写比例、控制依据、核验等级等字段。

- 适用范围：

- 支持所有类型关系：机构和个人、机构和机构、个人和个人。

每条记录都有明确方向，例如：

- 公司 → 董事：

- src_cif_id = 公司 cif_id

- dst_cif_id = 自然人 cif_id

- role_code = DIRECTOR

- 公司 → UBO：

- src_cif_id = 公司 cif_id

- dst_cif_id = 自然人 cif_id

- role_code = UBO

- ownership_pct、control_basis_code 必输。

- 公司 → 公司股东：

- src_cif_id = 被投资公司 cif_id

- dst_cif_id = 股东公司 cif_id

- role_code = SHAREHOLDER

- 零售客户自身角色：

- src_cif_id = dst_cif_id = 该自然人 cif_id

- role_code = RETAIL_CUSTOMER

-  - 角色模型（party_role）

party_role 为关系模型的角色定义字典，是全行客户主数据体系中用于描述主体间角色关系的标准化元数据表。该表用于定义所有在 party_relationship 中可引用的角色类型及其属性，包括角色业务语义、适用主体类型约束以及角色级校验规则。

角色模型的核心目标包括：

- 提供统一、标准化的角色定义，避免不同系统、不同业务域对同一角色含义产生不一致；

- 与 party_relationship 联合描述“主体—主体”间的关系；

- 支撑 AML/KYC/CDD 中必要的控制人识别、股权关系识别等要求；

- 对公客户：公司与其董事、UBO、联系人、网银操作人等的关系。

- 零售客户：自然人在本行为客户的身份（RETAIL_CUSTOMER）。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| role_code | VARCHAR(64) PK | 角色唯一编码，供 party_relationship.role_code 引用 |
| role_name_zh | VARCHAR(128) | 角色中文名称 |
| role_name_en | VARCHAR(128) | 角色英文名称 |
| role_description | VARCHAR(512) | 描述角色的业务语义，用于二线理解 |
| src_party_type | ENUM('INDIVIDUAL','ORGANIZATION','ANY') | 该角色在关系中的“起点主体类型”约束 |
| dst_party_type | ENUM('INDIVIDUAL','ORGANIZATION','ANY') | 该角色在关系中的“终点主体类型”约束 |
| is_self_relation | BOOLEAN | 是否允许 src = dst，自环关系（如 RETAIL_CUSTOMER 自环） |
| is_controlling_role | BOOLEAN | 是否视为 AML/KYC 中的控制类角色（例如 Director、UBO） |
| require_ownership_pct | BOOLEAN | 是否要求在 relationship 中提供股权比例 |
| require_control_basis | BOOLEAN | 是否要求在 relationship 提供控制依据（shareholding / voting / other） |
| domain_scope | ENUM('RETAIL','BUSINESS','BOTH') | 角色适用范围（此为主数据级别，不涉及业务权限） |
| is_active | BOOLEAN | 是否启用当前角色定义 |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |

说明

- party_role 仅定义「角色是什么」，不记录「谁扮演此角色」。

- 角色类型覆盖银行常见客户与关联人场景，包括：

- 零售客户角色：RETAIL_CUSTOMER

- 机构关联角色：DIRECTOR、UBO、SHAREHOLDER、AUTHORIZED_SIGNER、CONTACT_POINT、CONNECTED_PARTY

- 其他通用角色：FAMILY_MEMBER等、未来可以拓展。

当前零售和对公已使用的角色：

| role_code | role_type | 中文名称 | 英文名称 | is_active | 说明 |
| --- | --- | --- | --- | --- | --- |
| 001 | RETAIL_CUSTOMER | 零售客户 | Retail Customer | TRUE | 零售业务客户主角色。用于标识自然人在本行开立个人账户。以自环关系表示（src_cif_id = dst_cif_id）。 |
| 034 | DIRECTOR | 董事 | Director | TRUE | 公司→个人。董事为法定控制层成员。 |
| 085 | UBO | 实益拥有人 | Beneficial Owner | TRUE | 公司→个人。需披露持股比例与控制依据。 |
| 065 | PARTNER | 合伙人 | Partner | TRUE | 适用于合伙企业，公司→个人。 |
| 114 | SOLE_PROPRIETOR | 独资经营者 | Sole Proprietor | TRUE | 独资企业业主，控制权100%。 |
| 088 | AUTHORIZED_REPRESENTATIVE | 授权代表 | Authorised Representative | TRUE | 公司→个人。代表公司行事。 |
| 019 | BIB_CHECKER | 网银复核员 | Internet Banking Checker | TRUE | 公司→个人。具网银复核权限。 |
| 120 | BIB_MAKER | 网银制单员 | BIB Maker | TRUE | 公司→个人。具网银制单权限。 |
| 121 | BIB_ADMIN | 网银管理员 | BIB Administrator | TRUE | 公司→个人。维护网银权限。 |
| 122 | CONTACT_PERSON | 联系人 | Contact Person | TRUE | 公司→个人。沟通联系人。 |
| 300 | BODY_CORPORATE | 法人团体 | Body Corporate | TRUE | 公司→公司。用于法人股东或母公司。 |
| 084 | BENEFICIARY | 受益人 | Beneficiary | TRUE |  |
| 069 | SHAREHOLDER_OVER_25 | 股东≥25% | Shareholder ≥25% | TRUE |  |
| 103 | SENIOR_OFFICE | 高级管理人员 | Senior Officer | TRUE |  |
| 045 | GUARANTOR_FOR_LOAN | 担保人（贷款） | Guarantor (For Loan) | TRUE |  |
| 060 | TRUSTEE | 托管人 | Trustee | TRUE |  |
| 104 | SETTLE | 财产授与人 | Settlor | TRUE |  |
| 105 | PROTECTOR | 信托保护人 | Protector | TRUE |  |
| 117 | PROJECT_S_CONTACT_PERSON | 虚拟账户联络人 | Project S Contact Person | TRUE |  |
| 118 | CONTROLLING_PERSON_FOR_PASSIVE_NFE | 实际控制人类型 | Controlling Person (for Passive NFE) | TRUE |  |
| 119 | BIB_TRADER | 網銀交易員 | Trader | TRUE |  |

- - 联系方式模型（party_contact_information）

party_contact_information 属于 CIF 的 Attribute 信息层，用于管理主体（自然人 / 机构）的联系方式主档，包括：

- 手机号码、固定电话

- 电子邮箱

- 其他联系方式（如有）

设计目的：

- 统一管理 一个主体的多种联系方式、多条记录、多种用途（通知、对账单、业务联络等）；

- 支撑 Retail、Corp、运营、风控等多个域对“联系方式”的一致使用；

- 为后续扩展（如同一自然人作为多个对公 Connected Party 时复用联系方式）提供基础。

设计原则：

- 一主体多联系方式：同一 cif_id 可以有多条记录；

- 一类型可多条，但可标记主用：同一主体可有多个手机号 / 邮箱，但有 is_primary 标志；

- 用途可区分：通过 usage 区分登录用、通知用、应急用等；

字段定义

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | BIGINT UNSIGNED | 主键，自增 ID。 |
| cif_id | BIGINT UNSIGNED | 外键，引用 party.cif_id，表示该联系方式属于哪个主体。 |
| contact_type | ENUM('MOBILE','PHONE','EMAIL','OTHER') | 联系方式类型：手机号 / 固话 / 邮箱 /其他。 |
| contact_value | VARCHAR(256) | 联系方式内容（如手机号、邮箱地址等）。存储格式与保护方式由技术与安全团队在 TRD 中定义。 |
| usage | SET('LOGIN','NOTIFICATION', 'STATEMENT','EMERGENCY','BUSINESS','MARKETING','OTHER') | 用途标签：登录、通知、对账单、应急联络、日常业务联络、营销等，可多选。 |
| is_primary | TINYINT(1) | 是否为该主体在本 contact_type 下的主联系方式（如主手机号、主邮箱）。同一主体 + 类型下最多允许 1 条记录 is_primary=1。 |
| is_verified | TINYINT(1) | 是否已验证（例如手机号完成 OTP 验证、邮箱完成验证链接确认）。 |
| verification_method | ENUM('OTP','CALL_BACK','EMAIL_LINK','MANUAL','OTHER') NULL | 验证方式（如 OTP、回拨电话、邮件链接、人工验证等）。 |
| verification_date | DATETIME NULL | 最近一次验证通过的时间。 |
| status | ENUM('ACTIVE','INACTIVE') | 状态：ACTIVE=当前可用；INACTIVE=已停用（如客户要求更换、失效号码等）。 |
| updated_by_case_no | VARCHAR(64) NULL | 最近一次新增或变更该联系方式的业务单号（如开户申请、资料变更申请、年检 Case 等）。 |
| remarks | VARCHAR(256) NULL | 备注（例如“仅用于紧急联络”“客户要求不用于营销”等说明）。 |
| created_at | DATETIME | 记录创建时间。 |
| updated_at | DATETIME | 最近一次更新该记录的时间。 |

-  - 地址模型（party_address）

用于记录主体（自然人或机构）的地址信息，是 CIF 的核心共用模型。每条记录代表“某主体在某一地址类型下的一条有效地址”。地址分为两大类：中国大陆地址（MAINLAND） 与 海外地址（OVERSEAS，含香港）。

支持全量地址管理：通讯地址、居住地址、公司地址、税务地址、身份证地址、邮寄地址、秘书公司地址、营业地址等。

**地址模型（party_address）**

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| addr_id | BIGINT UNSIGNED | 主键，自增 |
| cif_id | BIGINT UNSIGNED | 主体主键，外键 → party.cif_id |
| address_type | ENUM('RESIDENTIAL','CORRESPONDENCE','TAX','COMPANY','ID_CARD','POSTAL','REGISTERED','BUSINESS') | 地址用途类型（居住/通讯/税务/公司/身份证/邮寄/注册/营业/秘书地址） |
| address_category | ENUM('MAINLAND','OVERSEAS','CORPORATE') | 地址分类：大陆地址/海外地址（含香港）/对公专用结构（如只用于机构的复杂楼宇格） |
| country | CHAR(2) | ISO-3166-1 alpha-2 国家码（香港=“HK”；中国=“CN”）。用于主表快速检索与风控分流 |
| address_key | VARCHAR(36) | 地址明细主键，指向明细表（party_address_mainland_detail 或 party_address_overseas_detail） |
| updated_by_case_no | VARCHAR(64) | 审批/触发单号（如年检、Dormant 激活、客户自助更新等） |
| status | ENUM('ACTIVE','INACTIVE','DELETED') | 业务状态（逻辑删除用 DELETED，保留审计） |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |
| created_by | VARCHAR(64) | 创建人（柜员/系统） |
| updated_by | VARCHAR(64) | 更新人（柜员/系统） |

**party_address_mainland_detail**

适用于所有大陆地址

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | BIGINT UNSIGNED | 主键，自增 |
| address_key | VARCHAR(36) | 主键键值（与主表关联，唯一） |
| country | CHAR(2) | 固定为 CN |
| province | VARCHAR(64) | 省 |
| city | VARCHAR(64) | 市 |
| district | VARCHAR(64) | 区/县 |
| detail | VARCHAR(255) | 详细地址（街道/道路/门牌等，支持标准化） |

**party_address_overseas_detail**

适用于所有非大陆地址，包含香港地址。为适配港澳台及海外楼宇/单元表达，采用分段字段。

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| id | BIGINT UNSIGNED | 主键，自增 |
| address_key | VARCHAR(36) | 主键键值（与主表关联，唯一） |
| country | CHAR(2) | ISO-3166-1 alpha-2 |
| province | VARCHAR(64) NULL | 州/省/地区（可空） |
| city | VARCHAR(64) NULL | 城市 |
| street | VARCHAR(128) NULL | 街道/屋苑/大厦名称（示例：“太古城 太古城道”） |
| building | VARCHAR(64) NULL | 期/座（示例：“第 5 期” 或 “A座”） |
| tower | VARCHAR(64) NULL | 塔/栋（对公或大型园区常用） |
| floor | VARCHAR(32) NULL | 楼层（示例：“20/F”） |
| room | VARCHAR(32) NULL | 房号/单位（示例：“Room 2003”） |

适用范围：

- 同时支持自然人与机构。

- 香港地址按海外口径（OVERSEAS）存储。

- 支持多地址并存，同一 address_type 可多条。客户可能在不同国家/地区拥有住所（例如：香港 + 内地；香港 + 英国）或者客户在工作地、家人住所等处都有实际居住痕迹。

-  - 国籍模型（party_nationality）

属于 CIF – Attribute 信息层，用于管理自然人（Individual Party）的国籍信息。支持客户拥有多个国籍，记录完整的国籍来源、主国籍、有效期及证据来源，满足监管审计、风险管理、CRS/FATCA、制裁筛查等要求。

- 适用对象：仅适用于 identity_category = INDIVIDUAL 的自然人 Party

- 支持数量：每个自然人 最多 4 个国籍 记录

- 服务范围：同时服务零售域（Retail）和对公域（作为 UBO / Director 等自然人角色）

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| id | BIGINT UNSIGNED AUTO_INCREMENT | 自增主键 |
| cif_id | BIGINT UNSIGNED NOT NULL | 外键 → party.cif_id |
| country_code | CHAR(2) NOT NULL | 国籍国家代码（ISO 3166-1 alpha-2） |
| source_system | VARCHAR(32) | 来源系统：APP / OPS / ONBOARDING |
| channel | CHAR(4) | 渠道编码 |
| biz_no | VARCHAR(32) | 审批单号 / 更新流水号 |
| created_at | DATETIME NOT NULL | 创建时间 |
| created_by | VARCHAR(32) NOT NULL | 创建人 |
| updated_at | DATETIME NOT NULL | 更新时间 |
| updated_by | VARCHAR(32) NOT NULL | 更新人 |

-  - 税务模型（party_tax_residency）

party_tax_residency 用于集中管理银行客户在本行声明的税务居民身份（Tax Residency）。该模型以客户（cif_id）为主档，以国家/地区（tax_country_code）为维度，记录客户在不同司法辖区的税务居民信息、TIN（税务识别号）及相关自我声明信息，是 FATCA / CRS 等国际税务合规制度的数据基础。

本模型适用于 个人客户（INDIVIDUAL） 与 机构客户（ORGANIZATION），支持每个客户记录最多 4 个当前生效的税务居民身份，并保留完整的历史记录用于审计与年审复核。

模型用途包括但不限于：

- FATCA / CRS 申报（生成 XML 电子报文）

- 识别客户是否为 US Person

- 年检（Periodic Review）及资料更新流程

- 客户风险评级（包括高风险司法管辖区识别）

- 监管、内部审计及外部审计取证

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| tax_residency_id | BIGINT UNSIGNED | 主键，自增 ID |
| cif_id | BIGINT UNSIGNED | 外键 → party.cif_id |
| tax_country_code | CHAR(2) | 客户声明的税务居民国家（ISO 3166-1 Alpha-2，例如 HK、CN、US） |
| tin | VARCHAR(64) | 税务识别号码（Tax Identification Number） |
| no_tin_reason_code | ENUM('A','B','C') | 无 TIN 原因：A=该国不发放 TIN；B=客户尚未取得；C=其他原因 |
| no_tin_reason_remark | VARCHAR(255) | 当原因代码为 C（其他原因）时必须提供说明 |
| source_type | ENUM('CRS_SELF_CERT','FATCA_W8_W9','MANUAL_REVIEW','SYSTEM_DERIVED') | 税务信息来源：客户自我声明、FATCA 表单、人工复核、系统推断 |
| certification_signed_at | DATE | 客户（或机构授权人）签署 CRS/FATCA 自我声明表单的日期（法律意义） |
| last_review_date | DATE | 银行最近一次复核该条税务居民信息的日期（资料更新/年检） |
| effective_from | DATE | 税务居民状态生效日期 |
| effective_to | DATE | 税务居民状态终止日期；为空代表当前有效 |
| status | ENUM('ACTIVE','INACTIVE') | 当前记录状态：ACTIVE=当前有效；INACTIVE=已失效（用于历史追溯） |
| updated_by_case_no | VARCHAR(64) | 修改此记录的工单编号或内部审批编号 |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 最近更新时间 |

说明

•	party_tax_residency 通过 cif_id 与 party 主档关联，零售与对公共用一张表。

•	同一客户下：

o	最多允许 4 条 status = ACTIVE 的记录；

o	tax_country_code 不得重复。

•	certification_date = 客户完成 CRS/FATCA 自我声明并提交的日期，用于判断声明有效性与后续是否需重新收集。

•	last_review_date 记录银行内部对该条税务信息最近一次复核的日期，支撑年检 / 定检流程。

•	税务居民国家和 TIN 信息供：

o	CRS 申报（生成 CRS XML）

o	FATCA 申报判断（结合 US Tax Resident）

o	客户风险评级、国家风险（如 CBI/RBI、高风险司法辖区）等模块使用。

- - FATCA 信息模型（party_fatca_profile）

party_fatca_profile 为 FATCA（Foreign Account Tax Compliance Act）领域的客户身份信息模型，用于集中管理客户在 FATCA 框架下的身份识别结果、文档类型、实体分类、尽职调查（Due Diligence）状态以及文档有效性判断。该模型以客户（cif_id）为维度，保证任一时点仅存在一条当前有效的 FATCA Profile 记录（支持历史版本保留）。

本模型不直接判断客户的税务居民身份（如是否为 US Tax Resident），而是基于 party_tax_residency、party_address、party_identity 等主数据完成自动/人工识别后，由 FATCA 领域对客户 FATCA 状态、文件类型等进行统一管理。

适用场景：

- 零售客户：US Indicia、US Tax Resident、US Account Holder、W-8/W-9 文档信息、签字日期、有效性检查

- 企业客户：实体 FATCA 分类（如 Passive NFFE / Participating FFI）、GIIN 号码、非独立实体（Disregarded Entity）、是否需穿透至控制人

- 合规监控：FATCA DD 状态、文档过期提醒、客户 FATCA 分类变更

- FATCA 申报：生成 IRS 报送所需字段（如 GIIN、FATCA Classification、Doc Type）

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| fatca_profile_id | BIGINT UNSIGNED | 主键，自增 ID |
| cif_id | BIGINT UNSIGNED | 外键 → party.cif_id |
| customer_category | ENUM('NEW','PRE_EXISTING') | FATCA 新客户/既有客户分类 |
| fatca_status | ENUM('FATCA_RELEVANT','NOT_RELEVANT','EXEMPT') | FATCA 状态：需申报 / 不适用 / 豁免 |
| us_indicia_flag | BOOLEAN | 是否存在 US Indicia（如美国地址、美国电话、美国出生地等） |
| us_tax_resident | BOOLEAN | 是否美国税务居民，与 party_tax_residency 保持一致 |
| us_account_holder | BOOLEAN | 是否美国账户持有人（US Account Holder） |
| fatca_doc_type | ENUM('W8_BEN','W8_BEN_E','W8_IMY','W8_EXP','W8_ECI','W9','SELF_CERT','OTHER') | FATCA 文档类型 |
| other_fatca_doc_type | VARCHAR(120) | 文档类型为 OTHER 时必填 |
| fatca_doc_sign_date | DATE | FATCA 文档签署日期（表单上的签字日期） |
| fatca_doc_expiry_date | DATE | FATCA 文档到期日（如适用；例如 W-8 系列三年有效期规则） |
| fatca_doc_validity | ENUM('VALID','INVALID','PENDING') | FATCA 文档有效性状态 |
| fatca_entity_classification | VARCHAR(8) | FATCA 实体分类，如 Active NFFE、Passive NFFE、Participating FFI 等 |
| non_consented_us_account_type | VARCHAR(8) | 非同意申报的 US 帐户类型（Non-consenting U.S. Account），对于特定实体分类为必填 |
| disregarded_entity | ENUM('Y','N') | 是否为 Disregarded Entity（非独立实体） |
| giin_code | VARCHAR(16) | GIIN（Global Intermediary Identification Number） |
| giin_status | ENUM('ACTIVE ','INACTIVE',’REVOKED’,’EXPIRED’) | GIIN 状态 ACTIVE,INACTIVE,REVOKED,EXPIRED |
| consent_to_report | BOOLEAN | 是否同意向 IRS / IRD 报送信息 |
| fatca_dd_status | ENUM('NOT_STARTED','PENDING','COMPLETE') | FATCA 尽调状态 |
| fatca_dd_completion_date | DATE | FATCA 尽调完成日期 |
| fatca_last_review_date | DATE | 最近一次 FATCA 复核日期（年检 / 定检） |
| effective_from | DATE | FATCA Profile 生效日期 |
| effective_to | DATE | FATCA Profile 结束日期；为空时表示当前有效 |
| status | ENUM('ACTIVE','INACTIVE') | 当前记录状态 |
| updated_by_case_no | VARCHAR(64) | 触发更新的审批单 / 工单编号（如 EDD、资料更新单） |
| remark | VARCHAR(255) | FATCA 合规备注 |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 最近更新时间 |

说明

- FATCA 视角下的客户分类（fatca_status / entity classification）

- FATCA 文档（W-8/W-9）类型、签署日期、有效期、有效性状态

- FATCA 尽职调查（Due Diligence）状态

- FATCA 实体分类（对公客户）

- GIIN（Global Intermediary Identification Number）信息

- US Indicia（美国迹象）识别状态

- FATCA Profile 的生命周期及历史记录（有效期间）

- - CRS 信息模型（party_crs_profile）

party_crs_profile 用于集中管理客户在 CRS（共同申报准则）制度下的自我声明（Self-Certification）、复核状态（Review Status）、声明有效性与地址一致性（Address Consistency）等信息，是以客户（cif_id）为维度的合规模型。

本模型不记录税务居民国家、TIN 等结构化税务信息，税务居民国家及 TIN 均由 party_tax_residency 管理。本模型主要承载 CRS 合规流程维度的数据，如自我声明是否已完成、签署日期、复核进度、地址一致性检查等。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| crs_profile_id | BIGINT UNSIGNED | 主键 |
| cif_id | BIGINT UNSIGNED | 外键 → party.cif_id |
| crs_self_cert_flag | BOOLEAN | 客户是否已完成 CRS 自我声明（开户时必须 TRUE） |
| crs_self_cert_form_type | VARCHAR(32) | CRS 自我声明表单/模板类型（预留扩展） |
| crs_entity_classification | VARCHAR(16) | 对公客户 CRS 实体分类，如 Active NFE / Passive NFE（对自然人留空） |
| crs_cert_sign_date | DATE | 客户签署 CRS Self-Cert 的日期（法律意义） |
| crs_update_date | DATE | 客户最近一次更新 CRS 信息的日期（如补件或资料更新流程完成） |
| crs_last_review_date | DATE | 银行内部最近一次复核 CRS 信息的日期（如年检） |
| address_consistency_flag | ENUM('Y','N') | 居住地址与通讯地址是否一致（H & I Test） |
| address_inconsistency_reason | VARCHAR(32) | 地址不一致原因编码（例如：Correspondence Only、Temporary、Work Address） |
| address_inconsistency_remark | VARCHAR(255) | 地址不一致原因的文本说明 |
| remark | VARCHAR(255) | 其它 CRS 相关补充说明 |
| updated_by_case_no | VARCHAR(64) | 触发更新的审批工单编号（年检、资料更新单等） |
| effective_from | DATE | CRS Profile 生效日期 |
| effective_to | DATE | CRS Profile 结束日期；为空代表当前有效 |
| status | ENUM('ACTIVE','INACTIVE') | CRS Profile 状态（仅允许一条 ACTIVE 记录） |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |

-  - 自然人就业模型（party_individual_employment_profile）

作为自然人 KYC 信息的重要组成部分，用于记录客户的职业类别、行业分类、雇主信息、年收入、财富来源等信息，是 AML 风险评分、客户尽职调查、交易监控、年检（PR）、Dormant 激活等流程的关键输入。该模块支持零售与对公业务（自然人 UBO、自然人董事、授权代表等），作为自然人档案的共用就业信息。支持 AML 风险评估所需字段：

- 行业风险（高风险行业）

- 收入合理性（收入 vs 交易）

- 财富来源（SOW）

- 资金来源（SOF）

- 资金流动方式（Means of Transfer）

- 支持内部员工识别za_bank_employee_flag = Y/N（合规与内部审计需要）

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| employment_profile_id | BIGINT UNSIGNED | 主键，自增 ID |
| cif_id | BIGINT UNSIGNED | 外键 → party.cif_id（自然人） |
| occupation | VARCHAR(64) | 职业类别（Full-time / Part-time / Self-employed / Retired 等） |
| industry | VARCHAR(64) | 行业类别（银行业、制造业、建筑业等，可对接行业码表） |
| position | VARCHAR(128) | 职位（如：软件工程师、销售经理、司机等） |
| employer_name | VARCHAR(255) | 雇主名称（如：ZA Bank（Hong Kong） Limited） |
| employer_address_id | BIGINT UNSIGNED | 工作地址（引用 party_address.address_id；避免重复地址信息） |
| years_in_company | DECIMAL(4,1) | 任职年数（如 5.0、1.5） |
| annual_income_amount | DECIMAL(18,2) | 年收入金额（HKD） |
| annual_income_range | VARCHAR(32) | 年收入区间（从数据字典选择） |
| income_source_desc | VARCHAR(255) | 收入来源说明（如薪资 + 奖金等） |
| source_of_funds | VARCHAR(64) | 资金来源（Salary / Commission / Business Profit / Investment / Gift 等） |
| source_of_funds_desc | VARCHAR(255) | 资金来源说明 |
| means_of_transfer | VARCHAR(64) | 资金转移方式（FPS / Bank Transfer / Cash Deposit 等） |
| means_of_transfer_desc | VARCHAR(255) | 转移方式说明 |
| source_of_wealth | VARCHAR(64) | 财富来源（Employment / Business / Investment / Inheritance 等） |
| source_of_wealth_desc | VARCHAR(255) | 财富来源说明 |
| estimated_net_worth_hkd | VARCHAR(32) | 预计净资产区间（供风险分析使用） |
| expected_fund_flow_country | VARCHAR(255) | 预计资金来源/去向国家（多个可逗号分隔） |
| expected_transaction_nature | VARCHAR(255) | 账户预期交易性质说明 |
| za_bank_employee_flag | BOOLEAN | 是否 ZA Bank 员工（Y/N），用于内部员工风险控制 |
| effective_from | DATE | 就业信息生效日期（SCD2） |
| effective_to | DATE | 就业信息结束日期（空 = 当前有效） |
| status | ENUM('ACTIVE','INACTIVE') | 当前记录状态 |
| updated_by_case_no | VARCHAR(64) | 更新对应的审批单号（如 PR 年检单） |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |

- 機構信息變更歷史模型（party_organization_change_history_profile）

Organization Change History Info表應用於記錄机构客户（Organization）在公司名稱，公司行業，UBO以及股權結構等信息的變更歷史。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 与主体绑定 |
| Company_Change_flag | CHAR(1) | 在过去的5年里，貴公司有过实质性的变化吗?(如所有权、公司名称、母公司的变更、经营国家、业务性质、产品/服务) 枚舉值：Y:Yes，N:No |
| Change_Type | CHAR(1) | 修改類型 1-Company Name 2-Industry 3-Ownership Structure 4-UBO |
| Start_date | NUM(8) | 開始日期 |
| End_date | NUM(8) | 結束日期 |
| Company_Name | CHAR(120) | 公司曾用名 |
| Old_Industry_Category | CHAR(6) | 舊行業大類名稱 |
| Old_Key_Products/Services | CHAR(6) | 舊行業小類名稱 |
| Reasons | CHAR(500) | 改變的原因 |
| Created_at | NUM(14) | 创建时间 |
| Updated_at | NUM(14) | 更新时间 |

- 機構交易對手信息模型（party_organization_counterparty_profile）

Organization Counterparty Info 記錄公司交易對手信息，包括買方賣方，資產所在國家/地區等。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 与主体绑定 |
| Type | CHAR(1) | 類型 S-Supplier B-Buyer A-Assets |
| Company_Name | CHAR(120) | 供應商/買方公司名稱 |
| Country | CHAR(3) | 供應商/買方/資產所在國家/地區 枚舉值：國家列表 |
| Percentage_of_Total | CHAR(4) | 供應商/買方佔總採購/總銷售之百分比, 佔總資產之百分比 |
| Created_at | NUM(14) | 创建时间 |
| Updated_at | NUM(14) | 更新时间 |

- 機構資產信息模型（party_organization_asset_profile）

Organization Asset Info表用於記錄客戶財富資金來源信息。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 与主体绑定 |
| Asset_Type | CHAR(1) | 資產類型 W-Wealth F-Fund |
| Source_Nature | CHAR(1) | 來源性質 I-Initial O-Ongoing |
| Source_Type | CHAR(3) | 來源類型 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET |
| Other_Source_Type | CHAR(60) | 其他来源，當Source_Type為other時輸入 |
| Source_Type_Country | CHAR(3) | 來源國家 |
| Created_at | NUM(14) | 创建时间 |
| Updated_at | NUM(14) | 更新时间 |

- 機構預期交易信息模型（party_organization_anticipated_txn_profile）

Organization Anticipated Txn Info記錄公司每月預期交易的轉賬類型，貨幣和金額。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | CHAR(10) | 与主体绑定 |
| Trading_Area_Flag | CHAR(1) | 轉賬區域標誌 C-Cross Border R-Regular Domestic |
| Transfer_Type | CHAR(1) | 轉賬類型 I-Inward O-Outward |
| Monthly_Transaction_Currency | CHAR(3) | 每月轉賬貨幣 枚舉值：11種貨幣列表 |
| Monthly_Transaction_Amount | NUM(16,2) | 每月轉賬金額 |
| Created_at | NUM(14) | 创建时间 |
| Updated_at | NUM(14) | 更新时间 |

- 機構預期交易國家信息模型（party_organization_anticipated_txn_country_profile）

Organization Anticipated Txn Country Info記錄公司預期做跨境交易的資金轉入轉出國家。

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | CHAR(10) | 与主体绑定 |
| Transfer_Type | CHAR(1) | 轉賬類型 I-Inward remittance 從哪個國家匯入資金 O-Outward remittance 匯出資金到哪個國家 |
| Country | CHAR(3) | 資金轉入轉出到哪個國家 枚舉值：國家列表 |
| Created_at | NUM(14) | 创建时间 |
| Updated_at | NUM(14) | 更新时间 |

-  - 零售域专用信息模型（party_retail_specific_information）

为零售业务域（Retail Domain）的领域专用信息模型，用于承载自然人在零售渠道（App / Mobile / Web）中的 UI 配置、登录上下文、客户偏好与渠道功能启用信息。该模型 不承载任何 KYC、CDD、AML 相关信息，仅存放零售渠道所需的体验类与配置类属性，包括登录名、昵称、头像、多语言偏好、市场推广同意、通知偏好等。

该模型为 retail domain 的扩展表，而非主数据属性表，其内容不会用于：

- 身份核验

- 交易验证

- 客户风险评级

- 税务居民判断

- 地址/KYC/EDD 等合规流程

其主要用途包括：

- App 登录体验

- 零售渠道功能启用（FPS/eDDA 等）

- 多语言界面与个性化交互

- 市场推广与同意管理

- 渠道侧风险控制及设备绑定策略

| 字段名 | 类型 | 说明 |
| --- | --- | --- |
| cif_id | BIGINT UNSIGNED | 外键 → party.cif_id（仅限 INDIVIDUAL） |
| username | VARCHAR(128) | App 登录名（零售域专用，不用于 KYC） |
| nickname | VARCHAR(128) | App 昵称 |
| avatar | VARCHAR(256) | 头像资源引用（资源 ID 或 URL） |
| marketing_consent_flag | BOOLEAN | 市场推广同意（0=不同意 / 1=同意） |
| language | VARCHAR(16) | 语言偏好（zh_CN / zh_HK / en 等） |
| timezone | VARCHAR(64) | 时间偏好（如：Asia/Hong_Kong） |
| communication_pref | JSON | 通知偏好（Push/SMS/Email 的开关配置） |
| security_controls | JSON | 设备绑定、2FA 偏好、渠道风控灰度开关 |
| onboarding_source | VARCHAR(64) | 注册来源（Paid_Ads / Referral / Organic / Other） |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |

- – 对公域专用信息模型（party_business_specific_information）

對公业务域（Business Domain）專用信息模型用于记录机构客户（Organization）在網銀渠道的信息資料管理， 本次需求暫不涉及網銀部分的改造。 

- 零售客户管理

客户信息查询是CIF系统的核心功能，为运营人员提供全面、高效的客户信息检索和管理能力。

- 客户查询

功能描述：支持多维度的客户信息搜索，满足精确查询需求。采用两行式布局设计，第一行为唯一标识符查询，第二行为条件筛选查询，提供更清晰的查询体验。支持多场景：既支持精确查询（通过唯一标识符），也支持范围筛选（通过条件组合）

- 客户查询条件

第一行：唯一标识符查询（6个字段）

| 字段名称 | 字段类型 | 选项值/格式 | 占位符文本 | 说明 |
| --- | --- | --- | --- | --- |
| 客户号 | 文本输入 | 数字格式 | 客户号 | 系统分配的唯一客户标识 |
| 身份证号 | 文本输入 | 字母数字组合 | 身份证号 | 客户身份证件号码 |
| 手机号码 | 文本输入 | +852 格式 | 手机号码 | 客户联系电话 |
| 邮箱地址 | 文本输入 | 邮箱格式 | 邮箱地址 | 客户电子邮箱 |
| 中文姓名 | 文本输入 | 中文字符 | 中文姓名 | 客户中文姓名 |
| 英文姓名 | 文本输入 | 英文字符 | 英文姓名 | 客户英文姓名 |

第二行：条件筛选查询（5个字段）

| 字段名称 | 字段类型 | 选项值/格式 | 占位符文本 | 说明 |
| --- | --- | --- | --- | --- |
| 客户类型 | 下拉选择 | 香港客户、大陆访客 | 客户类型 | 客户身份分类筛选 |
| 客户状态 | 下拉选择 | C00-C36（37个状态） | 客户状态 | 客户当前状态筛选 |
| 风险等级 | 下拉选择 | 低风险、中风险、高风险 | 风险等级 | 客户风险评级筛选 |
| 开户日期 | 日期范围选择 | 日期区间 | 开户开始日期 / 开户结束日期 | 客户开户时间范围筛选 |
| 下次年检日期 | 日期范围选择 | 日期区间 | 年检开始日期 / 年检结束日期 | 年检到期时间范围筛选 |

- 客户列表展示功能

功能描述：以表格形式展示客户信息，支持排序、分页、详情查看等操作。

页面设计：
列表字段配置， 排序要求是后台排序

| 列名 | 字段 | 显示格式 | 排序 |
| --- | --- | --- | --- |
| 客户号 | user_id | 文本 | 支持 |
| 客户类型 | customerType | 彩色标签 | 支持 |
| 客户姓名 | customerName | 文本 | 支持 |
| 英文姓名 | englishName | 文本 | 支持 |
| 账户状态 | accountStatus | 彩色标签 | 支持 |
| 手机号码 | phoneNumber | 文本 | 支持 |
| 风险等级 | riskLevel | 彩色标签 | 支持 |
| 客户状态 | status | 彩色标签 | 支持 |
| 注册日期 | registrationDate | 日期 | 支持 |
| 下次年检 | nextReviewDate | 日期 | 支持 |
| 操作 | action | 按钮 | 不支持 |

**分页配置**

每页显示：20条记录

分页控件：支持页码跳转、页面大小选择

总数显示：显示当前页范围和总记录数

快速跳转：支持输入页码快速跳转

设计样式，如果没有特别说明，所有数据列表需要分页配置的，都使用相同的配置。

**操作功能**

查看详情：点击"查看详情"按钮跳转到客户详情页面

列表排序：点击列标题进行升序/降序排序

- 客户详情页面功能

**功能描述：**提供客户的完整信息展示和管理功能，包括基本信息、异常状态提醒、身份证图片展示、多Tab页面等，为运营人员提供全面的客户视图。

- 客户主要信息

客户主要信息展示区域是CIF系统客户详情页面的核心组件，旨在为银行运营人员、FCC、产品业务等用户提供客户关键信息的快速浏览和识别功能。

**页面布局**

**列表字段信息****：**

| 字段名称 | 字段类型 | 备注 |
| --- | --- | --- |
| 客户头像 | 图片 | 证件的头像照片 |
| 英文姓名 | 文本 |  |
| 中文姓名 | 文本 |  |
| 客户类型 | 标签 | 如：香港客户、大陆访客,带颜色标签 |
| 用户号 | 数字 | 唯一标识 |
| 客户号 | 文本 | 唯一标识 |
| 手机号 | 文本 | 主要联系方式 |
| 证件号 | 文本 | 身份证明文件号码 |
| 邮箱 | 文本 | 绿色"已验证"标签 |
| 客户状态 | 标签组 | C03、C08、C36等状态码 |
| 客户等级 | 标签 | L1、L2、L3等等级 |
| 风险等级 | 标签 | 高风险、中风险、低风险 |
| 上次年检日期 | 日期 | 合规重要信息 |
| 下次年检日期 | 日期 | 自动计算 |
| 开户时间 | 日期时间 | 账户创建时间 |
| 证件照片 | 图片 | 身份证明文件照片，点击放大。 如果是MCV客户则展示正面，点击后显示正反两面 |
| ID Video | 链接 | 播放身份验证视频 |
| MS Video | 链接 | 播放面部识别视频 |

- Tab-基本信息

客户详情页基本信息Tab是CIF系统中最核心的信息展示模块，以表格形式展示客户的基础身份信息、联系方式、账户状态等关键数据。

**页面布局**

**列表字段**

| 字段名称 | 示例数据 | 业务规则 |
| --- | --- | --- |
| 用户号 | 898067644882902500 | 系统唯一标识，18位数字，不可编辑 |
| 客户号 | 8000602363 | 银行内部客户编号，10位数字 |
| 英文名 | John Wong | 客户英文姓名，支持空格和特殊字符 |
| 中文名 | 王小明 | 客户中文姓名 |
| 性别 | 男 | M=男(蓝色)，F=女(粉色)，其他显示原值 |
| 手机号 | 13812345234 | 11位手机号码或8位的香港号码格式 |
| 客户类型 | 香港客户 | HK_CUSTOMER=香港客户，MAINLAND_VISITOR=大陆访客 |
| 标签 | US Person PEP VIP | 支持多标签，不同颜色区分重要程度； US Person标签来自于审批单结果； PEP标签来自于审批单结果； VIP标签来自于客服系统； |
| 用户名 | samguo | 登录用户名，唯一标识 |
| 昵称 | John Wong | 客户自定义昵称，可为空 |
| 头像文件 | 54x54px圆形图片 | 显示头像图片 |
| 出生日期 | 1985-03-15 | YYYY-MM-DD格式，从数字格式转换 不能晚于当前日期，不能早于1900年 |
| 国籍 | 中国香港 | 国家编码转换为中文名称显示 |
| 出生国家 | 中国香港 | 出生地国家，编码转中文显示 |
| 邮箱 | john.wong@email.com | 客户邮箱地址 |
| 邮箱验证 | 已验证 | 1=已验证(绿色)，0=未验证(红色) |
| 证件号 | H123456789 | 身份证件号码，最多30个字符，敏感信息 |
| 账户号码 | 综合户：ACC1234567 南向通：ACC1234567 | 一个可以可能有两个账户 |
| 账户状态 | A04: 只出不进（限制入金） A20: 不活跃户（低频交易） |  |
| 上次年检日期 | datetime | YYYY-MM-DD格式，字体13px |
| 下次年检日期 | datetime | YYYY-MM-DD格式，临近时红色提醒 |

- Tab-基本信息-国籍信息

功能描述：在客户详情页面的基本信息Tab下，提供客户国籍信息的展示、管理和历史记录查询功能。支持多重国籍管理，最多支持4个国籍，并根据国家风险等级进行标识和管理。

业务价值：满足HKMA监管要求，支持制裁筛查、风险评估、税务申报等合规需要，为银行风控和合规管理提供数据支撑。

**页面布局**

功能要求：

-以卡片形式展示客户的国籍信息

-支持多重国籍显示，最多支持4个国籍

-国籍标签格式：国家名称 (国家代码)，国家代码必须符合ISO 3166-1标准

-根据国家风险等级使用不同颜色标识，分高中低：

点击国籍变更历史，右侧滑出页面展示。

-支持分页显示，每页10条记录（short分页设置）

-支持按变更时间排序

-记录完整的变更信息

**列表字段**

| **字段名称** | **字段类型** | **字段说明** | **示例值** |
| --- | --- | --- | --- |
| 变更时间 | datetime | 记录变更的具体时间 | 2024-01-15 14:30:25 |
| 业务编号 | varchar(50) | 关联的业务流程编号 | NAT202401150001 |
| 业务类型 | varchar(50) | 触发变更的业务类型 | AO（开户）、TE（资料修改） |
| 国籍 | varchar(100) | 变更涉及的国籍 | 加拿大 |
| 国籍代码 | varchar(3) | 国籍对应的国家代码 | CAN |
| 风险等级 | varchar(10) | 国家风险等级 | low, medium, high |
| 变更类型 | varchar(20) | 变更操作类型 | 新增、修改、删除 |

- Tab-基本信息-税务信息

香港作为共同申报准则（CRS）及美国《海外账户税收合规法案》（FATCA）的参与司法区，银行在开户及客户信息维护过程中须履行税务居民身份识别（Tax Residency Identification）。ZA Bank作为持牌银行，必须在客户开户、资料变更、定期审查等阶段，收集并维护客户的税务居民信息。

**页面布局**

- 唯一性规则：同一客户（cif_id）下最多允许4条有效记录

- 国家代码验证：tax_country_code 必须符合 ISO-3166 标准，且不能重复

- TIN必填规则：除非符合"不需提供TIN"的合规原因，否则TIN为必填

- 美国税务居民检查，如果税务国家为美国或者美国本土外小岛屿， 如tax_country_code = 'US'，fatca_status 必须为"US Person"

**字段列表**

| 字段名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 客户主键 | VARCHAR(36) | CIF系统唯一客户编号 |
| 税务国家序号 | INT | 1~4，支持最多4个税务居民国家 |
| 税务居民国家（ISO 3166） | CHAR(2) | 使用标准ISO-3166国家代码 |
| 税务居民国家（中文） | VARCHAR(128) | 简体中文显示 |
| 税务居民国家（英文） | VARCHAR(128) | 英文显示 |
| 税务识别号（TIN） | VARCHAR(64) | 若无须提供需填写原因 |
| 未提供TIN原因 | ENUM('A','B','C') | A=国家不发放TIN，B=未能取得TIN，C=其他原因（附说明） |
| 其他原因说明 | VARCHAR(255) | 当原因为C时填写 |
| FATCA身份 | ENUM('US Person','Non-US Person') | 用于FATCA申报判断 |
| 税务声明日期 | DATE | 记录客户完成声明的日期 |
| 最后更新时间 | DATETIME | 系统时间戳 |

**税务信息历史记录**

功能描述：提供税务信息的完整变更历史，支持按时间倒序查看所有修改记录。

- 新增记录：记录所有字段的初始值

- 修改记录：记录变更前后的字段对比

- 删除记录：记录删除的完整信息

- 支持分页显示，每页10条记录（short分页设置）

- Tab-基本信息-地址信息

提供统一的地址信息管理界面，支持居住地址和通讯地址的分别管理，实现地址变更历史的完整追踪，满足银行合规和审计要求

**页面布局**

**字段列表**

| 字段名称 | 字段说明 |
| --- | --- |
| 居住地址 | 客户实际居住的完整地址信息 |
| 通讯地址 | 客户接收邮件和通讯的地址，可与居住地址相同 |

**地址****信息历史记录**

功能描述：提供税务信息的完整变更历史，支持按时间倒序查看所有修改记录。

| **字段名称** | **数据类型** | **字段说明** |
| --- | --- | --- |
| 变更时间 | DATETIME | 地址变更的具体时间，格式：YYYY-MM-DD HH:mm:ss |
| 业务编号 | VARCHAR(50) | 关联的业务流程唯一编号 |
| 业务类型 | VARCHAR(100) | 业务类型：定期审查、客户申请等 |
| 地址类型 | VARCHAR(20) | 地址类型：居住地址、通讯地址 |
| 变更后地址 | VARCHAR(500) | 变更后的完整地址信息 |

- Tab-基本信息-就业信息

就业信息是客户基本信息的重要组成部分，包括就业状态、职业类型、所属行业、职位名称、公司信息、工作地址、收入信息等核心信息的管理。该模块支持就业信息变更历史记录追踪，满足香港金融管理局（HKMA）监管要求和银行合规需求，为风险评估、收入验证、KYC审核、合规审计等业务流程提供数据支持。并要求提供完整的客户就业信息变更审计轨迹，确保数据可追溯性

**页面布局**

**字段列表**

| **字段名称** | **字段说明** | **示例值** |
| --- | --- | --- |
| **就业状态** | 客户的就业状态分类 | 受雇 |
| **职业类型** | 客户的具体职业 | 软件工程师 |
| **所属行业** | 客户所在的行业分类 | 信息技术 |
| **职位名称** | 客户在公司的具体职位 | 高级软件工程师 |
| **公司名称** | 客户工作的公司名称 | 众安银行（香港）有限公司 |
| **公司电话** | 公司联系电话 | +852 3001 8888 |
| **工作邮箱** | 客户的工作邮箱地址 | john.wong@za.group |
| **工作地址** | 客户工作的完整地址 | 香港中环皇后大道中99号中环中心38楼 |
| **工作年限** | 客户的工作经验年限 | 5年 |
| **年收入** | 客户的年收入金额 | HK$600,000 |
| **收入区间** | 客户收入的区间分类 | 20万-50万 |
| **收入来源说明** | 详细描述收入的构成和来源 | 主要来源于软件开发工作薪资，包括基本工资、绩效奖金和年终奖金 |
| **雇佣日期** | 客户入职的具体日期 | 2019-03-15 |
| **备注** | 就业信息的补充说明 | 信息已通过KYC验证 |

**就业****信息历史记录**

功能描述：提供税务信息的完整变更历史，支持按时间倒序查看所有修改记录。

**字段列表**

| 字段名称 | 字段说明 | 示例值 |
| --- | --- | --- |
| 变更时间 | 就业信息变更的具体时间 | 2024-01-15 14:30:25 |
| 业务编号 | 关联的业务流程编号 | EMP202401150001 |
| 业务类型 | 如开户， 年检等 | 年检 |
| 变更类型 | 新增、修改、删除的标识 | 修改 |
| 职业信息 | 变更后的职业信息 | 高级软件工程师 |
| 公司名称 | 变更后的公司名称 | 众安银行（香港）有限公司 |
| 行业信息 | 变更后的行业信息 | 金融服务 |
| 月收入 | 变更后的月收入 | HK$55,000 |
| 年收入 | 变更后的年收入 | HK$660,000 |
| 变更原因 | 就业信息变更的具体原因 | 客户主动更新就业信息 |

- 对公客户管理

- 客户查询

功能描述：实现多维度的客户信息搜索，支持单一条件进行精准查询。

- 客户查询条件

**页面布局：**

**查询条件****字段说明：**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| All Customers | 按钮 | 筛选出所有的客户 |
| Today’ Onboarding | 按钮 | 筛选出所有今日onboarding的客户 |
| Accounts Opened in 7 Days | 按钮 | 筛选出所有在7日内开户的客户 |
| Periodic Review Due Today | 按钮 | 筛选出今日年检到期的客户 |
| Periodic Review Due in 7 Days | 按钮 | 筛选出年检还有7天到期的客户 |

**页面布局：**

**查询条件****字段说明：**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client No. | 文本输入 | 系统分配的唯一客户标识 |
| Company English Name | 文本输入 | 公司英文名称（支持模糊查询） |
| Company Chinese Name | 文本输入 | 公司中文名称（支持模糊查询） |
| ID Type | 下拉选择 | 客户的证件类型筛选 |
| ID Number | 文本输入 | 客户的证件号码 |
| Open Channel | 下拉选择 | 开户渠道筛选 |
| Open Date | 日期范围选择 | 客户开户时间范围筛选 |
| Next Review Date | 日期范围选择 | 年检到期时间范围筛选 |
| Company Type | 下拉选择 | 公司类型筛选 |
| Client Risk | 下拉选择 | 客户风险等级筛选 |

- 客户列表展示功能

功能描述：展示所有或者筛选

出的客户的部分信息，支持详情查看操作。

**页面布局：**

**表头****字段说明：**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client No. | 文本显示 | 系统分配的唯一客户标识 |
| Company Type | 文本显示 | 公司类型 |
| ID Type | 文本显示 | 客户的证件类型 |
| ID Number | 文本显示 | 客户的证件号码 |
| Company English Name | 文本显示 | 公司英文名称 |
| Open Channel | 文本显示 | 开户渠道 |
| Last Review Date | 文本显示 | 上次审查日期 |
| Next Review Date | 文本显示 | 下次审查日期 |
| Client Status | 标签显示 | 客户状态 |
| Client Risk | 标签显示 | 客户风险等级 标签颜色：Low risk-绿色；Medium risk-黄色；High risk-红色 |
| Action | 按钮 | 点击按钮，跳转到客户详情展示页面 |

设计样式，如果没有特别说明，所有数据列表需要分页配置的，都使用相同的配置。

**操作功能**

查看详情：点击"Detail"按钮跳转到客户详情页面

- 客户详情页面功能

功能描述：提供客户完整信息的显示和管理功能，包括基本信息、资产概览、签名信息等，为运营人员提供全面的客户信息。

- Overview 

Overview部分主要提供客户信息的概览，旨在为用户提供客户关键信息的快速浏览和识别功能。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client NO. | 文本显示 | 系统分配的唯一客户标识 |
| Company Type | 文本显示 | 公司类型 枚举值：详见12 其他附件中的Company Type数据字典 |
| ID Type | 文本显示 | 客户的证件类型 枚举值： CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |
| ID Number | 文本显示 | 客户的证件号码 |
| Company English Name | 文本显示 | 公司英文名称 |
| Company Chinese Name | 文本显示 | 公司中文名称 |
| Client Status | 文本显示 | 客户状态 存在多个状态 |
| Client Risk | 文本显示 | Risk scoring的风险等级 枚举值：High risk、Medium risk、Low risk |
| Account Officer | 文本显示 | 对应的客户经理 |

功能描述：点击“返回上一页”按钮可以回到上一页即客户查询页面。

- Tab-Company Info

Company Info部分主要提供公司客户的详细信息，例如基本信息、税务信息、地址信息，是公司客户详情页面的核心功能。

- Tab-Company Info-Company Base Information

Company Base Information部分是CIF系统中对公客户管理中核心的信息展示模块，以表格形式展示客户的基础信息关键数据。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Company English Name | 文本显示 | 公司英文名稱 |
| Company Chinese Name | 文本显示 | 公司中文名稱 |
| ID Type | 文本显示 | 證件類型 CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |
| ID Number | 文本显示 | 證件號碼 |
| Company Type | 文本显示 | 公司類型 枚舉值：参考12.1 Company Type数据字典 |
| Company Establish Date | 文本显示 | 公司成立日期 |
| Country of Registration | 文本显示 | 註冊國家 |
| Listing Company | 文本显示 | 是否上市公司 枚舉值：Y:Yes，N:No |
| High Tax Evasion Risk | 文本显示 | 是否高逃稅風險標誌 枚舉值：Y:Yes，N:No |
| Name(s) of Stock Exchange | 文本显示 | 证券交易所名称 |
| Bearer Shares Issued | 文本显示 | 是否可以發行/已經發行不記名股票 枚舉值：Y:Yes，N:No |
| Bearer Shares Flag | 文本显示 | 是否为能够发行无记名股票的中介所有者 枚舉值：Y:Yes，N:No |
| Channel | 文本显示 | 開戶渠道 枚舉值： O: Solely E-Onboarding Channel W: Walk-in customer A: Approved Business Partner Referral I: Internal customer B: RM Referral |
| Usage of products | 文本显示 | 高風險產品類型 枚舉值：01：Low Risk Product Only；03：At Least One High Risk Product |
| On PEC | 文本显示 | 是否政府標誌 枚舉值：Y:Yes，N:No |
| Number of layers of ownership structure | 文本显示 | 公司股權結構層數 |
| Account Officer | 文本显示 | 客戶經理代碼 |
| Account Officer Name | 文本显示 | 客户经理姓名 |
| Country Of Risk | 文本显示 | 風險國家 |
| Country Of Origin | 文本显示 | 总部注册国家 |
| Trading Name（English） | 文本显示 | 交易名稱（英文）（支持最多10個） |
| Trading Name（Chinese） | 文本显示 | 交易名稱（中文）（支持最多10個） |
| Classify | 文本显示 | 公司分類 枚舉值： S - SME C - CORP N - NBFI Z - ZA GROUP |

- Tab-Company Info- Contact Details

Contact Details页面提供统一的公司联络信息管理界面，包括主要联络人的联络信息。在该页面中，支持对公司地址信息的变更历史查询。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Company Registered Address | 文本显示 | 註冊地址 |
| Company Operation Address | 文本显示 | 營業地址 |
| Company Mailing Address | 文本显示 | 通訊地址 |
| Declaration | 文本显示 | 聲明註冊地址/營業地址是否為秘書公司地址 1-The registered address or business address is an address of a company secretarial firm 2-Both the registered address and business address addresses of a company secretarial firm 3-The registered address or business address is a residential address 4-Both the registered address and business address are residential addresses |
| Reason | 文本显示 | 註冊地址/營業地址為秘書公司地址的原因 |
| Contact Person First Name | 文本显示 | 聯絡人名字 |
| Contact Person Last name | 文本显示 | 聯絡人姓氏 |
| Contact Person Email Address | 文本显示 | 聯絡人電郵地址 |
| Contact Person Telephone Number | 文本显示 | 聯絡人手機號碼 |
| Contact Person Job Title | 文本显示 | 聯絡人頭銜 枚舉值：01：董事；02：商人；03：東主；04：總經理；05：行政人員；06：高級行政人員；07：財務總監；08：秘書；09：其他 |
| Other Contact Person Job Title | 文本显示 | 當字段“Contact Person Job Title”選擇“09：其他”時，該字段必輸； |
| Office Telephone Number | 文本显示 | 辦事處電話 |
| Company Website | 文本显示 | 公司網頁 |
| Group CIF Number | 文本显示 | 集團客戶號碼 |
| Group CIF Name | 文本显示 | 集團名稱 |

- **地址变更历史**

功能描述：对公司地址（注册地址、营业地址、通讯地址）的每一条变更和维护记录都进行展示，支持按时间倒序查看所有修改记录。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| 变更时间 | 文本显示 | 变更的时间 |
| 业务编号 | 文本显示 | 变更的业务编号 |
| 业务类型 | 标签 | 变更的业务类型 枚举值：Account Opening，Periodic Review，Trigger Event |
| 地址类型 | 文本显示 | 变更的地址类型 枚举值：注册地址、营业地址、通讯地址 |
| 变更后信息 | 文本显示 | 变更后的完整地址信息 |

- Tab-Company Info- Company Business Background

Company Business Background部分展示公司客户的业务背景的相关信息。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Office Location(s)􉳵􂰖􃹔􃖑􋟊 | 文本显示 | 辦公室地點 枚舉值：國家列表 |
| Principal Place of Operations | 文本显示 | 主要運營地 枚舉值：國家列表 |
| Countries of Foreign Branch(es)/Subsidiary(ies) / Factory(ies) | 文本显示 | 海外分支/子公司/工廠所在的國家/地區（支持最多增加10個） 枚舉值：國家列表 |
| Country of Key Counterparties | 文本显示 | 主要交易對手所在國家（支持最多增加5個） 枚舉值：國家列表 |
| Industry Category | 文本显示 | 行業大類 |
| Key Products/ Services | 文本显示 | 行業小類 |
| Name of Regulatory Body | 文本显示 | 監管機構名稱 |
| Regulatory Reference Number | 文本显示 | 監管機構參考編號 |
| Country of Regulatory Body | 文本显示 | 監管機構所在國家 枚舉值：國家列表 |
| Country of Employee(s)/ Number of Employee(s) | 文本显示 | 僱員所在國家/僱員數目（最多支持5組） 枚舉值：國家列表 |
| Whether WEB3 Company | 文本显示 | 是否WEB3公司 枚舉值：Y:Yes，N:No |
| Whether Greater China Company | 文本显示 | 是否Greater China公司 枚舉值：Y:Yes，N:No |
| Financial Background | 文本显示 | 公司经济类型 枚举值： 1110:STATE-OWNED(国有) 1120:COLLECTIVELY-OWNED(集体) 1140:JOINT OPERATION(联营) 1160:JOINT STOCK(股份) 1170:PRIVATE-OWNED(私营) 1175:INDIVIDUAL-OWNED(个体) 1200:FOREIGN CAPITAL(外资) 1330:HONG KONG,MACAU,OR TAIWAN CAPITAL(港澳台资) 9999:OTHERS(其他) |
| Is your Company a Holding Company | 文本显示 | 是否為控股公司 枚舉值：Y:Yes，N:No |
| Purpose for Account Opening | 文本显示 | 開戶目的（最多支持5個） 枚舉值： BO:BUSINESS OPERATIONS IS:PAYMENT OF HIRE IV:INVESTMENT SF:SAVING / FIXED DEPOSIT PR:PAYROLL LO:LOAN OT:OTHER |
| Others(Please Specify) | 文本显示 | 其他開戶目的 |
| Referral Party | 文本显示 | 開戶推薦人名稱 开户渠道是RM开户的，才需要展示该字段且必输 |

- Tab-Company Info- Financial Indicator of Latest Fiscal Year

Financial Indicator of Latest Fiscal Year部分是对客户最新财政年度的财务指标的管理，显示是否在制裁国家有相关业务的信息。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Annual Business Turnover(HKD) | 文本显示 | 全年營業額（等值港幣） |
| Net Profit(HKD) | 文本显示 | 淨利潤（等值港幣） |
| Asset Size(HKD) | 文本显示 | 資產規模（等值港幣） |
| Others(Please Specify) | 文本显示 | 其他 |
| Name of the Jurisdiction | 文本显示 | 司法管轄權（公司在制裁國家有進行商業活動的）（最多支持5個） 枚舉值：國家列表，第一個默認香港。 |
| Purpose of business in the Jurisdiction | 文本显示 | 於制裁國家進行商業行為之目的（最多支持5個） |
| Does the Company, to the best of your knowledge, have any current or planned business activity in the following countries / regions (Iran, Sudan, North Korea, Syria, Cuba, South Sudan, Crimea or Zimbabwe | 文本显示 | 公司目前或计划在伊朗、苏丹、朝鲜、叙利亚、古巴、南苏丹、克里米亞或津巴布韦等国家/地区开展业务活动吗？ 枚舉值：Y:Yes，N:No |
| To the best of your knowledge, does the company or any of the company's connected or other related parties have a presence in Iran, Sudan, North Korea, Syria, Crimea or Cuba, and/or are currently targeted by sanctions administrated by the following bodies: UN, EU, UKHMT, HKMA, OFAC, or as part of local sanctions law | 文本显示 | 据你所知，貴公司或貴公司的任何关联方或其他相关方是否存在在伊朗、苏丹、朝鲜、叙利亚，克里米亞或古巴，以及/或目前是以下国家实施制裁的目标 机构:联合国、欧盟、UKHMT、金管局、OFAC，或作为当地制裁法律的一部分 枚舉值：是/否 |
| Does the company or, to the best of your knowledge, any of its connected or other related parties have any current or planned business activity in Crimea / Sevastopol regions | 文本显示 | 据您所知，貴公司或其任何关联方或其他关联方是否有任何现有或计划在克里米亚/塞瓦斯托波尔地区进行的商业活动 枚舉值：是/否 |
| Does the company have any current or planned exposure to Russia in the energy / oil and gas sector, the military or defense | 文本显示 | 公司目前或计划在俄罗斯的能源/石油和天然气部门、军事或国防领域有任何业务 枚舉值：是/否 |
| Whether the annual turnover of the group exceeds 1.5 billion | 文本显示 | 该集团的年营业额是否超过 15 亿 枚舉值：Y:Yes，N:No |
| Non SME flag | 文本显示 | 是否SME標誌 枚舉值：Y:Yes，N:No |

- Tab-Company Info- Source of Wealth/Fund

Source of Wealth/Fund部分是对财富来源和资金来源信息的管理，显示相关信息。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Initial Source of Wealth | 文本显示 | 初始财富来源（最多支持輸入5個） 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET |
| Other Initial Source of Wealth | 文本显示 | 其他初始财富来源（最多支持輸入5個） |
| Ongoing Source of Wealth | 文本显示 | 持續财富来源（最多支持輸入5個） 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET |
| Other Ongoing Source of Wealth | 文本显示 | 其他持續财富来源（最多支持輸入5個） |
| Initial Source of Fund | 文本显示 | 初始資金来源（最多支持輸入5個） 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET |
| Other Initial Source of Fund | 文本显示 | 其他初始資金来源（最多支持輸入5個） |
| Ongoing Source of Fund | 文本显示 | 持續資金来源（最多支持輸入5個） 枚舉值 BI:BUSINESS INCOME IF:INTRA-GROUP FINANCING IP:INVESTMENT BY ULTIMATE BENEFICIAL OWNER / PARTNER OT:OTHER SA:SALE OF PROPERTY / ASSET |
| Other Ongoing Source of Fund | 文本显示 | 其他持續資金来源（最多支持輸入5個） |

- Tab-Company Info-Anticipated Transaction & Purpose of Usage

Anticipated Transection & Purpose of Usage界面显示公司客户的预期交易及用途的详细信息，是对具体交易信息的管理。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Domestic / International Transaction | 文本显示 | 本地/國際交易 枚舉值： A：本地電匯(Domestic Payment) B：國際電匯(International Payment) C：本地和國際電匯(Domestic and International Payment) |
| Does the Company intent to make Cross Border Transaction | 文本显示 | 公司會否進行跨境交易 枚舉值：Y:Yes，N:No |
| Countries (to) of Cross Border Transaction | 文本显示 | 匯出國家（最多支持10個） 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：國家列表 |
| Countries (from) of Cross Border Transaction | 文本显示 | 匯入國家（最多支持10個） 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：國家列表 |
| Monthly Number of Cross Border Transaction (Inward) | 文本显示 | 每月跨境轉賬交易量（匯入） 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly Inward Amount of Cross Border Transaction - Currency | 文本显示 | 每月跨境轉賬貨幣（匯入） 枚舉值：11種貨幣列表 |
| Monthly Inward Amount of Cross Border Transaction - Amount | 文本显示 | 每月跨境轉賬金額（匯入） |
| Monthly Number of Cross Border Transaction (Outward) | 文本显示 | 每月跨境轉賬交易量（匯出） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly Outward Amount of Cross Border Transaction - Currency | 文本显示 | 每月跨境轉賬貨幣（匯出） 枚舉值：11種貨幣列表 |
| Monthly Outward Amount of Cross Border Transaction - Amount | 文本显示 | 每月跨境轉賬金額（匯出） |
| Purpose for Cross-Border Transaction | 文本显示 | 跨境交易目的，支持多選 當問題“公司會否進行跨境交易”為“Y時必輸” 枚舉值： Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） |
| Other Purpose for Cross-Border Transaction | 文本显示 | 其他跨境交易目的 |
| Does the Company intend to make Regular Domestic Transaction | 文本显示 | 公司會否進行常規本地交易 枚舉值：Y:Yes，N:No |
| Monthly Number of Regular Transaction (Inward) | 文本显示 | 每月本地轉賬交易量（匯入） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly Inward Amount of Regular Transaction - Currency | 文本显示 | 每月本地轉賬貨幣（匯入） 枚舉值：11種貨幣列表 |
| Monthly Inward Amount of Regular Transaction - Amount | 文本显示 | 每月本地轉賬金額（匯入） |
| Monthly Number of Regular Transaction (Outward) | 文本显示 | 每月本地轉賬交易量（匯出） 枚舉值：1至20宗；21至50宗；50宗以上 |
| Monthly Outward Amount of Regular Transaction - Currency | 文本显示 | 每月本地轉賬貨幣（匯出） 枚舉值：11種貨幣列表 |
| Monthly Outward Amount of Regular Transaction - Amount | 文本显示 | 每月本地轉賬金額（匯出） |
| Purpose for Regular Transaction | 文本显示 | 本地交易目的，支持多選 當問題“公司會否進行常規本地交易”為“Y時必輸” 枚舉值： Trading 貿易 Investment 投資 Rental Payment / Collection 支付/收取租金 Payroll 支付薪金 Others(Please Specify): 其他（請註明） |
| Other Purpose for Regular Transaction | 文本显示 | 本地其他交易目的 |
| Does the Company intend to hold balances in the account with infrequent activity | 文本显示 | 公司是否預期在賬戶持有結餘而不進行交易 枚舉值：Y:Yes，N:No |
| Please indicate the amount and rationale | 文本显示 | 請提供金額及理由 |
| Estimated the monthly payment amount | 文本显示 | 估計每月付款金額 枚举值： 0至60,000港元 60,001至600,000港元 600,001至3000,000港元 3000,001至6,000,000港元 6,000,000港元以上 |
| Estimated the amount of monthly payments | 文本显示 | 估計每月付款數量 枚举值： 1至20宗 21至50宗 50宗以上 |
| Estimated monthly payee amount | 文本显示 | 估計每月收款金額 枚举值： 0至60,000港元 60,001至600,000港元 600,001至3000,000港元 3000,001至6,000,000港元 6,000,000港元以上 |
| Estimate the amount of monthly payee | 文本显示 | 估計每月收款數量 枚举值： 1至20宗 21至50宗 50宗以上 |

- Tab-Company Info- FATCA/CRS

FATCA/CRS部分就是对公司客户的FATCA/CRS信息的管理与展示。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| FATCA Documentation | 文本显示 | **FATCA 表格類型：** 01 - Form W-8BEN-E 02 - Form W-9 03 - Self-certification 04 - Others – Please specify 05 - Form W-8ECI 06 - Form W-8EXP 07 - Form W-8BEN 08 - Form W-8IMY and Withholding Statement 09 - Substitute Form W-8BEN-E |
| Others – Please specify | 文本显示 | 其他FATCA 表格類型 |
| FATCA Classification | 文本显示 | **FATCA 身份類型** 01:Active NFFE-501(c) organization 02:Active NFFE-Active NFFE 03:Active NFFE-Direct reporting NFFE 04:Active NFFE-Excepted nonfinancial entity in liquidation/bankruptcy 05:Active NFFE-Excepted nonfinancial start-up company 06:Active NFFE-Excepted territory NFFE 07:Active NFFE-Foreign gov/gov of U.S. possession/non-MO/US central bank of issue 08:Active NFFE-International orgainzation(non-MO) 09:Active NFFE-Nonfinancial group entity 10:Active NFFE- Nonprofit organization 11:Active NFFE-Publicly traded NFFE or its NFFE affiliate 12:Active NFFE-Sponsored direct reporting NFFE 13:CDCFFI-CDCFFI with only low-value accounts 14:CDCFFI-CDC investment advisors and investment managers 15:CDCFFI-CDC limited life debt investment entity 16:CDCFFI-Certified deemed-compliant nonregistering local bank 17:CDCFFI-CDC sponsored, closely held investment vehicle 18:CDCFFI-Nonreporting IGA FFI 19:EBO-Entity wholly owned by exempt beneficial owners 20:EBO-Exempt retirement plans 21:EBO-MO government and subdivisions,MO central bank of issue 22:EBO-International organization with MO presence 23:EBO-Territory financial institution 24:Excepted inter-affiliate FFI 25:FFI-Nonparticipating FFI,including limited FFI/branch 26:FFI-Participating FFI 27:FFI-Reporting Model 2 FFI 28:FFI-Reporting Mdoel 1 FFI 29:ODFFI-Owner-documented FFI with no specified U.S. Owner(s) 30:ODFFI-Owner-documented FFI with specified U.S. Owner(s) 31:Passive NFFE with no substantial U.S. owners 32:Passive NFFE with substantial U.S. owner(s) 33:RDCFFI-Registered deemed-compliant FFI 34:RDCFFI-Sponsored FFI that has not obtained a GIIN 35:Restricted distributor 36:Specified U.S. Person 37:Non-consenting U.S. account 38:U.S. Person other than Specified U.S. Person 39: Non-U.S. Person |
| Non-consenting U.S. account Sub-type | 文本显示 | **非自願同意的美國帳戶類型** 01 - USP 02 - Passive NFFE 03 - Dormant 04 - With US indicia 05 - Without US indicia  當FATCA Classification選擇"37: Non-consenting U.S. account" 時，該字段必輸 |
| Disregarded entity | 文本显示 | 非獨立實體公司 |
| Sign Date of FATCA Documentation | 文本显示 | 簽署FATCA表格日期 |
| FATCA Due Diligence Status | 文本显示 | FATCA盡職調查狀態 01:Completed - Normal FATCA 02:Completed - Enhanced FATCA Due Diligence 03:Incomplete 04:N/A |
| CRS Entity Classification | 文本显示 | CRS實體類型（不適用於獨資公司） |
| Sign Date of CRS Documentation | 文本显示 | 簽署CRS表格日期 |
| Tax Residency | 文本显示 | 稅務國家/地區（最多10个） |
| Tax identifying number（TIN） | 文本显示 | 稅務號碼（最多10个） |
| Reason for No TIN | 文本显示 | 無稅務編號原因（最多10个） 枚舉值如下： Reason A - The jurisdiction where the controlling person is a resident for tax purposes does not issue TINs to it residents. Reason B - The controlling person is unable to obtain a TIN.  Reason C - TIN is not required. |
| GIIN Code | 文本显示 | 全球中介機構識別號碼  當FATCA Classification為下列值時必輸 12-Active NFFE-Sponsored direct reporting NFFE 26-FFI-Participating FFI 27-FFI-Reporting Model 2 FFI 28-FFI-Reporting Model 1 FFI 03-Active NFFE-Direct reporting NFFE 33-RDCFFI-Registered deemed-compliant FFI |
| GIIN Status | 文本显示 | GIIN 狀態 枚舉值： ACTIVE,INACTIVE,REVOKED,EXPIRED |

- Tab-Company Info-Company Change

Company Change界面包括公司客户在5年内变更信息的记录，是对公司名称、产品与服务类型、公司股权结构、收益所有人信息变更的管理。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Have there been Material Changes to your Business in the last 5 years? | 文本显示 | 在过去的5年里，貴公司有过实质性的变化吗?(如所有权、公司名称、母公司的变更、经营国家、业务性质、产品/服务) 枚舉值：Y:Yes，N:No |
| Company Name Changed | 文本显示 | 公司名稱是否有變更過 枚舉值：Y:Yes，N:No |
| Old Company Name *10 | 文本显示 | 舊公司名稱，支持中文和英文名稱 |
| Products and Services Changed | 文本显示 | 產品與服務是否有改變過 枚舉值：Y:Yes，N:No |
| Old Industry Category/ Key Products/ Services *10 | 文本显示 | 舊行業大類名稱/舊行業小類名稱 |
| Ownership structure Changed | 文本显示 | 公司股權結構是否改變過 枚舉值：Y:Yes，N:No |
| Ownership structure Changed Period | 文本显示 | 公司股權結構改變開始日期 |
| Reasons for changing Company Ownership structure *1 | 文本显示 | 公司股權結構改變結束日期 |
| Beneficial Owners Changed | 文本显示 | 改變公司股權結構的原因 |
| Beneficial Owners Changed Period | 文本显示 | 收益所有人是否有改變過 枚舉值：Y:Yes，N:No |
| Reasons for changing Beneficial Owners *1 | 文本显示 | 收益所有人改變開始日期 |

- Tab-Company Info-Company Search–Company Secretary Information

Company Search–Company Secretary Information界面部分展示搜寻到的秘书公司的信息。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| ID Type | 文本显示 | 秘書公司證書類型 |
| ID Number | 文本显示 | 秘書公司證書號碼 |
| Company English Name | 文本显示 | 秘書公司英文名稱 |
| Company Chinese Name | 文本显示 | 秘書公司中文名稱 |
| Secretary Address | 文本显示 | 秘書公司-公司地址 |

- Tab-Company Info-Other Information

Other Information部分展示前面未展示的信息，作为公司相关信息的补充，使信息更加完整全面。

- **页面布局**

- **表头字段****说明**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client Open Date | 文本显示 | 客户开立日期 |
| Client Close Date | 文本显示 | 客户关闭日期 |
| Client Risk | 文本显示 | 客户风险級別 枚举值：H-High Risk；M-Medium Risk；L-Low Risk |
| CDD Last Review Date | 文本显示 | CDD上次审查日期 |
| CDD Next Review Date | 文本显示 | CDD下次审查日期 |
| High Risk Remarks | 文本显示 | 高風險說明 |

- Tab-Connected Party

Connected Party界面提供统一的相关人士信息管理界面，支持相关人士-自然人和相关人士-公司信息的分别管理。

- Tab-Connected Party-Individual

Individual模块提供统一的相关人士-自然人信息的管理界面。

- **页面布局**

- **表头字段****说明**

| **字段** | **字段类型** | **說明** |
| --- | --- | --- |
| ID Type | 文本显示 | 證件類型 |
| Other ID Type | 文本显示 | 其他證件類型 |
| ID Number | 文本显示 | 證件號碼 |
| First Name | 文本显示 | 英文名 |
| Last Name | 文本显示 | 英文姓氏 |
| Chinese Name | 文本显示 | 中文姓名 |
| Capacity of Connected Party | 文本显示 | 相關人士角色 |
| Controlling Person Type | 文本显示 | 實際控制人類型，當Capacity of Connected Party的類型存在Controlling Person時，則該字段為必輸 |
| Sign Date of CRS Documentation(for Controlling Person) | 文本显示 | 簽署CRS表格日期 |
| Tax residency | 文本显示 | 稅務國家/地區 *10 |
| Tax identification number ("TIN") | 文本显示 | 稅務號碼 * 10 |
| Reason for no TIN *5 | 文本显示 | 無稅務編號原因 |
| Ownership | 文本显示 | 股份佔比 |
| Job Title | 文本显示 | 職銜 |
| Permanent HKID | 文本显示 | 是否永久香港身份證 |
| ID Issue Date | 文本显示 | 身份證明文件簽發日期 |
| Gender | 文本显示 | 性別 |
| Date of Birth | 文本显示 | 出生日期 |
| Place of Birth | 文本显示 | 出生地 |
| Nationality | 文本显示 | 國籍（支持最多5個） |
| Email Address | 文本显示 | 電郵地址 |
| Telephone Number | 文本显示 | 電話號碼 |
| Residential Address | 文本显示 | 居住地址 |
| Beneficial Owner Source of Wealth (Only for Beneficial Owner) | 文本显示 | 實益擁有人的財富來源（只適用於實益擁有人） |
| Others(Please Specify) | 文本显示 | 當Beneficial Owner Source of Wealth選擇other時，該字段必輸 |
| Country(ies) in which Beneficial Owner attained his/her Initial Source of Wealth  (Only for Beneficial Owner) *5 | 文本显示 | 實益擁有人獲得初始財富來源的國家 （只適用於實益擁有人），支持多選 |
| Country(ies) in which Beneficial Owner attained his/her Ongoing Source of Wealth (Only for Beneficial Owner)  *5 | 文本显示 | 實益擁有人獲得持續財富來源的國家 （只適用於實益擁有人），支持多選 |
| Related Client On PEP | 文本显示 | PEP標誌 |

- **相关人士-自然人变更历史**

功能描述：提供相关人士-自然人的姓名、邮箱、电话、居住地址等信息的完整变更历史，支持按时间倒序查看所有修改记录。

- **页面设计**

- **表头字段****说明**

| **字段** | **字段类型** | **說明** |
| --- | --- | --- |
| 变更时间 | 文本显示 | 变更的时间 |
| 业务编号 | 文本显示 | 变更的业务编号 |
| 业务类型 | 标签 | 变更的业务类型 |
| 变更字段名称 | 文本显示 | 变更的地址类型 |
| 变更后信息 | 文本显示 | 变更后的完整信息 |

- Tab-Connected Party-Entity

Entity模块提供统一的相关人士-公司（法人团体）信息的管理界面。

- **页面布局**

- **表头字段****说明**

| **字段** | **字段类型** | **說明** |
| --- | --- | --- |
| ID Type | 文本显示 | 證件類型 CI-CERTIFICATE OF INCORORATION BR-BUSINESS REGISTRATION CERTIFICATE |
| Other ID Type | 文本显示 | 其他證件類型 |
| ID Number | 文本显示 | 證件號碼 |
| Registered Full Name in English | 文本显示 | 公司英文名稱 |
| Registered Full Name in Chinese (if applicable)# | 文本显示 | 公司中文名稱 |
| Entity Type of Connected Entity | 文本显示 | 相關人士實體類型（公司類型） 枚舉值： CO: LIMITED COMPANY (UNLISTED) CS: COOPERATIVE SOC EM: EMBASSY FD: FOUNDATION FI: FINANCIAL INSTITUTION FU: FUND GO: GOVERNMENT DEPARTMENT HC: HOLDING COMPANY JC: JOINT CORP LC: LIMITED COMPANY (LISTED) MS: MONEY SERVICE BUSINESS NG: NATIONAL OR LOCAL BODY GOVERNMENT PC: PRIVATE INVESTMENT COMPANY PF: PRIVATE INVESTMENT FUND PT: PRIVATE HOLDING TRUST SB: STATUTORY BODY SO: SOCIETY SP: SOLE PROPRIETORSHIP TR: TRUST TU: TRADE UNION  VS: VOLUNTARY SECTOR  JP: JOINT PERSON PA: Partnership |
| Capacity of Connected Entity | 文本显示 | 相關人士角色 枚舉值： 085-Beneficial Owner 034-Director 065-Partner 114-Sole Proprietor 084-Beneficiary 069-Shareholder ≥25% 103-Senior Officer 088-Authorised Representative 019-Internet Banking Representative 045-Guarantor (For Loan) 060-Trustee 104-Settlor 105-Protector 117-Project S Contact Person 118-Controlling Person (for Passive NFE) 119-Contact Person 120-BIB Maker 121-BIB Administrator |
| Ownership | 文本显示 | 股份佔比 |
| Listing on Stock Exchange | 文本显示 | 是否上市公司 枚舉值：Y:Yes，N:No |
| Name(s) of Stock Exchange | 文本显示 | 证券交易所名称 |
| Registered Office Address (Only for Senior Office, Director, Authorised Representative)# | 文本显示 | 公司註冊地址 |
| Principal Business Address (Only for Senior Office)# | 文本显示 | 主要營業地址 |

- **相关人士-公司变更历史**

功能描述：提供相关人士-公司名称、注册地址、运营地址等信息的完整变更历史，支持按时间倒序查看所有修改记录。

- **页面设计**

- **表头字段****说明**

| **字段** | **字段类型** | **說明** |
| --- | --- | --- |
| 变更时间 | 文本显示 | 变更的时间 |
| 业务编号 | 文本显示 | 变更的业务编号 |
| 业务类型 | 标签 | 变更的业务类型 |
| 变更字段名称 | 文本显示 | 变更的地址类型 |
| 变更后信息 | 文本显示 | 变更后的完整信息 |

- Tab-Asset Overview

Asset Overview是对公司客户的账户信息的展示。

- Tab-Asset Overview-Asset Overview

Asset Overview主要展示客户所有账户的资产总额，包括所有的活期账户和定期账户。

- **页面布局**

 

- **表头字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Currency (HKD equivalent) | 下拉选择 | 可以选择货币种类 枚举值：HKD,USD,CNY |
| Total amount (HKD equivalent) | 文本显示 | 根据选择的币种将所有账户的余额兑换为等值的币种金额，包括所有活期和定期 |

功能描述： 在Currency处可以选择HKD、USD、CNY货币中的一种。当选择一种货币后，将会在Total amount处展示该客户所有账户的余额兑换为等值货币的金额。

- Tab-Asset Overview-Application Information

Application Information部分是对账户详细信息的展示。

- **活期账户信息-****页面布局**

- **活期账户信息-****表头字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Application Type | 文本显示 | 存款类型 DD:活期 |
| Account/Contract No. | 文本显示 | 活期賬號 點擊賬號也可以彈窗顯示二級列表-貨幣餘額列表 |
| Account Type | 文本显示 | 賬戶類型 枚舉值： House Account Client Account SFGS Account Payroll account Solar House Account  SolarClient Account Suspense Account VPA Account |
| Account Name | 文本显示 | 賬戶名稱 |
| Currency (HKD equivalent) | 文本显示 | 貨幣（等值港幣） |
| Current Book Balance (HKD equivalent) | 文本显示 | 賬面餘額（等值港幣） |
| Open Date | 文本显示 | 賬戶開戶日期 |
| Close Date | 文本显示 | 賬戶銷戶日期 |
| Account Status | 文本显示 | A:正常 I:不活跃户 D:不动户 C:已销户 |
| Last Financial Transaction Date | 文本显示 | 最後金融交易日期 |

功能描述： Account/Contract No. 和Balance Detail部分均能跳转到所选定的活期账户详细信息展示页面。

- **Balance ****Details****按钮跳转**** **

功能描述：可以查看该客户的活期账户中所有货币种类下的账面余额信息和可用余额信息。

- **页面表头说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Currency | 文本显示 | 貨幣 |
| Current Book Balance | 文本显示 | 賬面餘額 |
| Available Balance | 文本显示 | 可用餘額 |

- **定期账户信息-****页面布局**

- **定期账户信息-表头字段说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Application Type | 文本显示 | 存款類型 TD:定期 |
| Account/Contract No. | 文本显示 | 定期賬號 |
| Serial No. | 文本显示 | 转期序号 |
| Currency | 文本显示 | 貨幣 |
| Status | 文本显示 | A:正常 I:不活跃户 D:不动户 C:已销户 |
| Product Code | 文本显示 | 产品代码 例子：TDC00001 |
| Principal | 文本显示 | 本金 |
| Tenor | 文本显示 | 存期 |
| Contract Rate | 文本显示 | 合约利率 |
| Interest Amount | 文本显示 | 利息金額 |
| Value Date | 文本显示 | 起息日 |
| Maturity Date | 文本显示 | 到期日 |
| Maturity Instruction | 文本显示 | 到期指示 枚舉值 0 - Pending For Indication 1 - PRIN ROLLOVER INT TO ACCOUNT 2 - Principal Plus Interest Rollover 5 - Principal And Interest To DD Account |

- Tab-Card Information

Card Information部分是对客户账户下所有卡的信息进行展示。

- **页面布局**

- **表头字段说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Card Number | 文本显示 | 卡號 |
| Account Number | 文本显示 | 賬號 |
| Open Date | 文本显示 | 開卡日期 |
| Activation Date | 文本显示 | 卡激活日期 |
| Activation Status | 文本显示 | 卡激活状态 |
| Close Date | 文本显示 | 銷卡日期 |

- Tab-Authorization Arrangement

Authorization Arrangement部分是对公司相关人员的签名信息的显示。其中，点击Signature Specimen部分中的查看签名可以弹出弹窗显示签名式样的图片。

- **页面布局**

- **表头字段说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| CI Number | 文本显示 | 客戶號 |
| Company English Name | 文本显示 | 公司英文名稱 |
| Signing Instruction | 文本显示 | 簽署指示 枚举值：Only one to sign，Any one to sign，Any two to sign，All to sign |
| ID Type | 文本显示 | 證件類型 |
| ID Number | 文本显示 | 證件號碼 |
| Authorized Representative English Name | 文本显示 | 签名代表的英文名 |
| Signature Specimen | 按鈕 | 點擊按鈕，彈窗顯示簽名式樣圖片 |

- **Modification History**

功能描述：是对Signing Arrangement的维护记录进行显示。默认按照时间顺序进行顺序显示，可在变更时间处选择逆序显示。

- **页面表头说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Modification Time | 文本显示 | 维护时间 |
| Case Number | 文本显示 | 業務編號 |
| Case Type | 文本显示 | 業務類型 Account Opening Trigger Event Periodic Review |
| Update Type | 文本显示 | 修改類型 Add Update Delete |
| ID Type | 文本显示 | ID 类型 |
| ID Number | 文本显示 | ID号 |
| Authorised Representative English Name | 文本显示 | 签名代表的英文名 |

- Tab-Company Search

Company Search部分是对TU查询的公司信息进行展示。

- Tab- Company Search - Entity Details

Entity Details 部分显示TU查询的公司基本信息。

- **页面布局**

- **表头****字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Company type | 文本显示 | 公司類型 |
| Company status | 文本显示 | 公司狀態 |
| Company name | 文本显示 | 公司名稱 |
| Incorporation date | 文本显示 | 成立日期 |
| Company registered address | 文本显示 | 公司註冊地址 |

- Tab- Company Search - Connected Party

Connected Party部分显示TU查询的相关人士信息。

- **页面布局**

- **表头****字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Role | 文本显示 | 相关人士的角色 |
| ID Type | 文本显示 | 證件類型 ID-HK Identity Card PP-Passport CD-Chinese Resident Identity Card PT-Others |
| Other ID Type | 文本显示 | 其他證件類型 |
| ID Number | 文本显示 | 證件號碼 |
| First Name | 文本显示 | 英文名 |
| Last Name | 文本显示 | 英文姓氏 |
| Percentage of share of shareholders | 文本显示 | 股份佔比 |

- Tab- Company Search - Related documents

Related Documents部分是对TU查询的文件的集合。页面显示文件全名，在表格最右侧的Preview可链接到原文档。

- **页面布局**

- **表头字段说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Business registration certificate | 按钮 | 点击按钮，链接Business registration certificate文件的原文档 按钮：查看 |
| Certificate of incorporate | 按钮 | 点击按钮，链接Certificate of incorporate文件的原文档 按钮：查看 |
| NAR1 | 按钮 | 点击按钮，链接M&A文件的原文档 按钮：查看 |
| M&A | 按钮 | 点击按钮，链接Business registration certificate原文档 按钮：查看 |
| Form1(a) | 按钮 | 点击按钮，链接Form1(a)文件的原文档 按钮：查看 |
| Form1© | 按钮 | 点击按钮，链接Form1©文件的原文档 按钮：查看 |
| Other | 按钮 | 点击按钮，链接Other文件的原文档 按钮：查看 |

- Tab-Block Code

Block Code部分是对信息维护记录的显示。

- **页面布局**

- **表头字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| Transaction Seq | 文本显示 | 交易流水號 |
| Rule ID Number | 文本显示 | 規則ID |
| Client Number | 文本显示 | 客戶號 |
| Account Number | 文本显示 | 賬號 |
| Vertical | 文本显示 | 部門 1-Retail 2-Business |
| Channel | 文本显示 | 渠道 枚舉值： 1-CAAS （對應對公銷戶系統） 2-ARK （客服系統） 3-EOB （對公KYC系統） |
| Block Code | 文本显示 | 狀態碼 |
| Action | 文本显示 | 狀態碼處理類型 枚舉值： 1-Add 增加狀態碼 2-Remove 移除狀態碼 |
| Reason | 文本显示 | 設置該狀態的原因 |
| Setting Mode | 文本显示 | 設置方式 1- 系統 2- 人工 |
| Checker | 文本显示 | 審核員 |  |

- Tab-Attachment

Attachment部分是对上传的相关附件的展示。其中“下载”按钮部分可以下载上传的附件文档。

- **页面布局**

- **表头字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| No. | 文本显示 | 序號 |
| Document Name | 文本显示 | 文件名稱 |
| Uploader | 文本显示 | 上傳人名稱 |
| Upload time | 文本显示 | 上傳時間 |
| Download | 按钮 | 下載按鈕 |

- Tab-Audit Log

Audit Log部分是对风险评估的记录模块，实现对客户风险评估过程的动态追踪，系统化地记录和管理针对客户的每一次风险评估活动、结论及其演变历程。

- **页面布局**

- **表头字段****说明**

| **字段** | **字段类型** | **说明** |
| --- | --- | --- |
| No. | 文本显示 | 序號 |
| Submission Time | 文本显示 | 提交時間 |
| Case Number | 文本显示 | 訂單號 |
| Case Type | 文本显示 | 訂單類型 |
| Risk Rate | 文本显示 | 訂單最終的Risk Rate |
| Total Score | 文本显示 | Risk Rate對應的分數 |
| Link | 按钮 | 訂單詳情跳轉鏈接 |

功能说明：点击“Detail”可以跳转到风险评估订单详情。

- 客户信息维护

功能描述：提供维护订单信息的查询功能和对Company Info、Connected Party、Authorization Arrangement和Attachment这4个部分信息预览、修改和审核的界面(如果涉及影響公司風險評估的字段信息修改，則系統自動創建TE單)。

审批流程：所有的信息修改都需要经过maker和checker的审批流程，当审批通过后才会在客户详情页面展示维护后的信息。审批流程如下图所示。

- 维护订单信息查询

功能描述：实现多维度的维护订单信息搜索，支持单一条件的精准查询。

- 维护订单查询条件

**页面布局：**

**表头字段****说明：**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client No. | 文本输入 | 系统分配的唯一客户标识 |
| Company English Name | 文本输入 | 公司英文名称（支持模糊查询） |
| Company Chinese Name | 文本输入 | 公司中文名称（支持模糊查询） |
| ID Type | 下拉选择 | 客户的证件类型筛选 |
| ID Number | 文本输入 | 客户的证件号码 |
| Open Channel | 下拉选择 | 开户渠道筛选 |
| Open Date | 日期范围选择 | 客户开户时间范围筛选 |
| Next Review Date | 日期范围选择 | 年检到期时间范围筛选 |
| Company Type | 下拉选择 | 公司类型筛选 |
| Client Risk | 下拉选择 | 客户风险等级筛选 |

- 维护订单列表展示功能

功能描述：展示所有或者筛选出的维护订单的部分信息。支持根据maker和checker角色的权限不同而在Actions显示不同的按钮。

**页面布局：**

**表头****字段说明：**

| **字段名称** | **字段类型** | **说明** |
| --- | --- | --- |
| Client No. | 文本显示 | 系统分配的唯一客户标识 |
| Company Type | 文本显示 | 公司类型 |
| ID Type | 文本显示 | 客户的证件类型 |
| ID Number | 文本显示 | 客户的证件号码 |
| Company English Name | 文本显示 | 公司英文名称 |
| Open Channel | 文本显示 | 开户渠道 |
| Last Review Date | 文本显示 | 上次审查日期 |
| Next Review Date | 文本显示 | 下次审查日期 |
| Application Status | 标签显示 | 维护订单状态 枚举值：待提交，待审批，审批中，审批成功，审批拒绝 |
| Client Risk | 标签显示 | 客户风险等级 标签颜色：Low risk-绿色；Medium risk-黄色；High risk-红色 |
| Action | 按钮 | 点击按钮，跳转到不同页面 枚举值：View，Edit，Check |

设计样式，如果没有特别说明，所有数据列表需要分页配置的，都使用相同的配置。

**角色权限**

| **角色** | **按钮权限** |
| --- | --- |
| Maker | View，Edit |
| Checker | View，Check |

**操作功能**

查看详情：点击"View"按钮跳转到订单详情页面

信息维护：点击“Edit”按钮跳转到维护客户信息页面

审核维护订单：点击“Check”按钮跳转到审核maker提交的维护订单页面

- 订单详情页面

功能描述：查看维护订单中客户的具体信息。所有的信息仅显示，不能进行修改。

- 客户信息维护

功能描述：新增一個訂單類型：“Company info update”支持对客户信息进行修改、新增和删除的操作。在修改完成后点击“保存”按钮即可将已编辑的内容保存为草稿，订单状态为“待提交”；点击“提交”按钮即可将维护订单提交给相应的checker进行审批，同时订单状态由“待提交”转变为“待审批”。

- Tab-Company Info

- **Tab-Company Info-Company Base Information**

支持对Company Base Information模块中的公司客户基础信息进行日常维护与更新操作。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Company English Name | CHAR(60) | 必输 | 公司英文名稱 |
| Company Chinese Name | CHAR(60) | 必输 | 公司中文名稱 |
| ID Type | CHAR(3) | 必输 | 證件類型 |
| ID Number | CHAR(30) | 必输 | 證件號碼 |
| Company Type | CHAR(2) | 必输 | 公司類型 |
| Company Establish Date | NUM(8) | 必输 | 公司成立日期 |
| Country of Registration | CHAR(3) | 必输 | 註冊國家 |
| Listing Company | CHAR(1) | 必输 | 是否上市公司 |
| High Tax Evasion Risk | CHAR(1) | 必输 | 是否高逃稅風險標誌 |
| Name(s) of Stock Exchange | CHAR(120) | 选输 | 证券交易所名称 |
| Bearer Shares Issued | CHAR(1) | 必输 | 是否可以發行/已經發行不記名股票 |
| Intermediate owners capable of issuing bearer shares | CHAR(1) | 必输 | 是否为能够发行无记名股票的中介所有者 |
| Channel | CHAR(1) | 必输 | 開戶渠道 |
| Usage of products | CHAR(1) | 必输 | 高風險產品類型 |
| On PEC | CHAR(1) | 必输 | 是否政府標誌 |
| Number of layers of ownership structure | CHAR(2) | 必输 | 公司股權結構層數 |
| Account Officer | CHAR(10) | 选输 | 客戶經理代碼 |
| Account Officer Name | CHAR(60) | 选输 | 客户经理姓名 |
| Accounting Centre | NUM(11) | 選輸 | 客戶經理所屬核算單元 |
| Country Of Risk | CHAR(3) | 选输 | 風險國家 |
| Country Of Origin | CHAR(3) | 选输 | 总部注册国家 |
| Trading Name（English） | CHAR(60) | 选输 | 交易名稱（英文）（支持最多10個） 点击“Add”按钮可以新增相关信息，点击“Delete”按钮可以删除所选择的信息 |
| Trading Name（Chinese） | CHAR(60) | 选输 | 交易名稱（中文）（支持最多10個） 点击“Add”按钮可以新增相关信息，点击“Delete”按钮可以删除所选择的信息 |

- **Tab-Company Info-Contact Details**

可以对Contact Details部分执行基础的信息维护操作，包括关键内容的修改和更新。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段****长****度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Company Registered Address | CHAR(210) | 必输 | 註冊地址 |
| Company Operation Address | CHAR(210) | 必输 | 營業地址 |
| Company Mailing Address | CHAR(210) | 必输 | 通訊地址 |
| Declaration of registered address and business address | CHAR(1) | 选输 | 聲明註冊地址/營業地址是否為秘書公司地址 |
| Reason | CHAR(500) | 选输 | 註冊地址/營業地址為秘書公司地址的原因 |
| Contact Person First Name | CHAR(30) | 必输 | 聯絡人名字 |
| Contact Person Last name | CHAR(30) | 必输 | 聯絡人姓氏 |
| Contact Person Email Address | CHAR(34) | 必输 | 聯絡人電郵地址 |
| Contact Person Telephone Number | CHAR(18) | 必输 | 聯絡人手機號碼 |
| Contact Person Job Title | CHAR(3) | 必输 | 聯絡人頭銜 |
| Other Contact Person Job Title | CHAR(30) | 选输 | 當字段“Contact Person Job Title”選擇“09：其他”時，該字段必輸； |
| Office Telephone Number | CHAR(18) | 选输 | 辦事處電話 |
| Company Website | CHAR(120) | 选输 | 公司網頁 |
| Group CIF Number | CHAR(12) | 选输 | 集團客戶號碼 |
| Group CIF Name | CHAR(120) | 选输 | 集團名稱 |

- **Tab-Company Info-Company Business Background**

支持对Company Business Background部分进行基本信息维护操作。

**页面布局**

**页面表头说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Office Location(s) | CHAR(3) | 选输 | 辦公室地點 |
| Principal Place of Operations | CHAR(3) | 必输 | 主要運營地 |
| Countries of Foreign Branch(es)/Subsidiary(ies) / Factory(ies) | CHAR(3) | 选输 | 海外分支/子公司/工廠所在的國家/地區（支持最多增加10個） 点击“Add”按钮可以新增一条信息，点击“Delete”按钮可以删除所选择的信息 |
| Country of Key Counterparties | CHAR(3) | 必输 | 主要交易對手所在國家（支持最多增加5個） 点击“Add”按钮可以新增一条信息，点击“Delete”按钮可以删除所选择的信息 |
| Industry Category 1 | CHAR(6) | 必输 | 行業大類1 |
| Key Products/ Services 1 | CHAR(6) | 必输 | 行業小類1 |
| Industry Category 2 | CHAR(6) | 必输 | 行業大類2 |
| Key Products/ Services 2 | CHAR(6) | 必输 | 行業大類2 |
| Name of Regulatory Body | CHAR(120) | 选输 | 監管機構名稱 |
| Regulatory Reference Number | CHAR(30) | 选输 | 監管機構參考編號 |
| Country of Regulatory Body | CHAR(3) | 选输 | 監管機構所在國家 |
| Country of Employee(s)/ Number of Employee(s) | CHAR(3)/CHAR(5) | 选输 | 僱員所在國家/僱員數目（最多支持5組） 点击“Add”按钮可以新增一条信息，点击“Delete”按钮可以删除所选择的信息 |
| Whether WEB3 Company | CHAR(1) | 选输 | 是否WEB3公司 |
| Whether Greater China Company | CHAR(1) | 选输 | 是否Greater China公司 |
| Financial Background | CHAR(5) | 选输 | 公司经济类型 |
| Is your Company a Holding Company | CHAR(1) | 选输 | 是否為控股公司 |
| Referral Party | CHAR(120) | 选输 | 開戶推薦人名稱 开户渠道是RM开户的，才需要展示该字段且必输 |

- **Tab-Company Info-Source of Wealth/Fund**

支持对Source of Wealth/Fund部分进行基础信息修改与维护。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Initial Source of Wealth | CHAR(3) | 必输 | 初始财富来源（最多支持輸入5個） 点击“Add”按钮新增相关来源信息，点击“Delete”按钮删除所选择的来源信息 |
| Other Initial Source of Wealth | CHAR(60) | 选输 | 其他初始财富来源（最多支持輸入5個） |
| Ongoing Source of Wealth | CHAR(3) | 必输 | 持續财富来源（最多支持輸入5個） 点击“Add”按钮新增相关来源信息，点击“Delete”按钮删除所选择的来源信息 |
| Other Ongoing Source of Wealth | CHAR(60) | 选输 | 其他持續财富来源（最多支持輸入5個） |
| Initial Source of Fund | CHAR(3) | 必输 | 初始資金来源（最多支持輸入5個） 点击“Add”按钮新增相关来源信息，点击“Delete”按钮删除所选择的来源信息 |
| Other Initial Source of Fund | CHAR(60) | 选输 | 其他初始資金来源（最多支持輸入5個） |
| Ongoing Source of Fund | CHAR(3) | 必输 | 持續資金来源（最多支持輸入5個） 点击“Add”按钮新增相关来源信息，点击“Delete”按钮删除所选择的来源信息 |
| Other Ongoing Source of Fund | CHAR(60) | 选输 | 其他持續資金来源（最多支持輸入5個） |

- **Tab-Company Info-Anticipated Transaction ****&**** Purpose of Usage**

可以对Anticipated Transaction & Purpose of Usage部分进行基本的信息修改维护。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Domestic / International Transaction | CHAR(1) | 必输 | 本地/國際交易 |
| Does the Company intent to make Cross Border Transaction | CHAR(1) | 必输 | 公司會否進行跨境交易 |
| Countries (to) of Cross Border Transaction | CHAR(3) | 选输 | 匯出國家（最多支持10個） 當問題“公司會否進行跨境交易”為“Y時必輸” “Add”按钮可以新增相关信息，“Delete”按钮可以删除所选择的信息。 |
| Countries (from) of Cross Border Transaction | CHAR(3) | 选输 | 匯入國家（最多支持10個） 當問題“公司會否進行跨境交易”為“Y時必輸” “Add”按钮可以新增相关信息，“Delete”按钮可以删除所选择的信息。 |
| Monthly Number of Cross Border Transaction (Inward) | CHAR(1) | 选输 | 每月跨境轉賬交易量（匯入） 當問題“公司會否進行跨境交易”為“Y時必輸” |
| Monthly Inward Amount of Cross Border Transaction - Currency | CHAR(3) | 选输 | 每月跨境轉賬貨幣（匯入） |
| Monthly Inward Amount of Cross Border Transaction - Amount | NUM(16,2) | 选输 | 每月跨境轉賬金額（匯入） |
| Monthly Number of Cross Border Transaction (Outward) | CHAR(1) | 选输 | 每月跨境轉賬交易量（匯出） |
| Monthly Outward Amount of Cross Border Transaction - Currency | CHAR(3) | 选输 | 每月跨境轉賬貨幣（匯出） |
| Monthly Outward Amount of Cross Border Transaction - Amount | NUM(16,2) | 选输 | 每月跨境轉賬金額（匯出） |
| Purpose for Cross-Border Transaction | CHAR(3) | 选输 | 跨境交易目的，支持多選 當問題“公司會否進行跨境交易”為“Y時必輸” |
| Other Purpose for Cross-Border Transaction | CHAR(200) | 选输 | 其他跨境交易目的 |
| Does the Company intend to make Regular Domestic Transaction | CHAR(1) | 必输 | 公司會否進行常規本地交易 |
| Monthly Number of Regular Transaction (Inward) | CHAR(1) | 选输 | 每月本地轉賬交易量（匯入） |
| Monthly Inward Amount of Regular Transaction - Currency | CHAR(3) | 选输 | 每月本地轉賬貨幣（匯入） |
| Monthly Inward Amount of Regular Transaction - Amount | NUM(16,2) | 选输 | 每月本地轉賬金額（匯入） |
| Monthly Number of Regular Transaction (Outward) | CHAR(1) | 选输 | 每月本地轉賬交易量（匯出） |
| Monthly Outward Amount of Regular Transaction - Currency | CHAR(3) | 选输 | 每月本地轉賬貨幣（匯出） |
| Monthly Outward Amount of Regular Transaction - Amount | NUM(16,2) | 选输 | 每月本地轉賬金額（匯出） |
| Purpose for Regular Transaction | CHAR(3) | 选输 | 本地交易目的，支持多選 當問題“公司會否進行常規本地交易”為“Y時必輸” |
| Other Purpose for Regular Transaction | CHAR(200) | 选输 | 本地其他交易目的 |
| Does the Company intend to hold balances in the account with infrequent activity | CHAR(1) | 选输 | 公司是否預期在賬戶持有結餘而不進行交易 |
| Please indicate the amount and rationale | CHAR(120) | 选输 | 請提供金額及理由 |

- **Tab-Company Info-FATCA/CRS**

可以对FATCA/CRS部分执行日常的信息维护，及时更新信息。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| FATCA Documentation | CHAR(2) | 必输 | FATCA 表格類型： |
| Others – Please specify | CHAR(2) | 选输 | 其他FATCA 表格類型 |
| FATCA Classification | CHAR(2) | 选输 | FATCA 身份類型 |
| Non-consenting U.S. account Sub-type | CHAR(2) | 选输 | 非自願同意的美國帳戶類型 |
| Disregarded entity |  | 选输 | 非獨立實體公司 |
| Sign Date of FATCA Documentation | NUM(8) | 选输 | 簽署FATCA表格日期 |
| FATCA Due Diligence Status | CHAR(2) | 必输 | FATCA盡職調查狀態 |
| CRS Entity Classification | CHAR(2) | 选输 | CRS實體類型（不適用於獨資公司） |
| Sign Date of CRS Documentation | NUM(8) | 选输 | 簽署CRS表格日期 |
| Tax Residency | CHAR(3) | 必输 | 稅務國家/地區（最多10个） |
| Tax identifying number（TIN） | CHAR(22) | 选输 | 稅務號碼（最多10个） “Add”按钮可以新增相关信息， “Delete”按钮可以删除所选择的信息。 |
| Reason for No TIN | CHAR(1) | 选输 | 無稅務編號原因（最多10个） “Add”按钮可以新增相关信息， “Delete”按钮可以删除所选择的信息。 |
| GIIN Code | CHAR(16) | 选输 | 全球中介機構識別號碼  當FATCA Classification為下列值時必輸 12-Active NFFE-Sponsored direct reporting NFFE 26-FFI-Participating FFI 27-FFI-Reporting Model 2 FFI 28-FFI-Reporting Model 1 FFI 03-Active NFFE-Direct reporting NFFE 33-RDCFFI-Registered deemed-compliant FFI |
| GIIN Status | CHAR(1) | 选输 | GIIN 狀態 |

- **Tab-Company Info-Company Change**

支持对Company Change部分进行变更信息的维护操作。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Have there been Material Changes to your Business in the last 5 years? | CHAR(1) | 必输 | 在过去的5年里，貴公司有过实质性的变化吗?(如所有权、公司名称、母公司的变更、经营国家、业务性质、产品/服务) |
| Company Name Changed | CHAR(1) | 选输 | 公司名稱是否有變更過 |
| Old Company Name | CHAR(90) | 选输 | 舊公司名稱，支持中文和英文名稱（最多支持10个） 点击“Add”按钮可以新增相关信息；点击“Delete”按钮可以删除所选择的信息 |
| Products and Services Changed | CHAR(1) | 选输 | 產品與服務是否有改變過 |
| Old Industry Category/ Key Products/ Services *10 | CHAR(6) | 选输 | 舊行業大類名稱/舊行業小類名稱 |
| Ownership structure Changed | CHAR(1) | 选输 | 公司股權結構是否改變過 |
| Ownership Changed Start | NUM(8) | 选输 | 公司股權結構改變開始日期 |
| Ownership Changed End | NUM(8) | 选输 | 公司股權結構改變結束日期 |
| Reasons for changing Company Ownership structure *1 | CHAR(500) | 选输 | 改變公司股權結構的原因 |
| Beneficial Owners Changed | CHAR(1) | 选输 | 收益所有人是否有改變過 |
| Beneficial Owners Changed Start | NUM(8) | 选输 | 收益所有人改變開始日期 |
| Beneficial Owners Changed End | NUM(8) | 选输 | 收益所有人改變结束日期 |
| Reasons for changing Beneficial Owners *1 | CHAR(500) | 选输 | 改變收益所有人的原因 |

- **Tab-Company Info-Company Search-Company Secretary Information**

支持对Company Search-Company Secretary Information部分进行信息更新。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| ID Type | CHAR(3) | 选输 | 秘書公司證書類型 |
| ID Number | CHAR(30) | 选输 | 秘書公司證書號碼 |
| Company English Name | CHAR(60) | 必输 | 秘書公司英文名稱 |
| Company Chinese Name | CHAR(60) | 选输 | 秘書公司中文名稱 |
| Secretary Address | CHAR(210) | 选输 | 秘書公司-公司地址 |

- **Tab-Company Info-Other Information**

支持对Other Information部分进行客户信息的更新维护。

**页面布局**

**表头字段****说明**

| **字段名称** | **字段長度** | **属性** | **说明** |
| --- | --- | --- | --- |
| Client Open Date | NUM(8) | 必输 | 客户开立日期 |
| Client Close Date | NUM(8) | 选输 | 客户关闭日期 |
| Client Risk | CHAR(1) | 必输 | 客户风险級別 枚举值：H-High Risk；M-Medium Risk；L-Low Risk |
| CDD Last Review Date | NUM(8) | 选输 | CDD上次审查日期 |
| CDD Next Review Date | NUM(8) | 必输 | CDD下次审查日期 |
| High Risk Remarks | CHAR(120) | 选输 | 高風險說明 |

- Tab-Connected Party

- **Tab-Connected Party-Individual**

可以对Connected Party中的Individual部分执行基础的信息维护操作，支持新增和删除相关人士-自然人及其相关信息。

**页面布局**

**页面表头说明**

| **字段** | **字段長度** | **說明** |
| --- | --- | --- |
| ID Type | CHAR(3) | 證件類型 |
| Other ID Type | CHAR(60) | 其他證件類型 |
| ID Number | CHAR(1) | 證件號碼 |
| First Name | CHAR(30) | 英文名 |
| Last Name | CHAR(30) | 英文姓氏 |
| Chinese Name | CHAR(60) | 中文姓名 |
| Capacity of Connected Party | CHAR(2) | 相關人士角色 |
| Controlling Person Type | CHAR(6) | 實際控制人類型，當Capacity of Connected Party的類型存在Controlling Person時，則該字段為必輸 |
| Sign Date of CRS Documentation(for Controlling Person) |  | 簽署CRS表格日期 |
| Tax residency | CHAR(3) | 稅務國家/地區 *10 |
| Tax identification number ("TIN") | CHAR(30) | 稅務號碼 * 10 |
| Reason for no TIN *5 | CHAR(200) | 無稅務編號原因 |
| Ownership | CHAR(3) | 股份佔比 |
| Job Title | CHAR(30) | 職銜 |
| Permanent HKID | CHAR(1) | 是否永久香港身份證 |
| ID Issue Date | NUM(8) | 身份證明文件簽發日期 |
| Gender | CHAR(3) | 性別 |
| Date of Birth | NUM(8) | 出生日期 |
| Place of Birth | NUM(8) | 出生地 |
| Nationality | CHAR(3) | 國籍（支持最多5個） |
| Email Address | CHAR(34) | 電郵地址 |
| Telephone Number | CHAR(18) | 電話號碼 |
| Residential Address | CHAR(210) | 居住地址 |
| Beneficial Owner Source of Wealth (Only for Beneficial Owner) | CHAR(3) | 實益擁有人的財富來源（只適用於實益擁有人）（支持最多5個） |
| Others(Please Specify) | CHAR(200) | 當Beneficial Owner Source of Wealth選擇other時，該字段必輸 |
| Country(ies) in which Beneficial Owner attained his/her Initial Source of Wealth  (Only for Beneficial Owner) | CHAR(3) | 實益擁有人獲得初始財富來源的國家 （只適用於實益擁有人），支持多選（支持最多5個） |
| Country(ies) in which Beneficial Owner attained his/her Ongoing Source of Wealth (Only for Beneficial Owner) | CHAR(3) | 實益擁有人獲得持續財富來源的國家 （只適用於實益擁有人），支持多選（支持最多5個） |
| Related Client On PEP | CHAR(1) | PEP標誌 |

**处理说明：**

- 点击“Add”按钮可以新增相关的信息；

- 点击“删除当前Individual”按钮可以删除所选择的相关人士-自然人信息；

- 点击“新增 Individual”按钮可以新增一个相关人士-自然人及其信息。

- **Tab-Connected Party-Entities**

可以对Connected Party中的Entities部分基本信息进行维护，同时支持新增和删除相关人士-公司及其相关信息。

**页面布局**

**表头字段****说明**

| **字段** | **字段長度** | **字段类型** | **說明** |
| --- | --- | --- | --- |
| ID Type | CHAR(3) | 文本输入 | 證件類型 |
| Other ID Type | CHAR(60) | 文本输入 | 其他證件類型 |
| ID Number | CHAR(1) | 文本输入 | 證件號碼 |
| Registered Full Name in English | CHAR(60) | 文本输入 | 公司英文名稱 |
| Registered Full Name in Chinese (if applicable)# | CHAR(60) | 文本输入 | 公司中文名稱 |
| Entity Type of Connected Entity | CHAR(2) | 文本输入 | 相關人士實體類型（公司類型） |
| Capacity of Connected Entity | CHAR(3) | 文本输入 | 相關人士角色 |
| Ownership | CHAR(3) | 文本输入 | 股份佔比 |
| Listing on Stock Exchange | CHAR(30) | 下拉选择 | 是否上市公司 |
| Name(s) of Stock Exchange | CHAR(120) | 文本输入 | 证券交易所名称 |
| Registered Office Address (Only for Senior Office, Director, Authorised Representative)# | CHAR(210) | 文本输入 | 公司註冊地址 |
| Principal Business Address (Only for Senior Office)# | CHAR(210) | 文本输入 | 主要營業地址 |

**处理说明：**

- 点击“刪除当前 Entity”按钮可以删除所选择的相关人士-公司信息；

- 点击“新增 Entity”按钮可以新增一个相关人士-公司及其信息。

- Tab-Authorization Arrangement

在Authorization Arrangement模块对签名信息进行维护，支持新增和删除签名及其相关信息。

**页面布局**

**表头字段****说明**

| **字段** | **字段長度** | **属性** | **概述** |
| --- | --- | --- | --- |
| CI Number | Char(12) | 显示 | 客户号 |
| Company English Name | Char(120) | 显示 | 公司英文名稱 |
| Signing Instruction | Char(1) | 选输 | 簽署指示 |
| ID Type | CHAR(3) | 可修改 | 證件類型 |
| ID Number | CHAR(1) | 选输 | 證件號碼 |
| Authorized Representative English Name | CHAR(120) | 必输 | 签名代表的英文名 |
| Signature Specimen |  | 必上传 | 签名照片 |

**处理说明：**

- 点击“上传”可以上传签名的照片；

- 点击“刪除当前 Signing”按钮可以删除所选择的签名信息；

- 点击“新增 Signing”按钮可以新增一条签名信息。

- Tab-Attachment

支持上传相关的附件。

**页面布局**

功能描述：点击“新增附件”按钮可以新增相关的附件。

- Tab-Change Record

功能描述：maker和checker可以查看原始字段和最后一次修改的字段对比。仅当maker提交订单之后，checker可以看到修改前后的对比；当checker退回maker的维护订单后，maker可以查看上一次修改的记录对比。修改后的字段使用红色框选高亮出来。

- 需觸發TE提示字段說明

如果公司信息修改涉及到如下字段的修改，則系統在maker提交時提示是否需要同步生成TE單。

| **字段** | **字段長度** | **屬性** | **概述** |
| --- | --- | --- | --- |
| **C****ompany Information** |
| Company English Name | CHAR(60) |  | 公司英文名稱 |
| Company Chinese Name | CHAR(60) |  | 公司中文名稱 |
| Trading Name | CHAR(60) |  | 公司交易名稱 該字段最多支持10個Trading Name |
| Company Previous Name | CHAR(60) |  | TU返回的公司曾用名 |
| ID Number | CHAR(30) |  | 公司證書號碼 如果修改，需觸發生成TE單 |
| Company Establish Date | NUM(8) |  | 公司成立日期 如果修改，需觸發生成TE單 |
| Country of Incorporation/ Registration | CHAR(3) |  | 公司成立/註冊地 如果修改，需觸發生成TE單 |
| Countries of Operation/Business | CHAR(3) |  | 公司主要運營國家/地區 如果修改，需觸發生成TE單 |
| Old Company Name（過去5年內） | CHAR(60) |  | 原公司名稱 修改需要重做Name Screening檢查； 支持中文和英文名稱 支持最多輸入十組 |
| Secretary Company English Name | CHAR(60) |  | 秘書公司名稱 如果修改，需觸發生成TE單 |
| **Company Address information     ** |
| Company Registered Address | CHAR(210) |  | 公司註冊地址 如果修改，需觸發生成TE單 |
| Company Operation Address | CHAR(210) |  | 公司運營地址 如果修改，需觸發生成TE單 |
| Company Mailing Address | CHAR(210) |  | 公司郵寄地址 如果修改，需觸發生成TE單 |
| **Connected individual ** |
| Capacity of connected party | CHAR(3) |  | 相關人士角色 **Limited Company****：** Directors Shareholder **Sole proprietorship**： Owner **Partnership：** Partner 如果修改，需觸發生成TE單 |
| First Name | CHAR(30) |  | 相關人士名字 如果修改，需觸發生成TE單 |
| Last Name | CHAR(30) |  | 相關人士姓氏 如果修改，需觸發生成TE單 |
| Chinese Name | CHAR(60) |  | 相關人士中文名稱 如果修改，需觸發生成TE單 |
| Identity Card Number | CHAR(30) |  | 相關人士證件號碼 如果修改，需觸發生成TE單 |
| Gender | CHAR(3) |  | 相關人士性別 如果修改，需觸發生成TE單 |
| Date of Birth | NUM(8) |  | 相關人士出生日期 如果修改，需觸發生成TE單 |
| Place of Birth | NUM(8) |  | 相關人士出生地 如果修改，需觸發生成TE單 |
| Identity Card Type | CHAR(3) |  | 相關人士證件類型 如果修改，需觸發生成TE單 |
| ID Issue Country | NUM(8) |  | 相關人士身份證識別出來的身份證簽發地 如果修改，需觸發生成TE單 |
| Residential Address | CHAR(210) |  | 相關人士居住地址 如果修改了地址中的國家/地區，則需要做Name Screening檢查。 |
| 2. Entities 公司 |
| Registered Full Name in English | CHAR(60) |  | 英文註冊全名 如果修改，需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| Registered Full Name in Chinese (if applicable) | CHAR(60) |  | 中文註冊全名（如適用） 如果修改，需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| ID Type | CHAR(3) |  | 證書類型 如果修改，需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| Others | CHAR(30) |  | 其他證書號碼 如果修改，需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| ID Number | CHAR(30) |  | 證書號碼 如果修改，需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| Registered Office Address (Only for Senior Office, Director, Authorised Representative) | CHAR(210) |  | 公司註冊地址 （只使用於高級管理人員、董事、獲授權代表） 如果修改了地址中的國家/地區，則需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |
| Principal Business Address (Only for Senior Office) | CHAR(210) |  | 主要營業地址 （只使用於高級管理人員） 如果修改了地址中的國家/地區，則需觸發生成TE單（PEP, Special Interest Subject，Sanctions，Soc） |

- 通知提醒

功能描述：当maker点击“提交”按钮后，会同步给相应的checker发一封通知邮件提醒checker有一个待审核订单。

**通知****文案**

| **郵件****標題** | Pending Order Requires Your Approval – [Order Number] |
| --- | --- |
| **通知內容** | Dear, This is a reminder that an order is pending your review and approval. Please find the details below: Order Type: Customer Information Maintenance Order Number: [Order Number] Customer ID: [Customer ID] Company Name: [Company Name] Please log in to the system to process this order at your earliest convenience. Should you have any questions, please do not hesitate to contact the relevant personnel or refer to the system operation guide. Thank you for your prompt attention and cooperation. |

**变量说明****：**

- [Order Number]：该维护订单的编号；

- [Customer ID]：维护订单中包含的客户编号；

- [Company Name]：维护订单中公司的名称。

- 维护订单审核

功能描述：支持对maker提交的维护订单进行审核。当checker开始审核维护订单时，订单状态由“待审批”转变为“审批中”。

- 审核

- **页面布局**

显示maker的界面，不能进行修改只能显示查看。

- **Approval Opinion****字段****说明**

| **字段** | **说明** |
| --- | --- |
| No Objection | 审批成功，审批流程结束，订单状态由“审批中”转变为“审批成功” |
| Reject | 审批拒绝，审批流程结束，订单状态由“审批中”转变为“审批拒绝” |
| Return | 订单退回给maker进行修改，订单状态由“审批中”转变为“待提交” |

- **操作功能**

显示修改前后对比：点击按钮显示该tab下所有修改字段的前后对比

高亮已修改部分：页面用红色框框选出修改的部分

- 通知提醒

功能描述：当checker提交“Return”意见后，会同步给相应的maker发一封通知邮件提醒maker有一个被退回的维护订单需要重新修改提交。

**通知文案**

| **郵件****標題** | Order Returned for Revision Reminder – [Order Number] |
| --- | --- |
| **通知內容** | Dear, Your order has been returned and requires modifications based on the checker’s feedback. Please find the details below: Order Type: Customer Information Maintenance Order Number: [Order Number] Customer ID: [Customer ID] Company Name: [Company Name] Please log in to the system to check the reasons for the return and make the necessary revisions. Should you have any questions, please do not hesitate to contact the relevant personnel or refer to the system operation guide. Thank you for your prompt attention and cooperation. |

**变量说明****：**

- [Order Number]：该维护订单的编号；

- [Customer ID]：维护订单中包含的客户编号；

- [Company Name]：维护订单中公司的名称。

- 對公KYC系統授權安排改造說明

- 在RM Account Opening， Periodic Review，Periodic Review Batch，Periodic Review For EOB，Periodic Review For EOB – Manual都增加Signing Instruction 字段，字段放在Company Information模塊中。

- 在RM Account Opening， Periodic Review，Periodic Review Batch，Periodic Review For EOB，Periodic Review For EOB – Manual都增加Signature Specimen字段，該字段放在頁面 Details of Connected Parties-Individuals模塊中，Signature Specimen字段需支持上傳簽名式樣的圖片。

- View Customer，Extension Approval For PR，Extension Approval For TE，Batch Name Screening Hit需支持顯示Signing Instruction 字段和Signature Specimen簽名式樣的圖片

- 关键客户数据加密需求

为满足内部信息安全管理要求，CIF 系统需对客户敏感信息实施强制性字段加密，通过技术手段降低客户信息在存储、访问及运维过程中的泄露风险。

- 数据加密目标

- 对 CIF 中涉及个人敏感信息的关键字段实施加密存储，避免明文落库；

- 防范特权账号、误操作或非授权访问导致的数据泄露风险；

- 在保障数据安全的前提下，兼顾业务查询、运营支持及客户服务场景；

- 确保在监管报送、审计及执法机关要求等合规场景下，可在严格权限控制下获取明文数据。

- 数据加密方式

对指定客户敏感字段在写入数据库前完成加密处理，数据库中仅存储密文数据，不保存明文信息。 详细需要参考：

[众安银行CIF系统数据库字段加密需求](https://zagroup.sharepoint.com/:w:/s/ZARetailCASAPayment/IQCI-ZwSibbST7GB0TdDGR-3AftCii8EPbBdspPbe_3Hj0U?e=SJRo7T)

- 验收场景

例子参考：

| **业务****场****景** | **预期结果** |
| --- | --- |
| 开户流程：提交有效身份证 | 成功创建客户，状态为 PENDING_REVIEW |
| 修改客户地址，需运营审批 | 状态为 PENDING_APPROVAL，运营审批后变更生效 |
| 客户提交无效证件 | 系统拦截，提示“证件信息有误” |

- 大数据需求

「无」

- 客服需求

「无」

- 运营需求

「无」

- 数据埋点需求

「无」

- 版本兼容需求

「无」

- 历史数据迁移需求

「无」

- 其他附件；

- Company Type数据字典

| **代码** | **代码明细** |
| --- | --- |
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

 2 / 2