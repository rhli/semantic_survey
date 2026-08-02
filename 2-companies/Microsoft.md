---
type: company
status: draft
company: microsoft
founded: 1975
hq: Redmond
---

# Microsoft（Power BI / Fabric）

企业 BI 存量最大的玩家。语义层资产是 Power BI semantic models（DAX 生态），2026 年统一进 **Fabric IQ** 叙事：semantic models + Ontologies 两层。它的语义层问题与别人不同——不是"建不建"，而是**几千万个存量语义模型如何 AI 化**。

## 语义层相关产品线

- [[Microsoft Fabric]] — Fabric IQ（semantic models + Ontology preview）、Data Agent、Prep for AI
- Power BI — semantic models 的原生宿主，Copilot 消费同一套 Prep for AI 配置
- Excel — 通过语义模型直连，仍是企业事实上的终端

## 战略与路线观察

- **存量即护城河也是包袱**：Power BI 语义模型是企业里部署最广的语义层，但质量参差（大量模型是分析师自助堆出来的）。微软的答案是 Prep for AI——给存量模型打"AI 就绪"补丁，而不是要求重建。
- **架构上最诚实的一条**：官方文档明确 DAX 生成工具**只读模型元数据与 Prep for AI 配置，忽略 agent 级指令**——grounding 必须编译进语义工件，prompt 补丁不可靠。这是全行业唯一把这条写成显式架构约束的厂商。
- **双层结构**：semantic models（指标/度量层）+ Fabric Ontology（实体/关系本体层，preview）——与 Databricks 的 Metric Views + Genie Ontology 双层同构，印证"指标语义 + 本体上下文"分层正在成为大厂共识。

## 时间线

- 2015：Power BI GA，语义模型（SSAS 血统）随之外溢为企业标准
- 2023：Fabric 发布，统一数据平台
- 2025–2026：Prep for AI、Data Agent、Fabric IQ（Ontology preview）陆续上线

## 相关来源

```dataview
LIST FROM "1-sources" WHERE contains(companies, this.company) SORT accessed DESC
```
