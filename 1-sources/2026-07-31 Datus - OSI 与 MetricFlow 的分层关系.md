---
type: source
status: draft
url: https://datus.ai/blog/osi-vs-metricflow/
source_type: blog
publisher: Datus
published: 2026
accessed: 2026-07-31
companies: [fivetran-dbt-labs, snowflake]
topics: ["[[Open Semantic Interchange]]", "[[Semantic Model]]", "[[Metrics Layer]]"]
---

# Datus: OSI vs MetricFlow — 规范层与执行层

## 摘要

厘清 OSI 与 MetricFlow 关系的分析文章。**这篇材料纠正了本库先前从 [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]] 抄来的一个错误表述**（"MetricFlow 是 OSI 的声明式规范基础"）。核心论点：两者不是替代关系，而在栈的不同层——OSI 是**语义契约**，MetricFlow 是**语义运行时**。

## 要点

**核心区分**：

| 维度 | OSI | MetricFlow |
|---|---|---|
| 类别 | 厂商中立的交换标准 | 语义指标引擎（dbt） |
| 主要职责 | 跨工具定义与交换语义元数据 | 把语义定义编译为仓库 SQL |
| 核心产物 | dataset、field、relationship、metric、dialect、extension | semantic model、entity、dimension、measure、metric、time spine、saved query |
| **是否运行时** | **否**——以文件与转换器形式编写和交换 | **是**——构建 dataflow plan 并渲染 SQL |
| 查询接口 | 自身无，依赖消费工具 | CLI、GraphQL、JDBC、Python SDK；BI 集成在 dbt 平台上 |
| 成熟度 | 早期：2025-09 发布，60+ 组织参与，版本仍在 **0.1.x** | dbt 生态内生产级 |
| 主要局限 | 自身不是执行引擎也不是查询 API | 最完整的 API/集成体验依赖 dbt 平台付费档；性能取决于仓库 |

作者认为最重要的一行是"是否运行时"——**一个不执行的标准和一个需要定义才能执行的引擎，是天然的搭档而非对手**。

**衔接点是 `semantic_manifest.json`**：

```
编写语义（OSI JSON 或 dbt semantic YAML）
        ↓
dbt parse / compile  ──►  semantic_manifest.json
        ↓
MetricFlow planner  ──►  dataflow DAG + SQL 生成
        ↓
仓库引擎（Snowflake · BigQuery · Databricks · Redshift · Postgres · Trino）
        ↓
消费接口（CLI · GraphQL · JDBC · Python SDK · BI 集成）
```

**dbt Labs 的官方表述**（作者引述）：MetricFlow **"作为 OSI 倡议的一部分维护"**，且近期 dbt Core 版本**能解析 OSI 语义文档**与原生 dbt 语义并存。所以两者是"对齐"而非"竞争"，但这与"OSI 以 MetricFlow 为规范基础"是不同的说法。

**MetricFlow 的运行时价值主张**（作者列举）：自动 join 选择、**防护 fan-out 与 chasm join**、累计指标的 time-spine 处理、针对支持的仓库实际执行 SQL。

**作者点破的四个常见误解**（都值得记）：

1. **"OSI 是 Snowflake 的产品"**——Snowflake 发起并领导工作组，但规范是 Apache 2.0 且设计上厂商中立，Databricks、Google、AWS、dbt Labs 等 60+ 组织参与。作者的论证很有力：**一个可移植性标准只有竞争平台都采纳才成立，这正是成员如此广泛的原因。**
2. **"采纳 OSI 意味着替换 MetricFlow"**——恰好相反。迁移到 OSI 是导出/导入练习，不是换引擎。
3. **"MetricFlow 需要付费 dbt 平台"**——**这条是本库要修正的关键点**。MetricFlow 是开源的，在 dbt Core 中通过 CLI 即可**编写并本地执行查询**。商业 dbt 平台加的是**托管的 Semantic Layer API**（GraphQL、JDBC、Python SDK）与托管 BI 集成。这些对 metrics-as-API 模式重要，但**不是定义指标或把它编译成 SQL 的前提**。
4. **"可移植 + 可执行的指标就自动是正确的指标"**——两者都不校验定义是否符合业务实际。它们保证定义能travel（OSI）与能跑（MetricFlow），不保证 `net_revenue` 排除了正确的测试账号或在正确日期确认收入。

**作者的开放性判断**：OSI 与 MetricFlow 都解决"语义如何表示与执行"，但**都不解决"语义如何被创建、验证并保持最新"**——一个完美可移植、完美可执行、但六个月前写好再没人碰过的指标，仍然是过时的。作者认为这是数据工程 agent 的目标空间。

## 关联

- 修正了 [[dbt Semantic Layer]] 与 [[Open Semantic Interchange]] 两页的先前表述。
- "MetricFlow 防护 fan-out 与 chasm join" 与 [[Unity Catalog]] 的 `rely.at_most_one_match`（声明但运行时不校验）构成正面架构对照，见 [[Join 正确性的保证方式]]。
- 偏差提示：Datus 卖数据工程 agent，文末"标准与引擎都不解决语义如何保持最新"的落点是自家产品定位。但前面的分层分析与四条误解澄清与 dbt 官方文档、[[2026-07-31 dbt Labs - Agents Schema SPEC]] 的实际内容一致，可采信。
- OSI 版本号存在口径差异：本文称 0.1.x，Agents Schema SPEC 内嵌的 schema 标为 0.2.0.dev0。两者时间点可能不同，均为 pre-1.0。
