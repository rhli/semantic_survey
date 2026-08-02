---
type: concept
status: draft
aliases: [OSI, Apache Ossie, 开放语义交换]
---

# Open Semantic Interchange

## 定义

厂商中立的语义定义交换标准（JSON / YAML 格式），Snowflake、Salesforce、dbt Labs 2025-09 发起，**2026 年进入 Apache 孵化器并更名 Apache Ossie (incubating)**——规范、社区、使命不变，治理从 Snowflake 主导的工作组转为 Apache 流程。核心产物为 dataset、field、relationship、metric、dialect、extension；转换器为 hub-and-spoke 架构（核心规范为枢纽，各厂商格式为辐条，避免两两互转）。

**它是规范层，不是运行时**——OSI 文档说明 `net_revenue` 是什么以及它与其他实体的关系，但自身不执行任何东西，也不提供查询接口，依赖消费工具。这是理解它与各家语义层引擎关系的关键。

> **修正记录**：本页先前写"以 MetricFlow 为声明式规范基础"，这是从 [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]] 抄来的过度表述。准确的关系是分层的：OSI 是**语义契约**，[[dbt Semantic Layer]] 的 MetricFlow 是**语义运行时**（消费定义、构建 dataflow DAG、产出仓库 SQL）。dbt Labs 的官方表述是 MetricFlow "作为 OSI 倡议的一部分维护"，且近期 dbt Core 版本能解析 OSI 文档与原生 dbt 语义并存，衔接点是 `semantic_manifest.json`。二者对齐但不在同一层。见 [[2026-07-31 Datus - OSI 与 MetricFlow 的分层关系]]。

## 为什么重要

- 回应"语义碎片化"：2026 年每家大厂各有一套语义层，同一企业可能维护 5+ 套互不兼容的 revenue 定义。OSI 提供第三条路：厂商中立定义，任何平台消费。
- Agent 时代的深层动因：agent 间协作需要可发现、可引用的标准化语义定义。
- 判断厂商开放性的试金石：跟踪各家的 OSI 适配进度（dbt converter 已合入；Cube、AtScale 为 working group 成员）。
- **已被当作规范内的 canonical 格式使用**。dbt 的 [[Agents Schema]] 规范明确写 "OSI is the canonical semantic-layer source"，其他格式（LookML 等）预期先转成 OSI 再进 `AGENTS.OSI_*`，且摄取时按内嵌 OSI JSON schema 严格校验、违规即抛错。**dbt 把自家上下文标准的语义路径让给 OSI 而非自家 MetricFlow YAML**，是目前最强的一个采纳信号。

## 参与方与时间线

- 2025-09：发布，Snowflake、Salesforce、dbt Labs 为创始方，Google 早期参与。
- 2026：进入 Apache 孵化器，更名 **Apache Ossie (incubating)**；官方成员名单 50+，含 Databricks、Atlan、AtScale、Cube、Denodo、DataHub、Collibra、Alation、ThoughtSpot、Omni、Lightdash、Sigma、Hex、Preset、RelationalAI、BlackRock、Mistral AI 等（完整名单见 [[2026-08-02 Apache - OSI 进入孵化器更名 Ossie]]）。
- **微软缺席**——名单上最显眼的空位，与 Fabric IQ 自建闭环的策略一致。
- 版本仍在 pre-1.0：Datus 材料称 0.1.x，Agents Schema 内嵌 schema 标为 **0.2.0.dev0**（时间点可能不同，均为 pre-1.0）。

关于"这是 Snowflake 的产品吗"这个常见质疑，有两个反驳：其一，**一个可移植性标准只有竞争平台都采纳才成立**，广泛参与是标准生效的前提条件；其二，进入 Apache 孵化器后治理已中立化——质疑在 2026 年后基本失去依据。

## 关键跟踪点

- [ ] 规范本身的表达力边界（哪些语义定义无法交换）——尤其 MetricFlow 的 `conversion`/`cumulative` 指标、Databricks 的 `one_to_many` join 能否无损映射
- [ ] 各 Tier-1 厂商的适配进度与真实互操作演示（而非"已加入"的表态）
- [ ] 与 MCP 的关系（定义交换 vs 运行时访问）；与 Agents Schema 的关系（交换格式 vs 仓内分发位置）
- [ ] 是否走到 1.0；OSI、Google OKF、Snowflake ARD、Databricks OpenSharing 之间哪个成为事实标准
- [ ] 一个反向风险：厂商各自的私有 ontology（如 [[Genie Ontology]]）扩散，可能让 OSI 停留在"指标定义交换"这个窄范围内

## 相关概念

[[Semantic Layer]]、[[Semantic Model]]、[[Metrics Layer]]、[[Context Layer]]

## 参考来源

- [[2026-08-02 Apache - OSI 进入孵化器更名 Ossie]]（更名与治理变更、50+ 成员名单）
- [[2026-07-31 Datus - OSI 与 MetricFlow 的分层关系]]（分层关系的核心依据）
- [[2026-07-31 dbt Labs - Agents Schema SPEC]]（OSI 作为 canonical 格式被使用的实证）
- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]（"OSI 以 MetricFlow 为规范基础"的错误来源，已修正）
- GitHub: `open-semantic-interchange/OSI`；官网已迁至 ossie.apache.org
