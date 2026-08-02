---
type: company
status: draft
company: cube
founded: 2019
hq: San Francisco
---

# Cube Dev

独立语义层厂商，API-first/headless 路线的代表。2018 年起源于"让 Slack chatbot 返回一致答案"的项目——**从第一天就是为应用和 agent 服务，不是为 dashboard**。这个出身决定了它与 dbt 的本质差异：dbt 从转换层长进语义层，Cube 从消费侧 API 长进语义层。

## 语义层相关产品线

- [[Cube]] — Cube Core（开源语义层，Apache 2.0）+ Cube 商业平台（D3，agentic analytics）

## 战略与路线观察

- **2026 年完成叙事切换**：从 "headless BI" 到 "agentic analytics platform built on a semantic layer"。语义层是地基（开源），上面长 Analytics Chat、workbooks、embedded（商业）。这是独立语义层厂商里最清晰的"开源获客 + 平台变现"结构。
- **接口面是六家里最宽的**：SQL（Postgres 线协议）、REST、GraphQL、MCP（2026-01 上线）、DAX/MDX——Power BI 可以像连 Analysis Services 一样连 Cube。它直接吃 AtScale 的存量兼容叙事，且开源。
- **与 dbt Semantic Layer 的对位是独立语义层的核心对决**：两者都跨仓、都 metrics-as-code-ish。分野在三点——Cube 拥有 serving 层（pre-aggregation 缓存是自己的引擎），dbt 不拥有执行层；Cube 按平台订阅收费，dbt 按 Queried Metrics 计量；Cube 的模型是 JS/YAML 独立仓，dbt 的模型与转换代码同仓。Brex 案例（弃 dbt SL 与 LookML 选 Cube）说明 serving 层与接口宽度在 agent 场景是决定性因素。
- OSI（[[Open Semantic Interchange]]）working group 成员。

## 时间线

- 2018：起源项目
- 2026-01-30：MCP server 上线（Claude/ChatGPT/Cursor/Codex 兼容）
- 2026-07：dbt Integration、Python Analysis 发布——**与 dbt 从竞争走向"模型可互操作"**，值得跟踪

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
