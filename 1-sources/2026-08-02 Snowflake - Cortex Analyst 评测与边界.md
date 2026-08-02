---
type: source
status: draft
url: https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst-evaluations
source_type: docs
publisher: Snowflake
published:
accessed: 2026-08-02
companies: [snowflake]
topics: ["[[Text2SQL]]", "[[Semantic Model]]"]
---

# Snowflake: Cortex Analyst evaluations 官方文档 + 第三方边界分析

## 摘要

两条材料的合并笔记。(1) 官方文档：Cortex Analyst 自带**评测机制**——YAML 配置指定语义视图 + verified queries 作为 ground truth，跑评测算 `sql_correctness` 指标，用于发现回归、优化定义。(2) Colrows 第三方分析：Snowflake 宣称的 90%+ 准确率来自**内部 150 题集**，未独立验证；语义模型大小上限存在文档矛盾（1 MB vs 某 quickstart 写 2 MB）。

## 要点

- **评测即产品**：`sql_correctness` 是目前唯一支持的指标；verified queries（问题 + 标准 SQL）既是生成时的引导示例，又是评测时的 ground truth——同一份资产两用。
- 准确率口径（Snowflake 工程博客，内部 150 题）：Cortex Analyst "90%+ SQL accuracy"，对比 single-shot GPT-4o 在同一内部集上 "plummeted to 51%"；宣称"接近 2 倍于 SOTA LLM 单次生成"。**均为内部基准，未独立验证**；对照 Spider 2.0（o1-preview 21.3%）可知企业级真实难度。
- Cortex Analyst 模型组合：Mistral Large + Codestral + Llama，按查询选最佳组合；**按消息计费**而非按 token。
- 语义视图硬约束：YAML ≤ 1 MB（去除 sample values 后 32K token），建议总列数 50–100；无原生 cumulative/conversion 指标类型、无自动时间粒度。
- 2026-03-02 起语义视图可用标准 SQL + `AGG()` 查询，不再强制 `SEMANTIC_VIEW()` 子句——消费门槛显著降低。

## 关联

- 支撑 [[Cortex Analyst]] 与 [[Snowflake Semantic Views]] 深填。
- "verified queries 既引导生成又充当评测 ground truth"是评测闭环设计的最简形态，直接回应本库此前关于"语义建模如何量化评测"的讨论——Snowflake 把答案做成了产品功能。
