---
type: comparison
status: draft
dimension: join-correctness
companies: [databricks, fivetran-dbt-labs]
created: 2026-07-31
updated: 2026-07-31
---

# Join 正确性的保证方式

## 为什么这是一个维度

语义层的核心承诺是"定义一次，任意维度组合查询，结果都对"。要兑现这个承诺，引擎必须处理两类经典的多表聚合错误：

- **fan-out（扇出）**：一对多 join 后，事实表的行被复制，`SUM` 把同一笔金额重复累加。
- **chasm join（鸿沟连接）**：两个互不相关的一对多分支通过共同维度连接，产生笛卡尔式膨胀，两侧的聚合同时被放大。

这两类错误的危险性在于**它们不报错**。查询成功返回，数字看起来合理，只是错的。所以"引擎是否防护，还是把责任交给建模者"是一个能真正区分产品的维度，比功能清单有价值得多。

## 结论先行

已深研的两家给出了**方向相反**的答案：

| 产品 | 机制 | 责任归属 | 错误后果 |
|---|---|---|---|
| [[dbt Semantic Layer]]（MetricFlow） | 走实体图**自动选择 join 路径**，主动防护 fan-out 与 chasm join，路径歧义时**告警** | 引擎 | 歧义被暴露出来 |
| [[Unity Catalog]] Metric Views | join 必须**预先声明**，基数靠 `rely.at_most_one_match` 声明，**运行时不校验** | 建模者 | `SUM`/`COUNT` **静默返回错误结果** |

这不是成熟度差异，而是设计哲学差异。MetricFlow 把 join 语义做成引擎的推导对象；Databricks 把它做成建模者的契约声明，换取更简单的实现和更好的查询计划。

## 逐家分析

### MetricFlow：引擎推导，主动防护

正确性建立在**实体（entity）图**上。每个语义模型声明自己的实体及类型，MetricFlow 据此推导有效 join 路径。实体类型的区分比一般理解的"主键/外键"更细，而这个细分正是防护的依据：

| 类型 | 语义 |
|---|---|
| `primary` | 每行一条记录，且包含数据平台中的**全部**记录 |
| `unique` | 每行一条记录，但可能只是全体的**子集**，允许 null |
| `foreign` | 同一记录可以有零条、一条或多条实例，允许 null |
| `natural` | 基于真实世界数据唯一标识记录的列或列组合 |

`primary` 与 `unique` 的区别（是否覆盖全体）决定 join 是否会丢行，`foreign` 标记出可能扇出的方向。引擎因此**不需要建模者告诉它基数**——基数从实体类型推导出来。

请求的维度分散在不同语义模型时，MetricFlow 走实体图寻找有效路径，**路径歧义时告警**而非静默选一条。累计指标的时间对齐由引擎的 time-spine 处理，不需要建模者手工构造日历表。

编译产物是 dataflow DAG。历史上多指标查询与派生指标被编译为各子查询的 **full outer join**，这既是正确性的保守选择（不丢行）也是性能瓶颈；2026 年 3 月引入 `MetricEvaluationPlan` 依赖图后，`DataflowPlanBuilder` 可接入不同 planner 来减少 full outer join 数量。**说明这条路线的代价出现在性能上，而非正确性上。**

**边界**：MetricFlow 保证的是**聚合的结构正确性**，不保证定义符合业务实际。它不会告诉你 `net_revenue` 是否排除了正确的测试账号、是否在正确日期确认收入。

### Unity Catalog Metric Views：建模者声明，运行时不校验

join 必须在 YAML 的 `joins` 块中预先声明，不支持查询时 join。基数由两个机制控制：

**`cardinality`** 决定 join 的语义。默认 `many_to_one`，把被连接表当维度查找表——多对多时**取第一条匹配行**（即静默丢数据而非报错）。设为 `one_to_many` 则把被连接表当独立事实源、在源粒度上单独聚合，从而允许一行源数据匹配多行。`one_to_many` 有一系列约束：其列不能用于 `fields`；单个聚合函数只能引用一个源；所有后代 join 必须也是 one_to_many。

**`rely.at_most_one_match: true`** 向查询分析器声明"被连接表对每行源数据至多匹配一行"，据此生成更高效的计划。官方文档明确警告：**该关系运行时不校验**。实际有多行匹配时，`SUM`、`COUNT` 等度量返回错误结果，且不报错。

```yaml
joins:
  - name: customer
    source: samples.tpch.customer
    on: source.o_custkey = customer.c_custkey
    rely:
      at_most_one_match: true
```

这把 fan-out 的正确性责任完整交回建模者，且**没有任何运行时安全网**。实践含义是它必须被写进评审流程——`rely` 的每一次使用都需要有人核实源数据的实际基数，且源数据变化后需要重新核实。

**换取了什么**：更简单的实现、更好的查询计划、以及 `one_to_many` 这种能在一个 metric view 里混合多个事实源粒度的表达力。后者是 MetricFlow 的实体图模型不容易直接表达的。

另外，`one_to_many` 的存在还有一个连带代价出现在加速上：**只要 metric view 里用了 `one_to_many` join，该视图的所有物化全部降级为 exact match only**，吃不到自动上卷。见 [[Query Acceleration]]。

## 待填（未深研）

| 产品 | 机制 | 状态 |
|---|---|---|
| [[Snowflake Semantic Views]] | | 待调研：relationship 声明是否校验基数 |
| [[Cube]] | | 待调研：`joins` 的 `relationship` 字段（`one_to_many` 等）是否只是声明 |
| [[AtScale]] | | 待调研：虚拟 OLAP 模型的多事实表处理与 SML 语言中的基数表达 |
| [[Aloudata CAN]] | | 待调研："明细级语义层、免宽表"如何处理多事实源聚合 |
| [[SuperSonic]] | | 待调研：S2SQL 中间语言是否涉及 join 正确性 |

## 初步判断（置信度中，样本仅两家）

**这个维度可能与"谁拥有执行层"相关**。MetricFlow 不拥有执行层，跨七种仓库生成 SQL，因此必须在自己这一层把 join 语义推导清楚——它没有别的地方可以兜底。Databricks 拥有执行层与优化器，可以把基数当作优化器提示（hint）来用，这在数据库领域是成熟做法（`RELY` 约束在传统数仓里就是不校验的），代价按数据库的惯例交给 DBA。

如果这个相关性成立，预期 [[Snowflake Semantic Views]] 会更接近 Databricks 的声明式路线，[[Cube]] 会更接近 MetricFlow。**这是一个可被后续调研否证的预测**，值得在深研 Snowflake 和 Cube 时专门核对。

## 参考来源

- [[2026-07-31 dbt - 语义模型与指标规范]]（实体四类型）
- [[2026-07-31 Datus - OSI 与 MetricFlow 的分层关系]]（MetricFlow 防护 fan-out 与 chasm join 的表述来源；第三方，待官方文档二次确认）
- [[2026-07-31 Databricks - Metric View YAML Reference]]（`cardinality`、`rely` 与运行时不校验的官方警告）
- [[2026-07-31 Databricks - Materialization for Metric Views]]（`one_to_many` 对物化的连带影响）

## 相关

- 概念页：[[Semantic Model]] · [[Query Rewrite]] · [[Metrics Layer]]
- 对比：[[Capability Matrix]] · [[Semantic Layer Vendor Landscape]]
