---
type: source
status: draft
url: https://datapao.com/genie-ontology-explained/
source_type: blog
publisher: Datapao（Databricks 合作伙伴）
published: 2026
accessed: 2026-07-31
companies: [databricks]
topics: ["[[Context Layer]]", "[[Text2SQL]]"]
---

# Datapao: Genie Ontology 详解（第三方视角）

## 摘要

第三方（Databricks 合作伙伴）对 Genie Ontology 的解读，补充了官方博客没写清的输入来源清单、OntoRank 的类比逻辑和产品状态。

## 要点

- 核心口号式概括：**"不是你把上下文写进 Genie，而是 Genie 从你的组织已经产出的一切里读上下文。"**
- 三类输入：
  1. Databricks 环境内：表、查询、dashboard、管道
  2. Unity Catalog：认证的指标定义、术语表（glossary）、语义标签
  3. **50+ 外部工具**：Slack、Jira、Google Drive、SharePoint、Confluence、邮件、日历（Gmail、Teams、Outlook、Calendar 连接 2026 年进入 Beta）
- 知识图谱"每次有东西变化就自动重建"。
- **OntoRank 的类比**：PageRank 靠指向网页的链接图判断可信度，OntoRank 把同样原理用于业务定义，且跨越根本不同的资产类型——表、文档、Slack 线程、dashboard。
- 产品状态：**gated public preview**（2026 年 6 月 Data + AI Summit 发布）。
- 消费面：Genie Spaces、Genie One、Genie Agents、Genie Code，以及通过 MCP 供外部工具使用。
- 第三方普遍强调的前提条件（多篇材料共同观点）：Ontology 只能映射已经存在于某处的含义。如果指标本身没定义、表没治理、没有约定的语义层，Ontology 会**忠实地把这团乱麻映射出来**。

## 关联

- 补充 [[Genie Ontology]] 页；"忠实映射乱麻"这一点是与官方"无需手工策展"叙事的重要对冲，也解释了为什么 [[Unity Catalog]] 的 Metric Views 与 Ontology 是互补而非替代关系。
- 偏差提示：Datapao 是 Databricks 合作伙伴，整体基调正面，但事实性描述（输入清单、产品状态）比官方博客更具体可用。
