---
type: meta
---

# 企业语义层调研

关于 Enterprise Semantic Layer 领域的长期调研知识库。结构与约定见 [[2026-07-31-vault-design]]。

## 入口

- **全景地图**：[[Semantic Layer Vendor Landscape]]（四阵营 + 调研分层）
- **能力矩阵**：[[Capability Matrix]]
- **核心概念**：[[Semantic Layer]] · [[Metrics Layer]] · [[Semantic Model]] · [[Text2SQL]] · [[Context Layer]] · [[Open Semantic Interchange]]

## Tier 1 深研对象

| 公司 | 产品 |
|---|---|
| [[Databricks]] | [[Unity Catalog]] · [[Genie]] |
| [[Snowflake]] | [[Snowflake Semantic Views]] · [[Cortex Analyst]] |
| [[dbt Labs]] | [[dbt Semantic Layer]] |
| [[Cube Dev]] | [[Cube]] |
| [[AtScale]] | （公司即产品） |
| [[Atlan]] | （公司即产品） |
| [[Aloudata]] | [[Aloudata CAN]] |

## 待修订（draft）

```dataview
TABLE type, file.mtime AS 更新时间 FROM "" WHERE status = "draft" AND type != "meta" SORT file.mtime DESC LIMIT 20
```

## 最近更新

```dataview
LIST FROM "" WHERE type != "meta" SORT file.mtime DESC LIMIT 10
```

## 全库统计

```dataview
TABLE length(rows) AS 数量 FROM "" WHERE type GROUP BY type
```
