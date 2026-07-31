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

企业级独立语义层厂商，虚拟 OLAP 路线：对 BI 工具呈现为 OLAP cube（讲 MDX/DAX），向下把查询翻译为优化的仓 SQL。十年以上企业部署史，面向 Fortune 500 存量场景。

## 语义层相关产品线

- [[AtScale]] — Universal Semantic Layer（语义建模画布 + 查询引擎 + 自动聚合管理）
- SML（Semantic Modeling Language）— 已开源的建模语言

## 战略与路线观察

- 定位"universal"：任意仓（Snowflake/BigQuery/Databricks/Redshift…）× 任意 BI 工具，是平台原生方案的直接对立面。
- 差异化核心是 autonomous aggregates（自动识别、创建、维护聚合表）和旧协议兼容（Excel/Power BI 用户无感迁移）。
- 2025 GigaOm Radar 语义层报告 Leader；OSI working group 成员；MCP server 已上线。
- 定价重（月费下限 $2,500–7,000），明确放弃中小市场。

## 时间线

- 2025：GigaOm Radar Leader
- 待补：SML 开源时间点

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
