---
type: company
status: draft
company: thoughtspot
founded: 2012
hq: Mountain View
capabilities: [text2sql, semantic-modeling, headless-bi]
ga_status: ga
---

# ThoughtSpot

搜索式 BI 的老兵（"Google for data" 的原始叙事），agentic analytics 转型最激进的 BI 厂商之一：Spotter 是其 AI agent 产品线。OSI（[[Open Semantic Interchange]]）成员。

## 语义层相关产品线

- Spotter — AI 分析 agent（NL 问答、自动洞察）
- ThoughtSpot 建模层 — worksheet/model 形态的语义层（搜索时代就为"任意问"设计，比 dashboard 时代的语义层更接近 agent 需求）
- Embedded analytics — 其传统强项

## 战略与路线观察

- **搜索式 BI 是 text2sql 的产品化先声**：ThoughtSpot 在 LLM 之前十年就在解"业务用户任意提问"的问题，其语义层设计（为开放域查询而非固定报表服务）天然更接近 agent 场景。这是它相对传统 BI 厂商的结构性优势。
- 短板同样明显：语义层绑定自家体验层，企业若已有 LookML/dbt 语义资产，ThoughtSpot 是"再建一套"而非"复用既有"。OSI 成员身份是它对这个短板的回应。

## 待调研问题

- [ ] Spotter 的 grounding 机制与准确率口径
- [ ] 语义建模层与 dbt/LookML 的互操作现状（OSI 落地进度）

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
