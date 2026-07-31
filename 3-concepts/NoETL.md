---
type: concept
status: draft
aliases: [逻辑数据编织, Data Fabric, 数据虚拟化, Data Virtualization]
---

# NoETL

## 定义

不物理搬运/复制数据，通过逻辑层（虚拟化、联邦查询、自动物化）直接在源数据或明细数据上提供分析能力的路线。Aloudata 的品牌化表述，国际上对应 data virtualization / logical data fabric。

## 为什么重要

传统指标平台需要预先建宽表/汇总表，开发周期长、灵活性差、资产爆炸。NoETL 路线主张"明细级语义层"：免宽表、任意粒度动态下钻，性能靠自动物化兜底——把 ETL 工作从人工转为系统自动代持。

## 关键设计维度

- 虚拟化程度：纯虚拟（查询全部下推）vs 混合（明细虚拟 + 热点自动物化）
- 跨源联邦：单仓内逻辑层 vs 跨异构源（Denodo、Aloudata AIR）
- 与 [[Query Acceleration]] 的耦合：NoETL 的可行性完全依赖自动物化和改写命中的质量

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "noetl") SORT company ASC
```

## 相关概念

[[Query Acceleration]]、[[Query Rewrite]]、[[Metrics Layer]]
