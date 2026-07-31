---
type: concept
status: draft
aliases: [数据治理, 访问控制]
---

# Data Governance

## 定义

数据的权限、合规与访问控制体系：行级安全、列级脱敏、对象级权限、审计日志、策略管理。

## 为什么重要

语义层的治理问题："业务用户/AI agent 通过语义层查数时，权限如何传导？"是平台原生方案的最强卖点——Databricks Metric Views 直接复用 Unity Catalog 的行级安全、列级脱敏和审计，治理不是外挂。独立语义层则需自建一套（Cube 的多租户 RBAC、AtScale 的行/列/对象级安全）。

## 关键设计维度

- 权限执行位置：数据平台层 vs 语义层 vs 消费层
- NL 查询的权限传导：agent 以谁的身份查？（runtime policy enforcement，Atlan 强调 per-query 执行）
- 三级控制粒度：数据集/列/行（SuperSonic 等的通用做法）

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "governance") SORT company ASC
```

## 相关概念

[[Data Catalog]]、[[Context Layer]]、[[Data Lineage]]
