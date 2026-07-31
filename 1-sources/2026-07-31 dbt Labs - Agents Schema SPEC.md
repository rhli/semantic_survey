---
type: source
status: draft
url: https://github.com/dbt-labs/agents_schema/blob/main/SPEC.md
source_type: doc
publisher: dbt Labs
published: 2026-06
accessed: 2026-07-31
companies: [fivetran-dbt-labs]
topics: ["[[Context Layer]]", "[[Open Semantic Interchange]]", "[[Semantic Model]]", "[[Data Governance]]"]
archive: "[[2026-07-31 dbt Labs - Agents Schema SPEC (raw)]]"
---

# dbt Labs: Agents Schema 规范（官方 SPEC）

## 摘要

Agents Schema 的 schema 契约文档。定义了 `AGENTS` schema 下的表结构、provider 注册机制、以及各元数据来源的规范化映射。读完 SPEC 会发现它与官方营销口径有两处实质差异——见下方"要点"末尾两条。

## 要点

**核心设计**：所有表位于名为 `AGENTS` 的 schema。写入时用非引号标识符，故 Snowflake 中实际存为大写 `AGENTS.*`。支持的类型只有四种：`VARCHAR`、`TEXT`、`BOOLEAN`、`VARIANT`（数组以 `PARSE_JSON` 写入）。

**`AGENTS.ROOT` 是 provider 注册表**，也是整个设计最巧的一处：

```sql
CREATE TABLE AGENTS.ROOT (
  provider    VARCHAR NOT NULL,
  key         VARCHAR NOT NULL,
  content     TEXT NOT NULL,
  PRIMARY KEY (provider, key)
);
```

唯一硬约束是 `(provider, key)` 唯一，`content` 是自由文本。SPEC 明确说：**"预期消费者是 AI agent 而非需要固定 schema 的确定性应用"**，因此 content 可以半结构化、反规范化、自由格式，且允许随 provider 与模型演进而改变形状。约定是 key 与被文档化的表名（去前缀）对应，如 `(dbt, model)` 描述 `AGENTS.DBT_MODEL`。

**Skills 机制**（这条容易被忽略但很重要）：`AGENTS.ROOT` 不只存元数据，还存**给 agent 读的指令**。约定 `key = skill/<name>`，`content` 为 markdown 正文，可带 YAML frontmatter，标准字段 `uses` 声明该 skill 可使用的 schema 与表：

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

`uses` 是**加性的、不表达排除**。解析结果落 `AGENTS.SKILL_USE`。CLI 每次运行会发布一个内置的 `skill/agents-schema-analyst`，按目标仓库匹配变体（Snowflake / Databricks / BigQuery）。

**当前的 provider 与表族**：

| Source | Tables |
|---|---|
| dbt | `DBT_MODEL`、`DBT_COLUMN`、`DBT_DEPENDENCY` |
| LookML | `LOOKML_VIEW`、`LOOKML_DIMENSION`、`LOOKML_MEASURE`、`LOOKML_EXPLORE` |
| OSI | `OSI_MODEL`、`OSI_DATASET`、`OSI_FIELD`、`OSI_METRIC`、`OSI_RELATIONSHIP` |
| Sigma | `SIGMA_DATA_MODEL`、`SIGMA_ELEMENT`、`SIGMA_COLUMN`、`SIGMA_METRIC` |
| Skills | `SKILL_USE` |
| Snowflake Semantic | **仅 `ROOT` 中的指针行，不建表** |

保留的 provider 名：`dbt`、`lookml`、`osi`、`sigma`、`skills`、`snowflake_semantic`、`user`。

每个 source 的摄取用 `CREATE OR REPLACE TABLE` **整体替换自己的表族**，然后插入解析结果。SPEC 明确要求消费方"把这些表当作生成的元数据，而非手工维护的状态"。各 provider 的 `ROOT` 行互不覆盖。

**OSI 是规范内的一等公民**。SPEC 原文：**"OSI is the canonical semantic-layer source — other formats (e.g. LookML) are expected to reach `AGENTS.OSI_*` by being converted to OSI first."** 摄取时按内嵌的 OSI JSON schema（`osi-schema.json`，版本 **OSI 0.2.0.dev0**）校验每个文件，违规则带文件名与 JSON path 抛错，**绝不静默摄取部分或空模型**。多方言表达式与结构化 `ai_context` 保留不扁平化：表达式列存完整的 `[{dialect, expression}, …]` 为 `VARIANT`。

**Snowflake Semantic 的处理方式体现了设计上的克制**：只在 `ROOT` 里写 `semantic_view/<全限定名>` 指针行，不复制语义定义。SPEC 说明理由——语义定义（维度、指标、关系、查询行为）活在 Snowflake 里，应该在那里检视。

**跨源查询**是官方举出的核心价值场景，例：从 `LOOKML_VIEW.sql_table_name` 关联到 `DBT_MODEL`。SPEC 诚实地标注这个 join **"是有意做成启发式的"**，因为 LookML 的 `sql_table_name` 是自由文本，只能作为 agent 的定位查询。

**摄取路径不限于 GitHub Actions**。SPEC 列了四条：厂商托管管道、CI/CD 作业、定时工作流、平台工程作业。本仓库提供的 dbt / LookML / OSI / skills GitHub reusable workflow "是一条实现路径，不是要求"。

---

**与官方营销口径的两处差异（重要）**：

1. **dbt provider 不含 MetricFlow 语义层内容**。营销材料（Fivetran 博客、opendatainfrastructure.com）称 dbt provider 提供"models, columns, descriptions, tests, lineage, and **semantic-layer metrics**"，但 SPEC 里 dbt 表族只有 `DBT_MODEL`、`DBT_COLUMN`、`DBT_DEPENDENCY` 三张，读的是 `manifest.json` 中 `resource_type = 'model'` 的节点。语义层内容的规范路径是 `AGENTS.OSI_*`。也就是说 **dbt Labs 自家的上下文标准把语义定义走 OSI 格式而非自家 MetricFlow YAML**——这是个很强的战略信号。
2. **权限模型远弱于宣传口径**。新闻稿称 Agents Schema "在既有安全与治理策略内工作"，但 SPEC 的 Permissions 一节写的是：`AGENTS.ROOT` 与所有 source 表**"应该对任何运行分析或 agentic 查询的仓库主体可读"**，写权限才需严格控制。这与 [[Genie Ontology]]、[[Atlan]] 的逐来源 ACL 执行是**性质不同**的模型——元数据实质上对全体查询者可读。数据本身不动，但业务逻辑、指标口径、血缘、所有者信息会被广泛暴露。

## 关联

- [[Agents Schema]] 产品页的技术依据。
- 官方定位与发布背景见 [[2026-07-31 Fivetran - dbt Labs 合并完成与联合发布]]。
- "OSI 为规范内 canonical 格式" 这条是 [[Open Semantic Interchange]] 概念页的关键证据，也侧面印证了 [[2026-07-31 Datus - OSI 与 MetricFlow 的分层关系]] 中"OSI 是规范层、MetricFlow 是执行层"的判断。
- 权限模型的差异是 [[上下文层的四条路线]] 中评估 Agents Schema 的核心扣分项。
- 全文存档：[[2026-07-31 dbt Labs - Agents Schema SPEC (raw)]]（规范处于 0.2.0.dev0 前期，变动会很快，存档为核对基线）
