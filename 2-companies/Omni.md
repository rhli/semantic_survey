---
type: company
status: draft
company: omni
founded: 2022
hq: San Francisco
capabilities: [semantic-modeling, text2sql]
ga_status: ga
---

# Omni

新一代 BI 里语义层做得最认真的厂商，创始团队出自 Looker 与 Stitch。定位是"LookML 的现代重写"：保留建模即代码的治理强度，去掉 LookML 的学习曲线。OSI（[[Open Semantic Interchange]]）成员。

## 语义层相关产品线

- Omni 平台 — BI + 语义层一体（公司即产品）
- 语义模型：YAML 代码 + 界面建模双层，共享同一模型定义
- AI 查询：NL 问答 grounding 在其语义模型上

## 战略与路线观察

- **它的赌注是"语义层不该是独立产品，而该长在 BI 里"**——与 headless 阵营（[[Cube]]、[[dbt Semantic Layer]]）正好相反。两种形态各有成立条件：Omni 赌企业想要"一个工具解决建模+消费"，headless 赌企业想要"语义层独立于任何消费工具"。
- 对本库的参照价值：Omni 是观察"BI-native 语义层"在 agent 时代演进的样本——它的模型能否像 LookML 一样成为 agent 的地基，决定了这条路线的天花板。

## 待调研问题

- [ ] 语义模型的表达力（vs LookML / MetricFlow 的指标类型覆盖）
- [ ] AI 查询的 grounding 机制与准确率口径
- [ ] OSI 适配进度

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
