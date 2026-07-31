---
type: concept
status: draft
aliases: [上下文层, 语境层]
---

# Context Layer

## 定义

位于企业数据系统与 AI agent 之间的治理层，把元数据翻译为 agent 可用的业务上下文：语义定义 + 血缘 + 策略 + ownership + 决策记忆。主张者称其为语义层的超集。

**注意：这是 Atlan 主导推广的概念**（2025–2026），有明显的厂商叙事成分，使用时需甄别哪些是真实架构需求、哪些是营销划界。

## 为什么重要

- 提出的真问题：语义层只回答"指标什么意思"，agent 还需要"何时、如何、在什么规则下能用"——权限、敏感度、来源可信度。
- 商业上是 catalog 厂商对语义层浪潮的反击：不做语义定义，而是摄取 dbt/Cube/LookML 的定义并"包一层"治理。

## 关键设计维度

- Atlan 版五组件：semantic layer、ontology、operational playbooks、lineage、active metadata
- 平台内对应物：Databricks 的 Genie Ontology（"持续学习的企业上下文层"）是同一叙事的平台原生版
- 运行时执行：per-query 策略执行、agent 身份、审计
- 交付协议：MCP server 是标准形态

## 各家实现

- [[Atlan]]（Enterprise Data Graph + Context Engineering Studio）
- Alation AIOS、Promethium 360° Context Hub
- [[Genie]]（Genie Ontology，平台内版本）

## 相关概念

[[Semantic Layer]]、[[Data Catalog]]、[[Data Governance]]
