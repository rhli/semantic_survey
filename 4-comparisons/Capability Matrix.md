---
type: comparison
status: draft
dimension: capability-matrix
companies: [databricks, snowflake, fivetran-dbt-labs, cube, atscale, atlan, aloudata, google, microsoft]
created: 2026-07-31
updated: 2026-08-02
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

## 主观评级

| 产品 | 语义表达力 | 查询编译 | 加速机制 | AI grounding | 开放性 | 备注 |
|---|---|---|---|---|---|---|
| [[Unity Catalog]] | star/snowflake join、one_to_many、composability、parameters、window（实验性） | 声明式 + 查询时确定性编译；fan-out 靠声明不校验 | 三级降级链；rollup 要求可加且单聚合，one_to_many 全降级 | Metric Views + [[Genie Ontology]] 双层 | 核心实现开源进 Spark；已加入 OSI；Power BI 缺席 | 治理有 ABAC 缺口，物化场景收紧 |
| [[dbt Semantic Layer]] | 实体四类型 + 自动 join 路径；5 类指标（simple/ratio/derived/cumulative/**conversion**）；time spine | dataflow DAG 编译，跨 7 种仓库生成 SQL；**主动防护 fan-out 与 chasm join**，歧义告警 | saved query → export → 两层缓存；声明式缓存对已配置维度的筛选仍命中，上游新鲜即自动失效；需 Enterprise | MetricFlow 定义 + MCP server；同厂商另有 [[Agents Schema]] | MetricFlow 与 Fusion 均 Apache 2.0；OSI 参与方且自家上下文标准以 OSI 为 canonical | 不拥有执行层——治理与性能取决于底层仓库；按 queried metrics 计费 |
| [[Agents Schema]] | 不自产语义，只分发；OSI 为 canonical 格式 | 无——只做发现层，agent 拿到上下文后自由写 SQL | 不适用 | 裸 SQL 读 `AGENTS.*`，MCP 可选；含 skills 分发 | 开源标准，客户自有，跨仓（Snowflake/Databricks/BigQuery） | **元数据对全体查询者可读**；批量快照非活图谱 |
| [[Snowflake Semantic Views]] | DDL 五块（TABLES/RELATIONSHIPS/FACTS/DIMENSIONS/METRICS）；**无 cumulative/conversion 类型、无自动时间粒度**——表达力弱于 MetricFlow | 引擎原生理解语义视图；2026-03 起标准 SQL + `AGG()` 可查询，消费门槛大降 | 无自有加速层，靠仓本身；Autopilot 从 BI 使用模式自动生成定义 | [[Cortex Analyst]] 读视图元数据 grounding；**内置评测闭环**（verified queries + `sql_correctness`）为各家独有 | 仓内限定；OSI（Apache Ossie）发起方 | YAML ≤1MB、建议 50–100 列；90%+ 准确率为内部 150 题口径 |
| [[Cube]] | cubes + views，JS/YAML；关系基数需声明（是否引擎校验待核实） | Semantic SQL + E-Graphs 作 agent guardrail；**六接口面**：SQL/REST/GraphQL/MCP/DAX/MDX | **自有 serving 层**：pre-aggregation 声明式物化 + 自动命中 | MCP server（2026-01）；编译期行级安全、多租户构造性支持 | Core Apache 2.0；OSI 成员 | 与 dbt SL 对决的本质差异：拥有执行/serving 层，按平台订阅而非按量 |
| [[AtScale]] | SML（YAML 对象，Git 管理）；OLAP 多维模型 | 虚拟 OLAP：MDX/DAX → 优化仓 SQL | **autonomous aggregates** 自动聚合管理——加速维度最成熟 | MCP server（stateless 规范）；One-Click Modeling | **SML 语言开源、引擎闭源**——开放的是可移植性不是实现；OSI 成员 | Excel/Power BI 存量兼容是隐形护城河；定价重 |
| [[Atlan]] | 不自产语义定义——摄取 dbt/Cube/LookML 定义并绑定治理元数据 | 不适用（不做查询编译） | 不适用 | hosted MCP server（采用 17x/调用 58x 增长）；Context Agents 自动生成上下文（2 周冷启动，89% 不劣于人写，自家口径） | 400+ 连接器；Context Lakehouse Iceberg-native BYOC | [[Context Layer]] 路线代表；+38% 准确率为自家口径 |
| [[Aloudata CAN]] | 要素化声明定义（度量/维度/限定）；**平台内建模无 DSL 文件**——与 metrics-as-code 阵营形态分野 | 逻辑计划自动生成无歧义 SQL；Agent 走 NL2MQL2SQL 中间语言 | 三级智能物化 + ETL 代持（物化运维平台化） | Aloudata Agent 以语义层为底座 | 闭源；国内部署 | "NoETL" 的实际含义是物化运维代持，不是没有 ETL |
| [[Looker]] | LookML（view/explore/measure）；**symmetric aggregates 引擎级 fan-out 防护**——比 MetricFlow 更早且强制 | 编译到仓 SQL；新增 in-database 模型可叠加 [[Snowflake Semantic Views]] | 无自有加速层 | Conversational Analytics GA + BI Agents + MCP；descriptions/synonyms/golden queries | 闭源，Google 栈内 | 语义层鼻祖（2012）；2026 Gartner ABI MQ Leader |
| [[Microsoft Fabric]] | tabular 模型 + DAX；Prep for AI 三件套（AI schemas/instructions/verified answers） | **DAX 生成工具只读模型元数据，忽略 agent 级指令**——grounding 必须编译进工件 | 无自有加速层 | Copilot + Data Agent；verified answers 确定性兜底；Fabric IQ = models + Ontology（preview） | 闭源；**缺席 OSI** | 存量最大；策略是给存量模型打 AI 补丁而非重建 |

## 按维度的深入对比

矩阵只能给出并列条目，真正的分歧在下面这些页里：

- [[Join 正确性的保证方式]]——引擎防护 vs 建模者声明，两种相反的责任归属
- [[上下文层的四条路线]]——上下文存在哪、谁拥有、冲突如何仲裁

## 参考来源

见 [[Semantic Layer Vendor Landscape]] 的来源列表。
