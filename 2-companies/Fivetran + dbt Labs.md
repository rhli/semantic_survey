---
type: company
status: draft
company: fivetran-dbt-labs
founded: 2016
hq: Oakland
aliases: [dbt Labs, Fivetran, dbt]
---

# Fivetran + dbt Labs

dbt 是 SQL 数据转换的事实标准（超过 10 万个数据团队），语义层战略是 **metrics-as-code**：指标定义与 transformation 代码同仓、同 Git/PR/CI 流程。2026 年 6 月与 Fivetran 完成合并后，语义层被放在更大的叙事里——**为可信 AI agent 提供开放数据基础设施**。

> **命名说明**：本页覆盖合并后的实体。2026-06-01 前的材料称 dbt Labs，官方合并后名称为 "Fivetran + dbt Labs"（新闻稿标注为过渡命名）。全库统一 slug 为 `fivetran-dbt-labs`，笔记正文按材料的时间点使用当时的名称。本库只跟踪其语义层/上下文层相关业务，Fivetran 的数据摄取业务不在范围内。

## 语义层相关产品线

- [[dbt Semantic Layer]] — MetricFlow（开源，Apache 2.0）编译引擎 + 平台侧的 serving API
- [[Agents Schema]] — 开源的 agent 上下文标准，把语义定义与元数据落进客户自己数仓的 SQL 表

配套：dbt Wizard（自主模型编写与调试，可生成语义模型）、dbt Fusion 引擎（Rust，dbt Core v2.0 中开源）、dbt State（管道缓存层）。

## 战略与路线观察

**两条语义路线并行，且第二条不走自家格式**。dbt 同时押注 MetricFlow（语义**执行**引擎，编译指标为仓库 SQL）与 Agents Schema（语义**分发**标准，让 agent 用裸 SQL 读上下文）。值得注意的是 Agents Schema 的规范把 **OSI 定为 canonical 的语义层格式**，其他格式（LookML 等）预期先转成 OSI 再进 `AGENTS.OSI_*`——dbt 自家的 MetricFlow YAML 不是这条路径上的一等公民，dbt provider 只摄取 `manifest.json` 的模型、列与依赖。把语义分发标准让给厂商中立格式，是"开放数据基础设施"叙事的实质动作，也是对 Snowflake 主导 OSI 的一种接受。详见 [[2026-07-31 dbt Labs - Agents Schema SPEC]]。

**Agents Schema 是上下文层竞争中最激进的"客户自有"路线**。相对 [[Genie Ontology]]（平台内自动抽取图谱）与 [[Atlan]]（外部 SaaS graph + MCP），Agents Schema 主张上下文就是**客户数仓里的普通 SQL 表**，任何能跑 SQL 的 agent 都能读，MCP 只是可选项之一。这条路线在避免锁定这个维度上最强，但代价出现在权限模型上（见下）。四条路线的对比见 [[上下文层的四条路线]]。

**开源与商业的边界比竞品口径宽**。常见说法"MetricFlow 开源但没有 dbt Cloud 就用不了语义层"是不准确的：MetricFlow 在 dbt Core 中通过 CLI 即可定义指标并**本地编译执行**；平台加的是托管的 Semantic Layer API（GraphQL、JDBC、Python SDK）、托管 BI 集成与缓存。合并后进一步开源：Fusion 引擎 runtime 作为 dbt Core v2.0 以 Apache 2.0 发布。真实的商业边界是 **metrics-as-API 与托管能力**，不是指标定义本身。

**锁定问题在合并后变得具体**。Fivetran 在合并前（2025-09-03）已收购 Tobiko Data，即 SQLMesh 与 SQLGlot 的母公司。因此单一实体现在同时控制 dbt Core、Fusion 引擎路线图、SQLMesh 与 dbt Cloud 定价。这是第三方讨论 dbt 替代方案时的核心论据（[[2026-07-31 Peliqan - dbt 替代方案与锁定担忧]]）。事实基础可独立核实；由此推出的风险程度是批评方立场。分析师在 Tobiko 收购时就提出的问题——多转换引擎的许可与支持路线图——合并后更尖锐，仍未公开回答。

**语义层是明确的商业变现重点**。计费按 "queried metrics" 计量（每次成功的计算请求按请求中的指标数计费），第三方基准报告直接建议"若语义层采纳是战略性的，现在就谈额度豁免"。这与平台原生方案（[[Unity Catalog]] 的 Metric Views 无独立语义层计费）在商业模式上是根本不同的处境：独立语义层必须为语义层本身定价，平台原生方案可以把它当作留住计算负载的赠品。

**跨平台中立是真实优势也是真实约束**。MetricFlow 支持 Snowflake、BigQuery、Databricks、Redshift、Postgres、Trino、DuckDB，这是相对 Databricks / Snowflake 平台绑定方案的核心卖点。约束在于它不拥有执行层——性能与治理最终取决于底层仓库，自身无法像平台原生方案那样继承一套统一的 RLS / 列掩码 / ABAC。

## 时间线

- 2016：dbt Labs 成立（前身 Fishtown Analytics）
- 2023-02：收购 Transform，获得 MetricFlow（待核实准确日期与当时能力范围）
- 2025-09-03：**Fivetran 收购 Tobiko Data**（SQLMesh + SQLGlot）（[[2026-07-31 Fivetran - Tobiko Data 收购与 SQLMesh 归属]]）
- 2025-09：[[Open Semantic Interchange]] 发布，dbt Labs 为参与方
- 2025-10-13：Fivetran 与 dbt Labs 宣布全股票合并
- 2026-06-01：**合并完成**，以 "Fivetran + dbt Labs" 运营；同日发布 dbt Core v2.0（Fusion 开源，alpha）、dbt State（preview）、dbt Wizard（beta）、**Agents Schema**（[[2026-07-31 Fivetran - dbt Labs 合并完成与联合发布]]）
- 2026-06：OSI 名单扩至含 Alation、Atlan、Collibra、Cube、ThoughtSpot

待核实：SQLMesh 捐赠 Linux Foundation 的准确日期（来源页面标 2025-03-25，与 Tobiko 收购时序矛盾）；Transform 收购的准确日期。

## 规模与背书

- 合并后服务超过 10 万个数据团队，合计 ARR 接近 6 亿美元（官方口径）
- 客户举例：OpenAI、Zendesk、Coupa、HubSpot、Siemens、Roche、Condé Nast、LVMH、Pfizer、Verizon
- 合并发布的客户引述（Zendesk、Inova Health、Tinuiti、Shutterstock、DocuSign）均为泛泛的"可信数据基础"表述，无量化指标，参考价值有限

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
