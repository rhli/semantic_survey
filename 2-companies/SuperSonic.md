---
type: product
status: draft
company: tencent-music
product: SuperSonic
capabilities: [text2sql, semantic-modeling, headless-bi, governance]
ga_status: ga
---

# SuperSonic

## 定位与解决的问题

腾讯音乐开源（Java）的 ChatBI + Headless BI 融合框架。核心思想：ChatBI 必须访问与传统 BI 相同的治理语义模型，语义层为 LLM 提供上下文减少幻觉。Tier 2 调研对象，但因开源可读源码 + S2SQL 设计对理解 [[Text2SQL]] grounding 架构很有价值。

## 核心概念与架构

处理链路：Knowledge Base → Schema Mapper（语义实体识别）→ Semantic Parser（NL → S2SQL）→ Semantic Corrector（合法性校验修正）→ Semantic Translator（S2SQL → 物理 SQL）。

- **S2SQL**：中间语义查询语言（`SELECT 维度名, SUM(指标名) FROM 模型名`）。LLM 生成 S2SQL 而非物理 SQL，确定性翻译器落地——把 LLM 自由度限制在语义模型内。是 grounding 三条路线中"中间语义语言"路线的代表。
- 语义建模对象：指标/维度/实体/标签及业务含义与关系；不搬运数据。
- 内置规则 parser（免 LLM 场景）；三级权限（数据集/列/行）；Java SPI 插件化。

## 待调研问题

- [ ] S2SQL 语法全貌与表达力边界（源码）
- [ ] Semantic Corrector 的修正策略
- [ ] 多轮对话与消歧实现
- [ ] 社区活跃度与版本演进（tencentmusic/supersonic vs supersonicbi/supersonic 两个 repo 的关系）

## 参考来源

- [[2026-07-31 TencentMusic - SuperSonic README]]
