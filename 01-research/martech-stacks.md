# MarTech 技术栈调研报告

> 调研日期: 2026-05-18 | 方法: Web search (公开新闻、厂商案例、职位描述) | 用途: Socialhub.AI x Microsoft Seattle 5/22 活动

---

## 1. Arc'teryx — 锁定度: 中

**已知技术栈:**
- ERP: **SAP S/4HANA Cloud** (Private Edition, Fashion & Vertical) — 母公司 Amer Sports 2025 年启动 RISE with SAP
- 搜索: **Algolia** (Search + Dynamic Re-ranking + AI Synonyms)
- AI: Google AI full-funnel strategy, 2025 Google AI Marketer of the Year
- 数据: **Qlik Sense + Qlik DataMarket**
- CDP/CRM/ESP: **未确认具体供应商**，营销层尚在建设中

**Socialhub 兼容性: 高** — SAP 合作直接适用 + DTC 转型窗口期 + 参会人为 CTO

---

## 2. Canada Goose — 锁定度: 中 (MS 生态内)

**已知技术栈:**
- ERP/Finance: **Microsoft Dynamics 365 for Finance**
- BI: **Microsoft Power BI**; Cloud: **Microsoft Azure**
- CDP/CRM/ESP: **未确认**，但参会人为 D365 架构师，暗示 D365 正在扩展

**Socialhub 兼容性: 极高** — Socialhub.AI 是 MS Co-sell Partner，参会人直接负责 D365 架构

---

## 3. Lululemon — 锁定度: 高

**已知技术栈:**
- ESP/Marketing: **Salesforce Marketing Cloud** (Journey Builder)
- CRM: **AgilOne** (可能已迁移)
- DMP: **Adobe Audience Manager**; Campaign Orchestration: **Unica Campaign** (IBM/HCL)
- Data: **Azure Databricks + Snowflake**

**Socialhub 兼容性: 中** — SF+Adobe+IBM 多厂覆盖，但碎片化创造整合需求

---

## 4. IHG Hotels & Resorts — 锁定度: 极高

**已知技术栈:**
- CRM: **Salesforce Einstein 1 Platform**
- CDP: **Salesforce Data Cloud** (数百万客户档案，跨 19 个品牌)
- Loyalty: **Salesforce Loyalty Management** (IHG One Rewards，全球酒店业首家)
- ESP: **Salesforce Marketing Cloud** (email/SMS/push)
- Service: **Salesforce Service Cloud**

来源: Salesforce PR 2024/04, Hotel Dive

**Socialhub 兼容性: 低** — SF 全栈深度绑定。但双人参会暗示高意向，需定位为 AI 增强补充方案。

---

## 5. JCPenney — 锁定度: 中

**已知技术栈:**
- ERP: **Oracle NetSuite ERP + Oracle Retail**
- 门店: **GK Software CLOUD4RETAIL** (650+ 门店部署中)
- CDP: 有 (具体供应商未确认); CRM segmentation (email/SMS/PLCC)
- $10 亿转型计划进行中，微服务架构 + AI/ML

**Socialhub 兼容性: 高** — 技术现代化窗口 + Head of Architecture 参会

---

## 6. The RealReal — 锁定度: 极高

**已知技术栈:**
- CRM: **Salesforce Sales Cloud**
- ESP: **Salesforce Marketing Cloud**
- CDP: **Salesforce Data Cloud**
- AI: **Agentforce** (Salesforce AI Agent)
- 3100 万+ 会员

**Socialhub 兼容性: 低** — SF 旗舰客户 + Agentforce，基本无替换空间

---

## 7. Gopuff — 锁定度: 中-高

**已知技术栈:**
- Customer Engagement/ESP: **Braze**
- CRM/Support: **Kustomer**
- Data: **Insight Cloud** (Seek + Koddi)
- Ad Tech: **Gopuff Ads** (自建 ML/AI, 1000+ real-time variables)
- Infra: Apache Kafka, Datadog, Cloudflare

**Socialhub 兼容性: 中** — Braze 深度部署，但 CDP 统一层有空间

---

## 8. VIZIO — 锁定度: 高 (自建)

**已知技术栈:**
- Ad Tech: **VIZIO Ads / Platform+** (自建)
- Data: **Inscape ACR** (1860 万+ devices)
- Parent: **Walmart** ($23 亿收购, 2024)

**Socialhub 兼容性: 低** — Ad tech 方向，非传统 CRM/ESP

---

## 9. Salt & Straw — 锁定度: 低

**已知技术栈:**
- Loyalty: 自有积分计划 + App (2024)
- CDP/CRM/ESP: **未确认**，小型 DTC 品牌

**Socialhub 兼容性: 高** — 低锁定 + Head of Digital Growth 参会

---

## 10. The AI Collective — N/A

- 非营利社区组织，非目标客户
- 潜在 Channel Partner

---

## 综合兼容性矩阵

| 公司 | CRM | CDP | ESP | 锁定度 | Socialhub 兼容性 |
|---|---|---|---|---|---|
| Canada Goose | D365 | 未知 | 未知 | 中 | **极高** |
| Arc'teryx | 未知 | 未知 | 未知 | 中 | **高** |
| JCPenney | Oracle | 未确认 | 未确认 | 中 | **高** |
| Salt & Straw | 未知 | 未知 | 未知 | 低 | **高** |
| Lululemon | SF+AgilOne | Adobe | SFMC | 高 | 中 |
| Gopuff | Kustomer | 无 | Braze | 中-高 | 中 |
| IHG | SF Einstein | SF Data Cloud | SFMC | 极高 | 低 |
| RealReal | SF Sales | SF Data Cloud | SFMC | 极高 | 低 |
| VIZIO | 自建 | 自建 | 自建 | 高 | 低 |
