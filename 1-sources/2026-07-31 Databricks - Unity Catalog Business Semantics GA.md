---
type: source
status: draft
url: https://www.databricks.com/blog/redefining-semantics-data-layer-future-bi-and-ai
source_type: blog
publisher: Databricks
published:
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Metrics Layer]]", "[[Text2SQL]]", "[[Semantic Model]]"]
---

# Databricks: Unity Catalog Business Semantics GA 并开源

## 摘要

Unity Catalog Business Semantics GA 公告（官方一手材料）。核心是 Metric Views：在数据层定义治理的 metrics、dimensions、rules，一次定义，SQL/dashboard/notebook/AI agent 全部消费同一定义，且宣布开源。

## 要点

- Metric Views 集中定义 data mappings、measures、dimensions（SQL/YAML），治理直接在 Unity Catalog 内；带语义元数据：display names、formats、synonyms（同时服务人和 AI）。
- 声明式定义 → 引擎查询时确定性编译执行，保证任何消费者拿到同一结果。
- Genie 与 Metric Views 全面集成：Genie space 可直接建在 Metric Views 上，NL 查询解析自治理定义而非推断逻辑——官方话术"Genie 不再幻觉指标，而是从单一事实源解析指标"。
- Genie Code：NL 辅助编写/修改 metric view 定义；可从 Tableau/Power BI 文件导入生成 metric view。
- 生态信号：Tableau 计划支持消费外部 metric 定义（delegated semantics）。

## 关联

- [[Unity Catalog]]、[[Genie]] 产品页的核心一手来源。
- 配套技术文档：docs.databricks.com/aws/en/uc-semantics/metric-views/（含 YAML 规范、join 建模、物化加速、agent metadata），深研时另立 source。
- 厂商一手材料，功能声明可信，效果声明（准确性等）需第三方验证。
