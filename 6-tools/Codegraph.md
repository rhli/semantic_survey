---
type: tool
status: draft
tool: Codegraph
category: code-index
license: 闭源（Cursor 生态 MCP server）
url:
---

# Codegraph

## 定位

代码库的**预建知识图谱**：把 workspace 里每个符号、边（调用/引用/包含）、文件索引进一个 SQLite 图，读取是亚毫秒级。以 MCP server 形式给 AI agent 提供代码智能（本库调研环境实际在用的工具）。

它回答的核心问题：**AI agent 理解代码时，不应该每次都重新 grep + 读文件来"重新发现"结构——结构应该被预先索引，查询应该直接命中。**

## 核心机制

- **预建索引 + 增量同步**：文件 watcher 监听变更，索引滞后写入约 1 秒。查询永远打在建好的图上，不打在原始文件上。
- **图模型**：节点是符号（函数/类/变量），边是关系（calls、imports、contains）。跨文件解析是 best-effort 的名字匹配。
- **面向 agent 的工具分层**：`search`（按名找符号）→ `context`（一次组合 search + node + callers + callees）→ `callers`/`callees`（结构遍历）→ `impact`（改动影响面）→ `trace`（X 如何到达 Y 的完整调用路径，能桥接回调、React 重渲染这类动态分发——grep 永远跟不下来的边）。
- **使用纪律即设计**：工具说明里明确写了反模式——"不要先 grep 再找符号""不要为重构影响面手动遍历 caller 链"。**索引的价值只有在 agent 被训练/引导去信任索引时才兑现**。
- **诚实标注滞后**：响应里带 staleness banner（"这些文件自上次索引同步后被编辑过"），让 agent 知道何时该回退到读原始文件。

## 关键设计决策

1. **结构查询与语义搜索分离**：它不做向量检索，做的是确定性图遍历。"谁调用了这个函数"是结构问题，用嵌入相似度回答是拿错工具。
2. **读路径极廉价**：亚毫秒读取使"每步决策前都查一下图"成为可行的工作方式，改变了 agent 与代码库交互的经济学。
3. **可组合的窄工具**：十个职责单一的小工具，而非一个万能 query 接口——agent 更容易学会何时用哪个。

## 对 "codegraph for data" 的映射

这是本库跟踪此工具的原因。把同样的设计哲学平移到数据域：

| Codegraph（代码域） | 数据域的对应物 |
|---|---|
| 符号（函数/类） | 表、列、指标、维度、语义模型 |
| 调用边 | join 关系、血缘、指标依赖（derived metric → 原子 metric） |
| `trace`（X 如何到达 Y） | 血缘路径：这个 dashboard 的数字经过哪些表/模型回到源系统 |
| `impact`（改动影响面） | 改这个列的口径会波及哪些指标/报表/agent 答案 |
| 预建索引 + 文件 watcher | 持续爬取 catalog/查询日志/dbt 产物，增量重建图 |
| staleness banner | 元数据新鲜度标注（"该表血缘最后更新于…"） |
| 亚毫秒结构查询 | agent 在生成 SQL **之前**先查图，而不是生成之后才发现 join 错了 |

现有产品里，[[Data Catalog]] 做了"图的存储"但查询接口面向人（搜索框）；[[Context Layer]]（[[Genie Ontology]]、[[Atlan]]）做了"自动建图"但图的查询对 agent 是黑盒。**"codegraph for data" 的空白点在两者交集**：面向 agent 的、亚毫秒级的、确定性的数据结构遍历接口。MCP 工具分层（search/context/callers/trace）几乎是现成的接口设计参照。

## 参考来源

- 本页基于该工具在本调研环境中的实际使用（MCP server 的工具说明与行为观察），无公开文档链接。
