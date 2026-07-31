---
type: product
status: draft
company: snowflake
product: Semantic Views
capabilities: [semantic-modeling, metrics-layer, governance]
ga_status: ga
---

# Snowflake Semantic Views

## 定位与解决的问题

Snowflake 的原生语义层：语义视图作为 catalog 一等对象（与表、视图同级），定义逻辑实体、维度、指标，所有连接 Snowflake 的工具消费同一治理定义。

## 核心概念与架构

- YAML 定义的语义视图，存储在 Snowflake catalog 内；查询引擎原生理解语义视图（非外部系统改写），性能优势。
- **Semantic View Autopilot**（2026 初）：ML 分析仓元数据 + 连接的 BI 工具使用模式（Looker、dbt、Sigma、ThoughtSpot），自动提议语义视图定义——建模成本从天级降到分钟级。
- 下游：[[Cortex Analyst]] 直接在语义视图上做 NL 推理。

## 语义模型的表达方式

- YAML：逻辑表（业务实体）→ 组合出 metrics 与 dimensions。细节待深研。

## 限制与边界

- 仅在 Snowflake 生态内生效；多仓/仓外数据场景不适用。

## 待调研问题

- [ ] 语义视图 YAML 规范全文与表达力边界
- [ ] Autopilot 的生成质量与人工审核流程
- [ ] 与 OSI 的互转（Snowflake 是 OSI 发起方）
- [ ] Horizon Catalog 与语义视图的关系

## 参考来源

- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
