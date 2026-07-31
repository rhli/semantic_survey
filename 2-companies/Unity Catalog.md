---
type: product
status: draft
company: databricks
product: Unity Catalog
capabilities: [data-catalog, governance, lineage, semantic-modeling, metrics-layer, acceleration]
ga_status: ga
---

# Unity Catalog

## 定位与解决的问题

Databricks 的统一 catalog 与治理层，2026 年起通过 Business Semantics（Metric Views）把语义层做成 catalog 的原生治理对象——指标与表、视图同级，复用同一套权限、血缘、审计体系。

## 核心概念与架构

- **Metric View**：核心语义对象。分离 measure 定义与 dimension 分组，定义一次、查询时任意维度动态编译（防止为每种维度组合物化视图）。
- 声明式定义 → 查询时确定性编译执行，所有消费者（SQL/dashboard/notebook/Genie/第三方工具）同一结果。
- 治理复用 UC：行级安全、列级脱敏、审计日志。
- 性能：可配置预计算 + 增量刷新，查询引擎自动改写命中 materialized views。

## 语义模型的表达方式

- SQL（`CREATE VIEW ... WITH METRICS`）或 YAML；Catalog Explorer 可视化编辑器；Genie Code 自然语言辅助编写。
- 建模支持 star/snowflake 多级 join、指标可组合、窗口度量（period-over-period、累计）。
- 面向 AI 的 agent metadata：synonyms、display names、格式规则。
- 支持从 Tableau / Power BI 文件导入生成 metric view。

## 限制与边界

- 语义锁定在 Databricks 生态内（与 Snowflake Semantic Views 同样的问题）。
- Business Semantics 已宣布开源，实际开源范围待核实。

## 待调研问题

- [ ] Metric View YAML 规范全文（fields/measures/joins/filters 语法）
- [ ] 查询编译机制细节：如何防 fan-out、多级聚合处理
- [ ] 开源部分的实际边界
- [ ] 与 OSI 格式的互转支持

## 参考来源

- [[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]
- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
- 官方文档：docs.databricks.com/aws/en/uc-semantics/metric-views/
