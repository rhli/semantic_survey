---
type: meta
---

# 企业语义层调研

关于 Enterprise Semantic Layer 领域的长期调研知识库。结构与约定见 [[2026-07-31-vault-design]]。

## 入口

- **全景地图**：[[Semantic Layer Vendor Landscape]]（五阵营 + 调研分层）
- **能力矩阵**：[[Capability Matrix]]
- **核心概念**：[[Semantic Layer]] · [[Metrics Layer]] · [[Semantic Model]] · [[Text2SQL]] · [[Context Layer]] · [[Open Semantic Interchange]]
- **工具参照系**：[[Codegraph]] · [[PageIndex]] · [[OpenViking]]（"给 AI 供上下文"的相邻领域工具）
- **Building blocks**：[[SQLGlot]] · [[OpenLineage]] · [[DataHub]] · [[OpenMetadata]] · [[Kuzu]]（"codegraph for data" 的候选构件：解析层 / 采集层 / 图谱层 / 存储引擎）

## Tier 1 深研对象

| 公司 | 产品 | 深研进度 |
|---|---|---|
| [[Databricks]] | [[Unity Catalog]] · [[Genie]] · [[Genie Ontology]] | ✅ |
| [[Fivetran + dbt Labs]] | [[dbt Semantic Layer]] · [[Agents Schema]] | ✅ |
| [[Snowflake]] | [[Snowflake Semantic Views]] · [[Cortex Analyst]] | ✅ |
| [[Cube Dev]] | [[Cube]] | ✅ |
| [[AtScale]] | （公司即产品） | ✅ |
| [[Atlan]] | （公司即产品） | ✅ |
| [[Aloudata]] | [[Aloudata CAN]] | ✅ |
| [[Google]] | [[Looker]] | ✅ |
| [[Microsoft]] | [[Microsoft Fabric]] | ✅ |

## Tier 2 观察

[[Salesforce]]（Tableau）· [[ThoughtSpot]] · [[Omni]] · [[SuperSonic]]

## 横向对比

```dataview
LIST FROM "4-comparisons" SORT file.name ASC
```

## 洞见

```dataview
LIST FROM "5-insights" SORT date DESC
```

## 待修订（draft）

```dataview
TABLE type, file.mtime AS 更新时间
FROM "" AND !"_templates"
WHERE status = "draft" AND type != "meta"
SORT file.mtime DESC
LIMIT 20
```

## 最近更新

```dataview
LIST
FROM "" AND !"_templates"
WHERE type != "meta" AND type != "archive"
SORT file.mtime DESC
LIMIT 10
```

## 全库统计

```dataview
TABLE length(rows) AS 数量
FROM "" AND !"_templates"
WHERE type
GROUP BY type
```
