---
type: source
status: draft
url: https://www.fivetran.com/blog/fivetran-acquires-tobiko-data-to-power-enterprise-grade-transformations
source_type: news
publisher: Fivetran
published: 2025-09-03
accessed: 2026-07-31
companies: [fivetran-dbt-labs]
topics: ["[[Semantic Layer]]"]
---

# Fivetran: 收购 Tobiko Data 与 SQLMesh 的归属

## 摘要

理解 [[Fivetran + dbt Labs]] 锁定风险论证的前置事实。Fivetran 在与 dbt Labs 合并**之前**已收购 Tobiko Data（SQLMesh + SQLGlot 的母公司）——也就是 dbt 最主要的开源竞争对手。合并完成后，同一实体同时控制 dbt Core、Fusion 引擎、SQLMesh 与 dbt Cloud 定价。

## 要点

**收购事实**：2025 年 9 月 3 日，Fivetran 收购 Tobiko Data。Tobiko 位于 San Mateo，收购前累计融资 2,180 万美元（含 2024 年 6 月 1,730 万美元 A 轮）。这是 Fivetran 当年第二笔收购，前一笔是 Census（进入 reverse ETL）。

**收购标的**：
- **SQLMesh**——开源数据转换框架，第三方定位为"与 dbt Labs 工具可对标"。
- **SQLGlot**——SQLMesh 底层的开源引擎，翻译不同 SQL 方言并检测编码错误。

**Fivetran 强调的 Tobiko 技术特性**（与语义层相关的是最后一条）：
- 智能模型追踪，只运行必要的部分
- 跨 SQL 方言的多引擎支持
- 模块化测试与开发环境
- **Semantic SQL understanding**——"让管道更易维护、更 AI-ready"

**当时的表态**（事后看值得对照）：收购公告称"我们对开放、可互操作生态的承诺不变。我们将继续支持并与 dbt Labs、Coalesce 等关键伙伴协作。" 一个月后（2025-10-13）Fivetran 就宣布与 dbt Labs 合并。

**SQLMesh 捐给 Linux Foundation**：Fivetran 宣布把 SQLMesh 捐赠给 Linux Foundation 以确保社区治理，初始成员含 Benzinga、CloudKitchens、Harness、Infinite Lambda、Jump AI、Minerva。

**分析师当时提出的问题**（现在仍未完全回答）：Fivetran 需要尽快明确 SQLMesh 与 SQLGlot 的许可策略，以及对**多个转换引擎**的集成与支持路线图。合并 dbt 后这个问题更尖锐——一家公司现在有两套竞争的转换框架。

## 关联

- [[Fivetran + dbt Labs]] 公司页的并购链条与锁定风险论证。
- 第三方（Peliqan）把"单一商业实体同时控制 dbt Cloud 定价、Fusion 引擎路线图与 SQLMesh"列为团队评估 dbt 替代方案的主要驱动因素，见 [[2026-07-31 Peliqan - dbt 替代方案与锁定担忧]]。
- **日期矛盾待核实**：SQLMesh 捐赠 Linux Foundation 的新闻稿页面标注日期为 **2025-03-25**，但这早于 Tobiko 收购（2025-09-03），而该新闻稿正文称 SQLMesh "由 Tobiko Data 团队开发，Tobiko 于 2025 年被 Fivetran 收购"。两者时序矛盾，捐赠实际应发生在收购之后（推测为 2026-03-25，页面年份疑为笔误）。未经二次核实前不作为确定时间线条目。
