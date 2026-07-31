---
type: source
status: draft
url: https://docs.can.aloudata.com/产品简介.html
source_type: docs
publisher: Aloudata
published:
accessed: 2026-07-31
companies: [aloudata]
topics: ["[[Metrics Layer]]", "[[NoETL]]", "[[Query Acceleration]]"]
---

# Aloudata: Aloudata CAN 产品简介（官方文档）

## 摘要

Aloudata CAN 定位为 NoETL 自动化指标平台：通过配置化指标定义 + 自动物化加速，IT 不需要重复开发宽表和汇总表，实现指标"定义即治理、定义即开发、定义即消费、定义即沉淀"。

## 要点

- 架构位置：数仓架构中的独立一层，位于数据开发平台之上、下游应用之下，将物理层与语义层解耦。
- 核心差异点是"NoETL 明细级语义层"：无需预构建宽表，支持任意粒度/维度动态下钻至明细；查询性能靠智能物化加速保障（按常用维度指标配置明细/汇总/预计算物化，查询自动改写命中）。
- 指标全生命周期：规范化定义（自动"同名不同义、同义不同名"判重）、自动化生产、语义化目录、开放服务（API/JDBC）。
- 字段级指标血缘 + 指标多版本。
- 产品线全景（官网）：CAN（指标平台）、AIR（逻辑数据编织 / Data Fabric）、BIG（数据血缘）。
- 客户背书：麦当劳中国数据中台等。

## 关联

- [[Aloudata CAN]] 产品页的核心一手来源。
- NoETL / 明细级语义层与 AtScale 的聚合管理、Cube 的 pre-aggregation 是同一问题（[[Query Acceleration]]）的不同解法，值得专门对比。
