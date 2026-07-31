---
type: product
status: draft
company: fivetran-dbt-labs
product: dbt Semantic Layer (MetricFlow)
capabilities: [semantic-modeling, metrics-layer, query-rewrite, acceleration, headless-bi]
ga_status: ga
---

# dbt Semantic Layer

## 定位与解决的问题

**metrics-as-code** 路线的代表，也是这条路线的定义者。指标、维度、实体定义在 YAML 中，与 dbt transformation 模型同仓，走 Git / PR / CI 流程。解决的是"指标定义与数据加工逻辑脱节"——BI 工具里的指标口径与仓库里的建模逻辑分属两个系统、两套版本控制、两批责任人。

与平台原生方案（[[Unity Catalog]]、[[Snowflake Semantic Views]]）的根本区别在于**它不拥有执行层**。这既是最大卖点（跨 Snowflake、BigQuery、Databricks、Redshift、Postgres、Trino、DuckDB 一套定义），也是最大约束（治理与性能最终取决于底层仓库）。

## 核心概念与架构

### MetricFlow：编译链

MetricFlow（开源，Apache 2.0）是语义编译引擎。名字来自它的实现方式——**指标请求被编译为基于 dataflow 的查询计划，再优化并翻译为引擎特定 SQL**。

```
编写语义（dbt semantic YAML 或 OSI JSON）
        ↓
dbt parse / compile  ──►  semantic_manifest.json
        ↓
MetricEvaluationPlan（指标依赖图）
        ↓
DataflowPlanBuilder  ──►  dataflow DAG
        ↓
优化（谓词下推、减少 full outer join）
        ↓
引擎特定 SQL
        ↓
消费接口（CLI · GraphQL · JDBC · Python SDK · MCP · BI 集成）
```

`semantic_manifest.json` 是**编写与执行的衔接点**，也是 OSI 定义能接入的地方（dbt Core 可解析 OSI 文档与原生语义并存）。

单次查询的处理步骤：解析指标请求（哪个指标、哪些维度、什么时间范围、什么筛选）→ 解析语义模型引用（读 `node_relation` 定位物理表，加载维度与实体）→ **走实体图找有效 join 路径**（路径歧义时告警）→ 生成目标引擎的优化 SQL（处理方言差异，如 Snowflake 的 `DATE_TRUNC` vs BigQuery 的 `TIMESTAMP_TRUNC`）。

`MetricEvaluationPlan` 是 2026 年 3 月引入的结构（metricflow #1984、#1989）。此前多指标查询与派生指标都被处理为各子查询的 **full outer join**；引入依赖图后，`DataflowPlanBuilder` 跟随求值计划的依赖而非递归跟随指标定义，从而允许接入不同的 planner——一个沿用既有逻辑，一个专门优化以减少 full outer join 数量。说明 join 数量爆炸是这个引擎真实的性能瓶颈所在，且仍在积极优化中。

### 三类构件

**Entity（实体）**——数据粒度的标识，也是自动 join 的依据。四种类型，区分比一般理解的"主键"更细：

| 类型 | 语义 |
|---|---|
| `primary` | 每行一条记录，且包含数据平台中的**全部**记录 |
| `unique` | 每行一条记录，但可能只是全体的**子集**，允许 null |
| `foreign` | 同一记录可以有零条、一条或多条实例，允许 null |
| `natural` | 基于真实世界数据唯一标识记录的列或列组合 |

**Dimension（维度）**——指标的 group by 参数。`categorical` 或 `time`（带 `time_granularity`）。文档标注对 SCD（缓慢变化维）**目前存在限制**。

**Metric（指标）**——五种类型：

| 类型 | 关键属性 | 用途 |
|---|---|---|
| `simple` | `agg`、`expr`、`time_granularity`、`agg_time_dimension`、`join_to_timespine`、`fill_nulls_with`、`non_additive_dimension` | 基础聚合；`non_additive_dimension` 处理半可加度量 |
| `ratio` | `numerator`、`denominator`（各可为指标名或含 `name`/`filter`/`alias` 的字典） | 转化率、占比 |
| `derived` | `expr`、`input_metrics`（每个可带 `alias`、`filter`、`offset_window`） | 指标间算术；`offset_window` 做同比环比 |
| `cumulative` | `input_metric`、`window`、`grain_to_date`、`period_agg` | 滚动窗口、MTD/YTD |
| `conversion` | `entity`、`calculation`、`base_metric`、`conversion_metric`、`window`、`constant_properties` | 漏斗分析（如 view-to-purchase）；`constant_properties` 确保两事件在某维度取值相同 |

`conversion` 与 `cumulative` 的 `grain_to_date` / `period_agg` 是这套规范相对 [[Unity Catalog]] Metric Views（measures + 实验性 `window` 块）的明显表达力优势——漏斗分析在 Databricks 侧需要在 measure 表达式里自己写。

## 语义模型的表达方式

### 规范正在换代（引用时必须注意版本）

**新版（dbt Core 1.12+）**：`semantic_model` 内联在 model 下，`metrics` 是与 `semantic_model`、`columns` **并列的顶层列表**（不嵌套在 `semantic_model` 里），类型参数提到每个 metric 的顶层：

```yaml
models:
  - name: orders
    semantic_model:
      agg_time_dimension: order_date
      entities:
        - name: order
          type: primary
          expr: order_id
        - name: customer
          type: foreign
          expr: customer_id
      dimensions:
        - name: order_date
          type: time
          type_params:
            time_granularity: day
        - name: status
          type: categorical
    metrics:
      - name: order_count
        type: simple
        agg: count
        expr: 1
      - name: conversion_rate
        type: ratio
        numerator: orders
        denominator: sessions
```

**旧版**：顶层 `semantic_models:` 块 + 独立 `measures:` 列表，指标通过 `type_params` 引用 measure：

```yaml
semantic_models:
  - name: transactions
    model: ref('fact_transactions')
    defaults:
      agg_time_dimension: transaction_date
    entities:
      - name: transaction
        type: primary
        expr: transaction_id
    measures:
      - name: transaction_total
        agg: sum

metrics:
  - name: revenue
    type: simple
    type_params:
      measure: transaction_total
  - name: sales_minus_revenue
    type: derived
    type_params:
      expr: sales - revenue
      metrics: [sales, revenue]
```

两处变化的实质：（1）语义定义与 dbt model 从"并列的两个文件"变成"同一个 model 的一个 section"，进一步贴合 metrics-as-code；（2）**`measures` 这个中间概念被消解**——simple metric 直接带 `agg` 与 `expr`，少一层间接。副作用是 2026 年前的 dbt 语义层教材大面积过时。

一个 dbt model 可定义多个语义模型。

### 与 OSI 的关系（本库先前表述有误，已修正）

先前记的"MetricFlow 是 OSI 的声明式规范基础"是从二手材料抄来的过度表述。准确的分层是：

- **[[Open Semantic Interchange]] 是规范层**——厂商中立的交换标准（JSON/YAML），定义 dataset、field、relationship、metric、dialect、extension。**不是运行时**，自身无查询接口。
- **MetricFlow 是执行层**——消费语义定义、构建语义图、通过内部 dataflow DAG 规划查询、产出仓库 SQL。

dbt Labs 的官方表述是 MetricFlow **"作为 OSI 倡议的一部分维护"**，且近期 dbt Core 版本能解析 OSI 文档。二者对齐但不是同一层，也不是"OSI 以 MetricFlow 为规范"。

一个侧面印证：dbt 自家的 [[Agents Schema]] 规范把 **OSI 定为 canonical 语义层格式**，MetricFlow YAML 不在其上下文分发路径上。

### 创作辅助

**dbt Wizard** 可生成语义模型，在 dbt 平台或本地 CLI 均可。dbt Labs 另发布了 agent skills 仓库（`dbt-labs/dbt-agent-skills`），含 `building-dbt-semantic-layer` skill——用 agent 辅助语义建模已是官方支持路径，与 Databricks 的 Genie Code 是同类动作。

## 能力细节

### 正确性：主动防护 fan-out 与 chasm join

这是 MetricFlow 相对竞品最硬的一处架构差异。它靠实体图**自动选择 join 路径**，并主动防护 fan-out 与 chasm join，路径歧义时告警；累计指标由引擎处理 time-spine。

对照 [[Unity Catalog]]：Databricks 的 join 必须预先声明，基数靠 `rely.at_most_one_match` 声明且**运行时不校验**——声明错了 `SUM`/`COUNT` 静默返回错误结果。两家在"正确性由谁负责"上的选择相反。详见 [[Join 正确性的保证方式]]。

需要限定的是：MetricFlow 保证的是**聚合的结构正确性**，不保证定义符合业务实际。它不会告诉你 `net_revenue` 是否排除了正确的测试账号、是否在正确日期确认收入。

### acceleration：saved queries → exports → 两层缓存

三者是递进依赖关系，不是三个独立功能。

**saved queries** 是地基：把逻辑相关的指标、维度、筛选组合成命名查询。关键性质是**它是 dbt DAG 中的节点**，在血缘中可见——语义层查询因此进入了 dbt 既有的依赖与编排体系。可在 `dbt_project.yml` 用 `saved-queries` 做项目级配置。

**exports** 在 saved query 上追加配置，定义写出的 schema 与表名，由 dbt job scheduler 执行为表或视图。官方意图值得记：**让指标逻辑集中在 dbt 而非变成数仓里的一个视图**。实践上它是"BI 工具连不上语义层 API"场景的退路——先物化，再让可视化工具连数仓。

**两层缓存**：

| 层 | 机制 |
|---|---|
| result cache | 借用数据平台自身内建的缓存层 |
| declarative cache | 用 saved query 配置**预热**，需预判要缓存哪些查询 |

```yaml
saved-queries:
  my_saved_query:
    +cache:
      enabled: true
```

声明式缓存的关键细节：
- **必须已定义 export 才能缓存**（`cache: enabled: true` 单独无效）。
- **对已在 saved query 配置中的维度加筛选，仍然命中缓存**——不必为每个筛选组合单独建缓存。这一条决定了自动加速的真实覆盖面，是与 Databricks 物化的 rollup match 对位的机制。
- **检测到相关上游模型有新鲜数据时自动失效**，下次运行 saved query 时刷新或重建。这比 Databricks 的"按 schedule 刷新、命中物化的查询读上次刷新结果"在新鲜度语义上更清晰。

数据驻留：缓存**用客户自己数仓的存储**，不在 dbt Labs 系统上持久保留，且是客户 opt-in。

### headless-bi：消费接口

CLI、GraphQL API、JDBC、Python SDK、**dbt MCP server**（需 PAT 或 service token + 生产环境 ID）。托管 BI 集成含 Tableau、Excel、Hex、Mode。

## 商业模式与开源边界

**"没有 dbt Cloud 就用不了语义层"是不准确的**，本库先前照抄了竞品口径。准确的边界：

| 能力 | dbt Core（开源） | dbt 平台（付费） |
|---|---|---|
| 定义指标与语义模型 | ✅ | ✅ |
| 编译为 SQL 并本地执行查询（CLI） | ✅ | ✅ |
| 托管 Semantic Layer API（GraphQL / JDBC / Python SDK） | ❌ | ✅ |
| 托管 BI 集成、job scheduler 驱动的 exports | ❌ | ✅ |
| 两层缓存 | ❌ | Enterprise / Enterprise+ |

真实的商业边界是 **metrics-as-API 与托管能力**，不是指标定义本身。

**计费按 "Queried Metrics" 计量**：每次成功的 compute/render 请求**按请求中的指标数**计数；元数据调用不计费，开发环境（IDE）运行不计费。另一维度是 Successful Models Built（部署环境运行，按模型逐个计）。

| 档 | 价格 | 语义层相关额度 |
|---|---|---|
| Developer | 免费 | 1 seat、1 project、3,000 models/月 |
| Starter | $100/user/月 | ≤5 seats、15,000 models/月、**5,000 queried metrics/月** |
| Business | 约 $1,200/developer/月（第三方基准，非官方标价） | SSO、RBAC、审计日志、高级 CI、语义层 |
| Enterprise | 议价 | 100,000 models/月、**20,000 queried metrics/月**、30 projects |
| Enterprise+ | 议价 | 不限 project + PrivateLink / IP 限制 |

这与平台原生方案在商业模式上处境根本不同：独立语义层必须为语义层本身定价，平台原生方案（[[Unity Catalog]] 的 Metric Views 无独立语义层计费）可以把它当作留住计算负载的赠品。第三方基准报告直接建议"若语义层采纳是战略性的，现在就谈额度豁免"。

## 限制与边界

- **不拥有执行层**：治理（RLS、列掩码、ABAC）与性能取决于底层仓库，无法像平台原生方案那样继承单一套策略。这是跨平台中立的代价。
- **消费型定价的预算不确定性**：queried metrics 与 successful models 两个计量维度叠加，且仓库计算另计。第三方称五人团队"很容易达到五位数年度总额"（[[2026-07-31 Peliqan - dbt 替代方案与锁定担忧]]，有商业动机，需打折）。
- **learning curve**：YAML 语义建模的概念门槛（entity 四类型、agg_time_dimension、time spine）高于 BI 拖拽式，需要专职 analytics engineer。
- **规范换代造成的资料断层**：新旧两套 YAML 写法并存，网上教程大面积过时。
- **join 数量是真实性能瓶颈**：多指标与派生指标查询历史上编译为 full outer join，2026 年仍在优化中。
- **缓存需 Enterprise/Enterprise+**，且环境必须在 release track 上（不能是 legacy dbt Core 版本）。
- **不解决"语义如何保持最新"**：可移植且可执行的指标，若六个月无人维护仍然是过时的。这是标准与引擎共同的空白（Datus 观点，虽服务其商业定位但判断成立）。

## 待调研问题

- [ ] 新旧 YAML 规范的迁移路径与是否有自动转换工具；旧规范的弃用时间线
- [ ] MetricFlow 防护 chasm join 的具体机制（源码可读）：与 Cube、AtScale 的处理方式对比
- [ ] full outer join 优化的实际效果：DFS planner 落地后的性能改善数据
- [ ] dbt Core 能解析 OSI 文档的**版本门槛与表达力覆盖度**（哪些 OSI 构件无法映射到 MetricFlow）
- [ ] declarative cache 的成本模型：缓存表的存储与刷新开销如何评估
- [ ] Fusion 引擎（dbt Core v2.0）与语义层的交互：Fusion 是否改变 MetricFlow 的编译路径
- [ ] dbt Wizard 生成语义模型的质量与人工审核工作量（对位 Genie Code、Snowflake Autopilot）
- [ ] MetricFlow 与 [[Agents Schema]] 的关系：MetricFlow 定义能否（以及如何）经 OSI 转换进 `AGENTS.OSI_*`
- [ ] 语义层是否含在 Business 档（官方 FAQ 与第三方基准口径冲突，见来源笔记）

## 参考来源

- [[2026-07-31 dbt - 语义模型与指标规范]]（官方规范）
- [[2026-07-31 dbt - 语义层服务能力与计费]]（服务层与商业模式）
- [[2026-07-31 Datus - OSI 与 MetricFlow 的分层关系]]（修正 OSI 关系与 dbt Core 边界）
- [[2026-07-31 Fivetran - dbt Labs 合并完成与联合发布]]（Fusion 开源、dbt Wizard）
- [[2026-07-31 Peliqan - dbt 替代方案与锁定担忧]]（成本批评，需打折）
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
- GitHub: `dbt-labs/metricflow`（含 #1984、#1989 的编译器演进）

## 相关

- [[Fivetran + dbt Labs]]（公司页）· [[Agents Schema]]（同厂商的另一条语义路线）
- 概念页：[[Metrics Layer]] · [[Semantic Model]] · [[Query Rewrite]] · [[Query Acceleration]] · [[Headless BI]] · [[Open Semantic Interchange]]
- 对比：[[Join 正确性的保证方式]] · [[Capability Matrix]]
