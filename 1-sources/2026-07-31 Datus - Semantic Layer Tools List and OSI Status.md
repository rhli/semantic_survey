---
type: source
status: draft
url: https://datus.ai/blog/semantic-layer-tools-list-osi/
source_type: blog
publisher: Datus
published:
accessed: 2026-07-31
companies: [dbt-labs, cube, atscale, snowflake, databricks]
topics: ["[[Semantic Layer]]", "[[Open Semantic Interchange]]"]
---

# Datus: Semantic Layer Tools in 2026 — Complete List + OSI Status

## 摘要

2026 年语义层工具全景清单（15 个），按架构分三类：standalone（dbt Semantic Layer、Cube、AtScale）、platform-native（Snowflake Semantic Views、Databricks Metric Views、LookML、Power BI）、API/BI-native（GoodData、ThoughtSpot、Sigma、Lightdash、Preset、Omni、Domo、Dremio）。附各工具 OSI 适配进度。

## 要点

- Standalone 三强定位：dbt = code-first（YAML 与 transformation 同仓，MetricFlow 开源但 serving API 需 dbt Cloud）；Cube = API-first/headless（25+ 连接器，SQL/REST/GraphQL/MCP + 专用 AI API）；AtScale = 虚拟 OLAP（查询下推 + MDX/DAX，面向 Excel/Power BI 存量）。
- 定价快照（2026-07）：dbt $100/user/mo 起；Cube Cloud $40/dev/mo 起（自托管免费）；AtScale $10–28/DSO/mo，月费下限 $2,500–7,000。
- OSI 状态：dbt converter 已合入；Cube、AtScale 为 working group 成员。
- 全部工具都在补 AI agent 访问路径（MCP 为主）。

## 关联

- 本清单是 [[Semantic Layer Vendor Landscape]] 分阵营框架的主要依据之一。
- 注意：Datus 自身是该领域参与者，清单基本客观但入选口径需交叉验证。
