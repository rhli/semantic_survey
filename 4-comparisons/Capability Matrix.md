---
type: comparison
status: draft
dimension: capability-matrix
companies: [databricks, snowflake, fivetran-dbt-labs, cube, atscale, atlan, aloudata]
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
| [[dbt Semantic Layer]] | 实体四类型 + 自动 join 路径；5 类指标（simple/ratio/derived/cumulative/**conversion**）；time spine | dataflow DAG 编译，跨 7 种仓库生成 SQL；**主动防护 fan-out 与 chasm join**，歧义告警 | saved query → export → 两层缓存；声明式缓存对已配置维度的筛选仍命中，上游新鲜即自动失效；需 Enterprise | MetricFlow 定义 + MCP server；同厂商另有 [[Agents Schema]] | MetricFlow 与 Fusion 均 Apache 2.0；OSI 参与方且自家上下文标准以 OSI 为 canonical | 不拥有执行层——治理与性能取决于底层仓库；按 queried metrics 计费 |
| [[Agents Schema]] | 不自产语义，只分发；OSI 为 canonical 格式 | 无——只做发现层，agent 拿到上下文后自由写 SQL | 不适用 | 裸 SQL 读 `AGENTS.*`，MCP 可选；含 skills 分发 | 开源标准，客户自有，跨仓（Snowflake/Databricks/BigQuery） | **元数据对全体查询者可读**；批量快照非活图谱 |
| [[Snowflake Semantic Views]] | | | | | | |
| [[Cube]] | | | | | | |
| [[AtScale]] | | | | | | |
| [[Atlan]] | | | | | | |
| [[Aloudata CAN]] | | | | | | |

## 按维度的深入对比

矩阵只能给出并列条目，真正的分歧在下面这些页里：

- [[Join 正确性的保证方式]]——引擎防护 vs 建模者声明，两种相反的责任归属
- [[上下文层的四条路线]]——上下文存在哪、谁拥有、冲突如何仲裁

## 参考来源

见 [[Semantic Layer Vendor Landscape]] 的来源列表。
