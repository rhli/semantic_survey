---
type: concept
status: draft
aliases: [无头 BI, Semantic API]
---

# Headless BI

## 定义

把语义层作为独立服务、自身不带可视化界面，通过开放 API 向任意下游（BI 工具、应用、embedded analytics、AI agent）暴露一致语义。**它是一种厂商侧架构模式，不是 BI 的一种**——名字里的 "BI" 指它服务的消费形态，"headless" 指砍掉了头（UI）。

与 [[BI]] 页的分工：BI 页讲消费侧机制（怎么连、查询怎么编译、北向接口的形态分类）；本页讲这个模式为什么存在、谁在选它、代价是什么。

## 为什么存在

- **LookML 证明了语义层的价值，也暴露了锁定的代价**：2012 年 Looker 发明 metrics-as-code，但语义资产只能在 Looker 里消费。企业换了 BI 工具，语义层就得重建。Headless 的回答：语义层独立于任何体验层，"define once, serve everywhere"。
- **"everywhere" 的宽度就是北向接口的宽度**——这是这个模式的全部工程内容，接口形态分类见 [[BI]] 页。
- 经济学判断：headless 厂商赌企业想要"语义层独立于消费工具"；BI-native 厂商（[[Looker]]、[[Omni]]）赌企业想要"一个工具解决建模+消费"。两个赌注都成立，取决于买方已有多少消费端资产。

## 2026 年的转折

消费者从 BI 工具变成 agent，MCP 成为标配暴露方式。headless 厂商（[[Cube]]、[[dbt Semantic Layer]]）因为本来就是 API-first，**最早拿到 agent 入场券**——不需要改造产品，只需要多加一个接口。但 BI-native 阵营在补 MCP（Looker MCP 已 GA），两条路线的接口差距正在收窄，剩下的本质差异是**语义资产的所有权位置**。

## 关键设计维度

- API 广度：Cube 是标杆（SQL/REST/GraphQL/MCP/DAX/MDX 六接口）；dbt SL 提供 SQL/JDBC/GraphQL/MCP
- 兼容旧协议：[[AtScale]] 对 Excel/Power BI 讲 MDX/DAX——存量世界的入场券
- 指标服务化：[[Aloudata CAN]] 的"开放指标服务"（API/JDBC）是同一形态的国内表述
- serving 层归属：Cube 自带 pre-aggregation 引擎，dbt SL 不拥有执行层——接口相同，性能责任不同

## 各家实现

```dataview
TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "headless-bi") SORT company ASC
```

## 相关概念

[[Semantic Layer]]、[[BI]]、[[Metrics Layer]]、[[Text2SQL]]
