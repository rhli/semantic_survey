---
type: product
status: draft
company: dbt-labs
product: dbt Semantic Layer (MetricFlow)
capabilities: [semantic-modeling, metrics-layer, query-rewrite, headless-bi]
ga_status: ga
---

# dbt Semantic Layer

## 定位与解决的问题

metrics-as-code 路线的代表：指标、维度、实体定义在 YAML 中，与 dbt transformation 模型同仓，走 Git/PR/CI 流程。解决"指标定义与数据加工逻辑脱节"的问题。

## 核心概念与架构

- **MetricFlow**（开源，Apache 2.0）：语义编译引擎。指标请求 → dataflow-based query plan → 优化 → engine-specific SQL（支持 Snowflake/BigQuery/Databricks 等 10+ 仓）。
- serving 层（Semantic Layer API：JDBC/GraphQL/MCP）是 dbt Cloud 功能，不能自托管——最大争议点。
- 指标类型：measures、simple、ratio、derived（指标依赖指标）。

## 语义模型的表达方式

- YAML（semantic models + metrics），与 dbt model 文件并列。**MetricFlow 格式已成为 OSI 的声明式规范基础**，是行业事实标准的候选。

## 限制与边界

- serving 绑定 dbt Cloud（$100/user/mo 起）；dbt Core 用户无法使用完整语义层。
- 学习曲线：YAML 语义建模概念门槛高于 BI 拖拽式。

## 待调研问题

- [ ] MetricFlow 的 dataflow plan 编译机制（源码可读）
- [ ] semantic model YAML 规范 vs OSI 规范的差异
- [ ] 与 dbt Fusion 引擎的关系
- [ ] 缓存/加速能力现状（相对 Cube 的弱项）

## 参考来源

- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
- GitHub: github.com/dbt-labs/metricflow
