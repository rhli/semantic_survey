---
type: company
status: draft
company: dbt-labs
founded: 2016
hq: Philadelphia
---

# dbt Labs

Analytics engineering 工具链厂商（dbt 是数据转换的事实标准），语义层战略是 metrics-as-code：指标定义与 transformation 代码同仓、同流程。

## 语义层相关产品线

- [[dbt Semantic Layer]] — MetricFlow（开源，Apache 2.0）+ dbt Cloud serving API

## 战略与路线观察

- MetricFlow 被选为 OSI 的声明式规范基础——dbt 的语义定义格式正在成为行业标准，影响力超出其商业产品。
- 商业模式张力：MetricFlow 开源但 serving 必须 dbt Cloud（$100/user/mo 起），社区长期不满，是竞品（Cube 等）的主要攻击点。

## 时间线

- 2022：收购 Transform，获得 MetricFlow
- 2026-01：MetricFlow 成为 OSI 规范基础，OSI converter 合入

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
