---
type: source
status: draft
url: https://docs.databricks.com/aws/en/uc-semantics/metric-views/materialization
source_type: docs
publisher: Databricks
published:
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Query Acceleration]]", "[[Query Rewrite]]", "[[Data Governance]]"]
archive: "[[2026-07-31 Databricks - Materialization for Metric Views (raw)]]"
---

# Databricks: Metric Views 的物化加速（官方文档）

## 摘要

Metric View 的查询加速机制文档。核心是三级查询改写降级链和"可加性"（additive）门槛——决定了自动加速在什么情况下生效、什么情况下回落到扫源表。功能状态：Preview。

## 要点

- **三级降级链**：exact match → rollup match → unaggregated match → 直接读源表。优化器依次尝试。
- **exact match**：查询的 group-by 表达式与 measures 与某个物化完全对应。
- **rollup match**：查询粒度比物化更粗（更少维度或更宽的时间粒度），引擎把细粒度结果折叠上卷。三个硬条件：
  1. **所有 measure 必须可加**：`SUM`、`COUNT`、`MIN`、`MAX`、`BIT_AND`、`BIT_OR`、`BIT_XOR`、`BOOL_AND`、`BOOL_OR`。任何 `DISTINCT` 聚合（`COUNT(DISTINCT)`、`SUM(DISTINCT)`）不可加；`MEDIAN` 依赖分布也不行。
  2. **measure 定义中只能有一个聚合函数**：`sum(cost) + min(revenue)` 这类复合表达式不合格。
  3. **参与的 filter 必须确定性**：`WHERE region = 'US'` 可以，`rand()`/`uuid()` 不行；measure 内的 `FILTER` 子句同样要求确定性。
- window measure 一律不可 rollup。
- **一旦 metric view 使用 `one_to_many` join，其所有物化全部降级为 exact match only**。
- **物化的治理约束**（重要）：
  - 源表不能有 RLS、列掩码或 ABAC 策略。
  - filter、fields、measures 不能使用 invoker-dependent 表达式（`current_user()`、`is_member()` 等），否则创建/变更/刷新失败，报 `METRIC_VIEW_MATERIALIZATION_WITH_INVOKER_DEPENDENT_EXPRESSIONS_NOT_SUPPORTED`（SQLSTATE 42K0E）。
- 其他限制：定义了 parameters 的 metric view 不能物化；物化创建后不能改 owner，不支持 group ownership。
- 新鲜度语义：命中物化的查询读上次刷新的结果，未命中的查询读实时数据——**同一个 metric view 的不同查询可能看到不同新鲜度的数据**。官方建议把刷新排在上游批处理管道之后，或用 unaggregated 物化让所有查询读同一快照。
- 官方建议为不同查询形态创建多个 aggregated 物化；避免过细的维度（如毫秒时间戳）。
- 修改 metric view 后不自动排刷新；物化在首次调度更新完成前不参与自动改写。

## 关联

- [[Unity Catalog]] 的加速能力与真实边界。
- 全文存档：[[2026-07-31 Databricks - Materialization for Metric Views (raw)]]（功能为 Preview，规则可能变动，存档为核对基线）
- 与 [[Aloudata CAN]] 的"NoETL 免宽表 + 自动物化"、[[AtScale]] 的 autonomous aggregates、[[Cube]] 的 pre-aggregation 是 [[Query Acceleration]] 的正面对比材料——注意 Databricks 的物化仍需人工声明要物化哪些维度/指标组合，不是全自动。
