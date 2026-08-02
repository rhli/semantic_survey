---
type: source
status: draft
url: https://cloud.google.com/blog/products/business-intelligence/looker-updates-for-agentic-bi-at-next26
source_type: blog
publisher: Google Cloud
published: 2026-04
accessed: 2026-08-02
companies: [google]
topics: ["[[Semantic Layer]]", "[[Text2SQL]]", "[[Headless BI]]"]
---

# Google Cloud: Looker updates for agentic BI at Next '26

## 摘要

Google Cloud Next '26 的 Looker 发布主线：把 Looker 重建为 Gemini 驱动的 agentic BI。核心论点与全行业一致——LookML 语义层是防止幻觉、保证单一事实源的地基。值得注意的差异化：LookML 开始支持**库内分析模型**，可叠加在 Snowflake Semantic Views 和 BigQuery Graph 之上。

## 要点

- **Looker BI Agents**：不止回答，还触发下游业务动作、监控异常，全部 grounding 在 LookML 语义层上。
- **Conversational Analytics** 已 GA（2025-11），新增推理与语义 grounding 升级、管理员端到端可观测；可直接发布进 Gemini Enterprise。
- **LookML AI Agent**：VS Code 扩展，自然语言生成生产级 LookML（"vibe-coding" 建模）。
- **Conversational Analytics API** GA：把治理过的对话体验嵌入第三方应用；MCP 支持开放 BI（客户已用 Claude Desktop + Looker MCP 服务 3000+ 用户）。
- **Knowledge Catalog 集成**（Preview）：Looker 把元数据转成 **semantic graph**，给 agent 提供自主完成任务的上下文。
- **Universal semantic layer**：LookML 新增 in-database 分析模型支持，可表达图模型与复杂语义本体（BigQuery Graph、Snowflake Semantic Views 之上）。
- 第三方观察（Rittman Analytics）："如果 LookML 不对，再多 prompt engineering 也修不好"——语义层成为 Looker/BigQuery 栈一切能力的地基。

## 关联

- 支撑 [[Looker]] 产品页与 [[Google]] 公司页。
- "LookML 叠加在 Snowflake Semantic Views 之上"是平台语义层开始互操作的首个重要信号，与 [[Open Semantic Interchange]] 的推进互为印证。
