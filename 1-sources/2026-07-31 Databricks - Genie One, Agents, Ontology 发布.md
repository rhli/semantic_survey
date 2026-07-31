---
type: source
status: draft
url: https://www.databricks.com/blog/introducing-genie-one-genie-ontology-and-genie-agents
source_type: blog
publisher: Databricks
published: 2026-06
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Text2SQL]]", "[[Context Layer]]"]
---

# Databricks: Genie One、Genie Agents、Genie Ontology 发布

## 摘要

2026 年 6 月 Data + AI Summit 的 Genie 三件套发布公告（官方一手材料）。Genie 从"AI/BI 里的对话分析助手"扩展为三层产品：Genie One（业务用户的 AI coworker）、Genie Agents（可自治的领域 agent）、Genie Ontology（自动上下文层）。

## 要点

**问题陈述**：业务上下文散落在 dashboard、查询、管道、wiki、工单、文档、聊天里。AI 找不到就用推断填补空白，答案"好的情况下是泛泛而谈，坏的情况下是错的"。当代 agent 靠迭代探测（iterative probing）补偿，又慢又贵还牺牲质量。

**Genie One**：
- 通过原生连接器 + Lakehouse federation + Lakeflow Connect + 与 Gmail/Slack/Teams 的双向集成，跨整个数据资产取数并编排动作。
- 能力集：定时与告警、监控、文档生成、自定义 skills、自定义 MCP 支持。
- 交付面：原生嵌入 Slack 与 Microsoft Teams（可 @mention，支持公开频道和线程）、新的 iOS/Android app、Genie MCP App（让已有第三方 agent 调用 Genie 而不改工作流）。
- 客户引述：Uplight。

**Genie Agents**：
- 从 Genie Spaces 演进而来。官方称客户已创建**超过一百万个 Genie Spaces**。
- 可自治执行多步工作流（MCP 连接、定时任务、文档与产物生成、写回外部系统），"无需监督或干预"。
- 可 grounding 在文档、文件、知识源等非结构化数据上，不限于表和视图。
- 创建方式：在 Genie One 或 Genie Code 里一句 prompt 起一个 agent，然后限定范围、跑 benchmark、分享。
- 客户引述：Foot Locker。

**Genie Ontology**：
- 自动从表、查询、dashboard、管道、连接的应用中抽取知识片段，组织成"公司如何运作、数据到底意味着什么"的**活图谱**（living graph）。涵盖指标定义、业务术语、特殊计算口径，以及概念、指标、表、团队之间的关系。
- **权威度判定是核心创新**：用类似 PageRank 的方法加权——定义来自哪里、该来源作者的相对权威度、被依赖的频率、与认证及广泛使用资产的关联紧密度、新鲜度。Genie 从权重最高的来源作答。
- 按来源逐一执行权限，只展示用户本就有权查看的内容。官方定位：解决上下文问题而**不需要团队手工策展、也不需要维护另一套权限系统**。

**准确率 benchmark（需打折）**：Genie 首次回答正确率 84.5%，最强通用 coding agent 52.4%，最弱 25%；延迟为最强 coding agent 的 1/2。口径写明：Databricks 内部 benchmark，**28 题**真实数据分析题集，2026 年 6 月，竞品匿名。样本量极小，只能作方向性信号。

**治理**：每个答案默认按 source-native ACL 或 Unity Catalog 执行权限；MCP、工具与成本由 Unity AI Gateway 统一治理。

## 关联

- [[Genie]]（One + Agents）与 [[Genie Ontology]] 产品页的核心一手来源。
- Genie Ontology 是 [[Context Layer]] 叙事的平台原生版本，与 [[Atlan]] 的跨平台 context layer 构成直接对位。
- 厂商一手材料：功能声明可信；"无需手工策展"和 benchmark 数字需第三方验证（第三方视角见 [[2026-07-31 Datapao - Genie Ontology Explained]]）。
