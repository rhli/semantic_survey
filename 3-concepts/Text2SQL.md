---
type: concept
status: draft
aliases: [NL2SQL, ChatBI, Conversational BI, 自然语言查询]
---

# Text2SQL

## 定义

用自然语言查询结构化数据的能力。2024 年后与语义层深度绑定：主流架构不再让 LLM 直接生成物理 SQL，而是 grounding 在治理的语义模型上。

## 为什么重要

这是 2026 年语义层市场的第一驱动力：所有厂商都在讲"语义层是让 AI 不产生幻觉指标的前提"。准确率数据（40% → 83%+）被全行业引用。

## 关键设计维度（grounding 架构的三条路线）

1. **中间语义语言**：LLM 生成受限的语义查询语句，确定性翻译器落到物理 SQL。代表：SuperSonic 的 S2SQL。
2. **声明式定义 + 查询时编译**：NL 解析为对语义对象（metric view / semantic view）的引用，引擎确定性编译。代表：Databricks Genie + Metric Views、Snowflake Cortex Analyst + Semantic Views。
3. **语义 API 消费**：agent 通过 MCP/SQL API 查询独立语义层。代表：Cube、dbt MCP server、AtScale MCP。

其他维度：多轮对话、消歧与澄清、准确率评测方法、权限如何随 NL 查询传导。

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "text2sql") SORT company ASC
```

## 相关概念

[[Semantic Layer]]、[[Semantic Model]]、[[Context Layer]]
