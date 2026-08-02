---
type: product
status: draft
company: microsoft
product: Microsoft Fabric (IQ / Data Agent)
capabilities: [semantic-modeling, metrics-layer, text2sql, governance]
ga_status: preview
---

# Microsoft Fabric（语义层相关部分）

## 定位与解决的问题

Fabric 是微软的统一数据平台；语义层相关部分统称 **Fabric IQ** = semantic models + Ontologies。解决的问题：让 Copilot 与 Data Agent 在企业存量 Power BI 语义模型上给出可信答案，同时为 agent 时代补一层本体。

## 核心概念与架构

- **Semantic models**：Power BI 的语义层工件（表、度量、关系、DAX）。Data Agent 查询它时走 **DAX 生成工具**，该工具只读模型元数据 + Prep for AI 配置。
- **Prep for AI 三件套**（Power BI Desktop / Service 里配置）：
  - **AI Data Schemas**：圈定 AI 可用的表/列/度量子集——降歧义的第一手段是**缩小可选空间**；
  - **AI Instructions**：业务术语、同义词、解读指引；
  - **Verified Answers**：触发短语绑定已验证的答案配置——高频问题的确定性兜底。
- **Fabric Ontology（preview）**：ontology-native 数据源，表达领域知识与实体关系，供 Data Agent 消费；另有 Graph 数据源（GQL，preview）。
- **Data Agent**：多源 agent（lakehouse SQL、KQL、语义模型 DAX、Ontology、Azure AI Search），agent 级指令只做跨源路由与格式——**模型相关的语义必须进 Prep for AI**。

## 语义模型的表达方式

DAX/MDX 血统的表格模型（tabular），TMDL 文本格式可版本化；Prep for AI 配置附着在模型上。Ontology 为独立工件（preview 阶段，表达力待观察）。

## 限制与边界

- 深度绑定微软生态（Fabric 容量、Power BI、M365）；跨平台消费语义模型依然困难——OSI（[[Open Semantic Interchange]]）成员名单里**没有微软**，值得注意。
- Prep for AI 是"给存量模型打补丁"路线：治理上限取决于原模型质量，对烂模型是放大器而非修复器。
- Ontology 与 Graph 均 preview，表达力与稳定性未经验证。

## 待调研问题

- [ ] Fabric Ontology 的建模方式与 [[Genie Ontology]]、[[Looker]] Knowledge Catalog 的三方对比
- [ ] verified answers 与 Snowflake verified queries 的机制异同（[[Cortex Analyst]]）
- [ ] 微软缺席 OSI 的真实原因与替代策略

## 参考来源

- [[2026-08-02 Microsoft - Fabric 语义模型 AI 就绪]]
