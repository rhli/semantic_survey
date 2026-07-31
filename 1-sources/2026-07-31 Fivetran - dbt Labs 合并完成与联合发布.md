---
type: source
status: draft
url: https://www.fivetran.com/press/fivetran-dbt-labs-complete-merger-to-create-the-data-infrastructure-for-trusted-ai-agents
source_type: news
publisher: Fivetran
published: 2026-06-01
accessed: 2026-07-31
companies: [fivetran-dbt-labs]
topics: ["[[Context Layer]]", "[[Semantic Layer]]", "[[Open Semantic Interchange]]"]
---

# Fivetran: 与 dbt Labs 合并完成及首批联合发布（官方新闻稿）

## 摘要

2026 年 6 月 1 日 Fivetran 与 dbt Labs 完成全股票合并（2025 年 10 月 13 日宣布）。合并实体初期以 **"Fivetran + dbt Labs"** 名义运营，George Fraser 任 CEO，Tristan Handy 任 President。同日发布首批联合产品，其中 **Agents Schema** 直接进入语义层/上下文层赛道。

## 要点

**合并事实**：
- 全股票交易，2025-10-13 宣布，2026-06-01 完成。
- 合并后服务超过 10 万个数据团队，客户举例 OpenAI、Zendesk、Coupa、HubSpot。
- 官方对外名称为 "Fivetran + dbt Labs"（新闻稿原文 "Initially operating as Fivetran + dbt Labs"），暗示是过渡命名。
- 定位叙事：**Open Data Infrastructure for trusted AI agents**。分工表述是 Fivetran 保证 agent 跑在完整、持续同步、可靠的数据上；dbt 保证数据经过定义、测试并通过治理的业务逻辑、共享语义上下文可信。

**问题陈述**（值得记录，它定义了整套产品的设计前提）：AI agent 正在成为企业数据的主要消费者，且行为方式与今天数据栈服务的人类分析师不同——**持续运行、并行、机器速度**，且很多组织希望走向"大部分 agent 自治、无人在环"。这提高了对数据的要求：可靠、新鲜、被治理、跨系统可访问。

**四项联合发布**：

| 产品 | 状态 | 内容 |
|---|---|---|
| **dbt Core v2.0** | alpha | dbt Fusion 引擎 runtime 开源为 dbt Core v2.0，Apache 2.0。本地可安装发行版免费提供 Fusion 全部能力（核心语言特性 + 仓库 adapter），可从终端登录解锁平台功能 |
| **dbt State** | preview | 数据管道缓存层，只构建变化的部分，宣称降低底层基础设施成本 30%+ |
| **dbt Wizard** | beta | 自主的模型编写、重构、调试助手。grounding 在完整 dbt 项目上下文上（血缘、测试、contract、**已定义的指标**） |
| **Agents Schema** | 开源标准 | 指定数仓/湖中的**单个 schema** 作为 AI agent 的共享上下文层 |

**Agents Schema 的官方定位**（原文要点）：
- 指标定义、语义模型、dbt 血缘、业务文档存为**普通 SQL 表**。
- 元数据从原本拥有它的系统发布进来，工具举例 GitHub Actions、元数据连接器、自定义集成。
- 兼容**任何**仓库、湖、摄取工具或"能跑 SQL 的 agent"。
- 三点价值主张：**客户自有**（customer-owned）的上下文层、在既有安全与治理策略内工作、通过更丰富上下文提升 token 效率、**免除新基础设施与厂商锁定的 agent 系统**。

**客户引述**：Zendesk、Inova Health、Tinuiti、Shutterstock、DocuSign。均为泛泛的"可信数据基础"表述，无量化指标，参考价值有限。

**一个细节**：联合发布在 **Snowflake Summit 2026** 上展示，Fivetran 与 dbt Labs 各有独立展位。合并后仍在 Snowflake 生态大会上首发，与"跨平台中立"的叙事一致。

## 关联

- [[Fivetran + dbt Labs]] 公司页的核心一手来源。
- [[Agents Schema]] 产品页的官方定位依据（技术细节见 [[2026-07-31 dbt Labs - Agents Schema SPEC]]）。
- 与 [[Genie Ontology]]、[[Atlan]] 构成 [[Context Layer]] 三条不同路线的对位，见 [[上下文层的四条路线]]。
- 厂商一手材料：合并事实与产品发布可信；"降低成本 30%+"、"提升 token 效率"等收益声明无第三方验证。
