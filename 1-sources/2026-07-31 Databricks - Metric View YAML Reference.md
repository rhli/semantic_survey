---
type: source
status: draft
url: https://docs.databricks.com/aws/en/uc-semantics/metric-views/yaml-reference
source_type: docs
publisher: Databricks
published: 2026-07-24
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Semantic Model]]", "[[Metrics Layer]]", "[[Query Rewrite]]"]
---

# Databricks: Metric View YAML 语法参考（官方文档）

## 摘要

Metric View YAML 规范的完整语法参考。顶层字段：`version`、`comment`、`source`、`parameters`、`filter`、`joins`、`fields`、`measures`、`materialization`。是理解 Databricks 语义模型表达力边界的一手材料。

## 要点

- **source 可以是 metric view 本身**，构成 composability（可组合性）；也可以直接写 SQL 查询文本。
- **fields / measures 二分**：fields 是查询时可用于 `SELECT`/`WHERE`/`GROUP BY` 的标量列（分类列或未聚合数值列）；measures 必须用聚合函数表达，查询时须通过 `MEASURE()` 函数引用。measures 可引用先前定义的 fields 或 measures。
- `dimensions` 是 `fields` 的向后兼容同义词；Catalog Explorer 低代码编辑器界面叫 Fields，但生成的 YAML 用 `dimensions`。
- **joins**：star schema（`LEFT OUTER JOIN`，只 join 本次查询需要的表）；snowflake schema 靠嵌套 `joins`；`on` 或 `using` 二选一。`source` 前缀指源表，join 名前缀指被连接表，无前缀默认指被连接表。
- **cardinality**：默认 `many_to_one`（维度查找，多对多时取第一条匹配行）；`one_to_many` 把被连接表当作独立事实源在源粒度聚合（需 DBR 18.1 + YAML 1.1）。one_to_many 列不能用于 fields；单个聚合函数只能引用一个源；其所有后代 join 必须也是 one_to_many。
- **rely.at_most_one_match**：向优化器声明 join 不 fan-out 以生成更高效计划。文档明确警告**运行时不校验**，声明错误会导致 `SUM`/`COUNT` 静默返回错误结果。
- **parameters**：把 metric view 当表值函数调用，查询时绑定值（含 `data_type`、可选 `default`）。定义了 parameters 的 metric view 不能物化。
- **面向 AI/BI 的语义元数据**（需 YAML 1.1）：`display_name`（≤255 字符）、`format`、`synonyms`（最多 10 个，各 ≤255 字符）、`comment`。注意 YAML 注释（`#`）与 Unity Catalog comment 是两套东西，`ALTER VIEW` 会清除未写入 YAML `comment` 字段的 UC comment。
- **window measures（标注 Experimental）**：`order`（须确定性字段）、`range`、`semiadditive`（`first`/`last`）、`offset`（如 `-12 month`，需 DBR 18.1 + YAML 1.1）。range 取值：`current`、`cumulative`、`trailing/leading N unit [inclusive|exclusive]`（默认 exclusive）、`all`。
- **wildcard 批量导入**（DBR 18.2 + YAML 1.1）：`source.*`、`<join>.*`、`.* EXCEPT (...)`、STRUCT 展开。wildcard 上不能加 name 和任何语义元数据；创建时展开，源表新增列需 `CREATE OR REPLACE` 或 `ALTER` 才能纳入。
- **materialization**：`mode` 必须为 `relaxed`；`materialized_views` 分 `aggregated`（需 dimensions/measures）与 `unaggregated`（每个 metric view 只允许一个）；支持 `cluster_by`（可 `auto: true`）、`partition_by`、`schedule`（不支持 `TRIGGER ON UPDATE`）。materialization 块内必须用 `dimensions:` 关键字，即使顶层用 `fields:`。
- 坑点：string 型 field 一律为 `STRING`，源列是 `CHAR(n)` 时空格填充丢失，导致比较结果与源表不一致。

## 关联

- [[Unity Catalog]] 产品页的语义模型表达力部分主要依据本文档。
- `rely` 不校验、`fields/measures` 二分、composability 这几点在与 [[dbt Semantic Layer]]、[[Cube]] 对比时是关键差异点。
