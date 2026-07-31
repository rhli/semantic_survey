---
type: concept
status: draft
aliases: [查询改写, 语义编译, Query Compilation]
---

# Query Rewrite

## 定义

把对语义对象的查询（指标 + 维度）编译/改写为在物理数据上执行的 SQL 的过程。语义层的"编译器"，决定正确性和性能。

## 为什么重要

这是语义层最核心的技术机制：声明式定义之所以能"定义一次、任意维度查询"，靠的是查询时确定性编译。也是防 fan-out、正确处理多级聚合（如 distinct count）的关键。

## 关键设计维度

- 编译目标：单一引擎方言（平台原生）vs 多引擎方言（MetricFlow 的 dataflow plan → engine-specific SQL；AtScale 的 inbound MDX/DAX/SQL → outbound 仓 SQL）
- 改写时机与协同：是否自动改写命中物化结果（与 [[Query Acceleration]] 耦合）
- 联邦执行：跨源查询下推（Denodo、AtScale、Aloudata AIR 的虚拟化路线）
- 正确性保障：join 基数处理、fan-out 防护、指标可组合性

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "query-rewrite") SORT company ASC
```

## 相关概念

[[Semantic Model]]、[[Query Acceleration]]、[[Headless BI]]
