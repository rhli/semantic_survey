---
type: company
status: draft
company: atlan
founded: 2019
hq: Singapore / 远程
capabilities: [data-catalog, lineage, governance]
ga_status: ga
---

# Atlan

元数据/catalog 厂商，2025–2026 年自我重定位为 "Context Layer for AI"：不与 dbt/Cube 竞争语义定义，而是摄取它们的定义并绑定治理元数据，通过 MCP 交付给 agent。

## 语义层相关产品线

- Enterprise Data Graph — 80+ 连接器的统一元数据图谱
- Context Engineering Studio / Context Products — 面向 agent 的上下文打包
- Semantic View Generator — 从 catalog 资产生成治理指标视图
- Atlan MCP server — 向 Claude/Cursor/ChatGPT 等暴露治理上下文

（是否拆分产品页待深研后定，当前统一在本页。）

## 战略与路线观察

- [[Context Layer]] 概念的主要推手，叙事是"语义层服务 BI，context layer 服务 agent"——catalog 厂商对语义层浪潮的升维反击。
- 核心宣称：治理上下文使 AI SQL 准确率 +38%（自家 AI Labs 测试，需第三方验证）。
- 背书：Gartner MQ for D&A Governance Leader（2026）、Forrester Wave Data Governance（Q3 2025）；客户 Mastercard、Workday、GM 等。
- 与平台的既竞争又互补：给 Databricks Genie 喂跨平台上下文（"UC Metrics 治理湖内，Atlan 治理湖外"）。

## 时间线

- 2026：Gartner MQ D&A Governance Leader

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
