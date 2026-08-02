---
type: tool
status: draft
tool: Kuzu
category: graph-engine
license: MIT（原仓库已归档；分叉延续）
url: https://github.com/kuzudb/kuzu
---

# Kuzu

## 定位

"图界的 DuckDB"：嵌入式、进程内的属性图数据库，Cypher 查询，列式存储 + 向量化/因子化查询处理器，学术血统（University of Waterloo，worst-case optimal join 论文落地）。宣称路径查询比 Neo4j 快 374 倍（厂商口径）。

**重大变故：2025-10 被 Apple 收购后原 GitHub 仓库归档、停止开发。** 社区分叉延续：Vela Partners 的 fork（加了多 writer 并发，定位 AI agent 记忆）、LadybugDB。选用时必须按"维护一个归档项目的分叉"来评估风险。

## 核心机制

- 进程内嵌入式：无服务器、无 Docker，`pip install kuzu` 即用——部署形态与 SQLite 相同。
- 列式磁盘存储 + CSR 邻接索引；多跳遍历与多对多 join 是其设计甜区（OLAP 图查询），不是 OLTP。
- 内置向量检索与全文检索；官方 MCP server（getSchema / query / generateKuzuCypher，支持只读模式）。

## 对 "codegraph for data" 的意义

**存储引擎层的候选，但不是唯一答案**。值得注意的对照：[[Codegraph]] 自己用的是 **SQLite** 而不是图数据库——它的查询负载（callers/callees/impact）是一两跳的定点遍历，关系表 + 索引足够，换来的是零依赖与成熟稳定。

选型判断：
- 查询以**短路径定点遍历**为主（这个指标依赖哪些表？这个列上游是谁？）→ SQLite/Postgres 关系表就够，别引入图数据库。
- 需要**任意长度路径查询、图算法**（影响面传播、循环检测、中心性排序——比如给数据资产跑一个 OntoRank 式权威度）→ Kuzu 这类图引擎才有理由存在。
- Kuzu 的归档状态意味着：用它就要接受 Vela/LadybugDB 分叉的治理风险，或者把它当"可替换的存储后端"隔离在接口之后。

## 参考来源

- [[2026-08-02 SQLGlot 与开源血缘生态]]
