---
type: tool
status: draft
tool: PageIndex
category: doc-index
license: MIT
url: https://github.com/VectifyAI/PageIndex
---

# PageIndex

## 定位

VectifyAI 开源的**无向量、推理式 RAG** 框架（2025-09 发布，2026 年 3 月已 23k+ stars）：不用向量数据库、不做 chunking，而是给长文档建一棵**层级树索引**（可理解为"为 LLM 优化的目录树"），检索时让 LLM 在树上**推理导航**，模拟人类专家翻阅文档的方式。

## 核心机制

两步：

1. **建树**：分析文档结构，递归切分成自然章节（而非任意长度的 chunk），生成一棵树。每个节点带标题、AI 生成的摘要、起止页码。一棵 50 页文档的树约 30–50 个节点，整棵树是一个 JSON，**放得进单个 LLM 上下文窗口**，且每个节点都可人工检视。
2. **推理式检索**：查询到来时，把**去掉正文的树**（只有标题和摘要）交给 LLM，问"答案可能在哪几个节点"，LLM 返回 node_id 列表和推理轨迹，再取回这些节点的正文。可选 MCTS 增强多跳检索。

宣称在 FinanceBench 上达 98.7% 准确率（厂商口径）。

## 关键设计决策

1. **检索从"相似度"变成"推理"**：向量库对每个 chunk 并行算余弦相似度——快但不理解；PageIndex 让 LLM 思考答案住在哪一节，能跟随交叉引用（"见附录 G"）、识别多部分问题需要跨章节取料。
2. **可解释性免费获得**：每次检索产出显式推理轨迹（为什么选这些节点），带页码和章节引用。向量检索的"为什么命中"本质上是不可回答的。
3. **索引即 JSON，无基础设施**：不依赖向量数据库，索引可检视、可 diff、可版本化。
4. **代价**：检索时延与成本远高于向量检索（每次检索是一次或多次 LLM 调用），适合高价值、低 QPS 的深度文档场景，不适合毫秒级召回。

## 生态与延伸

同一团队在做"long-context AI infra"矩阵：PageIndex File System（把树索引提到文件级，支撑百万文档语料）、OpenKB（把文档编译成互链 wiki）、ChatIndex（长对话历史的树索引）、ConDB（KV-cache 原生的上下文数据库）、PageIndex MCP。

## 与本库主题的关联

- 与 [[Codegraph]] 是**同一设计哲学在不同数据形态上的实例**：预建结构索引 + agent 直接导航结构，取代"黑盒相似度召回"。Codegraph 的图对应代码，PageIndex 的树对应文档。
- 对 "for data" 的启示：数据域的对应物不是把表结构做 embedding，而是让 agent 能**沿语义结构导航**——从业务术语到指标定义到物理表到血缘，正如 PageIndex 从章节标题导航到正文。[[Genie Ontology]] 的活图谱 + OntoRank 是平台厂商对这个方向的回答，但索引形态与导航接口均未开放。
- 其"索引可检视、可 diff"的性质，正好对上本库 [[BI 与 AI 场景的 User Story]] 里"答案必须自带可审计性"的要求。

## 参考来源

- GitHub 仓库与官方文档（vectifyai-pageindex.mintlify.app）
- Towards AI 的评测报道（2026-03，含 98.7% FinanceBench 口径，厂商数据未独立验证）
