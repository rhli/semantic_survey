---
type: source
status: draft
url: https://www.allocatingintelligence.com/snowflake-semantic-views-vs-databricks-metric-views
source_type: blog
publisher: Allocating Intelligence
published:
accessed: 2026-07-31
companies: [snowflake, databricks]
topics: ["[[Metrics Layer]]", "[[Semantic Model]]"]
---

# Allocating Intelligence: Snowflake Semantic Views vs Databricks Metric Views

## 摘要

两大平台在数月内先后发布原生语义层，打法镜像：定义一次、集中治理、所有 dashboard 和 AI agent 消费同一定义。差异在生态绑定——Snowflake 语义喂 Cortex AI，Databricks 语义挂在 Unity Catalog 治理体系下。

## 要点

- Snowflake：Semantic Views 是 catalog 一等对象，所有连接 Snowflake 的工具看到同一治理定义；Cortex Analyst 直接在语义视图上推理。战略是"智能留在数据所在的平台内"（无外部 API、无中间件延迟）。
- Databricks：Metric Views 经 Unity Catalog Business Semantics GA；治理复用 UC 的行级安全、列级脱敏、审计日志——治理不是外挂而是同一体系。
- 选型判断：消费主要在单一平台内 → 用原生层；跨云跨平台 → 需要独立语义层。
- 两者都是平台锁定，跨平台指标治理需要外部 context layer（Atlan 等借此切入）。

## 关联

- 支撑 [[Semantic Layer Vendor Landscape]] 平台原生阵营的"镜像打法"判断。
- 后续深研 [[Unity Catalog]]、[[Snowflake Semantic Views]] 时的对比基线。
