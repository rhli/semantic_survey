---
type: comparison
status: draft
dimension: vendor-landscape
companies: [databricks, snowflake, fivetran-dbt-labs, cube, atscale, atlan, aloudata]
created: 2026-07-31
updated: 2026-07-31
---

# Semantic Layer Vendor Landscape

企业语义层领域的厂商全景图。首次梳理于 2026-07-31，作为本知识库调研范围的地图。

## 总体格局：四个阵营

2026 年的语义层市场可以分为四个阵营，各自的切入点和商业逻辑不同：

### 1. 平台原生（Platform-native）

数据平台厂商把语义层做成平台的原生对象，逻辑是"语义跟着数据走"，代价是锁定在单一平台。

| 公司 | 产品 | 状态 | 要点 |
|---|---|---|---|
| Databricks | [[Unity Catalog]]（Business Semantics / Metric Views） | GA 2026，核心实现开源进 Spark（SPARK-54119） | 指标定义为 UC 治理对象，SQL/YAML 定义，查询时确定性编译 |
| Databricks | [[Genie]]（Genie One + Genie Agents） | GA（2026-06 发布） | NL 查询 grounding 在 Metric Views 上；已扩展为 AI coworker + 自治 agent，多端交付 |
| Databricks | [[Genie Ontology]] | gated public preview | 自动上下文层：从 lakehouse + 50+ 外部工具抽取知识，OntoRank 权威度排序 |
| Snowflake | [[Snowflake Semantic Views]]（+ Semantic View Autopilot） | GA，Autopilot 2026 初发布 | 语义视图为 catalog 一等对象；Autopilot 从 BI 使用模式自动生成语义定义 |
| Snowflake | [[Cortex Analyst]] | GA | 基于 Semantic Views 的 conversational analytics API |
| Google | Looker / LookML | 成熟 | BI 原生语义层的代表，Gemini 集成 |
| Microsoft | Power BI semantic models / Fabric | 成熟 | DAX 生态，企业存量最大 |
| Salesforce | Tableau Semantics / Data 360 | 演进中 | OSI 发起方之一 |

### 2. 独立语义层（Standalone / vendor-neutral）

语义层本身就是产品，跨仓、跨 BI 工具，"三强"格局：

| 公司 | 产品 | 开源 | 要点 |
|---|---|---|---|
| **Fivetran + dbt Labs** | [[dbt Semantic Layer]]（MetricFlow） | MetricFlow 开源（Apache 2.0）；Fusion 引擎 2026-06 开源为 dbt Core v2.0 | metrics-as-code，YAML 与 dbt 模型同仓；5 类指标含 conversion/cumulative；主动防护 fan-out 与 chasm join。开源边界是"定义+编译"，托管 API 与缓存需付费 |

（同厂商的 [[Agents Schema]] 属上下文层形态，见下一节。）

| Cube | [[Cube]]（Cube Core 开源 + Cube 商业版） | Core 开源（Apache 2.0） | API-first/headless：SQL、REST、GraphQL、MCP；内置 pre-aggregation 缓存；agentic analytics 叙事 |
| AtScale | [[AtScale]] | 闭源（SML 语言已开源） | 虚拟 OLAP + 智能聚合管理，MDX/DAX 兼容，面向 Excel/Power BI 存量企业 |
| Denodo | Denodo Platform | 闭源 | 老牌数据虚拟化 + 语义层，联邦查询任意源 |
| Palantir | Foundry Ontology | 闭源 | Ontology 驱动，语义服务于分析+操作闭环，重实施 |

### 3. Metadata / Context Layer 阵营

从 catalog/元数据切入，叙事是"语义层只回答指标是什么，context layer 回答 AI agent 何时、如何、在什么规则下使用数据"——把语义层包进更大的上下文层：

| 公司 | 产品 | 要点 |
|---|---|---|
| Atlan | [[Atlan]]（Enterprise Data Graph、Context Engineering Studio、MCP server） | 自我定位"Context Layer for AI"；摄取 dbt/Cube/LookML 语义定义并绑定治理元数据；宣称 grounding 使 AI SQL 准确率提升 38% |
| Databricks | [[Genie Ontology]] | 平台原生：自动抽取 + OntoRank 权威度排序，逐来源 ACL |
| **Fivetran + dbt Labs** | [[Agents Schema]] | **异类**：不做服务，只做标准。上下文落成客户自己数仓 `AGENTS` schema 里的普通 SQL 表，任何能跑 SQL 的 agent 可读，MCP 仅为可选项；OSI 为其 canonical 语义格式；代价是元数据对全体查询者可读 |
| Alation | AIOS（AI Operating System） | 老牌 catalog 转型 agent 上下文管理 |
| Collibra | — | 治理向 catalog，语义层动作较少 |
| Promethium | 360° Context Hub / Mantra | Context-native，聚合 catalog/BI/语义层元数据，NL 查询 |

### 4. 国内厂商 / 指标平台

国内市场以"指标平台/指标中台"为主要形态，普遍强调指标口径统一 + ChatBI：

| 公司 | 产品 | 要点 |
|---|---|---|
| Aloudata（大应科技） | [[Aloudata CAN]] | NoETL 自动化指标平台：明细级语义层、免宽表、自动物化加速；另有 AIR（逻辑数据编织）、BIG（血缘） |
| 数势科技 | SwiftMetrics（指标平台）、SwiftAgent（NL 分析助手） | 指标全生命周期管理 + 大模型分析，金融客户多 |
| Kyligence | Kyligence Zen（+ DeepInsight） | 指标平台，Kylin OLAP 血统 |
| 腾讯音乐（开源） | [[SuperSonic]] | 开源 ChatBI + Headless BI 融合框架，S2SQL 中间语义查询语言，国内开源影响力最大 |

### 开源项目补充

- [[Cube]] Core、MetricFlow、[[SuperSonic]]（见上）
- WrenAI / Wren Engine：面向 AI agent 的 context layer，MDL 语义模型
- Malloy：Google 出品的语义建模/查询语言，编译到 SQL
- Lightdash：dbt-friendly 开源 BI，自带语义层

## 横向主题（需单独跟踪）

- **[[Open Semantic Interchange]]（OSI）**：Snowflake 发起的厂商中立语义定义标准（JSON/YAML），Apache 2.0，进入 Apache 孵化（"Ossie"），2025-09 发布，60+ 组织参与，2026-06 名单扩至含 Alation、Atlan、Collibra、Cube、ThoughtSpot。**是规范层不是运行时**——修正先前"基于 MetricFlow 规范"的表述，二者是契约与运行时的分层关系。当前最强采纳信号是 dbt 的 [[Agents Schema]] 把 OSI 定为 canonical 语义格式。
- **[[Text2SQL]] 与语义层的关系**：全行业统一叙事——LLM 直接对裸 schema 写 SQL 准确率约 40%，grounding 在治理语义定义上可达 83%+（dbt 内部测试口径，多家引用）。语义层被重新定位为 AI 的事实基础。
- **MCP（Model Context Protocol）**：2026 年语义层产品的标配暴露方式。但 [[Agents Schema]] 提出了反向主张——上下文放进数仓表，裸 SQL 即可读，MCP 只是可选项之一。
- **Semantic Layer vs Context Layer vs Catalog**：三者边界正在重划，是理解 Atlan 等厂商定位的关键。
- **建模成本与上下文覆盖问题的四条解法**：人工定义 + 自动图谱并存（Databricks，OntoRank 仲裁）、自动生成交人审核（Snowflake Autopilot）、摄取他家定义做跨平台上下文层（Atlan）、**发布既有元数据进客户自己数仓的 SQL 表**（Agents Schema）。已沉淀为独立对比页 [[上下文层的四条路线]]；Databricks 一侧的原始洞见见 [[2026-07-31 Databricks 的双层语义架构]]。
- **正确性责任归属**：各家对 fan-out / chasm join 的处理方式分歧很大，从引擎主动防护（MetricFlow）到声明但运行时不校验（Databricks `rely`）。见 [[Join 正确性的保证方式]]。
- **并购与整合**：2025-09 Fivetran 收购 Tobiko Data（SQLMesh）；2026-06-01 Fivetran 与 dbt Labs 合并完成。转换层与摄取层正在合并为单一层，单一实体同时持有 dbt 与其头部开源竞品。

## 调研分层建议（待确认）

- **Tier 1 深研**（产品页 + 技术机制）：Databricks、Snowflake、Fivetran + dbt Labs、Cube、AtScale、Atlan、Aloudata
- **Tier 2 次要**（公司页 + 能力概述）：Looker/LookML、Power BI、SuperSonic、数势科技、Kyligence、Palantir Foundry
- **Tier 3 观察名单**（landscape 页提及即可）：Denodo、Alation、Collibra、Promethium、WrenAI、Malloy、Lightdash、Tableau、ThoughtSpot、GoodData、Sigma

## 参考来源

- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
- [[2026-07-31 Allocating Intelligence - Snowflake vs Databricks Semantic Layer]]
- [[2026-07-31 Databricks - Unity Catalog Business Semantics GA]]
- [[2026-07-31 Aloudata - Aloudata CAN 产品介绍]]
- [[2026-07-31 Atlan - Context Layer for AI Agents]]
- [[2026-07-31 TencentMusic - SuperSonic README]]
