---
type: source
status: draft
url: https://www.databricks.com/blog/redefining-semantics-data-layer-future-bi-and-ai
source_type: blog
publisher: Databricks
published: 2026
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Metrics Layer]]", "[[Text2SQL]]", "[[Semantic Model]]", "[[Open Semantic Interchange]]"]
---

# Databricks: Unity Catalog Business Semantics GA 并开源

## 摘要

Unity Catalog Business Semantics GA 公告（官方一手材料）。核心是 Metric Views：在数据层定义治理的 metrics、dimensions、rules，一次定义，SQL/dashboard/notebook/AI agent 全部消费同一定义。同时宣布把核心实现开源到 Apache Spark。

## 要点

**对传统方案的三点批判**（理解其定位逻辑的关键）：
1. 专有且碎片化——语义锁在各 BI 工具的模型里，跨组织无法治理、无法执行访问策略、无法追溯血缘。
2. 定义位置太下游——语义层坐在展示层而非数据基础层，导致同一指标为不同 dashboard 反复重定义，脆弱且难扩展。
3. 对 AI 不友好——传统层依赖繁重的前期建模，跟不上快速变化的业务问题和 agent 的开放式提问，每次变更都要专家介入。

**三点自我主张**：开放可复用（SQL/API 访问，开放格式存储，可移植）、核心治理（继承底层数据的治理策略，upstream 而非 downstream）、为 AI 设计（丰富语义元数据，无需繁重前期建模）。

**Metric Views**：
- 在 SQL 中集中定义 data mappings、measures、dimensions，直接在 Unity Catalog 治理。
- 声明式定义 → 引擎查询时**确定性编译并执行** SQL，保证任何消费者（人或 agent）从同一定义得到同一结果。
- 语义元数据：display names、formats、synonyms——同时服务人与 AI。
- 可移植到：AI/BI Dashboards、Genie、Notebooks、SQL 应用、连接 Databricks 的第三方工具。

**GA 新增能力**：
- **Materialization（Preview）**：自动预聚合 + 增量刷新 + 智能查询改写 + 透明路由。官方叙事是"团队不必再决定该打哪张聚合表、不必为不同性能档位复制逻辑、不必为不同工作负载建独立管道"。
- **新 UI（Public Preview）**：Catalog Explorer 内点选式创建管理，可视化定义表间关系、内联绘图、发布前端到端测试，让非技术用户也能建模。
- **Genie Code**：把 agentic AI 带进创作流程——bootstrap 语义模型（建议 measures、dimensions、synonyms、文档）、识别既有定义问题并给改进建议、用真实数据验证改动、逐条评审批准变更。

**开源（重要，此前记录不完整）**：
- 把 Metric View **核心实现开源到 Apache Spark OSS**，目标是下一个 Apache Spark 版本，进度可跟踪 **SPARK-54119**；Unity Catalog OSS v0.5 支持"coming soon"。
- 目的表述：客户可在开放系统中用标准 SQL 定义业务语义，治理在数据基础层而非下游工具内。
- **已加入 Open Semantic Interchange（OSI）** 并在积极贡献。

**与 AI/BI 的整合三点**：
1. AI/BI Dashboards 可直接建在 Metric Views 上，所有视觉、筛选、下钻、对比用同一套认证的 measures 和 dimensions。
2. Genie space 可直接建在 Metric Views 上——"Genie 不再幻觉指标，而是从单一事实源解析指标"。
3. **反向提升路径**：在没有既有 Metric View 的情况下建 AI/BI Dashboard 时，其中的 join、filter、计算字段可一键提升为新的 Metric View，立刻成为组织语义层的一部分，并自动获得物化加速。

**客户引述**：
- iFood（Pedro Alves，Data Manager）：标准化指标显著减少了对数工作量，查询"某些情况下快 10 倍"，Genie 准确率因更一致的预聚合数据而有意义地提升。
- Zalando（Timur Yüre，Engineering Manager）：措辞谨慎——"presents an exciting opportunity"、"a promising contribution"，属于早期评价而非成效背书。

**生态整合名单**（判断"处处可用"叙事真实度的依据）：
- BI/分析：Tableau（计划在其 relationship data model 中支持 delegated semantics，**预计 2026 年底**）、Sigma（实时查询 Metric Views，不搬数据，执行时继承 UC 安全治理）、ThoughtSpot（今年晚些原生支持，Spotter 可 NL 查询）、Hex（已完整集成）、Omni（**双向**，Omni 中的更新可推回 Unity Catalog）、Domo。
- 治理/可观测：Atlan（把 UC Metrics 纳入 Context Graph，绑定血缘、owner、业务定义，不增加额外权限开销）、Collibra（**双向同步**，Collibra 批准的指标可流入 Databricks）、Monte Carlo（指标与其管道的端到端可观测）、Anomalo（指标监控、漂移检测）。
- **缺席者：Power BI**（名单中无微软，第三方材料指出两家在语义层上是竞争关系，见 [[2026-07-31 Colrows - Databricks Metric Views 的边界]]）。

## 关联

- [[Unity Catalog]]、[[Genie]] 产品页的核心一手来源。
- 技术细节另见 [[2026-07-31 Databricks - Metric View YAML Reference]] 与 [[2026-07-31 Databricks - Materialization for Metric Views]]。
- 厂商一手材料：功能与生态声明可信；性能与准确率声明（10x、Genie 准确率提升）为客户引述口径，无独立验证。官方未提及的治理缺口见 [[2026-07-31 Databricks Community - Metric Views 不支持 ABAC]]。
