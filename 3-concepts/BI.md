---
type: concept
status: draft
aliases: [Business Intelligence, 商业智能]
---

# BI

## 定义

BI 工具（Tableau、Power BI、Looker、帆软）本质上是 **SQL 生成器 + 可视化器**。它与语义层的关系、以及它消费语义的接口形态，是理解语义层赛道格局的前提。

本页的核心抽象：**语义查询（semantic query）是广义概念**——BI 拖拽和 AI 问数都是它的实例。两者只是同一编译管线的两个入口：

```
BI 拖拽字段 ──┐
              ├→ 语义查询（"给我 GMV by 城市"）→ 语义层编译 → 物理 SQL → 数仓
AI 自然语言 ──┘
```

## 没有语义层时，BI 怎么工作

只有两种模式，各有致命伤：

- **模式 A：直连物理表**。分析师把 `orders`、`users`、`refunds` 拖进画布，手拉 join，拖"金额"做 SUM。GMV 要不要扣退款？每个分析师在每个 workbook 里各自决定——十个报表十个口径。
- **模式 B：连宽表**。数据团队提前用 ETL 把常用维度组合物理打宽（`ads_gmv_by_city_daily`），BI 连它。口径统一了，但维度组合爆炸——业务想按"城市 × 渠道 × 会员等级"看，宽表里没有，就得排期等开发。这是"表资产爆炸"的根源，也是 [[NoETL]] 路线的攻击点。

语义层消除这个二选一：**把"业务概念 → 物理表"的映射从每个 workbook 里抽出来，集中管一份**。

## 语义查询的编译旅程

以 Tableau + [[Cube]] 为例，业务用户拖了"GMV"和"城市"：

1. Tableau 生成一条语义查询——它以为在查一张叫 `orders_view` 的表；
2. 语义层查模型：`gmv = SUM(amount) WHERE status != 'refunded'`；`orders` 与 `cities` 按 `city_id` join（one_to_many）；当前用户只能看华东区（行级权限）；
3. 编译成物理 SQL（join、过滤、权限全部织入），检查 pre-aggregation 是否命中；
4. 打向数仓执行，返回。

三个关键点：

- **BI 工具不知道物理表的存在**。它看到的是可拖拽的指标和维度列表，不是 47 列的 `orders` 表。
- **编译发生在查询时**。改一次指标定义，所有报表下次刷新自动生效——不需要改任何 dashboard。
- **口径统一的机制是"没得选"**。用户拖 GMV 永远拿到同一个定义，不是靠自律，是因为接触不到裸表。

## 北向接口：五类形态

"北向"借自网络领域：朝向上游消费方（BI 工具、应用、agent）的接口；朝向下游数据源的叫南向（即各方言 SQL 的生成能力，[[SQLGlot]] 所在的层）。北向接口决定**谁能消费这份语义**：

| 接口 | 消费方 | 工作方式 |
|---|---|---|
| SQL 线协议 | 几乎所有 BI 工具 | 语义层伪装成 Postgres；BI 发的 SQL 实际查的是逻辑表。[[Cube]] SQL API、[[dbt Semantic Layer]] 的 SQL 接口 |
| MDX / DAX | Excel 透视表、Power BI | 微软 OLAP 世界的原生语言。[[AtScale]]、Cube 讲 DAX，Power BI 以为在连 Analysis Services。**存量企业最重要的接口** |
| REST / GraphQL | 应用开发者 | 查询是 JSON（`{measures, dimensions, filters}`），返回 JSON。嵌入式分析的主流方式 |
| JDBC / ODBC | 老牌 BI 工具 | 传统标准连接 |
| MCP | AI agent | 本质区别：agent 先发**发现**调用（有哪些指标、什么意思），再发查询 |

隐形的第六类是**原生接口**：[[Looker]] 的 Explore 直接消费 LookML、Power BI 直接消费自家语义模型——BI 与语义层同属一家时不需要开放接口。接口零成本，但语义资产锁死在自家体验层。

## 语义查询：BI 与 AI 的统一抽象

BI 拖拽和 AI 问数（[[Text2SQL]]）在语义层眼里是同一件事的不同入口，差异只在两头：

- **入口**：拖拽是结构化输入（字段从模型里选，天然消歧）；自然语言是开放输入（需要先理解"GMV"指什么）。AI 场景多出来的全部复杂性——grounding、消歧、verified examples——都是在把开放输入**还原成** BI 早就有的那种结构化语义查询。
- **出口**：BI 返回表格给图表渲染；agent 返回答案给自然语言包装。

这个视角的实践价值：**评估一个 AI 问数产品，就看它把 NL 还原成语义查询的质量**——还原所依据的工件（语义模型、verified queries、同义词）就是准确率的真实上限。这也解释了 [[BI 与 AI 场景的 User Story]] 的结论：AI 没有发明新需求，它只是把 BI 时代"建模时人工消歧"的成本挪到了运行时。

## 为什么北向接口形态决定语义层的价值半径

- **[[Headless BI]] 运动的本质就是北向接口的开放化**。LookML 2012 年就发明了 metrics-as-code，但只服务 Looker；Cube、dbt SL 把同一份定义经标准接口暴露给所有消费方。"Define once, serve everywhere" 里的 everywhere，具体指的就是北向接口的宽度。
- **接口形态决定治理能传多远**。SQL 接口只能传查询和结果；MCP 能传发现、描述、同义词、示例查询。BI 工具不需要发现阶段（建模时人已选定模型），agent 必须现查——这是 2026 年各家抢着上 MCP server 的接口层原因。
- **南向 × 北向合起来定义"通用性"宣称**。[[AtScale]] 的 "Universal" 翻译过来就是：南向会讲五种仓的方言 SQL，北向会讲 MDX/DAX 给 Excel、SQL 给 Tableau、MCP 给 agent。

## 相关概念

[[Semantic Layer]]、[[Headless BI]]、[[Text2SQL]]、[[Query Rewrite]]、[[Metrics Layer]]
