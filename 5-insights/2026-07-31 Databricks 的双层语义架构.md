---
type: insight
status: draft
date: 2026-07-31
topics: ["[[Semantic Layer]]", "[[Context Layer]]", "[[Text2SQL]]", "[[Semantic Model]]"]
---

# Databricks 的双层语义架构：确定性内核 + 概率性外壳

## 判断

Databricks 并行运行着两套性质不同的语义系统，二者的分工是理解其语义层战略的关键，也可能是这个赛道上一个更普遍的架构范式。

**确定性内核** = [[Unity Catalog]] Metric Views。人工治理、YAML 声明、查询时确定性编译。覆盖面窄（只有被显式定义的指标），但在覆盖到的地方结果可复现、可审计、口径唯一。

**概率性外壳** = [[Genie Ontology]]。自动从表、查询、dashboard、管道以及 50+ 外部工具（Slack、Jira、Drive、SharePoint、Confluence、邮件、日历）抽取知识片段，构建持续更新的图谱。覆盖面极广（几乎所有留下痕迹的业务含义），但每条知识的可信度是概率性的。

两层的衔接点是 **OntoRank**——PageRank 式的权威度排序，加权因素包括来源类型、作者权威度、被依赖频率、与认证资产的关联紧密度、新鲜度。它的作用是让人工治理的定义**自动获得优先级**，而不需要显式的路由规则：认证过的 Metric View 权重最高被优先采信，Slack 里的讨论降级为佐证。

这个设计回答了语义层的一个长期两难：手工建模保证正确但覆盖不了企业的长尾语义，自动抽取覆盖广但不可信。Databricks 的答案不是二选一，而是**分层 + 用权威度排序做仲裁**。

## 为什么这值得单独记

同一个痛点（"建模成本高、覆盖不了长尾"），主要厂商给出了三种不同答案，这个对比比功能对照表有价值：

| 厂商 | 路线 | 终点状态 |
|---|---|---|
| Databricks | 人工定义 + 自动图谱**并存**，OntoRank 仲裁 | 两套系统长期共存 |
| Snowflake | Semantic View **Autopilot**：从 BI 使用模式自动生成定义，交人审核 | 仍是一套人工确认的定义 |
| Atlan | 摄取他家语义定义 + 绑定治理元数据，做跨平台 [[Context Layer]] | 一套跨平台的上下文层，不自产定义 |

Snowflake 的路线更保守也更可控：自动化用在**降低建模成本**上，最终事实源仍然唯一。Databricks 更激进：接受"大部分企业语义永远不会被显式建模"这一现实，转而用排序机制在混乱中找出最可信的那一条。

## 依据

- Metric Views 的确定性来自声明式定义 + 查询时编译，官方明确表述"每个消费者从同一定义得到同一结果"（[[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]）。
- Genie Ontology 的自动抽取范围、OntoRank 加权因素、"不需要团队手工策展"的定位（[[2026-07-31 Databricks - Genie One, Agents, Ontology 发布]]）。
- OntoRank 的 PageRank 类比与跨资产类型排序、输入来源清单、gated public preview 状态（[[2026-07-31 Datapao - Genie Ontology Explained]]）。
- Snowflake Autopilot 的路线对照（[[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]）。

## 这个架构的软肋

**外壳的质量完全依赖内核的存在**。第三方共识是：Ontology 只能映射已经存在于某处的含义，如果指标没定义、表没治理，它会忠实地把这团乱麻映射出来。所以"自动上下文层"不能代替治理工作，反而**依赖**认证 Metric View、glossary 这些高权威度锚点来校准排序。宣传口径容易让人误读为"不用做治理了"。

**确定性只覆盖了算法，没覆盖映射**。Metric Views 保证"营收这个指标怎么算"是确定的，但"用户这句话问的是哪个指标、哪些维度"仍是 LLM 的概率判断。竞品据此批评 Databricks "标准化了定义但没标准化执行"（[[2026-07-31 Colrows - Databricks Metric Views 的边界]]）——批评方有商业动机（自己卖"语义执行层"），但指出的架构分工是真实的。严格地说，Databricks 收窄了 LLM 的自由度，但没有消除它。

**内核本身也有确定性缺口**。fan-out 防护依赖 `rely.at_most_one_match` 声明且运行时不校验，声明错误会让 `SUM`/`COUNT` 静默返回错误结果；ABAC 策略不作用于 metric view；物化场景下源表不能带 RLS / 列掩码 / ABAC。所谓"确定性内核"是有前提条件的确定性。

## 置信度与待验证点

对"双层并存 + 权威度仲裁"这个架构描述本身，置信度**高**——三份材料（含官方一手）一致，且各自独立描述了两层的机制。

对"这是一个可推广的范式"这个判断，置信度**中**——需要看另外两个信号才能确认：

- [ ] Snowflake 是否会跟进类似的自动上下文层（若跟进，说明是趋势而非 Databricks 的独特选择）
- [ ] Genie Ontology 从 gated preview 走到 GA 后的实际效果，尤其 OntoRank 排序错误时的可干预性

对准确率证据，置信度**低**：官方 84.5% vs 52.4% 的对比基于 28 题内部题集、竞品匿名，只支撑"grounding 有显著提升"的定性结论，具体数字不可引用为事实。

## 后续动作

- [ ] 调研 Snowflake 时重点对比 Autopilot 与 Ontology 的路线差异，验证上面的三分法
- [ ] 调研 Atlan 时判断其 Context Graph 与 Genie Ontology 的真实重叠度（双方都自称互补）
- [ ] 若这个三分法成立，在 `4-comparisons/` 下沉淀独立对比页：语义层如何解决建模成本问题
