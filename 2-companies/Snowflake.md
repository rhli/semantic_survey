---
type: company
status: draft
company: snowflake
founded: 2012
hq: Bozeman, MT
---

# Snowflake

云数仓平台厂商，语义层战略是"智能留在数据所在的平台内"：Semantic Views 为 catalog 一等对象，Cortex AI 直接在其上推理。

## 语义层相关产品线

- [[Snowflake Semantic Views]] — 原生语义视图 + Semantic View Autopilot（自动生成）
- [[Cortex Analyst]] — 基于语义视图的 conversational analytics API
- Horizon Catalog — 治理目录（待调研是否单独立页）

## 战略与路线观察

- OSI 的主要发起方——一边做平台原生锁定，一边主导开放标准，值得跟踪其真实动机与执行。
- Autopilot 路线（从 BI 使用模式自动学习语义定义）是降低建模成本的差异化打法。

## 时间线

- 2025：Semantic Views 发布
- 2026 初：Semantic View Autopilot 发布
- 2026-01：OSI 标准发布（发起方）

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
