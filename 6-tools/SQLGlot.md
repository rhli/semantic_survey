---
type: tool
status: draft
tool: SQLGlot
category: sql-parser
license: MIT
url: https://github.com/tobymao/sqlglot
---

# SQLGlot

## 定位

零依赖的纯 Python SQL 解析器 / transpiler / 优化器，支持 30+ 方言。**"理解 SQL 文本"这个需求的开源事实标准**——SQLMesh、Apache Superset、Dagster、dlt、Ibis 都建在它的 AST 上。Tobiko Data 出品（该公司 2025-09 被 Fivetran 收购，见 [[Fivetran + dbt Labs]]）。

## 核心机制

- **解析为 AST，而非正则**：每个方言有完整 tokenizer/parser/generator，转译保证语法与语义双正确。
- **优化器即分析器**：optimizer 模块做列限定（qualify）、作用域构建，这是列级血缘的基础。
- **列级血缘**：`sqlglot.lineage.lineage(column, sql, schema, sources)` 在查询 DAG 上自顶向下追踪一个列的来源链（`root.col -> intermediate.col -> cte.col`），CTE/子查询自动展开。
- 附带一个实验性执行引擎，可在 Python 对象上直接跑 SQL（用于单测）。

## 对 "codegraph for data" 的意义

**解析层的不二之选**。codegraph 之于代码的第一步是把源文件解析成符号与边；数据域的对应物是把 SQL（仓库里的视图、dbt 模型、查询日志、BI 报表定义）解析成表/列依赖图。SQLGlot 恰好提供这三样原料的统一解析：方言覆盖决定你能吃多少企业的真实 SQL 存量，列级血缘决定图的边能细到什么粒度。

它与 [[Codegraph]] 的分工类比：codegraph 的索引器 ≡ SQLGlot 解析 + 血缘提取；SQLite 图存储两边可以一样。差异在增量维护——codegraph 靠文件 watcher，数据域要靠查询日志流与 [[OpenLineage]] 事件。

## 边界

- 血缘假设查询构成 DAG（无循环引用）；动态 SQL、存储过程、UDF 内部逻辑不可见。
- 纯 Python，解析快但不是为海量日志流设计的吞吐量；生产管线通常只用它解析**定义**（视图/模型），不解析每条执行记录。

## 参考来源

- [[2026-08-02 SQLGlot 与开源血缘生态]]
