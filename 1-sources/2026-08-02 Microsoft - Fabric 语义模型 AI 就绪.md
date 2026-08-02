---
type: source
status: draft
url: https://learn.microsoft.com/en-us/fabric/data-science/semantic-model-best-practices
source_type: docs
publisher: Microsoft
published:
accessed: 2026-08-02
companies: [microsoft]
topics: ["[[Semantic Model]]", "[[Text2SQL]]", "[[Context Layer]]"]
---

# Microsoft: Fabric 语义模型的 AI 就绪最佳实践（官方文档）

## 摘要

Microsoft Learn 官方文档：Power BI 语义模型要服务好 Fabric Data Agent 和 Copilot，必须配置 **Prep for AI** 三件套。关键架构事实：Data Agent 的 DAX 生成工具**只读语义模型元数据和 Prep for AI 配置**，完全忽略 agent 级指令——语义供给必须落在模型里，不能落在 prompt 里。

## 要点

- **Prep for AI 三件套**：AI Data Schemas（圈定 AI 可用的表/列/度量子集，降歧义）、AI Instructions（业务术语与解读指引）、Verified Answers（触发短语绑定特定可视配置，保证高频问题答案一致）。
- **Fabric IQ**：微软语境下的语义层总称 = semantic models + **Ontologies**（preview）。Data Agent 的数据源类型还包括 Graph（GQL，preview）与 Ontology（ontology-native，preview）——微软在语义模型之外另建本体层。
- 模型卫生直接决定 AI 输出质量：业务化命名、显式度量、文档化。
- 官方 checklist 明确：不要在 Data Agent 级写模型相关指令（会被 DAX 生成工具忽略）；agent 指令只用于跨源路由、格式、语气。

## 关联

- 支撑 [[Microsoft]] 公司页与 [[Microsoft Fabric]] 产品页。
- "DAX 生成工具忽略 agent 级指令"是一个有力的架构论据：**grounding 必须编译进语义工件，prompt 层的补丁不可靠**——与 [[Text2SQL]] 的三条 grounding 路线讨论直接相关。
