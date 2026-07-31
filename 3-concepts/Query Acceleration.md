---
type: concept
status: draft
aliases: [查询加速, 物化加速, Pre-aggregation, Aggregate Management]
---

# Query Acceleration

## 定义

语义层为保障查询性能采用的物化、缓存、预计算机制，以及查询自动改写命中这些物化结果的能力。

## 为什么重要

语义层承诺"任意维度动态查询"，但直接对明细算会慢/贵。加速机制是"灵活性"和"性能"矛盾的解法，也是各家技术差异最大的地方之一（业界宣称可降仓成本 30–70%）。

## 关键设计维度（三种代表性解法）

1. **智能聚合自动管理**：系统观察查询模式自动创建/维护聚合表。代表：AtScale（autonomous aggregates）。
2. **声明式 pre-aggregation**：开发者配置物化，查询规划器自动命中。代表：Cube。
3. **自动物化 + 改写**：按常用维度指标配置明细/汇总/预计算物化，查询自动改写命中。代表：Aloudata CAN；Databricks Metric Views 借力 materialized views 自动改写。

其他维度：物化的增量刷新、缓存失效策略、语义缓存（识别不同表述的同义查询）。

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "acceleration") SORT company ASC
```

## 相关概念

[[Query Rewrite]]、[[NoETL]]、[[Metrics Layer]]
