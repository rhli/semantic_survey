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

Snowflake 的托管 text2sql 服务：REST API 形态，业务用户用自然语言查询，返回 grounding 在 [[Snowflake Semantic Views]] 上的 SQL 与结果。战略卖点"智能留在平台内"：无外部 API 调用、无中间件延迟、权限继承自数仓。

## 核心概念与架构

- **模型组合**：Snowflake 托管的 Mistral Large + Codestral + Llama，按查询动态选最佳组合——用户不选模型，Snowflake 对准确率负责。
- **计费按消息而非 token**——把"一次问答"定价，成本可预测，也隐含 Snowflake 对单次问答成功率的信心。
- ** grounding 三件套**：语义视图的描述、同义词、预定义 join 路径 + **verified queries**（已验证的问题-SQL 对）。注意机制细节：Cortex Analyst 读语义视图的**元数据**来指导 SQL 生成，**并不直接查询语义视图本身**——生成的 SQL 仍打在物理表上。
- **评测即产品**：Cortex Analyst evaluations——YAML 配置指定语义视图 + verified queries 作为 ground truth，跑评测算 `sql_correctness`。同一份 verified queries 既是生成时的引导示例、又是评测时的 ground truth，形成持续回归检测闭环。**这是目前各家中唯一内置的语义层准确率评测机制。**

## 准确率口径（引用必须带限定词）

- Snowflake 工程博客（内部 150 题集）：Cortex Analyst "90%+ SQL accuracy"；single-shot GPT-4o 同集 "plummeted to 51%"；宣称接近 2 倍于 SOTA LLM 单次生成。
- **全部为内部基准，未经独立验证**。对照公开基准（Spider 2.0 上 o1-preview 21.3%，见 [[2026-08-02 Spider 2.0 - 企业级 Text2SQL 基准]]）可知：90%+ 反映的是"语义视图治理良好 + 题目在覆盖域内"的条件概率，不是任意企业环境的期望准确率。

## 限制与边界

- 只在 Snowflake 生态内成立；语义视图缺失时无降级策略的官方说明（裸 schema 回退行为待验证）。
- 语义模型规模上限直接限制覆盖域（50–100 列建议值，见 [[Snowflake Semantic Views]]）——大宽域场景需要拆多个语义视图，多视图路由能力待调研。

## 待调研问题

- [ ] 多语义视图场景的路由与消歧
- [ ] 语义模型缺失/质量差时的行为
- [ ] evaluations 的 CI 集成方式（能否进 PR 流程）

## 参考来源

- [[2026-08-02 Snowflake - Cortex Analyst 评测与边界]]
- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
