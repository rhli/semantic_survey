---
type: company
status: draft
company: cube
founded: 2019
hq: San Francisco
---

# Cube Dev

独立语义层厂商，API-first/headless 路线的代表。2018 年起源于"让 Slack chatbot 返回一致答案"的项目——从第一天就是为应用和 agent 服务，不是为 dashboard。

## 语义层相关产品线

- [[Cube]] — Cube Core（开源语义层，Apache 2.0）+ Cube 商业版（agentic analytics 平台）

## 战略与路线观察

- 2026 年叙事从 headless BI 升级为 agentic analytics platform：语义层是地基，上面长出 Analytics Chat、workbooks、embedded 等。
- 开源策略清晰：Core 永久开源，商业版加平台能力（多租户、RBAC、托管、BI 集成），两者模型完全兼容。
- 标杆客户案例：Brex（弃 dbt SL 和 LookML 选 Cube）。
- OSI working group 成员。

## 时间线

- 2018：起源项目
- 2026：Cube Core 重新定位为"open-source semantic layer for AI, BI and embedded analytics"

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
