---
type: company
status: draft
company: snowflake
founded: 2012
hq: Bozeman, MT
---

# Snowflake

云数仓平台厂商。语义层战略一句话：**"智能留在数据所在的平台内"**——语义视图是 catalog 一等对象，Cortex AI 直接在其上推理，无外部 API、无中间件。同时它是 [[Open Semantic Interchange]]（现 Apache Ossie）的主要发起方：一手平台锁定，一手开放标准。

## 语义层相关产品线

- [[Snowflake Semantic Views]] — 原生语义视图（catalog 一等对象）+ Semantic View Autopilot（从 BI 使用模式自动生成）
- [[Cortex Analyst]] — 基于语义视图的 conversational analytics 服务（REST API，按消息计费）
- Horizon Catalog — 治理目录（待调研是否单独立页）

## 战略与路线观察

- **"平台原生 + 标准发起方"的双轨并不矛盾，而是互相成就**：OSI 越成功，别家语义定义越容易流进 Snowflake 消费侧（Cortex Analyst 只认语义视图，不管定义从哪来）；Snowflake 锁定的是**执行与消费**，不是定义。理解这一点才能看懂它为什么真心推 OSI。
- **Autopilot 是四条上下文覆盖路线里最保守也最务实的一条**（见 [[上下文层的四条路线]]）：从 BI 使用模式自动生成定义、交人审核，终点仍是人工确认的单一事实源——不像 Databricks 那样引入权威度排序的复杂性，也不像 Atlan 那样另建一层。
- **把评测做成了产品功能**：Cortex Analyst evaluations（verified queries + `sql_correctness`）让"语义模型质量"有了持续可测的工程闭环，这是目前各家里最完整的内置评测方案。
- 与 Databricks 的镜像差异：Databricks 把语义拆成"Metric Views（治理指标）+ Genie Ontology（自动上下文）"两层；Snowflake 把赌注压在**一层**——语义视图既要服务 BI 又要 grounding AI，用 Autopilot 解决覆盖问题。哪条对，取决于"自动图谱"是否真能达到人工定义的可靠性。

## 时间线

- 2025：Semantic Views 发布
- 2025-09：作为创始方发起 OSI
- 2026 初：Semantic View Autopilot 发布
- 2026-03-02：语义视图支持标准 SQL + `AGG()` 查询（消费门槛大降）
- 2026：OSI 进入 Apache 孵化器，更名 Ossie

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
