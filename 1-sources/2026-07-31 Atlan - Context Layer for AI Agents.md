---
type: source
status: draft
url: https://atlan.com/know/context-layer-for-ai-agents/
source_type: blog
publisher: Atlan
published:
accessed: 2026-07-31
companies: [atlan]
topics: ["[[Context Layer]]", "[[Data Catalog]]", "[[Text2SQL]]"]
---

# Atlan: Context Layer for AI Agents 企业指南

## 摘要

Atlan 的定位宣言：context layer 位于企业数据系统与 AI agent 之间，把原始元数据翻译成治理的业务含义（指标定义、跨系统实体身份、策略执行、来源追溯）。语义层只是 context layer 五个组件之一。

## 要点

- Context layer 五组件：semantic layer（治理指标）、ontology（实体关系）、operational playbooks（路由）、lineage（溯源）、active metadata（决策记忆）。
- 与语义层的边界话术：语义层回答"这个指标什么意思"（服务 BI），context layer 回答"AI agent 何时、如何、在什么规则下能用"（服务 agent）。
- 核心数据宣称：Atlan AI Labs 测试，治理上下文使 AI SQL 准确率提升 38%（174 查询 / 522 次评估）。
- 产品要素：Enterprise Data Graph（80+ 连接器）、Context Engineering Studio、Semantic View Generator、Atlan MCP server（暴露给 Claude/Cursor/ChatGPT 等）。
- 策略：不替代 dbt/Cube/LookML 的语义定义，而是摄取它们并绑定治理元数据（ownership、certification、policy tags、lineage）——"包一层"的平台打法。
- 背书：Gartner MQ for D&A Governance Leader（2026）。

## 关联

- [[Atlan]] 公司页核心来源；[[Context Layer]] 概念页的定义出处（注意这是 Atlan 主导的营销概念，需甄别）。
- 与 [[Semantic Layer Vendor Landscape]] 中"阵营 3"的切入逻辑对应。
