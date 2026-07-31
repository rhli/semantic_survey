---
type: product
status: draft
company: snowflake
product: Cortex Analyst
capabilities: [text2sql]
ga_status: ga
---

# Cortex Analyst

## 定位与解决的问题

Snowflake 的 conversational analytics 能力（Cortex AI 家族），API-first：向应用嵌入 NL 数据问答。NL 查询 grounding 在 [[Snowflake Semantic Views]] 上。

## 核心概念与架构

- 直接在语义视图上推理，答案与 finance dashboard、报表用同一指标定义。
- 战略卖点是"智能留在平台内"：无外部 API 调用、无中间件延迟、无独立鉴权层。

## 待调研问题

- [ ] API 形态与嵌入方式（vs Genie 的产品化界面路线）
- [ ] 语义模型缺失时的行为（裸 schema 回退？）
- [ ] 准确率评测口径
- [ ] 与 Cortex 家族其他能力（Search、Agents）的组合关系

## 参考来源

- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
