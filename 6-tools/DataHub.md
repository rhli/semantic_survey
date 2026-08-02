---
type: tool
status: draft
tool: DataHub
category: metadata-graph
license: Apache-2.0（Core）；DataHub Cloud 商业
url: https://github.com/datahub-project/datahub
---

# DataHub

## 定位

LinkedIn 起源的开源元数据平台，与 [[OpenMetadata]] 并称开源元数据图谱双雄。对本库的意义不在"又一个 catalog"，而在于：**它是现成的、带 API 与 MCP 的数据资产图**——"codegraph for data" 的图存储与消费层有现成实现可以研究或复用。

## 核心机制

- 图模型：资产（dataset/dashboard/pipeline）为节点，血缘、ownership、标签、术语为边与属性；原生列级血缘。
- 摄入：推送（[[OpenLineage]] REST 端点、Spark listener 插件）+ 拉取（连接器爬取）双通道。
- AI 面：Ask DataHub（对话式 agent 回答数据资产问题）、托管 MCP server 把 catalog 直连 AI 工具。
- 商业侧 DataHub Cloud（原 Acryl）提供托管与增强。

## 对 "codegraph for data" 的意义

它证明了"数据资产图"这条路的可行性，也暴露了与 codegraph 体验的差距：

- **查询接口面向人而非 agent**：图存在，但主要消费方式是搜索框与 UI。MCP server 是补齐 agent 面的开始，但工具粒度（搜资产、拿血缘）离 codegraph 的 `trace`/`impact` 式确定性结构查询还有距离。
- ** freshness 靠连接器调度**，不是 codegraph 式的文件 watcher 秒级增量——图的新鲜度是配置出来的，不是架构保证的。
- 务实结论：如果做 "for data"，DataHub 是值得对接的**上游图源**（它已经是很多企业的元数据事实库），而不是要重复建造的轮子；差异化空间在 agent 消费层。

## 参考来源

- [[2026-08-02 SQLGlot 与开源血缘生态]]
