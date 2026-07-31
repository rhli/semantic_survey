---
type: concept
status: draft
aliases: [数据目录, 元数据目录]
---

# Data Catalog

## 定义

对数据资产（表、视图、dashboard、模型、指标）的编目、搜索与管理系统，核心是元数据：schema、ownership、描述、标签、质量信号。

## 为什么重要

Catalog 回答"数据在哪、归谁、能不能用"；语义层回答"业务概念怎么算"。两者互补，且边界正在融合：Unity Catalog 把指标做成 catalog 对象，Atlan 从 catalog 出发摄取语义定义（见 [[Context Layer]]）。

## 关键设计维度

- 覆盖范围：单平台（Unity Catalog、Snowflake Horizon）vs 跨平台（Atlan、Alation、Collibra、开源 DataHub/OpenMetadata）
- 主动元数据（active metadata）：使用模式、查询历史作为元数据来源
- 对 AI 的暴露：MCP server、business glossary 与语义定义的绑定

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "data-catalog") SORT company ASC
```

## 相关概念

[[Context Layer]]、[[Data Governance]]、[[Data Lineage]]
