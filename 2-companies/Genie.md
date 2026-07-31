---
type: product
status: draft
company: databricks
product: Genie (Genie One + Genie Agents)
capabilities: [text2sql]
ga_status: ga
---

# Genie

## 定位与解决的问题

Databricks 面向业务用户的 AI 产品线。起点是 AI/BI 里的对话分析助手，2026 年 6 月 Data + AI Summit 后扩展为三层结构：**Genie One**（业务用户的 AI coworker）、**Genie Agents**（可自治的领域 agent）、以及底层的 [[Genie Ontology]]（自动上下文层，单独成页）。

官方的问题陈述值得记录，因为它定义了整条产品线的设计前提：业务上下文散落在 dashboard、查询、管道、wiki、工单、文档、聊天里；AI 找不到就用推断填补空白，"答案好的情况下是泛泛而谈，坏的情况下是错的"；当代 agent 靠迭代探测（iterative probing）补偿，又慢又贵还牺牲质量。三件套是对"上下文而非模型能力是瓶颈"这一判断的产品化回答。

## 核心概念与架构

### Genie 与语义层的关系

Genie 是 [[Unity Catalog]] Metric Views 的主要 AI 消费方。Genie space 可以直接建在 Metric Views 上，此时自然语言查询被解析为对语义对象的引用、由引擎确定性编译，而非让 LLM 对裸 schema 自由生成 SQL。官方话术是"Genie 不再幻觉指标，而是从单一事实源解析指标"。

这属于 [[Text2SQL]] grounding 三条路线中的**声明式定义 + 查询时编译**路线（另两条是 SuperSonic 的中间语义语言、Cube 的语义 API 消费）。

需要注意其边界：Metric Views 保证"指标算法是确定的"，但"把这句自然语言映射到哪些指标和维度"仍是 LLM 的概率性判断。竞品据此批评 Databricks"标准化了定义但没标准化执行"（见[[2026-07-31 Colrows - Databricks Metric Views 的边界]]），这个批评有商业动机但指向真实的架构分工——详见 [[2026-07-31 Databricks 的双层语义架构]]。

### Genie One

定位"data-smart AI coworker"，已 GA。相对于原来的对话分析，扩展在两个方向：

**数据广度**：通过原生连接器、Lakehouse Federation、Lakeflow Connect，以及与日常业务工具（Gmail、Slack、Teams）的双向集成，跨整个数据资产取数并编排动作。

**从洞察到行动**：定时任务与告警、监控、文档生成、自定义 skills、自定义 MCP 支持。官方举的两个场景说明了产品意图——销售负责人让 Genie 结合日历、邮件与 Lakehouse 数据准备每日客户会议简报；总经理粘贴上月经营回顾文档，让 Genie 用最新库存数据和团队讨论记录更新它。

**交付面**：原生嵌入 Slack 与 Microsoft Teams（可 @mention，支持公开频道与线程）、iOS / Android app、以及 **Genie MCP App**——让已经采用或自建了 agent 的组织在不改工作流的前提下调用 Genie。最后这条是防守性设计：承认企业不一定以 Databricks 为 agent 入口。

### Genie Agents

从 Genie Spaces 演进而来（官方称客户已创建**超过一百万个** Genie Spaces，是这条产品线最有力的采纳度数据）。相对 Space 的两点升级：

- **自治执行**：使用与 Genie One 相同的工具集（MCP 连接、定时任务、文档与产物生成、写回外部系统）完成多步工作流，官方措辞是"无需监督或干预"。
- **可 grounding 在非结构化数据上**：文档、文件、知识源与结构化数据并列，覆盖真实业务问题的完整上下文。

创建方式是"一句 prompt 起一个 agent"：在 Genie One 或 Genie Code 里描述需求 → 限定范围（scope）→ 跑 benchmark → 分享给同事使用或二次定制。benchmark 环节是 Genie Space 时代就有的机制（curated、governed、verified logic and benchmarks），在 agent 化后保留下来，是它相对通用 coding agent 的差异点之一。

## 能力细节

### 准确率数据（需谨慎使用）

官方内部 benchmark：Genie 首次回答正确率 **84.5%**，最强通用 coding agent 52.4%，最弱 25%；延迟为最强 coding agent 的一半。

口径限定写得很清楚：Databricks 内部 benchmark、**28 题**真实数据分析题集、2026 年 6 月、竞品匿名。28 题的样本量不足以支撑精确到小数点的结论，且题集与评判标准均由厂商自定、竞品匿名无法复核。**只能当作方向性信号**——即"grounding 在治理语义与上下文图谱上，相对裸 agent 有显著提升"这个定性判断可信，具体数字不可引用为事实。

与之呼应的第三方口径：客户 iFood 称因更一致的预聚合数据而看到 Genie 准确率"有意义的提升"（无量化）。

### 治理

每个答案默认按 source-native ACL 或 Unity Catalog 执行权限；MCP、工具与成本由 **Unity AI Gateway** 统一治理，为管理员提供单一治理面板。这是平台原生 AI 产品相对外挂 copilot 的结构性优势：权限不需要第二套系统，agent 无法成为绕过访问策略的侧门。

## 限制与边界

- **平台绑定**：Genie 局限于 Databricks 生态。跨平台的企业上下文需要外部 context layer 补充（[[Atlan]] 正是以此定位切入，并已把 UC Metrics 纳入自己的 Context Graph）。
- **概率性组件不可消除**：语义层保证指标算法确定，NL → 语义对象的映射仍是 LLM 判断。
- **依赖上游治理质量**：Genie 的效果上限由 Metric Views 与 Ontology 的输入质量决定（详见 [[Genie Ontology]] 中"忠实映射乱麻"的问题）。
- **"无需监督或干预"的自治宣称缺乏第三方验证**，尤其在写回外部系统的场景下，风险边界与回滚机制未见公开材料。

## 待调研问题

- [ ] Genie 的 NL 解析架构细节：解析为语义对象引用 vs 直接生成 SQL 的比例、回退策略、消歧与澄清交互设计
- [ ] Genie Agents 的 benchmark 机制：如何定义、如何评分、能否防回归
- [ ] 自治写回外部系统的权限模型与审计（以谁的身份写、如何回滚）
- [ ] Genie space 建在 Metric Views 上 vs 建在裸表上的准确率差异（是否有公开数据）
- [ ] Genie MCP App 的接口形态与能力边界
- [ ] 定价模型与开通条件（三个产品分别计费方式）
- [ ] 与 Snowflake [[Cortex Analyst]] 的产品化路线对比：Genie 走完整产品 + 多端交付，Cortex Analyst 走 API-first 嵌入

## 参考来源

- [[2026-07-31 Databricks - Genie One, Agents, Ontology 发布]]（核心一手来源）
- [[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]（与 Metric Views 的整合）
- [[2026-07-31 Colrows - Databricks Metric Views 的边界]]（对 grounding 完整性的批评）

## 相关

- [[Genie Ontology]] · [[Unity Catalog]]
- 概念页：[[Text2SQL]] · [[Context Layer]]
