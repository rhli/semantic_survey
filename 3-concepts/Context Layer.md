---
type: concept
status: draft
aliases: [上下文层, 语境层]
---

# Context Layer

## 定义

位于企业数据系统与 AI agent 之间的治理层，把元数据翻译为 agent 可用的业务上下文：语义定义 + 血缘 + 策略 + ownership + 决策记忆。主张者称其为语义层的超集。

**注意：这是 Atlan 主导推广的概念**（2025–2026），有明显的厂商叙事成分，使用时需甄别哪些是真实架构需求、哪些是营销划界。

## 为什么重要

- 提出的真问题：语义层只回答"指标什么意思"，agent 还需要"何时、如何、在什么规则下能用"——权限、敏感度、来源可信度。
- 商业上是 catalog 厂商对语义层浪潮的反击：不做语义定义，而是摄取 dbt/Cube/LookML 的定义并"包一层"治理。

## 关键设计维度

四条路线在这几个维度上分歧最大，详见 [[上下文层的四条路线]]：

- **上下文存在哪 / 谁拥有**：厂商的服务（Atlan、Genie Ontology）vs **客户自己数仓里的表**（[[Agents Schema]]）
- **从哪来**：自动抽取（Genie Ontology）vs 自动生成交人审核（Snowflake Autopilot）vs 摄取他家定义（Atlan、Agents Schema）
- **冲突如何仲裁**：OntoRank 权威度排序（Databricks）vs 单一人工确认定义（Snowflake）vs **不做仲裁**（Agents Schema 明确只做发现层）
- **新鲜度语义**：活图谱持续重建 vs 连接器同步周期 vs CI/CD 触发的整表替换快照
- **元数据自身是否需要访问控制**：逐来源 ACL 执行（Genie Ontology、Atlan）vs 全体查询者可读（Agents Schema 规范要求）
- **交付协议**：MCP server 是主流形态，但 Agents Schema 主张裸 SQL 即可、MCP 只是可选项

Atlan 版五组件：semantic layer、ontology、operational playbooks、lineage、active metadata。

## 各家实现

- [[Atlan]]（Enterprise Data Graph + Context Engineering Studio）——跨平台 SaaS graph
- [[Genie Ontology]]（Databricks）——平台原生自动图谱 + OntoRank
- [[Agents Schema]]（Fivetran + dbt Labs）——开源标准，落客户数仓 SQL 表
- Alation AIOS、Promethium 360° Context Hub

## 一个共同的空白

四条路线都解决"上下文如何被表示、发现、分发"，**都不解决"上下文如何被验证并保持正确"**。一个完美可发现、完美可分发、但六个月无人维护的指标定义仍然是过时的。这是判断这个概念是否只是营销划界的一个有用切口。

## 相关概念

[[Semantic Layer]]、[[Data Catalog]]、[[Data Governance]]、[[Open Semantic Interchange]]、[[Text2SQL]]
