---
type: company
status: draft
company: google
founded: 1998
hq: Mountain View
---

# Google（Looker / BigQuery）

语义层赛道里最容易被低估的玩家：[[Looker]] 的 LookML 是**现代语义层概念的发明者之一**（2012 年起），而 Google 正把它重建为 agentic BI 的地基。2026 年 Gartner ABI 魔力象限 Leader。

## 语义层相关产品线

- [[Looker]] — LookML 语义层 + Conversational Analytics（GA）+ BI Agents + MCP
- BigQuery — 数仓侧，BigQuery Graph 提供图模型能力
- Knowledge Catalog（Preview）— 把元数据转成 semantic graph 供 agent 消费
- Gemini Enterprise — agent 统一入口，Looker agent 可发布进去

## 战略与路线观察

- **"Agentic Data Cloud"**：BigQuery + Knowledge Catalog + Gemini + Looker（体验层）作为一个系统集成，而非四个产品。
- 最值得注意的一步：LookML 新增 **in-database 分析模型支持**，可叠加在 **Snowflake Semantic Views** 与 BigQuery Graph 之上——平台语义层之间开始出现真实的互操作，不是 OSI 纸面互操作。
- 与 Databricks/Snowflake 的差异：Google 的语义层叙事**从 BI 出发**（LookML 存量），而非从数仓治理出发。这决定了它的强项是消费体验与建模成熟度，弱项是"语义跟着数据走"的治理深度。

## 时间线

- 2012：Looker 创立，LookML 发明"建模即代码"的语义层范式
- 2020：Google 完成对 Looker 收购（$2.6B）
- 2025-11：Conversational Analytics GA
- 2026-04（Next '26）：BI Agents、LookML AI Agent、Knowledge Catalog 集成、in-database 模型支持发布

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
