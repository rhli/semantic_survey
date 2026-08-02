---
type: tool
status: draft
tool: OpenViking
category: agent-context
license: AGPL-3.0
url: https://github.com/volcengine/OpenViking
---

# OpenViking

## 定位

火山引擎（字节跳动 Viking 团队）2026 年 1 月开源的 **agent 上下文数据库**（27k+ stars）：把 agent 需要的三类上下文——记忆（memory）、资源（resources/RAG 知识）、技能（skills）——统一进一个**虚拟文件系统**，让 agent 用 `ls`、`tree`、`find` 浏览自己的上下文，而不是查询一个黑盒向量库。团队前身是 VikingDB 向量数据库（2019 年起支撑字节内部），这个项目是他们对"后向量时代"上下文管理的回答。

## 核心机制

- **`viking://` URI + 文件系统范式**：每段上下文有确定性路径，agent 像开发者操作文件一样定位和操作上下文——路径本身就是寻址方案。
- **L0/L1/L2 分层加载**：内容处理成三级——L0 摘要、L1 概览、L2 详情——按需加载，控制 token 成本。agent 先看摘要决定要不要深入，与人类"先看目录再翻章节"同构。
- **目录递归检索**：目录定位 + 语义搜索结合，递归精确获取。
- **检索轨迹可观测**：每次检索留下可视化的目录遍历轨迹，可以观察、调试、指导检索逻辑优化。
- **会话自动管理**：自动压缩会话内容（资源引用、工具调用等），提炼长期记忆——"越用越聪明"的自迭代。
- 生产形态是独立 HTTP 服务；有 LangChain/LangGraph 集成、MCP、CLI（`ov`）。

## 关键设计决策

1. **上下文是"可导航的空间"而非"可检索的池子"**：文件系统隐喻给了 agent 确定性的定位手段（browse/grep/find），检索从一次性召回变成多步导航。
2. **三类上下文统一编址**：memory、知识、技能在同一棵树下，避免了传统方案里 memory store、向量库、工具注册表三套系统各自为政。
3. **分层加载是成本模型**：L0/L1/L2 本质是把"上下文预算是稀缺资源"做成了一等设计约束。

## 与本库主题的关联

- 与 [[Codegraph]]、[[PageIndex]] 构成同一趋势的三个实例：**agent 上下文从"向量池 + 相似度召回"转向"结构化空间 + 导航式访问"**。三者分别落在代码、文档、agent 运行时上下文。
- "上下文数据库"这个自我定位，与 [[Context Layer]] 的厂商叙事（Atlan 的 "context layer 是语义层的超集"）是同一判断的不同侧面——上下文正在成为独立的基础设施层。
- 对 "for data" 的启示：其 L0/L1/L2 分层可直接映射到数据元数据——L0 是表/指标的一句话业务含义，L1 是语义模型结构与口径，L2 是完整 DDL/血缘/样例数据。agent 生成 SQL 前按层深入，而不是把整个 catalog 塞进上下文。
- 注意许可证是 **AGPL-3.0**（GitHub 页面口径；有第三方文章误写 Apache 2.0），商用需评估。

## 参考来源

- GitHub 仓库（volcengine/OpenViking）与官方文档（docs.openviking.ai）
- 第三方解读：faun.pub 的 OpenViking Explained（2026）
