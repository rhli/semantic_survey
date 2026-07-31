---
type: company
status: draft
company: aloudata
founded: 2021
hq: 杭州
---

# Aloudata（大应科技）

国内数据平台厂商，NoETL 路线的旗手：主张不搬运数据、免宽表，靠逻辑层 + 自动物化提供分析能力。创始团队出自蚂蚁集团数据平台。

## 语义层相关产品线

- [[Aloudata CAN]] — NoETL 自动化指标平台（明细级语义层）
- Aloudata AIR — 逻辑数据编织平台（Data Fabric / 虚拟化，待调研）
- Aloudata BIG — 数据血缘平台（字段级血缘，待调研）

## 战略与路线观察

- 三条产品线构成一个整体叙事：AIR 解决数据接入虚拟化，CAN 解决指标语义层，BIG 解决血缘——都围绕 [[NoETL]]。
- 与国际厂商的对位：CAN 的"明细级语义层 + 自动物化"≈ AtScale 的聚合管理 + Cube 的 pre-aggregation 的合体，但强调免建模宽表。
- 客户：麦当劳中国等消费/金融客户。

## 时间线

- 待补：产品发布时间线

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
