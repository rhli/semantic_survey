---
type: product
status: draft
company: databricks
product: Unity Catalog (Business Semantics / Metric Views)
capabilities: [data-catalog, governance, lineage, semantic-modeling, metrics-layer, query-rewrite, acceleration]
ga_status: ga
---

# Unity Catalog

## 定位与解决的问题

Databricks 的统一 catalog 与治理层。2026 年通过 **Business Semantics**（核心实现是 **Metric Views**）把语义层做成 catalog 的原生治理对象——指标与表、视图同级，复用同一套权限、血缘与审计。

官方对传统方案的三点批判勾勒了它的定位逻辑：BI 工具自带的语义层**专有且碎片化**（定义锁在各工具模型里，跨组织无法治理与追溯）、**位置太下游**（坐在展示层而非数据基础层，同一指标为不同 dashboard 反复重定义）、**对 AI 不友好**（依赖繁重前期建模，跟不上 agent 的开放式提问）。对应的主张是 upstream 治理：语义定义在数据基础层，一次定义、处处消费。

Business Semantics 于 2026 年 GA，同时宣布把 Metric View 核心实现开源到 Apache Spark。

## 核心概念与架构

Metric View 本质是一种特殊的视图，用 `CREATE VIEW ... WITH METRICS` 创建，定义体是 YAML。它与标准视图的根本差异在于**聚合时机**：标准视图在创建时就锁定了聚合与分组，Metric View 只声明"度量是什么"，把"按什么维度聚合"留到查询时决定。

两类成员构成语义模型：

- **fields**（维度，`dimensions` 是向后兼容的同义词）：查询时可用于 `SELECT`、`WHERE`、`GROUP BY` 的标量列。可以是分类列（region、status），也可以是未聚合的数值列（age、price），后者允许查询时用 `SUM`/`AVG` 临时聚合。
- **measures**（度量）：必须用聚合函数表达，不预设聚合层级。**查询时必须通过 `MEASURE()` 函数引用**——这是 Metric View 在 SQL 语法层面的关键标记。measures 可以引用先前定义的 fields 或 measures，从而组合出复杂 KPI。

查询执行路径是**声明式定义 → 查询时确定性编译**：引擎根据本次查询实际请求的 fields 和 measures，只连接需要的表、生成正确的聚合表达式。这既避免了为每种维度组合预建物化视图，也保证任何消费者（SQL 编辑器、notebook、dashboard、Genie Agent、第三方 BI）从同一定义得到同一结果。

## 语义模型的表达方式

### YAML 顶层结构

| 字段 | 说明 |
|---|---|
| `version` | 必填。YAML 规范版本（如 `1.1`），是规范格式版本而非自定义修订号 |
| `comment` | 可选。metric view 描述 |
| `source` | 必填。源数据：任何表状 UC 资产（**含另一个 metric view**）或 SQL 查询文本 |
| `parameters` | 可选。查询时绑定的命名参数，使 metric view 可作为表值函数调用 |
| `filter` | 可选。作用于所有查询的 SQL 布尔表达式 |
| `joins` | 可选。star / snowflake schema 连接 |
| `fields` | 条件必填。维度定义（无 `measures` 时必填） |
| `measures` | 条件必填。度量定义（无 `fields` 时必填） |
| `materialization` | 可选。物化加速配置 |

### 基础示例（star schema）

```yaml
version: 1.1
source: samples.tpch.lineitem
joins:
  - name: orders
    source: samples.tpch.orders
    on: source.l_orderkey = orders.o_orderkey
  - name: part
    source: samples.tpch.part
    on: source.l_partkey = part.p_partkey
fields:
  - name: Order Status
    expr: orders.o_orderstatus
  - name: Part Name
    expr: part.p_name
measures:
  - name: Total Revenue
    expr: SUM(l_extendedprice * (1 - l_discount))
  - name: Line Item Count
    expr: COUNT(1)
```

命名空间规则：`source` 前缀指 metric view 的源表，join 的 `name` 前缀指被连接表；`on` 子句中无前缀的引用默认指被连接表。

### 度量的表达力

```yaml
measures:
  - name: order_count
    expr: COUNT(1)
    display_name: 'Order Count'
  - name: total_revenue
    expr: SUM(o_totalprice)
    comment: 'Gross revenue from all orders'
    display_name: 'Total Revenue'
    synonyms: ['revenue', 'total sales']
  - name: unique_customers
    expr: COUNT(DISTINCT o_custkey)
  # 组合多个聚合的计算型度量
  - name: avg_order_value
    expr: SUM(o_totalprice) / COUNT(DISTINCT o_orderkey)
    synonyms: ['AOV', 'average order']
  # 带条件的度量
  - name: open_order_revenue
    expr: SUM(o_totalprice) FILTER (WHERE o_orderstatus = 'O')
    synonyms: ['backlog', 'outstanding revenue']
```

### 连接建模

**snowflake schema** 靠嵌套 `joins` 表达多级连接，引用时用完整点路径：

```yaml
version: 1.1
source: samples.tpch.orders
joins:
  - name: customer
    source: samples.tpch.customer
    'on': o_custkey = c_custkey
    joins:
      - name: nation
        source: samples.tpch.nation
        'on': c_nationkey = n_nationkey
fields:
  - name: customer_nation
    expr: customer.nation.n_name
```

**cardinality** 控制连接语义。默认 `many_to_one`，把被连接表当维度查找表（多对多时取第一条匹配行）。设为 `one_to_many` 则把被连接表当作独立事实源、在源粒度上单独聚合，从而让一行源数据匹配多行：

```yaml
version: 1.1
source: main.sales.customers
joins:
  - name: orders
    source: main.sales.orders
    on: orders.customer_id = source.customer_id
    cardinality: one_to_many
fields:
  - name: customer_name
    expr: customer_name
measures:
  - name: customer_count
    expr: count(*)
  - name: order_count
    expr: count(orders.order_id)
  - name: total_order_revenue
    expr: sum(orders.amount)
```

`one_to_many` 的约束：其列不能用于 `fields`（field 必须对每行源数据解析为单值）；单个聚合函数只能引用一个源（但可以对多个聚合的结果做算术，如 `count(orders.order_id) / count(*)`）；一个 one_to_many join 的所有后代 join 必须也是 one_to_many。需要 DBR 18.1 + YAML 1.1。

### 其他建模能力

- **composability**：`source` 可以是另一个 metric view，此时可在新视图中引用其 fields 和 measures。这是官方推荐的复杂 KPI 组织方式——先定义原子度量，再逐层组合。
- **parameters**：定义 `name` / `data_type` / 可选 `default`，查询时以命名参数传入，一个 metric view 可服务多种查询变体。注意定义了 parameters 的 metric view **不能物化**。
- **window measures（标注 Experimental）**：用声明式 `window` 块表达滚动、累计、半可加聚合，而非在 measure 表达式里写 SQL 窗口函数。字段为 `order`（须为确定性字段，`rand()`/`current_timestamp()` 会导致不可预测的排序）、`range`、`semiadditive`（`first`/`last`）、`offset`（如 `-12 month`，用于同比环比）。`range` 取值：`current`、`cumulative`、`trailing/leading N unit [inclusive|exclusive]`（默认 exclusive）、`all`。

```yaml
measures:
  - name: rolling_7day_customers
    expr: COUNT(DISTINCT o_custkey)
    display_name: '7-Day Rolling Customers'
    window:
      - order: order_date
        range: trailing 7 day
        semiadditive: last
```

- **wildcard 批量导入**（DBR 18.2 + YAML 1.1）：`source.*`、`<join>.*`、`.* EXCEPT (col1, col2)`、`<struct>..*` 展开 STRUCT。wildcard 上不允许 `name` 和任何语义元数据；多源导入同名列会冲突报错，需 `EXCEPT` 排除后显式定义。wildcard 在创建时展开，源表后续新增的列需 `CREATE OR REPLACE` 或 `ALTER` 才纳入。

### 面向 AI 的语义元数据

需 YAML 1.1。`display_name`（可视化工具中的标签，≤255 字符）、`format`（显示格式规范）、`synonyms`（供 AI 与 BI 工具发现该字段的别名，最多 10 个）、`comment`（出现在 Unity Catalog 与文档工具中）。这些是"同一份定义同时服务人和 agent"的实现载体——synonyms 直接影响 Genie 对自然语言的解析准确度。

## 能力细节

### query-rewrite / acceleration：物化的三级降级链

物化（Preview）配置在 `materialization` 块中：`mode` 必须为 `relaxed`，`materialized_views` 分 `aggregated`（需指定 dimensions / measures）与 `unaggregated`（每个 metric view 最多一个），可配 `schedule`、`cluster_by`（支持 `auto: true`）、`partition_by`。

查询时优化器按固定顺序尝试改写：

1. **exact match** — 查询的 group-by 表达式与 measures 与某个物化完全对应
2. **rollup match** — 查询粒度更粗，引擎把细粒度结果上卷
3. **unaggregated match** — 命中未聚合物化
4. 都不命中则**直接读源表**

关键在于 rollup 的门槛（这决定了自动加速的真实覆盖面）：

| 度量形态 | 可命中类型 | 原因 |
|---|---|---|
| 单个可加聚合（`SUM`/`COUNT`/`MIN`/`MAX`/位运算/布尔聚合） | rollup 可用 | 可从部分结果重新聚合 |
| `COUNT(DISTINCT)`、`SUM(DISTINCT)`、`MEDIAN` | 仅 exact match | 不可从部分结果重算（MEDIAN 依赖分布） |
| 一个表达式含多个聚合（`SUM(x) + MIN(y)`） | 仅 exact match | 定义中只允许一个聚合函数 |
| window measure | 仅 exact match | — |
| 含非确定性 filter | 不参与 rollup | filter 须确定性（`FILTER` 子句同样要求） |

更强的约束：**只要 metric view 里用了 `one_to_many` join，该视图的所有物化全部降级为 exact match only**。

实践含义是：越贴近真实业务的复杂指标（去重计数、复合口径、同比环比、一对多事实关系），越吃不到自动上卷的红利，退化为"必须为每种查询形态预先声明一个物化"——这与预建宽表的差距被压缩了。官方也建议为不同查询形态创建多个 aggregated 物化。

**新鲜度语义值得注意**：命中物化的查询读上次刷新结果，未命中的查询读实时数据，因此同一个 metric view 的不同查询可能看到不同新鲜度的数据。官方建议把刷新排在上游批处理管道之后，或用 unaggregated 物化让所有查询读同一快照。

### governance：继承 UC，但有明确缺口

未物化的 metric view 继承 Unity Catalog 的治理体系：行级安全、列级脱敏、审计日志、血缘。这是平台原生方案相对独立语义层的最强卖点——治理不是外挂的第二套系统。

但"完整继承"需要两处限定：

- **ABAC 策略不作用于 metric view**。官方论坛已确认这是已知限制：ABAC 不能直接应用于 view，而 metric view 本质是 view（`CREATE VIEW ... WITH METRICS`）。在源表上生效的 ABAC 策略，查询 metric view 时不生效。
- **物化场景下治理约束显著收紧**：源表不能有 RLS、列掩码或 ABAC 策略；`filter`、`fields`、`measures` 中不能出现 invoker-dependent 表达式（`current_user()`、`is_member()` 等），否则创建、变更或刷新直接失败（`METRIC_VIEW_MATERIALIZATION_WITH_INVOKER_DEPENDENT_EXPRESSIONS_NOT_SUPPORTED`，SQLSTATE 42K0E）。

换言之，更准确的表述是"未物化的 metric view 在 ABAC 之外继承 UC 治理"。这一层在官方叙事中不可见。

### 正确性：fan-out 防护靠声明，不做运行时校验

`rely.at_most_one_match: true` 向查询分析器声明"被连接表对每行源数据至多匹配一行"，据此生成更高效的计划（尤其当被连接表的字段出现在筛选条件中时）。

```yaml
joins:
  - name: customer
    source: samples.tpch.customer
    on: source.o_custkey = customer.c_custkey
    rely:
      at_most_one_match: true
```

文档明确警告：**该关系运行时不校验**。如果实际有多行匹配，`SUM`、`COUNT` 等度量返回错误结果，且不报错。这把 fan-out 的正确性责任交回给建模者——是一个需要在评审流程里覆盖的风险点，也是与"引擎保证正确性"的宣传口径之间的落差。

## 创作路径

四条路径并存，覆盖不同角色：

1. **SQL DDL**：`CREATE VIEW ... WITH METRICS`，YAML 写在 `$$..$$` 中；也可用 `ALTER VIEW` 修改。
2. **Catalog Explorer UI（Public Preview）**：点选式定义 fields 与 measures、可视化配置表间关系、内联绘图、发布前端到端测试。UI 生成的 YAML 使用 `dimensions` 关键字。
3. **Genie Code**：编辑器内的 agentic AI 助手。bootstrap 语义模型（建议 measures、dimensions、synonyms、文档）、识别既有定义的问题并建议改进、用真实数据验证改动、逐条评审批准变更。上下文感知：在 UI 标签页更新编辑器，在 YAML 标签页插入 YAML。
4. **从既有资产反向提升**：建 AI/BI Dashboard 时产生的 join、filter、计算字段可一键提升为新 Metric View，随即成为组织语义层的一部分并获得物化加速；也支持从 Tableau / Power BI 文件导入生成 metric view。

第 4 条在采纳路径上有战略意义——它把"存量 dashboard 逻辑"作为语义层的冷启动来源，绕开了"先建模才能用"的门槛。

## 开放性

- **开源**：Metric View 核心实现开源到 Apache Spark OSS，目标为下一个 Apache Spark 版本（进度跟踪 **SPARK-54119**），Unity Catalog OSS v0.5 支持"coming soon"。开源范围是 metric view 的定义与执行实现，**不含 Genie 那一整套**。
- **OSI**：Databricks 已加入 [[Open Semantic Interchange]] 并在积极贡献。
- **第三方消费**：Sigma（实时查询，执行时继承 UC 治理）、Hex（已完整集成）、Omni（双向同步，Omni 内的修改可推回 UC）、Domo、ThoughtSpot（Spotter NL 查询，今年晚些）、Tableau（delegated semantics，预计 2026 年底）；治理侧 Atlan（纳入 Context Graph）、Collibra（双向同步）、Monte Carlo、Anomalo。
- **Power BI 缺席**：微软 2026 年初移除 BI Compatibility Mode，要求指标定义在 Power BI / Fabric 语义模型内，两个平台在语义层上是竞争而非互操作关系。考虑到 Power BI 的企业存量，这是"处处可用"叙事的实质缺口。

## 限制与边界

- **平台绑定**：Metric Views 活在 Unity Catalog 内、描述 lakehouse 数据。跨仓场景需先把数据接入 Databricks（Lakehouse Federation 可缓解但不消除）。
- **定义 ≠ 执行**（竞品的核心批评，值得独立评估）：Metric View 标准化了指标含义，但把任意自然语言问题变成治理的确定性 SQL 是消费方的职责；主要 AI 消费方 Genie 既局限于 Databricks，本身又是 LLM 驱动的概率性组件。
- **join 必须预先声明**，不支持查询时 join。
- **measure 表达式内不支持原生 SQL 窗口函数**，必须用声明式 `window` 块（且该功能标注 Experimental）。
- **仍需人工编写与维护**：数据模型变化时需有人更新定义。Genie Code 降低成本但不消除责任。
- 版本门槛较碎：多项能力要求 YAML 1.1 与特定 DBR 版本（one_to_many 需 18.1，wildcard 需 18.2）。物化为 Preview，UI 创作为 Public Preview，window measures 为 Experimental。
- 类型坑：string 型 field 一律为 `STRING`，源列为 `CHAR(n)` 时空格填充丢失，导致相同比较在源表与 metric view 上结果不同。
- 运维坑（第三方经验，待验证）：用 `CREATE OR REPLACE` 会清掉 Unity Catalog 权限，应改用 `ALTER VIEW`；`ALTER VIEW` 会清除未写入 YAML `comment` 字段的 UC comment。

## 待调研问题

- [ ] SPARK-54119 的实际开源范围与进度：开源实现与 Databricks 托管版的功能差距
- [ ] Metric View YAML 与 OSI 规范的互转支持程度（Databricks 是 OSI 后加入方，MetricFlow 才是规范基础）
- [ ] 查询编译的实现细节：编译后的 SQL 长什么样，多级 join 下如何保证聚合正确性（开源后可读源码）
- [ ] composability 的层数上限与性能影响
- [ ] `at_most_one_match` 声明错误在实践中的发生率与检测手段
- [ ] 物化的成本模型：多个 aggregated 物化的存储与刷新开销如何评估
- [ ] Lakehouse Federation 下的 metric view 行为（跨源时物化与改写是否可用）
- [ ] 与 Genie Ontology 的具体交互协议：认证的 Metric View 如何进入 OntoRank 的权威度计算

## 参考来源

- [[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]（GA 公告、开源、生态）
- [[2026-07-31 Databricks - Metric View YAML Reference]]（语法规范）
- [[2026-07-31 Databricks - Materialization for Metric Views]]（加速机制）
- [[2026-07-31 Databricks Community - Metric Views 不支持 ABAC]]（治理缺口）
- [[2026-07-31 Colrows - Databricks Metric Views 的边界]]（竞品批评视角）
- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]（与 Snowflake 对位）
- 官方文档索引：docs.databricks.com/aws/en/uc-semantics/metric-views/

## 相关

- [[Genie]] · [[Genie Ontology]]（下游 AI 消费方）
- [[2026-07-31 Databricks 的双层语义架构]]（综合判断）
- 概念页：[[Metrics Layer]] · [[Semantic Model]] · [[Query Rewrite]] · [[Query Acceleration]] · [[Data Governance]]
