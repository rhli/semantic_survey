---
type: company
status: draft
company: atlan
founded: 2019
hq: Singapore / 远程
capabilities: [data-catalog, lineage, governance]
ga_status: ga
---

# Atlan

元数据/catalog 厂商，2025–2026 年完成了自我重定位：**"Context Layer for AI"**。核心策略一句话：不与 dbt/Cube/LookML 竞争语义定义，而是**摄取**它们的定义、绑定治理元数据（ownership、certification、policy、lineage），通过 MCP 交付给一切 agent——"包一层"的平台打法。

## 语义层相关产品线

- **Enterprise Data Graph** — 400+ 连接器的统一元数据图谱（2025 年还是 80+，扩张极快）
- **Context Agents**（2026-06）— 自动读查询历史/血缘/列使用/BI 语义模型/文档，生成并持续更新业务上下文；宣称冷启动从 1 年+ 缩到 2 周，89% AI 生成上下文被评为不劣于人写
- **Context Engineering Studio**（2026-06）— 上下文的 SDLC：build-test-review-approve-deploy-learn，工件是版本化的 Context Repositories（含 soul.md、Skills、语义模型）
- **Context Lakehouse** — Iceberg-native、BYOC 的上下文持久化底座；MCP 读 + A2A 写回，上下文"复利"
- **Atlan MCP server** — hosted 远程端点（本地版已废弃）；工具面含 search_assets、traverse_lineage、update_assets、glossary CRUD、DQ 规则、query_asset

## 战略与路线观察

- **[[Context Layer]] 概念的主要推手**，叙事是"语义层服务 BI（这个指标什么意思），context layer 服务 agent（何时、如何、在什么规则下用）"——catalog 厂商对语义层浪潮的升维反击。这个五组件框架（语义层只是其一）是 Atlan 主导的营销概念，但方向判断被行业验证：Databricks 的 [[Genie Ontology]]、微软的 Fabric Ontology 都在向同一位置走。
- **它的真正赌注是"上下文可移植性 = 客户所有权"**：同一份上下文经 MCP 送达 Claude/Copilot/Cursor/Gemini/Cortex/Genie——平台越分裂，中立上下文层越有价值。这与平台厂商"上下文跟着数据走"形成结构性对立。
- **MCP 采用数据是它最硬的证据**：2025-09 以来客户采用 17x、月调用量 58x 增长，75+ 企业接入。说明"agent 消费治理上下文"已从叙事进入真实使用。
- 核心宣称需甄别：治理上下文使 AI SQL 准确率 +38%（自家 AI Labs，174 查询/522 次评估，未第三方验证）。
- 与平台既竞争又互补：给 Databricks Genie 喂跨平台上下文（"UC 治理湖内，Atlan 治理湖外"）。背书：Gartner MQ D&A Governance Leader（2026）；客户 Mastercard、Workday、GM。

## 时间线

- 2019：创立（catalog 赛道）
- 2025：重定位 Context Layer for AI
- 2026-06（Snowflake Summit）：Context Agents + Context Engineering Studio + MCP 扩容三连发
- 2026：Gartner MQ D&A Governance Leader

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
