---
type: product
status: draft
company: cube
product: Cube (Core + Cloud)
capabilities: [semantic-modeling, metrics-layer, query-rewrite, acceleration, headless-bi, governance, text2sql]
ga_status: ga
---

# Cube

## 定位与解决的问题

API-first 的 headless 语义层：定义一次（metrics、dimensions、joins、访问规则），通过 SQL（Postgres 兼容）、REST、GraphQL、MCP 暴露给任意下游——BI、embedded analytics、AI agent。Cube Core 开源（Apache 2.0），商业版是其上的 agentic analytics 平台。

## 核心概念与架构

- 建模对象：**cubes**（业务实体，含 measures/dimensions，对应星型模型的事实/维度）+ **views**（cube 间 join 关系）。JS/YAML 代码定义，版本控制 + CI 部署。
- **Pre-aggregation 缓存引擎**：声明式配置物化，查询规划器自动评估命中，亚秒级延迟 + 降仓成本。
- 安全：行级、多租户访问控制。
- 商业版叠加：Analytics Chat（NL 查询）、workbooks/dashboards、embedded 表面、托管、RBAC、Tableau/Power BI/Excel/Sheets 集成。

## 语义模型的表达方式

- JavaScript 或 YAML；开源与商业版模型双向完全兼容。
- 查询时可扩展：AI 在治理定义之上构造 ad-hoc 计算，而不是绕过。

## 限制与边界

- 自托管需承担部署、扩缩容、升级的运维成本。
- 比 BI-native 层需要更多建模工作。

## 待调研问题

- [ ] pre-aggregation 的匹配算法与刷新机制（源码可读）
- [ ] SQL API 的方言兼容边界
- [ ] AI API / MCP server 的具体形态
- [ ] Brex 案例细节（为何弃 dbt SL 和 LookML）

## 参考来源

- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
- GitHub: github.com/cube-js/cube
