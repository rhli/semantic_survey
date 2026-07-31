---
type: product
status: draft
company: databricks
product: AI/BI Genie
capabilities: [text2sql]
ga_status: ga
---

# Genie

## 定位与解决的问题

Databricks 的自然语言数据查询产品（AI/BI Genie）。核心主张：NL 查询 grounding 在 [[Unity Catalog]] Metric Views 的治理定义上——"Genie 不再幻觉指标，而是从单一事实源解析指标"。

## 核心概念与架构

- **Genie space**：可直接建在 Metric Views 上的问答空间；NL 查询被解析为对语义对象的引用，运行时确定性编译（[[Text2SQL]] 三条路线中的"声明式定义 + 查询时编译"）。
- **Genie Ontology**：官方称"持续学习的企业上下文层"——自动从表、dashboard、查询、连接应用中抽取业务含义，Unity Catalog Metrics 提供人工治理的定义输入。是 [[Context Layer]] 叙事的平台原生版。
- **Genie Code**：NL 辅助编写 metric view 定义（编辑器内 copilot）。

## 待调研问题

- [ ] Genie 的 NL 解析架构：直接 SQL 生成 vs 语义对象引用的比例与回退策略
- [ ] Genie Ontology 的学习机制与人工治理定义的合并逻辑
- [ ] 准确率评测与消歧/澄清交互设计
- [ ] 定价与开通条件

## 参考来源

- [[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]
