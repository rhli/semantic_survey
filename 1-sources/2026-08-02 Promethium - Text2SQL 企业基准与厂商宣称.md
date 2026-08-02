---
type: source
status: draft
url: https://promethium.ai/guides/enterprise-text-to-sql-accuracy-benchmarks-2/
source_type: blog
publisher: Promethium
published:
accessed: 2026-08-02
companies: []
topics: ["[[Text2SQL]]", "[[Semantic Layer]]"]
---

# Promethium: Text-to-SQL Accuracy — Enterprise Benchmark Reality vs. Vendor Claims

## 摘要

厂商博客，系统梳理学术基准与企业现实的差距：Spider 1.0 上 85–86% 的执行准确率在企业条件下崩塌，并给出"上下文层级越高、准确率越高"的分层数据。注意 Promethium 自己是语义层厂商，分层数据服务于其产品叙事，但引用的基准数字与学术一手来源一致。

## 要点

- Spider 1.0：fine-tuned GPT-4 约 83–86% EX；但库只有 10–20 张表、schema 干净——企业里几乎不存在这种条件。
- BIRD：GPT-4o 总体 **52.54%**（简单 56%、中等 35%、困难 41%）；人类专家 92–93%，是真实天花板。
- Spider 2.0 企业条件：Snowflake 上峰值 59%、多平台 38%、dbt 实现 40%；GPT-4o 总体掉到 **6%**（vs Spider 1.0 的 86%），o1-preview 21.3%。
- 上下文层级与准确率的对应（厂商模型，方向可信、具体数字待独立验证）：仅技术元数据 10–20%；+ 关系 20–40%；+ catalog 与定义 40–70%；+ 语义层 70–90%；+ 业务知识 90–99%。

## 关联

- "裸 schema 6–40%"区间的主要来源之一（与 [[2026-08-02 Spider 2.0 - 企业级 Text2SQL 基准]] 互证）。
- 支撑 [[BI 与 AI 场景的 User Story]] 故事 5 与 [[Semantic Layer]] 的准确率论据。
- **偏差提示**：Promethium 是语义层厂商，"语义层把准确率拉到 70–90%"是其产品主张；引用其分层数据时应标注厂商口径。
