---
type: source
status: draft
url: https://community.databricks.com/t5/data-engineering/abac-policies-not-working-on-metric-views/td-p/155791
source_type: docs
publisher: Databricks Community（官方论坛）
published:
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Data Governance]]"]
---

# Databricks Community: ABAC 策略不作用于 Metric Views

## 摘要

社区问答（已标记 Solved）：用户在事实表上成功应用了 ABAC（属性级访问控制）策略，但查询以该表为源的 metric view 时策略未生效。

## 要点

- 官方确认这是**已知限制**：ABAC 策略不能直接应用于 view；metric view 本质是一种特殊的 view（`CREATE VIEW ... WITH METRICS`），因此同样受限。
- 意味着"Metric Views 完整继承 Unity Catalog 治理"这一说法需要限定——ABAC 这一维度存在缺口。行级安全和列级脱敏在未物化场景下可继承（另见 [[2026-07-31 Databricks - Materialization for Metric Views]]：物化场景下源表不能带 RLS/列掩码/ABAC）。

## 关联

- [[Unity Catalog]] 的治理边界；是对官方"治理复用 UC"叙事的具体修正。
- 一手证据（官方论坛且已确认），可信度高，但需持续跟踪——这类限制可能在后续版本解除。
