---
type: company
status: draft
company: aloudata
founded: 2021
hq: 杭州
---

# Aloudata（大应科技）

国内数据平台厂商，**NoETL 路线的旗手**：主张不搬运数据、免宽表，靠逻辑语义层 + 自动物化提供分析能力。创始团队出自蚂蚁集团数据平台。架构范式总称**语义编织（Semantic Fabric）**：物理数据之上建统一语义层，把表/字段/指标/维度编织成 AI 和 BI 可直接消费的语义对象。

## 语义层相关产品线

- [[Aloudata CAN]] — NoETL 自动化指标平台（明细级语义层 + 三级物化）
- Aloudata AIR — 逻辑数据编织平台（跨源统一集成与联邦查询，待调研）
- Aloudata BIG — 主动元数据平台（算子级血缘，待调研）
- **Aloudata Agent** — 企业级可信数据分析智能体，以统一语义层为底座，走 **NL2MQL2SQL** 路径（自然语言→指标查询语言→SQL），做可信问数、异常检测、波动归因、报告生成

## 战略与路线观察

- **四条产品线是一个闭环叙事**：AIR 解决接入虚拟化，BIG 解决血缘与治理，CAN 解决指标语义层，Agent 是 AI 消费出口——全部围绕 NoETL。这是国内厂商里最完整的"语义层优先"架构。
- **NL2MQL2SQL 是国内厂商的共同选择**：中间加一层指标查询语言而非直接 NL2SQL，与 [[SuperSonic]] 的 S2SQL 同构。这个共识值得重视——在中文业务语境与复杂口径下，直接编译被认为不可靠，中间语义语言承担消歧与审计。
- **与国际厂商的对位**：CAN 的"明细级语义层 + 自动物化"≈ AtScale 聚合管理 + Cube pre-aggregation 的合体，但强调免建模宽表；Agent + CAN 的组合 ≈ Cortex Analyst + Semantic Views 的国内版。
- 治理主张鲜明："以最小治理范围覆盖最大业务需求"——治理聚焦原子层，应用层 NoETL 代持。
- 客户：麦当劳中国等消费/金融客户。

## 时间线

- 待补：产品发布与融资时间线

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
