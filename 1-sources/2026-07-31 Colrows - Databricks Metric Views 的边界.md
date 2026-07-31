---
type: source
status: draft
url: https://colrows.com/blogs/databricks-metric-views/
source_type: blog
publisher: Colrows（竞品厂商）
published: 2026
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Metrics Layer]]", "[[Query Rewrite]]"]
---

# Colrows: Databricks Metric Views 的边界（竞品视角）

## 摘要

竞品厂商对 Metric Views 的批评性分析。核心论点：Metric View 是"治理的指标定义"，不是"跨仓的编译期执行层"——定义与执行是两件事。

## 要点

- 承认部分：对 Databricks 上的治理指标是"实实在在、值得欢迎的一步"，开源规范是"真正的加分项"。
- **批评一：Databricks-scoped**。Metric View 活在 Unity Catalog 里、描述 lakehouse 数据；跨仓问题需要先把数据搬进 Databricks。
- **批评二：定义 ≠ 执行**（核心论点）。Metric View 标准化了指标"是什么意思"，但把任意问题变成治理的、确定性的 SQL 是**消费方的职责**；而主要的 AI 消费方 Genie 既局限于 Databricks，又是 LLM 驱动的（即概率性的）。
- **批评三：仍需人工编写与维护**。模型变化时得有人更新定义。
- 补充的功能限制（第三方整理，需核实）：
  - measure 表达式内不支持原生 SQL 窗口函数，必须用 YAML 的声明式 `window` 块。
  - 不能在查询时 join，所有 join 逻辑必须在定义中声明。
  - **Power BI 不原生支持 Metric Views**：微软 2026 年初移除 BI Compatibility Mode，要求指标定义在 Power BI/Fabric 语义模型内——两个平台是竞争而非互操作关系。
- 实践建议（第三方经验）：用 `ALTER VIEW` 而非 `CREATE OR REPLACE`，避免清掉 Unity Catalog 权限；先定义原子 measure，再用 `MEASURE()` 组合出复杂 KPI；物化只用在真正昂贵的操作上，以控制维护开销与数据陈旧。

## 关联

- [[Unity Catalog]] 的"限制与边界"章节的主要第三方依据。
- "定义 ≠ 执行"的批评直指 [[2026-07-31 Databricks 的双层语义架构]]中概率性外壳那一层的软肋。
- 偏差提示：Colrows 是卖"语义执行层"的竞品厂商，全文导向自家产品，批评方向有商业动机；但 Power BI 缺席、必须预声明 join 这些事实性限制可独立验证。
