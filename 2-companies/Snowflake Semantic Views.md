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

Snowflake 的原生语义层：语义视图是 catalog 一等对象（与表、视图同级），定义逻辑表、维度、事实、指标与表间关系，所有连接 Snowflake 的工具消费同一治理定义。与 [[Unity Catalog]] Metric Views 是直接的镜像竞品。

## 核心概念与架构

- **DDL 五块结构**：`CREATE SEMANTIC VIEW` 用 TABLES（逻辑表 + 主键）、RELATIONSHIPS（join 声明）、FACTS（行级数值）、DIMENSIONS、METRICS（带聚合公式的 KPI）五个块定义。也可在 Snowsight UI 向导里建。
- **查询引擎原生理解语义视图**，不是外部系统改写 SQL——这是平台原生路线的性能与治理优势来源。
- **消费门槛已大幅降低**：2026-03-02 起可用标准 SQL + `AGG()` 函数查询语义视图，不再强制 `SEMANTIC_VIEW()` 子句，无需特殊连接器。
- **Semantic View Autopilot**（2026 初）：ML 分析仓元数据 + 连接的 BI 工具使用模式（Looker、dbt、Sigma、ThoughtSpot），自动提议语义视图定义，交人审核——建模成本从天级降到分钟级。四条上下文覆盖路线之一，见 [[上下文层的四条路线]]。
- 下游：[[Cortex Analyst]] 读语义视图的元数据（描述、同义词、join 路径、verified queries）做 NL 推理。

## 语义模型的表达方式

DDL/YAML 双形态。硬约束：YAML ≤ 1 MB（去除 sample values 后 32K token；另有某 quickstart 页面写 2 MB，**文档自相矛盾，以 1 MB 为准更稳妥**）；建议总列数 50–100。

表达力边界（与 MetricFlow 对照明显）：

- **无原生 cumulative / conversion 指标类型**——MetricFlow 五类指标里的两类在 Snowflake 要手写窗口/漏斗 SQL；
- **无自动时间粒度**（time spine 需手工）；
- RELATIONSHIPS 是声明式 join 路径，**是否做基数校验待核实**（[[Join 正确性的保证方式]] 的待填项）。

## 限制与边界

- **仓内限定**：语义视图只看得见 Snowflake 里的数据——dbt 模型、Looker 指标、SaaS 对象都在覆盖外。这是平台原生路线的结构性边界，也是 [[Atlan]] 等跨平台层的切入点。
- Business Glossary 在 H2 2026 路线图上（当前缺失）。
- 生态绑定：价值与 Snowflake 仓位成正比，多仓场景不适用。

## 待调研问题

- [ ] RELATIONSHIPS 的基数校验行为（运行时校验还是声明免责）
- [ ] Autopilot 生成质量与人工审核工作流细节
- [ ] 与 OSI/Ossie 的互转成熟度（Snowflake 是发起方，converter 应最完整）
- [ ] Horizon Catalog 与语义视图的治理集成深度

## 参考来源

- [[2026-08-02 Snowflake - Cortex Analyst 评测与边界]]
- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
