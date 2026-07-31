---
type: company
status: draft
company: databricks
founded: 2013
hq: San Francisco
---

# Databricks

Lakehouse 平台厂商。语义层战略是**把语义作为数据层的治理对象**（upstream 而非 downstream），并在其上叠加一层自动抽取的企业上下文图谱供 AI 消费。

## 语义层相关产品线

- [[Unity Catalog]] — catalog + 治理 + **Business Semantics（Metric Views）**：人工治理的确定性语义内核
- [[Genie]] — Genie One（业务用户的 AI coworker）+ Genie Agents（可自治的领域 agent）
- [[Genie Ontology]] — 自动上下文层，OntoRank 权威度排序（gated public preview）

配套：Genie Code（创作期 agentic 助手）、Unity AI Gateway（MCP / 工具 / 成本治理）。

## 战略与路线观察

**双层语义是核心架构选择**。人工治理的 Metric Views（确定但覆盖窄）与自动抽取的 Genie Ontology（覆盖广但概率性）并存，靠 OntoRank 做权威度仲裁。这与 Snowflake 的 Autopilot 路线（自动生成定义交人审核，终点仍是单一人工定义）是不同的答案。详见 [[2026-07-31 Databricks 的双层语义架构]]。

**与 Snowflake 高度镜像但不完全对称**。两家都在数月内推出原生语义层 + NL 查询产品（Metric Views ↔ Semantic Views，Genie ↔ Cortex Analyst），差异在：生态基础（lakehouse vs warehouse）、开放策略（Databricks 开源核心实现，Snowflake 是 OSI 发起方）、AI 产品形态（Genie 走完整产品 + Slack/Teams/移动端多面交付，Cortex Analyst 走 API-first 嵌入）、以及上下文层路线（Ontology vs Autopilot）。

**开放性是对 Snowflake 的差异化，但范围有限**。Metric View 核心实现开源进 Apache Spark（SPARK-54119），Unity Catalog OSS v0.5 跟进；已加入 [[Open Semantic Interchange]]。但开源范围是 metric view 的定义与执行，Genie 那一整套不在其中——开放的是"语义定义的载体"，不是"AI 消费能力"。

**生态策略是拉拢 BI 与治理厂商**，但 Power BI 缺席。已整合/承诺：Sigma、Hex、Omni（双向）、Domo、ThoughtSpot、Tableau（预计 2026 年底）；治理侧 Atlan、Collibra（双向）、Monte Carlo、Anomalo。微软 2026 年初移除 BI Compatibility Mode，要求指标定义在 Fabric 语义模型内，两家在语义层上是竞争关系。考虑 Power BI 的企业存量，这是"处处可用"叙事的实质缺口。

**采纳路径设计值得注意**：支持把既有 AI/BI Dashboard 中的 join、filter、计算字段一键提升为 Metric View，也支持从 Tableau / Power BI 文件导入。把存量 dashboard 逻辑当作语义层的冷启动来源，绕开"先建模才能用"的门槛——这是对自己"传统语义层依赖繁重前期建模"这一批判的自洽回答。

## 时间线

- 2025：Metric Views 首次发布（预览）
- 2026 年 6 月（Data + AI Summit）：Genie One、Genie Agents、Genie Ontology 发布；Unity Catalog Metrics 发布并接入 Genie Ontology（[[2026-07-31 Databricks - Genie One, Agents, Ontology 发布]]）
- 2026：Unity Catalog Business Semantics GA，宣布核心实现开源到 Apache Spark（SPARK-54119），加入 OSI（[[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]）
- 2026 年底（承诺）：Tableau 支持 delegated semantics

待补：Metric Views 首次发布的准确日期与当时的能力范围。

## 客户与背书

- iFood：指标标准化后显著减少对数工作量，查询"某些情况下快 10 倍"，Genie 准确率有提升（客户引述，无独立验证）
- Zalando：措辞谨慎（"exciting opportunity"、"promising contribution"），属早期评价
- Foot Locker：Genie Agents 用于北美各品牌的高管与业务团队
- Uplight：Genie One
- 采纳度数据：客户已创建超过一百万个 Genie Spaces（官方口径）

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
