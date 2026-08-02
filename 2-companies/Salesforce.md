---
type: company
status: draft
company: salesforce
founded: 1999
hq: San Francisco
---

# Salesforce（Tableau）

OSI（现 [[Open Semantic Interchange]] / Apache Ossie）的三个创始方之一（与 Snowflake、dbt Labs 并列）。手里的语义层资产是 Tableau（2019 年 $15.7B 收购），战略叙事是 "ending semantic drift"：让 AI agent 与 BI 工具共享同一份企业上下文。

## 语义层相关产品线

- Tableau Semantics / Tableau Next — Tableau 的语义层与 agentic 分析演进（待深研）
- Data 360（原 Data Cloud）— 客户数据平台侧的语义统一
- Agentforce — agent 平台，消费语义层的出口

## 战略与路线观察

- **OSI 创始方的动机值得玩味**：Salesforce 不拥有数仓，语义互操作对它只有好处——客户用在 dbt/Snowflake 的既有语义投资可以直接驱动 Tableau 与 Agentforce（"bring your own semantic layer"）。**开放标准是挑战者的武器**，Salesforce 在语义层市场正是挑战者。
- 与微软形成镜像：两家都是应用/BI 巨头，微软选择自建闭环（Fabric IQ，缺席 OSI），Salesforce 选择共建标准。**这个分岔是观察"开放 vs 封闭"策略的最佳对照组**。

## 时间线

- 2019：收购 Tableau
- 2025-09：作为创始方发起 OSI
- 2026：OSI 进入 Apache 孵化器（Ossie）

## 待调研问题

- [ ] Tableau Semantics 的技术形态与 OSI 的关系（谁先谁后、是否互转）
- [ ] Agentforce 消费语义层的具体路径

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
