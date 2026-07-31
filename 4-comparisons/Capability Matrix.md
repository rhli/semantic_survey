---
type: comparison
status: draft
dimension: capability-matrix
companies: [databricks, snowflake, dbt-labs, cube, atscale, atlan, aloudata]
created: 2026-07-31
updated: 2026-07-31
---

# Capability Matrix

Tier-1 对象的能力矩阵。客观部分由 Dataview 从产品页 frontmatter 自动聚合（改产品页即改矩阵）；深度评级待逐家深研后手工补充。

## 自动聚合：产品 × 能力

```dataview
TABLE company AS 公司, capabilities AS 能力, ga_status AS 状态
FROM "2-companies"
WHERE type = "product" OR (type = "company" AND capabilities)
SORT company ASC
```

## 按能力反查

```dataviewjs
const caps = ["semantic-modeling","metrics-layer","text2sql","data-catalog","lineage","governance","query-rewrite","acceleration","headless-bi","noetl"];
const pages = dv.pages('"2-companies"').where(p => p.capabilities);
dv.table(["能力", "产品"], caps.map(c => [
  c,
  pages.filter(p => p.capabilities.includes(c)).map(p => p.file.link).array()
]));
```

## 主观评级（待深研后填写）

| 产品 | 语义表达力 | 查询编译 | 加速机制 | AI grounding | 开放性 | 备注 |
|---|---|---|---|---|---|---|
| [[Unity Catalog]] | star/snowflake join、one_to_many、composability、parameters、window（实验性） | 声明式 + 查询时确定性编译；fan-out 靠声明不校验 | 三级降级链；rollup 要求可加且单聚合，one_to_many 全降级 | Metric Views + [[Genie Ontology]] 双层 | 核心实现开源进 Spark；已加入 OSI；Power BI 缺席 | 治理有 ABAC 缺口，物化场景收紧 |
| [[Snowflake Semantic Views]] | | | | | | |
| [[dbt Semantic Layer]] | | | | | | |
| [[Cube]] | | | | | | |
| [[AtScale]] | | | | | | |
| [[Atlan]] | | | | | | |
| [[Aloudata CAN]] | | | | | | |

## 参考来源

见 [[Semantic Layer Vendor Landscape]] 的来源列表。
