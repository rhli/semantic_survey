---
type: product
status: draft
company: databricks
product: Genie Ontology
capabilities: [semantic-modeling, data-catalog, lineage, governance]
ga_status: preview
---

# Genie Ontology

## 定位与解决的问题

Databricks 的自动上下文层，2026 年 6 月 Data + AI Summit 发布，状态为 **gated public preview**。它是 [[Genie]] One 与 Genie Agents 的底层能力。

它解决的是与 [[Unity Catalog]] Metric Views 不同的问题。Metric Views 解决"已经决定要治理的指标如何被确定性地计算"；Genie Ontology 解决"企业绝大多数业务含义**从未被显式定义过**，散落在 dashboard、查询、管道、wiki、工单、聊天里，AI 怎么找到并判断该信哪个"。

第三方一句话概括很准确：**不是你把上下文写进 Genie，而是 Genie 从你的组织已经产出的一切里读上下文。**

这与 Snowflake 的 Semantic View Autopilot 是两条不同的路。Autopilot 自动生成语义定义、交给人审核确认，终点仍是一套人工确认的定义；Genie Ontology 自动构建一层持续更新、按权威度排序的知识图谱，与人工定义**并存互补**。

## 核心概念与架构

### 输入来源（三类）

1. **Databricks 环境内**：表、查询、dashboard、管道、notebook、血缘、表与列的描述
2. **Unity Catalog**：认证的指标定义（即 Metric Views）、术语表（glossary）、语义标签
3. **50+ 外部连接器**：Slack、Jira、Google Drive、SharePoint、Confluence、邮件、日历（Gmail、Teams、Outlook、Calendar 连接于 2026 年进入 Beta）

### 知识抽取与图谱

从上述来源抽取"知识片段"（snippets of knowledge），组织成一张关于"公司如何运作、数据到底意味着什么"的**活图谱**（living graph），每次有东西变化就自动重建。图谱内容覆盖：

- 指标定义、业务术语、特殊计算口径
- 概念、指标、表、团队之间的关系

这是经典知识图谱意义上的 ontology，但特点是**自我维护**——不要求团队手工策展。

### OntoRank：权威度排序（核心创新）

自动抽取必然遇到冲突：同一个概念在 Slack 里、在某张 dashboard 里、在某个认证 Metric View 里各有一个定义，信哪个？OntoRank 是对这个问题的回答，思路借自 Google PageRank。

PageRank 通过分析指向网页的链接图来判断可信度；OntoRank 把同样的原理用于业务定义，且**跨越根本不同的资产类型**（表、文档、Slack 线程、dashboard）。加权因素：

- 定义来自哪里（来源类型）
- 该来源作者的相对权威度
- 被依赖 / 使用的频率
- 与已认证及广泛使用资产的关联紧密度
- 新鲜度

Genie 从权重最高的来源作答。实践含义是**认证过的 Metric View 会被优先采信，Slack 里的讨论只作为佐证**——这正是两套语义系统的衔接点：人工治理的定义通过权威度机制自动获得优先级，而不需要显式的路由规则。

### 权限模型

按来源逐一执行权限（source-native ACL 或 Unity Catalog），只展示用户本就有权查看的内容。官方强调的价值是：解决上下文问题**不需要维护另一套权限系统**，agent 也无法成为绕过访问策略的侧门。

### 消费面

Genie Spaces、Genie One、Genie Agents、Genie Code，以及通过 **MCP** 供外部工具使用。

## 能力细节

准确率数据来自 Genie 整体 benchmark（84.5% 首次正确率 vs 最强通用 coding agent 52.4%），口径为 Databricks 内部 28 题题集、竞品匿名——样本量过小，仅作方向性信号，详见 [[Genie]]。有第三方材料把这组对比表述为 text-to-SQL 准确率"从约 50% 提升到 84.5%"，该措辞未见官方原文支撑，引用需谨慎。

## 限制与边界

**最重要的一条（第三方共识）**：Ontology 只能映射**已经存在于某处**的含义。如果指标本身没定义、表没治理、没有约定的语义层，Ontology 会**忠实地把这团乱麻映射出来**。有材料引 CIO.com 的表述：ontology 不保证正确性，薄弱的基础数据"只会加速你既有的混乱"。

这条限制解释了 Databricks 产品组合的内在逻辑——Genie Ontology 不是 Metric Views 的替代品，恰恰**依赖** Metric Views、glossary、认证资产提供高权威度的锚点。把它当作"不用做数据治理了"的理由是误读。

其他边界：

- **平台绑定**：处理 lakehouse 原生上下文 + 50+ 连接器，但跨数据平台（Snowflake、BigQuery、SAP 等）的语义仍在其覆盖外。这正是 [[Atlan]] 等跨平台 context layer 的切入点，双方关系是互补的（Atlan 向 Genie 反馈跨系统上下文）。
- **状态为 gated public preview**，可用性与稳定性未经广泛验证。
- **OntoRank 的可解释性与可干预性未知**：当排序结果不符合预期时，数据团队能否干预、如何干预，公开材料未说明。这对生产采纳是关键问题。
- 自动抽取聊天与邮件内容涉及数据边界与合规问题，公开材料只强调权限继承，未讨论抽取范围的可控性。

## 待调研问题

- [ ] OntoRank 的权重计算是否可解释、可干预、可覆盖（override）
- [ ] "活图谱"的重建频率与增量机制；变更如何影响已有答案的一致性
- [ ] 认证 Metric View 在 OntoRank 中的具体优先级机制（硬优先还是加权）
- [ ] 抽取范围的可控性：能否排除特定来源 / 频道 / 目录；合规审计能力
- [ ] 与 Unity Catalog glossary、语义标签的具体协作方式
- [ ] MCP 暴露的上下文形态：外部 agent 拿到的是图谱查询接口还是打包的上下文
- [ ] gated preview 的开通条件与 GA 时间预期
- [ ] 与 [[Atlan]] Context Graph 在能力上的实际重叠与分工（双方都声称互补，需独立判断）

## 参考来源

- [[2026-07-31 Databricks - Genie One, Agents, Ontology 发布]]（官方一手来源）
- [[2026-07-31 Datapao - Genie Ontology Explained]]（第三方，输入清单与产品状态更具体）

## 相关

- [[Genie]] · [[Unity Catalog]]
- 概念页：[[Context Layer]] · [[Text2SQL]] · [[Data Catalog]]
- 综合判断：[[2026-07-31 Databricks 的双层语义架构]]
