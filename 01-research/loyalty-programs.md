# 忠诚度/会员营销调研

> 调研日期: 2026-05-18 | 方法: 已有调研文件整合 + Web search 补充 | 用途: Socialhub.AI x Microsoft Seattle 5/22 活动
> 覆盖公司: Arc'teryx, Canada Goose, Lululemon, JCPenney, IHG, RealReal, Gopuff, Salt & Straw, VIZIO, The AI Collective

---

## 1. Arc'teryx

### 计划名称与规模
- **无消费者忠诚度计划** — Arc'teryx 目前没有面向普通消费者的积分/会员/忠诚度计划
- **PRO Program**: 仅面向持有最高级别户外认证的专业人士（向导、救援人员等），需申请审核，每年续期
- **ReGEAR Program**: 二手回收换 20% 商店积分，属于可持续发展举措而非忠诚度计划
- 会员规模: 未公开（PRO Program 为小众专业群体）

### 核心权益
- PRO: 专业折扣价购买、产品终身有限保修
- ReGEAR: 旧装备换 20% 购物信用额度
- 无消费者级别的积分、等级、生日奖励等传统忠诚度权益

### 个性化营销成熟度: 高级
- 2025 Google AI Marketer of the Year，全漏斗 AI 广告策略成熟
- 使用 Algolia 实现产品发现与动态重排序
- 根据地区、天气、活动信号调整网站商品展示
- Google Data Quality Score 95%（全球前 1%）
- **但**: 营销个性化集中在广告和电商层，**缺乏会员关系管理层的个性化**

### 已知 CRM/CDP 供应商
- ERP: SAP S/4HANA Cloud (Amer Sports 集团 RISE with SAP)
- 搜索/推荐: Algolia
- 数据分析: Qlik Sense + Qlik DataMarket
- AI 广告: Google AI (Performance Max, Product Studio)
- **CDP/CRM/ESP: 未确认** — 营销层供应商空白是关键发现

### 痛点信号
1. DTC 占收入 60%，拥有海量第一方数据，但缺乏统一会员管理平台
2. 多工具碎片化（Google Ads AI、Algolia、Qlik 各自独立），缺乏统一客户智能视图
3. 无消费者忠诚度计划 = 客户留存和复购依赖广告再触达而非关系运营
4. 母公司 Amer Sports 2026 年 $4 亿 IT 投资，但 AI 项目与核心系统并行，整合需求强烈

### Socialhub.AI CIP 可解决的问题
- **从零建立消费者忠诚度体系**: CIP 可帮助 Arc'teryx 从"无忠诚度计划"跳跃到"AI 驱动的超个性化会员运营"
- **统一客户数据视图**: 整合电商 + 160+ 门店 + 广告数据，消除 Algolia/Qlik/Google 各自为政的数据孤岛
- **SAP 生态协同**: Socialhub.AI 与 SAP/Emarsys 技术合作进行中，直接匹配 Amer Sports 的 SAP 基础设施
- **DTC 60% 第一方数据激活**: 将广告侧的个性化能力延伸到会员生命周期全域

---

## 2. Canada Goose

### 计划名称与规模
- **Basecamp Community** — 免费社区制会员计划
- 会员规模: **未公开**（公司未在财报中披露具体数字）
- DTC 占收入 72%（FY2024/25），CRM 邮件打开率 25.3%

### 核心权益
- 免费配送和退货
- 生日奖励
- 会员专属产品和活动（如首款鞋类系列独家提前购买）
- 新品系列首发通知
- 线下活动和工坊邀请
- 2 天快递

### 个性化营销成熟度: 初级-中级
- CRM 邮件打开率 25.3%（行业中等水平），说明个性化触达仍有优化空间
- 数字营销占 2024 年预算 55%
- 正在招聘 VP, Data, Automation & AI — 意味着 AI 驱动的个性化能力尚在建设中
- 2024 年新任 CDIO（Alfredo Tan）正在重新定义技术路线

### 已知 CRM/CDP 供应商
- ERP/Finance: **Microsoft Dynamics 365 for Finance**
- BI: **Microsoft Power BI**
- Cloud: **Microsoft Azure**
- CDP/CRM/ESP: **未确认** — 关键空白区域，D365 架构师参会暗示 CRM 层可能在评估中

### 痛点信号
1. VP Data/AI 岗位开放 = AI 能力尚未建立，正在从零搭建
2. CDIO 刚上任（2024），技术路线图正在重写
3. CRM 邮件打开率 25.3% = 个性化不足，千人一面推送
4. Basecamp 社区仅提供基础权益，缺乏数据驱动的个性化体验
5. DTC 72% 但 CDP 供应商未知 = 可能缺乏统一客户数据平台

### Socialhub.AI CIP 可解决的问题
- **D365 原生集成**: Socialhub.AI 作为 Microsoft Co-sell Partner，CIP 可直接在 D365 数据基础上构建客户智能层
- **填补 CDP 空白**: Canada Goose 的 D365 + Power BI + Azure 技术栈中缺少 CDP，CIP 精准填补
- **提升 CRM 个性化**: 将 25.3% 邮件打开率通过 AI 超个性化提升到行业领先水平
- **支撑 VP Data/AI 新战略**: 为新设的 Data/AI 团队提供即用型 AI Agent 客户运营能力
- **活动现场最强切入**: 参会人 Johnney Cao 直接负责 D365 架构，对话路径最短

---

## 3. Lululemon

### 计划名称与规模
- **Lululemon Membership** — 免费三级制
- 会员规模: **北美 2800 万** Essential 会员（FY2025，截止 2025 年 2 月）
- 等级:
  - **Collective** — 免费入门级
  - **Collective Plus** — 年消费达 $500 解锁
  - **Collective Pinnacle** — 年消费达 $1,000 解锁

### 核心权益
| 权益 | Collective | Collective Plus | Pinnacle |
|---|---|---|---|
| 新品优先购买 | 有 | 有 | 有 |
| 免费门店改裤脚 | 有 | 有 | 有 |
| 无小票退货 | 有 | 有 | 有 |
| 会员专属体验 | 有 | 有 | 有 |
| 合作伙伴福利 (Oura, Peloton, AG1) | 有 | 有 | 有 |
| 专属产品访问 | - | 有 | 有 |
| 私人导购 (即将上线) | - | - | 有 |
| 补货优先通知 | - | - | 有 |

### 个性化营销成熟度: 高级
- 2025 年 8 月任命首任 Chief AI & Technology Officer（Ranju Das，前 Amazon AI）
- AI 驱动的个性化已嵌入 App 和网站: 产品推荐、尺码建议、忠诚度优惠定向
- 行为科学方法驱动会员设计: 连接感、认同感、专属感、激励感、支持感
- 2026 年战略三大支柱: 健康、AI、社区

### 已知 CRM/CDP 供应商
- ESP: **Salesforce Marketing Cloud** (Journey Builder)
- CRM: **AgilOne**（可能已迁移）
- DMP: **Adobe Audience Manager**
- Campaign Orchestration: **Unica Campaign** (IBM/HCL)
- Data: **Azure Databricks + Snowflake**

### 痛点信号
1. 技术栈碎片化严重: SF + AgilOne + Adobe + IBM Unica = 四大厂商并存，整合成本高
2. 2800 万会员规模下的 1:1 超个性化尚未完全实现
3. CAITO 刚上任不到 1 年，AI 战略正在从规划走向执行
4. 免费会员体系 = 无付费门槛 = 需要通过个性化体验驱动转化和 ARPU 提升

### Socialhub.AI CIP 可解决的问题
- **统一碎片化技术栈**: CIP 作为 AI 编排层，整合 SFMC + Adobe + Unica 的多源数据
- **2800 万会员规模的超个性化**: CIP 的 AI Agent 架构专为大规模 1:1 个性化设计
- **ESP 集成增强**: Socialhub.AI 已有 Emarsys 集成经验，可叠加 SFMC 集成
- **注意**: Lululemon 自建倾向强，需定位为"增强现有能力"而非"替换"

---

## 4. JCPenney

### 计划名称与规模
- **JCPenney Rewards** — 2024 年 4 月全新上线，免费加入
- 会员规模: **2000 万+** Rewards 会员
- 目标: 回馈客户 **$5 亿**
- 简化结构: 已取消原有的 Gold/Platinum 等级制度，统一为单一积分体系

### 核心权益
- 每消费 $1 至少赚 1 CashPass 积分
- 消费 $200 获 $10 CashPass 奖励
- 注册即送 $10 CashPass
- 生日 $10 CashPass
- 信用卡持卡人: 每 $1 赚 1.5 积分（快 50%），$133 即可获 $10 奖励
- 奖励可与其他优惠券叠加
- 平均每年到店消费 5 次

### 个性化营销成熟度: 中级
- $10 亿转型计划（2023 年启动），数据驱动和客户体验为核心
- "先数据清洗"策略 — 确保数据质量后再驱动 AI/ML
- AI/ML 已应用于商品规划、供应链、配送路线，但营销侧个性化仍在建设中
- 网站和移动 App 已有搜索优化 + 个性化推荐
- CIO 2025 年 11 月离职 = 技术领导层真空

### 已知 CRM/CDP 供应商
- ERP: **Oracle NetSuite ERP + Oracle Retail**
- 门店: **GK Software CLOUD4RETAIL** (650+ 门店部署中)
- CDP: 有但具体供应商未确认
- CRM: 有分群能力 (email/SMS/PLCC)
- $10 亿转型 = 微服务架构 + AI/ML，技术栈正在现代化

### 痛点信号
1. CIO 离职（2025.11）= 技术战略可能调整，决策权结构不确定
2. 新忠诚度计划刚上线（2024.04），运营优化需求迫切
3. 简化等级制度 = 可能缺乏差异化会员体验的能力
4. "先数据清洗"策略暗示历史数据质量差，CDP 能力不足
5. 后破产重组企业，预算敏感但转型意愿强

### Socialhub.AI CIP 可解决的问题
- **CDP 统一数据层**: 解决 Oracle + GK Software + 现有 CRM 的数据分散问题
- **新忠诚度计划的 AI 赋能**: 2000 万会员 + 新积分体系 = 需要 AI 驱动的个性化触达提升转化
- **Phydigital 融合**: 参会人 Umashankar 专长物理+数字融合，CIP 的全渠道能力完美匹配
- **快速 ROI 证明**: JCPenney 预算敏感，CIP 需要展示在 3-6 个月内可量化的转化提升

---

## 5. IHG Hotels & Resorts

### 计划名称与规模
- **IHG One Rewards** — 全球最大酒店忠诚度计划之一
- 会员规模: **1.6 亿+**，覆盖 19 个品牌、6,000+ 酒店
- 五级制:

| 等级 | 门槛 | 积分加成 |
|---|---|---|
| Club | 注册即得 | 基础积分 |
| Silver Elite | 10 晚/年 | +20% |
| Gold Elite | 20 晚或 40K 积分 | +40% |
| Platinum Elite | 40 晚或 60K 积分 | +60% |
| Diamond Elite | 70 晚或 120K 积分 | +100% |

### 核心权益
- 所有等级: 免费 WiFi、会员专属房价、保证 2PM 退房
- Gold+: 积分加速
- Platinum+: 免费房间升级（视供应情况）、72 小时提前保证预订
- Diamond: 100% 积分加成
- 里程碑奖励: 20 晚起每 10 晚额度可选奖励（最高 100 晚）
- 联名信用卡: 银卡自动 Silver，白金卡自动 Platinum

### 个性化营销成熟度: 高级
- 2024 年与 Salesforce 签署战略合作，全面部署 Einstein 1 Platform
- Data Cloud 创建跨 19 品牌的统一客户档案
- 预订放弃行为 → Data Cloud 分群 → Marketing Cloud 自动召回
- Service Cloud 提供 360 度客户视图
- SVP of AI（Wei Manfredi）为新设岗位，AI 战略正在加速

### 已知 CRM/CDP 供应商
- CRM: **Salesforce Einstein 1 Platform**
- CDP: **Salesforce Data Cloud**
- Loyalty: **Salesforce Loyalty Management** (全球酒店业首家)
- ESP: **Salesforce Marketing Cloud**
- Service: **Salesforce Service Cloud**
- Cloud: **Microsoft Azure** (广泛使用)

### 痛点信号
1. Salesforce 全栈锁定 = 但 1.6 亿会员规模下，任何 AI 增强需求都是巨大市场
2. SVP of AI 新设岗位 = 正在寻找 AI 技术合作伙伴
3. 19 个品牌跨越多个档次 = 品牌间个性化策略差异化是持续挑战
4. 酒店业特有痛点: 属性级别（单店）与品牌级别的数据打通
5. 双人参会（SVP AI + Director Sales & Marketing）= 高意向信号

### Socialhub.AI CIP 可解决的问题
- **AI 增强层（非替代）**: 在 Salesforce 全栈之上提供更深度的 AI Agent 驱动客户运营
- **差异化定位**: CIP 的 AI-native agent architecture 与 Salesforce 的 Agentforce 形成差异化
- **品牌级个性化编排**: 帮助 IHG 在 19 个品牌间实现差异化但协调的会员运营
- **Microsoft Azure 桥梁**: IHG 使用 Azure，Socialhub.AI 的 Microsoft Co-sell 身份是切入点
- **双人策略**: Wei Manfredi 谈 AI 架构，Daniel Shin 谈营销 ROI

---

## 6. The RealReal

### 计划名称与规模
- **RealReal Rewards** — 基于寄售金额的四级忠诚度计划（面向卖家/寄售人）
- **First Look** — 付费订阅制会员计划（面向买家）
- 注册会员: **3100 万+**（买家 + 寄售人）

### RealReal Rewards (寄售人侧)

| 等级 | 年度净销售额门槛 | 忠诚度加成 |
|---|---|---|
| Trendsetter | < $1,499 | 0% |
| Influencer | $1,500 - $4,999 | +1% |
| Tastemaker | $5,000 - $9,999 | +2% |
| VIP | $10,000+ | +5% + 免费 First Look + $100 积分 |

VIP 权益: 专属礼宾服务、上门取货、生日奖励、VIP 门店活动邀请、专属短信服务

### First Look (买家侧)

| 等级 | 月费 | 核心权益 |
|---|---|---|
| First Look | $12/月 | 提前 24 小时购物、专属促销 |
| First Look Platinum | $30-$49.95/月 | 以上全部 + 免费 2 日达 |

### 个性化营销成熟度: 高级
- 自研 **Athena AI** 鉴定系统（覆盖 35% 商品鉴定）
- 个性化是客户留存的核心策略（Glossy 专题报道）
- AI 应用: 鉴定、定价、推荐
- 2023 年成为 Salesforce 旗舰客户案例

### 已知 CRM/CDP 供应商
- CRM: **Salesforce Sales Cloud**
- ESP: **Salesforce Marketing Cloud**
- CDP: **Salesforce Data Cloud**
- AI Agent: **Salesforce Agentforce**

### 痛点信号
1. Salesforce 旗舰客户 + Agentforce = 替换可能性极低
2. 新 CRO（Tom Hanrahan）仅入职 2 个月 = 正在评估工具但预算未必调整
3. 买卖双边市场 = 寄售人和买家需要完全不同的个性化策略
4. First Look 定价变动频繁（$10→$12）= 会员变现策略仍在试错
5. 3100 万会员但活跃度未知 = 可能存在大量沉睡用户

### Socialhub.AI CIP 可解决的问题
- **双边市场个性化**: CIP 可帮助在买家和寄售人两侧同时运行差异化的 AI 驱动运营
- **沉睡用户激活**: 3100 万会员中的沉睡用户激活是 CIP 的典型场景
- **定位为补充**: 不替换 Salesforce，而是在特定场景（如 AI 推荐增强、跨渠道编排）提供增值
- **CRO 视角**: 用收入增长数字（转化率提升、ARPU 增长）切入对话

---

## 7. Gopuff

### 计划名称与规模
- **Gopuff FAM** — 付费订阅制会员计划
- 月费: **$7.99/月**（学生 $3.99/月 或 $39.99/年）
- 会员规模: 未公开

### 核心权益
- 无限免费配送，零附加费
- "Cheapest on the Planet" 低价: $2 有机鸡蛋/面包/牛奶
- 每周会员专属折扣（最高 75% off）
- 自有品牌产品 10% off
- 平均每月节省约 $30
- 学生版: 50% 折扣 + 合作伙伴福利（Bumble Premium、GauthPlus）

### 个性化营销成熟度: 高级（自研为主）
- 自研 ML 平台: **1000+ 实时变量模型**，< 50ms 推理延迟
- 应用: 需求预测、动态定价、个性化推荐、库存优化
- Gopuff Ads 自研广告平台
- ML-native 文化，Head of ML & AI (Steven Zhu) 为前 Amazon ML

### 已知 CRM/CDP 供应商
- Customer Engagement/ESP: **Braze**（深度部署）
- CRM/Support: **Kustomer**
- Data: **Insight Cloud** (Seek + Koddi)
- Infra: Apache Kafka, Datadog, Cloudflare

### 痛点信号
1. Braze + Kustomer + Insight Cloud = 客户数据碎片化，缺乏统一客户视图
2. 自研 ML 平台极强但聚焦运营侧（需求预测/定价），营销侧个性化可能依赖 Braze 能力
3. 公司经历困难期（裁员、仓库关闭），会员增长压力大
4. 付费会员模式 = 留存和 churn 预测是核心痛点

### Socialhub.AI CIP 可解决的问题
- **CDP 统一层**: 整合 Braze + Kustomer + Insight Cloud 的碎片化数据
- **AI 编排层**: 不替换 Braze/Kustomer，而是在上层提供跨渠道 AI 编排
- **Churn 预测与干预**: 付费会员流失预测和自动化干预是 CIP 的直接价值
- **技术对话**: Steven Zhu 是 ML 专家，需展示 AI Agent 架构的技术深度而非营销话术

---

## 8. Salt & Straw

### 计划名称与规模
- **Salt & Straw Rewards** — App 内积分制忠诚度计划（2024 年上线）
- 会员规模: 未公开（小型 DTC 品牌，预计数万级别）
- 另有 **Pints Club** 月度订阅制冰淇淋配送

### 核心权益
- 每消费 $1 赚 1 积分
- 积分兑换: 免费华夫筒、冰淇淋球等
- 生日奖励: $10 off 冰淇淋蛋糕
- App 内提前下单 + 免排队自取
- 一键复购
- 反馈入口

### 个性化营销成熟度: 初级
- App 刚上线（2024），积分体系基础
- 缺乏公开的 AI/ML 个性化能力
- 品牌规模小，技术投入有限

### 已知 CRM/CDP 供应商
- Loyalty App: 自有 App (iOS + Android)
- CDP/CRM/ESP: **未确认** — 小型 DTC 品牌，可能使用 Shopify + Klaviyo 等轻量级工具
- **关键人物**: Erika Borges (Head of Digital Growth) 曾在 Starbucks、Thanx、Cardlytics 任职，服务过 Target、Whole Foods、Albertsons

### 痛点信号
1. 企业级背景的领导 + 初级工具 = 需求与能力之间存在 gap
2. 积分体系基础，缺乏差异化体验和 AI 驱动的个性化
3. 门店 + 电商 + 订阅三渠道数据可能未打通
4. 品牌预算有限但增长意愿强

### Socialhub.AI CIP 可解决的问题
- **低技术栈锁定 = 最易切入**: 没有深度供应商绑定，CIP 可作为第一个企业级客户智能平台
- **全渠道统一**: 整合门店 POS + 电商 + Pints Club 订阅数据
- **Lifecycle Marketing**: Erika 的专长领域，CIP 的会员生命周期管理能力直接对话
- **注意**: 需确认 Socialhub.AI 是否有适合中小品牌的定价模式

---

## 9. VIZIO

### 计划名称与规模
- **无传统忠诚度计划** — VIZIO 为消费电子/智能电视品牌，不运营会员积分体系
- 核心资产: **Inscape ACR** 数据（1860 万+ 设备的自动内容识别），面向广告主而非消费者

### 个性化营销成熟度: N/A（Ad Tech 方向）
- 自研 VIZIO Ads / Platform+ CTV 广告平台
- 2024 年 12 月被 **Walmart 以 $23 亿收购**
- 方向: 零售媒体网络 + CTV 广告，非传统 CRM/ESP

### 已知供应商
- Ad Tech: 自建 (VIZIO Ads / Platform+)
- Data: Inscape ACR (自建)
- Parent: Walmart

### 痛点信号
- 决策权已上移至 Walmart 层面
- Ad tech 方向与传统忠诚度营销不匹配

### Socialhub.AI CIP 可解决的问题
- 非标准客户，CIP 核心能力与 VIZIO 业务方向匹配度低
- 潜在价值: 通过 VIZIO 进入 Walmart 零售媒体生态的间接路径

---

## 10. The AI Collective

### 计划名称与规模
- **社区会员制** — 非营利 AI 社区
- 会员规模: **225,000+** 全球 AI 创业者/技术人
- 非传统忠诚度计划，无积分/等级体系

### 对 Socialhub.AI 的定位
- **渠道合作伙伴**，非直接客户
- 与 Microsoft 已有合作关系，与 Socialhub.AI 的 Co-sell 身份直接对齐
- 通过社区触达 225K+ AI 从业者中的企业决策者
- 探索联合活动、内容合作、referral 机制

---

## 综合对比矩阵

| 公司 | 忠诚度计划 | 会员规模 | 计划类型 | 个性化成熟度 | CRM/CDP | 技术栈锁定 | CIP 契合度 |
|---|---|---|---|---|---|---|---|
| **Canada Goose** | Basecamp | 未公开 | 免费社区 | 初级-中级 | D365 (无 CDP) | 中 | **极高** |
| **JCPenney** | JCPenney Rewards | 2000 万+ | 免费积分 | 中级 | Oracle + GK | 中 | **高** |
| **Arc'teryx** | 无消费者计划 | N/A | N/A | 高级(广告侧) | SAP (无 CDP) | 中 | **高** |
| **Salt & Straw** | App 积分 | 未公开(小) | 免费积分 | 初级 | 未知 | 低 | **高** |
| **Lululemon** | Membership 三级 | 2800 万 | 免费分级 | 高级 | SF+Adobe+IBM | 高 | 中 |
| **Gopuff** | FAM 付费 | 未公开 | 付费订阅 | 高级(自研) | Braze+Kustomer | 中-高 | 中 |
| **IHG** | One Rewards 五级 | 1.6 亿 | 免费分级 | 高级 | Salesforce 全栈 | 极高 | 中(增强层) |
| **RealReal** | Rewards+First Look | 3100 万 | 混合(免费+付费) | 高级 | Salesforce 全栈 | 极高 | 低 |
| **VIZIO** | 无 | N/A | N/A | N/A | 自建 Ad Tech | 高 | 低 |
| **The AI Collective** | 社区会员 | 22.5 万 | 非营利 | N/A | N/A | N/A | 渠道伙伴 |

---

## 优先级排序

### P0 — 最高优先级（核心目标客户）

**1. Canada Goose** -- 综合评分: 5/5
- 理由: D365 架构师直接参会 + Microsoft Co-sell 完美对齐 + VP Data/AI 招聘中 + CDIO 刚上任 = 多重决策窗口叠加
- 切入策略: CIP 与 D365 原生集成，填补 CDP 空白
- 风险: 新领导可能倾向自建

**2. IHG Hotels & Resorts** -- 综合评分: 4.5/5
- 理由: 双人参会(最高意向信号) + SVP AI 新设岗位 + 1.6 亿会员 + Azure 使用
- 切入策略: AI 增强层定位（非替代 Salesforce），利用 Azure 桥梁
- 风险: Salesforce 全栈深度绑定

### P1 — 高优先级

**3. JCPenney** -- 综合评分: 4/5
- 理由: CIO 离职 + 新忠诚度计划 + 2000 万会员 + $10 亿转型 + Head of Architecture 参会
- 切入策略: CDP 数据统一 + 新忠诚度计划 AI 赋能
- 风险: 后破产重组，预算敏感

**4. Lululemon** -- 综合评分: 4/5
- 理由: 2800 万会员 + 首任 CAITO + 技术栈碎片化 + 高级架构师参会
- 切入策略: 技术栈整合层 + 超个性化规模化
- 风险: 自建倾向强，参会人非最终决策者

### P2 — 中优先级

**5. Arc'teryx** -- 综合评分: 3.5/5
- 理由: CTO 参会(最高技术决策者) + 无消费者忠诚度计划(从零建设) + SAP 生态匹配 + DTC 60%
- 切入策略: 帮助从零构建 AI 驱动的消费者忠诚度体系
- 风险: Amer Sports 集团统一采购 + 已有多工具生态

**6. Salt & Straw** -- 综合评分: 3/5
- 理由: 低技术栈锁定(最易切入) + 企业级背景领导(理解价值) + lifecycle marketing 专长
- 切入策略: 第一个企业级客户智能平台
- 风险: 品牌规模小，预算有限

**7. Gopuff** -- 综合评分: 3/5
- 理由: CDP 碎片化 + 付费会员 churn 痛点 + ML Head 参会
- 切入策略: CDP 统一层 + churn 预测
- 风险: 自研文化浓厚，可能倾向自建

### P3 — 低优先级 / 特殊定位

**8. RealReal** -- 综合评分: 2/5
- 理由: Salesforce 旗舰客户 + Agentforce 已部署 = 替换空间极小
- 切入策略: CRO 新上任窗口，用 ROI 数字探索边缘场景
- 风险: SF 深度绑定几乎不可能突破

**9. VIZIO** -- 综合评分: 1.5/5
- 理由: Ad tech 方向不匹配 + Walmart 决策权
- 切入策略: 技术合作探索，间接触达 Walmart 零售媒体生态

**10. The AI Collective** -- 定位: 渠道伙伴
- 理由: 非直接客户，225K+ AI 社区可作为分发渠道
- 切入策略: 联合活动、内容合作、referral 机制

---

> 数据来源: 公司官网、SEC 文件、Salesforce PR、行业媒体 (Glossy, Marketing Dive, Grocery Dive, Hotel Dive, RetailWire)、招聘信息、厂商案例 (Algolia, GK Software)、忠诚度评测 (The Points Guy, NerdWallet, U.S. News Travel)
