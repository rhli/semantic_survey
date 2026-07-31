---
type: concept
status: draft
aliases: [语义模型, 语义建模]
---

# Semantic Model

## 定义

对业务领域的结构化描述：实体（entities）、维度（dimensions）、度量（measures）、关系（join/层级），以及业务含义元数据（display name、synonyms、描述）。是语义层的"数据结构"。

## 为什么重要

语义模型的表达能力和表达方式决定了语义层的上限：能否表达复杂指标（比率、派生、多级聚合）、能否防止 fan-out 错误、AI 能否读懂。

## 关键设计维度

- 表达载体：YAML（dbt、Databricks Metric Views、Snowflake）、代码（Cube 的 JS/YAML）、可视化画布（AtScale）、专用语言（LookML、Malloy）
- 建模范式：星型/雪花 join 建模 vs 明细级逻辑模型（Aloudata 的 NoETL 路线）
- 面向 AI 的元数据：synonyms、格式规则、示例问题（Databricks 称 agent metadata）
- 自动生成：Snowflake Semantic View Autopilot 从 BI 使用模式学习生成定义

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "semantic-modeling") SORT company ASC
```

## 相关概念

[[Semantic Layer]]、[[Metrics Layer]]、[[Query Rewrite]]
