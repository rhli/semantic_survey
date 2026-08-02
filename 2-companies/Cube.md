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

API-first 的 headless 语义层：定义一次（metrics、dimensions、joins、访问规则），通过六类接口暴露给任意下游。2026 年的定位一句话：**agent 查询认证指标，永不直接写裸表 SQL**。Cube Core 开源（Apache 2.0），商业平台（D3）在其上加 agent 界面、workbooks、embedded 与托管。

## 核心概念与架构

- **建模对象**：cubes（业务实体，含 measures/dimensions）+ views（cube 间 join 组合）。JS/YAML 代码定义，Git 版本控制 + CI 部署。
- **六接口面**：SQL API（Postgres 方言+线协议，cube/view 呈现为表）、REST、GraphQL、**MCP**（2026-01-30 上线，agent 发现语义模型并以 Semantic SQL 查询）、DAX/MDX（Power BI/Excel 以 Analysis Services 方式连入，支持 live connection 与 DirectQuery）。
- **Semantic SQL + E-Graphs**：agent 使用的中间查询形态，runtime 充当 agent 与数仓之间的 guardrail——这是它与"直接编译到仓 SQL"路线（MetricFlow）的技术分野。
- **Pre-aggregation 缓存**：声明式配置物化，查询规划器自动评估命中。Cube 拥有自己的 serving 层——这是与 [[dbt Semantic Layer]]（不拥有执行层）最本质的架构差异。
- **编译期安全**：行级、多租户访问控制在查询编译时生效，不是运行期过滤。多租户是构造性支持（governance 从模型流向客户权限），这是它在嵌入式分析市场强的原因。

## 语义模型的表达方式

JavaScript 或 YAML，开源与商业版模型双向完全兼容。查询时可扩展：AI 在治理定义之上构造 ad-hoc 计算，而不是绕过定义。

## 限制与边界

- 自托管 Core 需自担部署、扩缩容、升级成本；Cloud 版定价按平台订阅（与 dbt 的按量计费形成对照）。
- 比 BI-native 层需要更多建模工作；模型是独立仓，不与 dbt 转换代码同仓（2026-07 的 dbt Integration 在弥合这一点，成熟度待观察）。
- join 正确性机制待核实：`joins` 的 `relationship` 字段（`one_to_many` 等）是声明还是引擎校验——[[Join 正确性的保证方式]] 的关键待填项。

## 待调研问题

- [ ] pre-aggregation 的匹配算法与刷新机制（源码可读）
- [ ] Semantic SQL / E-Graphs 的原理与表达力边界
- [ ] `relationship` 声明是否运行时校验
- [ ] Brex 案例细节（为何弃 dbt SL 和 LookML）

## 参考来源

- [[2026-08-02 Cube - Semantic Layer for AI Agents]]
- [[2026-07-31 Datus - Semantic Layer Tools List and OSI Status]]
- [[2026-07-31 Promethium - Top 10 Semantic Layer Tools 2026]]
- GitHub: github.com/cube-js/cube
