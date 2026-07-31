---
type: product
status: draft
company: fivetran-dbt-labs
product: Agents Schema
capabilities: [semantic-modeling, data-catalog, lineage]
ga_status: preview
---

# Agents Schema

## 定位与解决的问题

[[Fivetran + dbt Labs]] 在 2026 年 6 月合并当日发布的**开源 agent 上下文标准**。做法是在客户自己的数仓/湖里指定一个名为 `AGENTS` 的 schema，把指标定义、语义模型、dbt 血缘、业务文档全部落成**普通 SQL 表**。

官方类比很精准：**这是数据仓库的 `AGENTS.md`**——一个约定俗成的位置，让工具、agent 和人都能发现有哪些数据、谁拥有它、如何负责地使用它。

它解决的问题与 [[dbt Semantic Layer]] 不同。MetricFlow 解决"已定义的指标如何被正确编译执行"；Agents Schema 解决"agent 在写查询之前，怎么知道该用哪张表、`revenue` 是 gross 还是 net、'本月'如何界定"。官方的问题陈述是：这些信息今天散落在 wiki、Slack 线程、dashboard 和口耳相传里，agent 找不到就只能猜。

**它是四条上下文层路线中最激进的"客户自有"方案**。相对 [[Genie Ontology]]（平台内自动抽取 + OntoRank 排序）与 [[Atlan]]（外部 SaaS graph + MCP 交付），Agents Schema 的赌注是：**上下文应该是数据，放在你自己的仓里，用裸 SQL 查**——而不是一个服务。详见 [[上下文层的四条路线]]。

## 核心概念与架构

### `AGENTS.ROOT`：provider 注册表

整个设计最巧的一处。表结构只有三列：

```sql
CREATE TABLE AGENTS.ROOT (
  provider    VARCHAR NOT NULL,
  key         VARCHAR NOT NULL,
  content     TEXT NOT NULL,
  PRIMARY KEY (provider, key)
);
```

唯一硬约束是 `(provider, key)` 唯一，`content` 是自由文本。规范明确说明理由：**"预期消费者是 AI agent 而非需要固定 schema 的确定性应用"**，因此 content 可以半结构化、反规范化、自由格式，且允许随 provider 与模型演进而改变形状。Markdown 是自然选择但不强制。

约定（非强制）是 key 与被文档化的表名（去前缀）对应：`(dbt, model)` 描述 `AGENTS.DBT_MODEL`，`(lookml, explore)` 描述 `AGENTS.LOOKML_EXPLORE`。规范说明遵守这个约定能"避免消费方尤其是 LLM agent 混淆某行是在描述一张表还是自由上下文"。

这个设计的取舍值得注意：**为 LLM 消费者放弃了强 schema**。传统元数据标准（如 catalog API）会把每类元数据定义成严格结构；这里反过来承认"下游是能读自然语言的模型"，于是把可扩展性做成自由文本。代价是无法用确定性程序可靠消费。

### Skills：把指令也放进数仓

容易被忽略但很重要的一层。`AGENTS.ROOT` 不只存元数据，还存**给 agent 读的指令**。约定 `key = skill/<name>`，`content` 为 markdown 正文，可带 YAML frontmatter，标准字段 `uses` 声明该 skill 可使用的 schema 与表：

```markdown
---
uses:
  schemas:
    - QUICKSTART_FINANCE
  tables:
    - QUICKSTART_FINANCE.ARR_SNAPSHOT
---

# Revenue Skill
Use this skill when answering ARR, MRR, recurring revenue, or revenue trend questions.
```

`uses` 是**加性的、不表达排除**（即它是能力声明而非权限边界）。解析结果落 `AGENTS.SKILL_USE`，供消费方快速反查"哪些 skill 可能用到某 schema/表"。frontmatter 格式错误不阻止 skill markdown 发布到 `ROOT`，只是不进 `SKILL_USE`。

CLI 每次运行发布一个内置的 `skill/agents-schema-analyst`，按目标仓库匹配变体（Snowflake / Databricks / BigQuery），key 固定所以切换目标仓库是替换而非新增。

这意味着 Agents Schema 的野心不止元数据分发——它同时是 **prompt / 指令的分发通道**。

### provider 与表族

| Source | Tables |
|---|---|
| dbt | `DBT_MODEL`、`DBT_COLUMN`、`DBT_DEPENDENCY` |
| LookML | `LOOKML_VIEW`、`LOOKML_DIMENSION`、`LOOKML_MEASURE`、`LOOKML_EXPLORE` |
| OSI | `OSI_MODEL`、`OSI_DATASET`、`OSI_FIELD`、`OSI_METRIC`、`OSI_RELATIONSHIP` |
| Sigma | `SIGMA_DATA_MODEL`、`SIGMA_ELEMENT`、`SIGMA_COLUMN`、`SIGMA_METRIC` |
| Skills | `SKILL_USE` |
| Snowflake Semantic | **仅 `ROOT` 中的指针行，不建表** |

保留 provider 名：`dbt`、`lookml`、`osi`、`sigma`、`skills`、`snowflake_semantic`、`user`。`user` 供客户发布自有 skill、元数据、查询配方或运维上下文。

每个 source 的摄取用 `CREATE OR REPLACE TABLE` **整体替换自己的表族**，各 provider 的 `ROOT` 行互不覆盖。规范要求消费方"把这些表当作生成的元数据，而非手工维护的状态"。

支持的类型只有四种：`VARCHAR`、`TEXT`、`BOOLEAN`、`VARIANT`（数组以 `PARSE_JSON` 写入）。目标仓库：Snowflake、Databricks、BigQuery。

### OSI 是规范内的一等公民

规范原文：**"OSI is the canonical semantic-layer source — other formats (e.g. LookML) are expected to reach `AGENTS.OSI_*` by being converted to OSI first."**

摄取时按内嵌的 OSI JSON schema（`osi-schema.json`，版本 **OSI 0.2.0.dev0**）校验每个文件，违规则带文件名与 JSON path 抛错，**绝不静默摄取部分或空模型**。多方言表达式与结构化 `ai_context` 保留不扁平化：表达式列存完整的 `[{dialect, expression}, …]` 为 `VARIANT`，`synonyms` 拆成可查询数组。

这条设计有战略含量：**dbt 自家的上下文标准把语义定义走 [[Open Semantic Interchange]] 格式，而非自家 MetricFlow YAML**。把语义分发标准让给厂商中立格式，是"开放数据基础设施"叙事的实质动作。

### 对原生语义层的克制

Snowflake Semantic provider 只在 `ROOT` 里写 `semantic_view/<全限定名>` 指针行，**不复制语义定义**。规范说明理由：语义定义（维度、指标、关系、查询行为）活在 Snowflake 里，应该在那里检视。

这是设计上的自我限制——承认原生语义层是权威源，自己只做发现层，不做镜像。对比 [[Atlan]] 摄取并在自己 graph 中重新表达他家定义的做法，取向不同。

### 摄取路径

规范列了四条：厂商托管管道、CI/CD 作业、定时工作流、平台工程作业。仓库提供的 dbt / LookML / OSI / skills GitHub reusable workflow **"是一条实现路径，不是要求"**。

典型流程：仓库中的 workflow 调用本仓 workflow → checkout 并读取源元数据（dbt artifacts、LookML 文件、OSI YAML）→ 在锁定的 release tag 上运行 `agents-schema` CLI → CLI 把规范化元数据与仓库交付的 skills 写入 `AGENTS` schema → agent 与下游工具查询 `AGENTS` 获取贴近数据的上下文。

### 跨源查询

官方举出的核心价值场景：从 `LOOKML_VIEW.sql_table_name` 关联到 `DBT_MODEL`，把 BI 面向的对象连回建模的仓库表。规范诚实标注这个 join **"是有意做成启发式的"**（LookML 的 `sql_table_name` 是自由 LookML 文本），只能作为 agent 的定位查询。

`DBT_DEPENDENCY` 支持递归遍历求完整血缘：

```sql
WITH RECURSIVE lineage AS (
  SELECT downstream_id AS node_id FROM AGENTS.DBT_DEPENDENCY
  WHERE upstream_id = 'source.my_project.raw.account'
  UNION ALL
  SELECT d.downstream_id FROM AGENTS.DBT_DEPENDENCY d
  JOIN lineage l ON d.upstream_id = l.node_id
)
SELECT DISTINCT m.name, m.schema_name, m.description
FROM lineage JOIN AGENTS.DBT_MODEL m ON m.unique_id = lineage.node_id;
```

## 限制与边界

**权限模型是最大缺口，且与营销口径不符。** 新闻稿称 Agents Schema "在既有安全与治理策略内工作"，但规范的 Permissions 一节写的是：`AGENTS.ROOT` 与所有 source 表**"应该对任何运行分析或 agentic 查询的仓库主体可读"**，只有写权限需严格控制。

这与 [[Genie Ontology]]、[[Atlan]] 的逐来源 ACL 执行是**性质不同**的模型。数据本身确实不动（只写元数据），但**业务逻辑、指标口径、血缘、所有者信息、以及 skill 里的运营指令对全体查询者可读**。在指标定义本身敏感的场景（如未公开的财务口径、定价逻辑），这是实质风险。"在既有治理策略内工作"的准确含义是"不绕过数据的访问控制"，不是"元数据也受同等保护"。

**dbt provider 不含 MetricFlow 语义层内容。** 营销材料（Fivetran 博客、opendatainfrastructure.com）称 dbt provider 提供 "models, columns, descriptions, tests, lineage, and **semantic-layer metrics**"，但规范里 dbt 表族只有三张表，读的是 `manifest.json` 中 `resource_type = 'model'` 的节点。语义层内容的规范路径是 `AGENTS.OSI_*`。这意味着 dbt 语义层用户想让指标进 Agents Schema，需要先转成 OSI——这条转换链的成熟度未见说明。

其他边界：

- **只是发现层，不是执行层**。它告诉 agent 定义是什么，不保证 agent 按定义正确执行。对比 MetricFlow（编译保证聚合正确）、Metric Views（查询时确定性编译），Agents Schema 在正确性上不提供任何保障——agent 拿到上下文后仍是自由写 SQL。这是它与语义层的根本分工，也是它的天花板。
- **元数据是生成的快照，不是活状态**。`CREATE OR REPLACE TABLE` 整表替换 + 由外部 workflow 触发，意味着新鲜度取决于 CI 频率。对比 Genie Ontology 的"每次有东西变化就自动重建"活图谱，这里是批量刷新语义。
- **为 LLM 放弃强 schema 的代价**：`ROOT.content` 自由文本、允许改变形状，使确定性程序难以可靠消费。这个标准明确只服务 agent。
- **provider 覆盖面有限**：当前只有 dbt、LookML、OSI、Sigma、Snowflake Semantic 指针。Databricks Metric Views、Cube、AtScale 等均无 provider。
- **规范处于 pre-1.0**（内嵌 OSI schema 为 0.2.0.dev0），变动会很快。
- **跨源关联是启发式的**，规范自己承认 LookML↔dbt 的 join 不可靠。
- **"免除新基础设施"的说法需限定**：不需要新的元数据服务，但需要 CI/CD 管道、写权限的 service principal、以及数仓中的额外 schema 与存储。

## 待调研问题

- [ ] 元数据全员可读这一点在企业采纳中是否成为阻碍；是否有按 provider/表分权的演进计划
- [ ] MetricFlow 定义 → OSI → `AGENTS.OSI_*` 的转换链是否可用、覆盖度如何
- [ ] 与 MCP 的实际分工：官方称"MCP 只是一个选项，CLI 和 skill 也行"，但 agent 发现 `AGENTS` schema 存在的引导机制是什么
- [ ] skills 机制的治理：谁能发布 skill、如何评审、错误或恶意 skill 的影响面（`uses` 是加性声明而非强制边界）
- [ ] 采纳度：除 dbt/Fivetran 自身外是否有第三方 provider 实现或客户案例
- [ ] Databricks、Snowflake 是否会支持向 Agents Schema 发布（Snowflake 目前只是被动的指针行）
- [ ] 与 [[Genie Ontology]] 的实际重叠：在同一个 Databricks 环境里两者并存会怎样
- [ ] token 效率的宣称如何度量（官方称"通过更丰富上下文提升 token 效率"，机制未说明）

## 参考来源

- [[2026-07-31 dbt Labs - Agents Schema SPEC]]（官方规范，技术依据；含全文存档）
- [[2026-07-31 Fivetran - dbt Labs 合并完成与联合发布]]（官方定位与发布背景）
- [[2026-07-31 Peliqan - dbt 替代方案与锁定担忧]]（第三方概括）
- GitHub: `dbt-labs/agents_schema` · opendatainfrastructure.com/agents-schema

## 相关

- [[Fivetran + dbt Labs]]（公司页）· [[dbt Semantic Layer]]（同厂商的语义执行路线）
- 概念页：[[Context Layer]] · [[Open Semantic Interchange]] · [[Data Catalog]] · [[Data Lineage]] · [[Text2SQL]]
- 对位：[[Genie Ontology]] · [[Atlan]] · [[Snowflake Semantic Views]]
- 综合判断：[[上下文层的四条路线]]
