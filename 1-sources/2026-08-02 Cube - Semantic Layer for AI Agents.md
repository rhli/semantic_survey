---
type: source
status: draft
url: https://cube.dev/articles/semantic-layer-for-ai-agents-2026
source_type: blog
publisher: Cube
published: 2026
accessed: 2026-08-02
companies: [cube]
topics: ["[[Semantic Layer]]", "[[Text2SQL]]", "[[Headless BI]]"]
---

# Cube: Semantic Layer for AI Agents (2026) + MCP Server changelog

## 摘要

Cube 官方的 2026 定位宣言：语义层是给 agent "理解力"的东西——裸表上 LLM 每次 prompt 重新推导 join、粒度、指标逻辑，同一问题返回不同答案；语义层定义一次，agent 从治理过的集合里选，行级安全在查询执行前生效。配合 2026-01-30 的 MCP server 上线，Cube 完成从 headless BI 到 "agentic analytics platform" 的叙事切换。

## 要点

- 架构分层：Cube Core（Apache 2.0 开源语义层）是地基；商业平台（D3）在其上加 Analytics Chat、workbooks、dashboards、embedded。
- **接口面是六件套**：SQL（Postgres 线协议）、REST、GraphQL、MCP、DAX/MDX（Power BI/Excel 以 Analysis Services 方式连入）。
- **Semantic SQL + E-Graphs**：agent 用的中间查询形态，runtime 作为 agent 与数仓之间的 guardrail。
- MCP server（2026-01-30 上线）：Claude/ChatGPT/Cursor/Codex 兼容，agent 发现语义模型、用 Semantic SQL 执行治理查询，**永不直接写裸表 SQL**。
- 安全在**编译期**生效（行级、多租户），不是运行期过滤。
- 客户证言：Brex 在 dbt Semantic Layer 和 LookML 之上选了 Cube，理由是"语义层让 AI 在规模化下可用"。
- 400+ 公司用 Cube 建内部 BI 与嵌入式分析。

## 关联

- 支撑 [[Cube]] 产品页深填与 [[Cube Dev]] 公司页。
- "六接口 + 编译期安全"是与 [[dbt Semantic Layer]]（计算 Queried Metrics 计费、不拥有执行层）对比的关键差异点。
