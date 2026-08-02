---
type: tool
status: draft
tool: OpenMetadata
category: metadata-graph
license: Apache-2.0；商业侧 Collate
url: https://github.com/open-metadata/OpenMetadata
---

# OpenMetadata

## 定位

与 [[DataHub]] 并列的开源元数据图谱（商业侧为 Collate）。架构哲学差异：OpenMetadata 是 **schema-first + API-first**——所有元数据实体由强类型 JSON schema 定义，一切功能经 REST API 暴露，UI 只是 API 的消费者。

## 核心机制

- 统一元数据模型：实体（table/topic/dashboard/pipeline/…）与关系由版本化 schema 定义，扩展走 custom properties。
- 原生列级血缘；摄入框架支持拉取式连接器 + [[OpenLineage]] 事件。
- **MCP server 内置**：`/mcp` 端点以 JSON-RPC 2.0 把元数据图谱暴露给 AI agent——开源 catalog 里 agent 化最激进的一个。
- 商业侧 Collate 提供托管与增强（较新，运营记录尚在积累）。

## 对 "codegraph for data" 的意义

与 DataHub 相同的判断（见 [[DataHub]] 页）：现成图源，值得对接而非重建。它独有的参照价值是 **schema-first 的元数据模型治理**——实体的 schema 有版本、可演进、可校验。这正对应 codegraph 里"节点/边类型是显式 schema"的设计：图的查询可靠性取决于底层模型的纪律性。做 "for data" 时，元数据 schema 的版本治理是容易被跳过、之后一定会回来咬人的一层。

## 参考来源

- [[2026-08-02 SQLGlot 与开源血缘生态]]
