---
type: concept
status: draft
aliases: [指标层, 指标平台, Metric Store, 指标中台]
---

# Metrics Layer

## 定义

语义层中专注于指标（KPI）的部分：指标的定义、口径管理、版本、血缘和对外服务。国内市场常以"指标平台/指标中台"作为独立产品形态。

## 为什么重要

指标口径不一致是语义层最原始、最普遍的痛点；"定义一次、任意维度查询"（define once, query anywhere）是各家共同卖点。

## 关键设计维度

- 指标类型表达力：简单聚合 / 比率 / 派生指标 / 窗口度量（period-over-period、累计）
- 与维度的解耦：定义时不锁定 grouping，查询时动态选择维度（Databricks Metric Views、Snowflake Semantic Views 的核心机制）
- 指标治理：判重（同名不同义/同义不同名）、审批、版本（Aloudata CAN 强调）
- 服务方式：SQL 对象 vs API（见 [[Headless BI]]）

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "metrics-layer") SORT company ASC
```

## 相关概念

[[Semantic Model]]、[[Query Acceleration]]、[[Data Lineage]]
