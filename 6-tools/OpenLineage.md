---
type: tool
status: draft
tool: OpenLineage
category: lineage-standard
license: Apache-2.0
url: https://openlineage.io
---

# OpenLineage

## 定位

血缘元数据的**开放标准与事件 API**（LF AI & Data），Marquez 是其参考实现。解决的是"血缘如何跨工具流动"：生产侧（Airflow、Spark、Flink、dbt、Dagster）以统一事件格式吐出 dataset/job/run 元数据，消费侧（Marquez、DataHub、OpenMetadata、Atlan）各自存储与展示。

## 核心机制

- **事件模型**：run 生命周期事件（START/COMPLETE/FAIL）携带输入输出 dataset、schema facet、列级血缘 facet。
- **推送式**：血缘在作业执行时实时产生并推送，不依赖事后扫描——这是它与"解析 SQL 反推血缘"（[[SQLGlot]] 路线）的本质区别。
- Marquez 提供 OpenLineage 兼容的 HTTP 端点 + 可视化血缘图 + Lineage API（可做回填自动化、根因分析）。

## 对 "codegraph for data" 的意义

**采集层标准**。要维护一张鲜活的数据结构图，最大的工程问题是"图怎么跟上变化"。两条原料路线互补：

| 路线 | 原料 | 粒度 | 覆盖 |
|---|---|---|---|
| 解析（SQLGlot） | 视图/模型/查询文本 | 列级，含未执行的代码 | 只覆盖看得见的 SQL |
| 事件（OpenLineage） | 运行时执行事件 | 列级 facet + run 元数据 | 只覆盖接入的引擎 |

现实的 "for data" 系统两个都要：解析给出**声明的**结构（应该是什么），事件给出**实际的**结构（发生了什么）——两者的 diff 本身就是数据质量信号。这对应 codegraph 的 staleness banner：图的新鲜度必须是一等公民。

## 边界

- 只覆盖接入了 producer 集成的引擎；自研调度、存储过程、手工 SQL 是盲区。
- 标准是"血缘事件"，不含业务语义（指标口径、术语）——那是 [[Open Semantic Interchange]] 的层。

## 参考来源

- [[2026-08-02 SQLGlot 与开源血缘生态]]
