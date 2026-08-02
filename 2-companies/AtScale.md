---
type: company
status: draft
company: atscale
founded: 2013
hq: Boston
capabilities: [semantic-modeling, metrics-layer, query-rewrite, acceleration, headless-bi, governance]
ga_status: ga
---

# AtScale

企业级独立语义层厂商，虚拟 OLAP 路线：对 BI 工具呈现为 OLAP cube（讲 MDX/DAX），向下把查询翻译为优化的仓 SQL。十三年企业部署史，客户是 Fortune 1000 级别的存量世界——**它服务的是"Excel 和 Power BI 不会消失"这个现实**。

## 语义层相关产品线

- Universal Semantic Layer — 语义建模画布 + 查询引擎 + 自动聚合管理（公司即产品）
- SML（Semantic Modeling Language）— 开源的建模语言（YAML 对象，Git 管理）
- MCP server — 向 agent 暴露治理语义（兼容 2026-07 的 stateless MCP 规范）
- One-Click Modeling — AI 辅助生成语义模型

## 战略与路线观察

- **定位 "Universal" 的三层含义**：任意仓（Snowflake/BigQuery/Databricks/Redshift）× 任意消费端（SQL/MDX/DAX/Python/REST/MCP）× 定义与基础设施解耦。这是平台原生方案的直接对立面，也是它在大企业异构环境的立身之本。
- **开放叙事要拆开看**：SML 开源的是**语言**（规范 + Git 仓），引擎仍闭源。与 MetricFlow（引擎开源 Apache 2.0）是两种不同的"开放"——AtScale 开放的是模型可移植性，dbt 开放的是实现可审计性。评估时别被"open semantics"话术带偏。
- **差异化核心是 autonomous aggregates**：自动识别、创建、维护聚合表——[[Query Acceleration]] 维度上最成熟的一家，与 [[Aloudata CAN]] 的智能物化、Cube 的 pre-aggregation 是同一问题的三种工程实现。
- **旧协议兼容是它的隐形护城河**：Excel/Power BI 用户无感迁移（MDX/DAX 直通），这在 agent 叙事里容易被忽略，但对 Fortune 1000 是采购决定性因素。
- 定价重（月费下限 $2,500–7,000），明确放弃中小市场。2025 GigaOm Radar 语义层 Leader；OSI working group 成员。

## 时间线

- 2013：创立，虚拟 OLAP 路线
- 2025：GigaOm Radar Leader；SML 开源；MCP server 发布
- 2026：发布《2026 State of the Semantic Layer》报告（行业叙事资产）

## 待调研问题

- [ ] SML 的表达力边界（vs MetricFlow 五类指标）
- [ ] 虚拟 OLAP 引擎如何处理多事实表与 fan-out（[[Join 正确性的保证方式]] 待填项）
- [ ] One-Click Modeling 的生成质量

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
