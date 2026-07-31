---
type: source
status: draft
url: https://github.com/tencentmusic/supersonic
source_type: docs
publisher: Tencent Music (open source)
published:
accessed: 2026-07-31
companies: [tencent-music]
topics: ["[[Text2SQL]]", "[[Headless BI]]", "[[Semantic Model]]"]
---

# SuperSonic：ChatBI + Headless BI 融合的开源框架

## 摘要

腾讯音乐开源的 AI+BI 平台，核心思想是把 Chat BI（LLM 驱动）和 Headless BI（语义层驱动）融合：ChatBI 访问与传统 BI 相同的治理语义模型，语义层为 LLM 提供上下文减少幻觉。

## 要点

- 架构组件：Knowledge Base、Schema Mapper（语义实体识别）、Semantic Parser（NL → S2SQL）、Semantic Corrector（合法性校验修正）、Semantic Translator（S2SQL → 物理 SQL）。
- **S2SQL 是关键设计**：一种中间语义查询语言（SELECT 维度名, SUM(指标名) FROM 模型名），LLM 生成 S2SQL 而非物理 SQL，再由确定性翻译器落到物理表——把 LLM 的自由度限制在语义模型内。
- 语义建模对象：指标/维度/实体/标签及其业务含义与关系，不搬运不复制数据。
- 内置规则 based parser（demo/测试场景免 LLM）；三级权限：数据集/列/行。
- Java SPI 插件化扩展；已在腾讯音乐内部产品验证。

## 关联

- [[SuperSonic]] 产品页核心来源。
- S2SQL 的"中间语义语言"路线与 Databricks Metric Views 的"声明式定义 + 查询时编译"、Cube 的语义 SQL 是 [[Text2SQL]] grounding 的三种代表性架构，值得专门对比。
- 背景材料：DataFunSummit 2024 罗雷演讲（腾讯音乐数据平台演进：先 HeadlessBI 解决口径治理，再 ChatBI 解决易用性）。
