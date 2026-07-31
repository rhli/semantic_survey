---
type: concept
status: draft
aliases: [血缘, 数据血缘]
---

# Data Lineage

## 定义

数据从源到消费的加工链路追踪，粒度可到表级、字段级、指标级。

## 为什么重要

- 语义层场景下的特殊价值：指标血缘（这个 KPI 由哪些字段、经什么逻辑算出）是口径可信的基础。
- AI agent 场景：血缘是答案可解释、可审计的前提（Atlan 把 lineage 列为 context layer 五组件之一）。

## 关键设计维度

- 粒度：表级 / 字段级（Aloudata BIG、CAN 的字段级指标血缘）/ 语义对象级
- 获取方式：解析 SQL 日志 vs 声明式定义天然自带（语义层定义即血缘）
- 跨系统血缘：单平台内 vs 跨仓跨 BI 工具

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "lineage") SORT company ASC
```

## 相关概念

[[Data Catalog]]、[[Data Governance]]、[[Metrics Layer]]
