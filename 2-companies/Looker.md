---
type: product
status: draft
company: google
product: Looker (LookML)
capabilities: [semantic-modeling, metrics-layer, text2sql, headless-bi, governance]
ga_status: ga
---

# Looker

## 定位与解决的问题

BI 原生语义层的鼻祖与最成熟实现。LookML 在 2012 年就确立了今天的行业标准做法：**指标与 join 逻辑写成代码、Git 管理、所有消费方共享同一模型**。2026 年的问题变成了：这套为 dashboard 发明的语义层，如何成为 agent 的地基。

## 核心概念与架构

- **LookML**：声明式建模语言——view（表/派生表）、explore（join 组合）、measure/dimension。fan-out 防护是其原生设计：join 必须声明基数关系（`one_to_many` 等），聚合对称化（symmetric aggregates）自动消除扇出错误——**比 MetricFlow 更早解决了 join 正确性问题**，且是引擎强制而非声明免责。
- **Conversational Analytics**（GA 2025-11）：Gemini 驱动的 chat-with-your-data，以 LookML 为唯一事实源；可发布进 Gemini Enterprise；有 API 可嵌入第三方应用。
- **BI Agents**（Next '26）：不止回答，触发下游动作、监控异常。
- **LookML AI Agent**：VS Code 扩展，NL 生成生产级 LookML——AI 不只消费语义层，也参与建模。
- **MCP 开放**：Looker MCP server 已被客户用于 Claude Desktop 等外部客户端（3000+ 用户案例）。
- 准确率工程靠模型内三样：descriptions、synonyms、**golden queries**（与 Snowflake verified queries、Microsoft verified answers 同一招）。

## 语义模型的表达方式

LookML 代码（非 YAML），Git 版本控制；2026 新增 in-database 分析模型支持——LookML 可叠加在 [[Snowflake Semantic Views]] 与 BigQuery Graph 之上消费。

## 限制与边界

- LookML 有学习曲线，建模权集中在数据团队——这是它治理强度的来源，也是它在"业务自助建模"上的短板。
- 语义层绑定 Looker 体验层；虽然 MCP/API 在开放，但最完整的价值仍在 Google 栈内。
- 准确率数字官方未公布基准细节，只有"高于典型方案"的定性表述。

## 待调研问题

- [ ] symmetric aggregates 与 MetricFlow 实体图、Databricks rely 的三方对比（[[Join 正确性的保证方式]] 待补这一家）
- [ ] Knowledge Catalog 的 semantic graph 与 [[Genie Ontology]] 的形态差异
- [ ] LookML on Snowflake Semantic Views 的实际工作方式（谁编译谁）

## 参考来源

- [[2026-08-02 Google - Looker Agentic BI 发布]]
