---
type: meta
created: 2026-07-31
status: draft
---

# Semantic Survey Vault 设计

关于企业语义层（Enterprise Semantic Layer）领域的长期调研知识库，基于 Obsidian。

## 1. 目的

- **长期知识库**：持续跟踪语义层领域的演进（厂商、产品、技术路线、概念），不是一次性报告。
- **两层深度**：既覆盖产品能力层面（功能、定位、客户场景），也覆盖技术机制层面（架构、API、语义模型表达方式、查询改写原理等）。
- **工作流**：AI 负责调研并生成初稿、维护结构纪律（frontmatter、双链、模板）；人负责修订内容、沉淀判断。

初始调研对象：Databricks（Unity Catalog、Genie）、Snowflake、Atlan、Aloudata。后续可扩展（dbt Semantic Layer、Cube、AtScale、Looker 等）。

## 2. 设计原则

1. **概念页是长期骨架**。公司会兴衰、产品会改名，但 Semantic Model、Metrics Layer、NL2SQL 这些概念是稳定的。概念页解释概念本身，并链接各家实现。
2. **产品页是一等公民**。Genie 和 Unity Catalog 各自成页，不埋在公司页里；通过 frontmatter 标注归属公司和能力维度。
3. **事实与观点分离**。
   - `1-sources/`：材料说了什么（带出处和日期）；
   - `2-companies/`：这家/这个产品实际是什么（关键论断链回 source）；
   - `5-insights/`：我认为这意味着什么。
   三者不混写，过时信息可甄别。
4. **来源可追溯**。每条关键论断能回答"什么时候、从哪篇材料得出"。

## 3. 目录结构

```
semantic_survey/
├── Home.md            # vault 入口：导航 + Dataview 总览
├── 0-inbox/           # AI 调研原始产出、剪藏，待整理
├── 1-sources/         # 来源笔记：每篇文档/博客/talk/论文一条
├── 2-companies/       # 公司页 + 产品页（平铺，不建子文件夹）
├── 3-concepts/        # 概念页
├── 4-comparisons/     # 横向对比页（能力矩阵、按维度对比）
├── 5-insights/        # 综合判断与洞见
├── _templates/        # Templater/核心模板
├── _meta/             # vault 自身的设计与约定文档（本文件）
└── _assets/           # 图片等附件
```

## 4. 笔记类型与 frontmatter schema

所有笔记必须有 `type` 和 `status` 字段。`status: draft`（AI 初稿，未经人工修订）/ `reviewed`（人工修订过）/ `stale`（已知过时，待更新）。

### source（来源笔记）

```yaml
type: source
status: draft
url: https://...
source_type: docs | blog | talk | paper | news | pricing
publisher: Databricks
published: 2025-06-01        # 原文发布日期，未知则留空
accessed: 2026-07-31         # 调研日期
companies: [databricks]      # 涉及的公司（小写 slug）
topics: ["[[Metrics Layer]]", "[[Text2SQL]]"]
```

正文结构：摘要（3–5 句）→ 关键内容摘录/转述 → 与本库其他笔记的关联。
命名：`YYYY-MM-DD <Publisher> - <标题短语>.md`，日期用 accessed 日期。

### company（公司页）

```yaml
type: company
status: draft
company: databricks          # 小写 slug，全库统一
founded: 2013
hq: San Francisco
```

正文：一句话定位 → 语义层相关产品线（链接产品页）→ 战略与路线观察 → 时间线（重要发布/收购，逐条链 source）。

公司即产品的单产品厂商（如 AtScale、Atlan）不强行拆两页：合并为一个 company 页，frontmatter 额外携带 `capabilities` 和 `ga_status`，保证 Dataview 按 capability 聚合时不遗漏。

### product（产品页）

```yaml
type: product
status: draft
company: databricks
product: Unity Catalog
capabilities: [data-catalog, governance, metrics-layer]
ga_status: ga | preview | beta
```

正文：定位与解决的问题 → 核心概念与架构（技术机制）→ 语义模型的表达方式（DSL/YAML/API 示例）→ 能力细节（按 capability 分节）→ 限制与边界 → 参考来源。

### concept（概念页）

```yaml
type: concept
status: draft
aliases: [指标层, metric layer]
```

正文：定义 → 为什么重要/解决什么问题 → 关键设计维度 → 各家实现对比入口（链接产品页，或嵌入 Dataview 查询）→ 相关概念。

### comparison（对比页）

```yaml
type: comparison
status: draft
dimension: metrics-layer     # 对比的能力维度或主题
companies: [databricks, snowflake, atlan, aloudata]
```

正文：对比结论先行 → 逐家分析 → 矩阵表格（尽量用 Dataview 自动聚合，主观评级列手工维护）。

### insight（洞见）

```yaml
type: insight
status: draft
date: 2026-07-31
topics: ["[[Metrics Layer]]"]
```

正文：判断/推论 → 依据（链接 source 和产品页）→ 置信度与待验证点。
命名：`YYYY-MM-DD <判断短语>.md`。

## 5. capability 受控词表

产品页 `capabilities` 字段只能取以下值（需要新值时先在此登记，并建对应概念页）：

| capability | 概念页 | 含义 |
|---|---|---|
| semantic-modeling | [[Semantic Model]] | 语义模型定义：实体、维度、关系的建模与表达 |
| metrics-layer | [[Metrics Layer]] | 指标定义、口径管理、指标 API |
| text2sql | [[Text2SQL]] | 自然语言查询 / conversational BI |
| data-catalog | [[Data Catalog]] | 元数据编目、搜索、资产管理 |
| lineage | [[Data Lineage]] | 血缘追踪 |
| governance | [[Data Governance]] | 权限、合规、访问控制 |
| query-rewrite | [[Query Rewrite]] | 语义查询到物理 SQL 的编译/改写 |
| acceleration | [[Query Acceleration]] | 物化、缓存、预计算 |
| headless-bi | [[Headless BI]] | 开放 API 供下游 BI/应用消费语义 |
| noetl | [[NoETL]] | 逻辑数据编织 / 虚拟化，免搬运 |

## 6. 语言与命名约定

- 正文中文；术语、产品名、专有名词保留英文（首次出现可加中文注释）。
- 文件名英文，自然写法（`Unity Catalog.md`、`Metrics Layer.md`），不用 kebab-case——文件名即双链显示名。
- 公司 slug 全库统一小写：`databricks`、`snowflake`、`atlan`、`aloudata`。
- 双链指向页面用 `[[Unity Catalog]]`；正文首次提及相关概念时就建链，宁多勿少。

## 7. Dataview 视图

依赖 Dataview 插件（需在 Obsidian 中安装）。核心视图：

- **Home.md**：全库统计、最近更新的笔记、`status: draft` 待修订清单。
- **概念页内**：按 capability 拉出实现该能力的产品列表
  （`TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "metrics-layer")`）。
- **公司页内**：该公司的产品列表、相关 source 列表。
- **对比页内**：能力矩阵的客观部分自动聚合，主观评级手工写。

## 8. 工作流

1. **调研**：AI 围绕一个主题/公司调研，原始产出落 `0-inbox/`。
2. **结构化**：AI 将产出拆为 source 笔记（进 `1-sources/`），并创建/更新对应产品页、公司页、概念页，建双链，全部标 `status: draft`。
3. **修订**：人审阅 draft 笔记，修订后改 `status: reviewed`。
4. **沉淀**：积累一定材料后，写对比页和 insight。
5. **维护**：发现过时信息标 `status: stale`，触发重新调研。

`0-inbox/` 中已完成结构化的原料即删除，inbox 保持趋近于空。

## 9. 初始种子内容（首批搭建范围）

- `Home.md` + 全部模板（6 类）+ 本设计文档。
- 概念页：先建受控词表中的 10 个概念页骨架 + `Semantic Layer` 总览页。
- 公司页：Databricks、Snowflake、Atlan、Aloudata 四个骨架。
- 产品页：Unity Catalog、Genie（Databricks）；Cortex Analyst、Semantic Views（Snowflake）；Atlan 平台；Aloudata 产品线（AnyMetrics 等，调研后定）。
- 对比页：先建 `Capability Matrix.md` 一个。
- 种子页先建骨架（frontmatter + 章节标题 + 待调研问题清单），内容由后续调研工作流填充。

## 10. 非目标

- 不做自动化剪藏管道（Readwise 之类），来源笔记由调研工作流产生。
- 不引入 Dataview 之外的复杂插件依赖（Templater 可选，核心模板够用）。
- 不预先穷举所有厂商，按调研节奏逐步扩展。
