---
type: concept
status: draft
aliases: [OSI, Apache Ossie, 开放语义交换]
---

# Open Semantic Interchange

## 定义

厂商中立的语义定义交换标准（YAML 格式），2026 年 1 月正式发布，以 MetricFlow 为声明式规范基础，进入 Apache 孵化（代号 "Ossie"）。发起方包括 Snowflake、dbt Labs、Salesforce，Databricks、Cube、AtScale 等 40+ 厂商参与。

## 为什么重要

- 回应"语义碎片化"：2026 年每家大厂各有一套语义层，同一企业可能维护 5+ 套互不兼容的 revenue 定义。OSI 提供第三条路：厂商中立定义，任何平台消费。
- Agent 时代的深层动因：agent 间协作需要可发现、可引用的标准化语义定义。
- 判断厂商开放性的试金石：跟踪各家的 OSI 适配进度（dbt converter 已合入；Cube、AtScale 为 working group 成员）。

## 关键跟踪点

- [ ] 规范本身的表达力边界（哪些语义定义无法交换）
- [ ] 各 Tier-1 厂商的适配进度与真实互操作演示
- [ ] 与 MCP 的关系（定义交换 vs 运行时访问）

## 相关概念

[[Semantic Layer]]、[[Semantic Model]]、[[Metrics Layer]]

## 参考来源

- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
