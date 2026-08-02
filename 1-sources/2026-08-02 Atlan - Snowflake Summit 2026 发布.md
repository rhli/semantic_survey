---
type: source
status: draft
url: https://atlan.com/snowflake-summit-2026-announcements/
source_type: blog
publisher: Atlan
published: 2026-06
accessed: 2026-08-02
companies: [atlan]
topics: ["[[Context Layer]]", "[[Data Catalog]]"]
---

# Atlan: Snowflake Summit 2026 发布（Context Agents / Studio / MCP）

## 摘要

Atlan 在 Snowflake Summit 2026 的三连发：Context Agents（自动生成业务上下文）、Context Engineering Studio（上下文的 SDLC 生命周期）、MCP Servers 扩容。配套数字：AI 生成的上下文 89% 被评为不劣于人写；上下文冷启动从 1 年+ 缩到 2 周；MCP 自 2025-09 以来客户采用 17x、月调用量 58x 增长。

## 要点

- **Context Agents**：读查询历史、血缘信号、列使用情况、BI 语义模型、文档，自动生成并持续更新上下文。
- **Context Engineering Studio**：给业务上下文配上 build-test-review-approve-deploy-learn 的软件工程生命周期——"上下文工程"被产品化为有版本的工件（Context Repositories，含 soul.md、Skills、语义模型）。
- **MCP 部署形态**：本地 MCP 已废弃，官方推荐 hosted 远程端点（mcp.atlan.com/mcp）——集中治理、实时读 REST API、无中间缓存。工具面含 search_assets、traverse_lineage、update_assets（写回描述/认证状态）、glossary CRUD、DQ 规则管理、query_asset。
- **Context Lakehouse**：Iceberg-native、BYOC 的持久化上下文底座；MCP + A2A 双协议——MCP 读、A2A 写回观察与质量信号，上下文"复利"。
- 连接器从 80+ 涨到 **400+**；MCP server 覆盖 Cortex、Genie、Claude 全系、Codex、Gemini、LangGraph 等运行时，75+ 企业在使用。
- 客户：Mastercard、Workday、GM、CME Group、HubSpot 等。

## 关联

- 支撑 [[Atlan]] 公司页深填；更新其产品线（Context Agents、Studio、Lakehouse 均为新增）。
- 与 [[Genie Ontology]] 对照：两家都在做"自动生成上下文"，Atlan 多了工程化生命周期（审核/部署/版本），Databricks 多了权威度排序（OntoRank）——[[上下文层的四条路线]] 可据此细化 Atlan 一条。
