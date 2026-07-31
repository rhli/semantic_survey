---
type: company
status: draft
company: databricks
founded: 2013
hq: San Francisco
---

# Databricks

Lakehouse 平台厂商，语义层战略是"语义作为数据层的治理对象"：指标定义进 Unity Catalog，AI 消费靠 Genie。

## 语义层相关产品线

- [[Unity Catalog]] — catalog + 治理 + Business Semantics（Metric Views）
- [[Genie]] — AI/BI 自然语言查询 + Genie Ontology（企业上下文层）

## 战略与路线观察

- 打法与 Snowflake 高度镜像（原生语义层 + NL 查询产品），差异在 lakehouse vs warehouse 生态。
- Unity Catalog Business Semantics 选择开源，是对 Snowflake 的开放性差异化。
- OSI 参与方。

## 时间线

- 2026 初：Unity Catalog Business Semantics GA 并开源（[[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]）
- 2026 Data + AI Summit：Unity Catalog Metrics 发布，喂给 Genie Ontology

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
