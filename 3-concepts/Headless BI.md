---
type: concept
status: draft
aliases: [无头 BI, Semantic API]
---

# Headless BI

## 定义

把语义层作为独立服务，通过开放 API（SQL、REST、GraphQL、JDBC、MCP）向任意下游（BI 工具、应用、embedded analytics、AI agent）暴露一致的数据语义，自身不带可视化界面。

## 为什么重要

- 是独立语义层阵营的存在理由：语义不锁定在任何单一 BI 工具里。
- 2026 年的演进：MCP 成为面向 AI agent 的标准暴露方式，headless 的"消费者"从 BI 工具变成 agent。

## 关键设计维度

- API 广度：Cube 是标杆（SQL/REST/GraphQL/MCP/AI API）；dbt SL 提供 JDBC/GraphQL/MCP
- 兼容旧协议：AtScale 对 Excel/Power BI 讲 MDX/DAX
- 指标服务化：Aloudata CAN 的"开放指标服务"（API/JDBC）是同一形态的国内表述

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "headless-bi") SORT company ASC
```

## 相关概念

[[Semantic Layer]]、[[Metrics Layer]]、[[Text2SQL]]
