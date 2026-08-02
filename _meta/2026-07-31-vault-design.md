---
type: meta
created: 2026-07-31
status: draft
---

# Semantic Survey Vault 设计

关于企业语义层（Enterprise Semantic Layer）领域的长期调研知识库，基于 Obsidian。

## 1. 目的

- **长期知识库**：持续跟踪语义层领域的演进（厂商、产品、技术路线、概念），不是一次性报告。
- **两层深度**：既覆盖产品能力层面（功能、定位、客户场景），也覆盖技术机制层面（架构、API、语义模型表达方式、查询改写原理等）。
- **工作流**：AI 负责调研并生成初稿、维护结构纪律（frontmatter、双链、模板）；人负责修订内容、沉淀判断。

初始调研对象：Databricks（Unity Catalog、Genie）、Snowflake、Atlan、Aloudata。后续可扩展（dbt Semantic Layer、Cube、AtScale、Looker 等）。

## 2. 设计原则

1. **概念页是长期骨架**。公司会兴衰、产品会改名，但 Semantic Model、Metrics Layer、NL2SQL 这些概念是稳定的。概念页解释概念本身，并链接各家实现。
2. **产品页是一等公民**。Genie 和 Unity Catalog 各自成页，不埋在公司页里；通过 frontmatter 标注归属公司和能力维度。
3. **事实与观点分离**。
   - `1-sources/`：材料说了什么（带出处和日期）；
   - `2-companies/`：这家/这个产品实际是什么（关键论断链回 source）；
   - `5-insights/`：我认为这意味着什么。
   三者不混写，过时信息可甄别。
4. **来源可追溯**。每条关键论断能回答"什么时候、从哪篇材料得出"。

## 3. 目录结构

```
semantic_survey/
├── Home.md            # vault 入口：导航 + Dataview 总览
├── 0-inbox/           # AI 调研原始产出、剪藏，待整理
├── 1-sources/         # 来源笔记：每篇文档/博客/talk/论文一条
├── 2-companies/       # 公司页 + 产品页（平铺，不建子文件夹）
├── 3-concepts/        # 概念页
├── 4-comparisons/     # 横向对比页（能力矩阵、按维度对比）
├── 5-insights/        # 综合判断与洞见
├── 6-tools/           # 工具页：与本库主题相关的工具/基础设施（索引、检索、agent 上下文）
├── _templates/        # 各类笔记模板
├── _meta/             # vault 自身的设计与约定文档（本文件）
├── _assets/           # 笔记中嵌入的图片、图表、截图（Obsidian 附件目录）
└── _archive/          # 关键原始材料的全文存档（防链接腐烂）
```

`_assets/` 与 `_archive/` 是两类不同的东西，不要混用：前者是笔记内容的一部分（嵌入显示的图），后者是不进入正文的证据留档。

空目录（如 `0-inbox/`、`_assets/`）需放 `.gitkeep`，否则 git 不跟踪，clone 后目录丢失。

## 4. 笔记类型与 frontmatter schema

所有笔记必须有 `type` 和 `status` 字段。`status: draft`（AI 初稿，未经人工修订）/ `reviewed`（人工修订过）/ `stale`（已知过时，待更新）。

### source（来源笔记）

```yaml
type: source
status: draft
url: https://...
source_type: docs | blog | talk | paper | news | pricing
publisher: Databricks
published: 2025-06-01        # 原文发布日期，未知则留空
accessed: 2026-07-31         # 调研日期
companies: [databricks]      # 涉及的公司（小写 slug）
topics: ["[[Metrics Layer]]", "[[Text2SQL]]"]
```

正文结构：摘要（3–5 句）→ 关键内容摘录/转述 → 与本库其他笔记的关联。
命名：`YYYY-MM-DD <Publisher> - <标题短语>.md`，日期用 accessed 日期。

可选 `archive` 字段：若做了全文存档，填存档笔记的双链。

### archive（原文存档）

放在 `_archive/`，用于对抗链接腐烂。**选择性存档**，只存三类：

1. 官方规范/参考文档（会持续修订，需要核对基线）
2. 技术白皮书
3. 可能下线消失的一手材料

普通博客、新闻不存——source 笔记里的要点摘录已足够。

命名：`<对应 source 笔记名> (raw).md`。加 `(raw)` 后缀是为了避免与 source 笔记同名造成双链歧义。

frontmatter 只需四项：

```yaml
type: archive
url: <原始 URL>
source_note: "[[<对应 source 笔记名>]]"
archived: YYYY-MM-DD
```

`type: archive` 使其在 Dataview 查询中可被排除（Home 页的"最近更新"已排除）。若在 Obsidian 中觉得存档笔记干扰搜索与图谱，可在设置 → 文件与链接 → 排除的文件中加入 `_archive`。

### company（公司页）

```yaml
type: company
status: draft
company: databricks          # 小写 slug，全库统一
founded: 2013
hq: San Francisco
```

正文：一句话定位 → 语义层相关产品线（链接产品页）→ 战略与路线观察 → 时间线（重要发布/收购，逐条链 source）。

公司即产品的单产品厂商（如 AtScale、Atlan）不强行拆两页：合并为一个 company 页，frontmatter 额外携带 `capabilities` 和 `ga_status`，保证 Dataview 按 capability 聚合时不遗漏。

### product（产品页）

```yaml
type: product
status: draft
company: databricks
product: Unity Catalog
capabilities: [data-catalog, governance, metrics-layer]
ga_status: ga | preview | beta
```

正文：定位与解决的问题 → 核心概念与架构（技术机制）→ 语义模型的表达方式（DSL/YAML/API 示例）→ 能力细节（按 capability 分节）→ 限制与边界 → 参考来源。

### concept（概念页）

```yaml
type: concept
status: draft
aliases: [指标层, metric layer]
```

正文：定义 → 为什么重要/解决什么问题 → 关键设计维度 → 各家实现对比入口（链接产品页，或嵌入 Dataview 查询）→ 相关概念。

### comparison（对比页）

```yaml
type: comparison
status: draft
dimension: metrics-layer     # 对比的能力维度或主题
companies: [databricks, snowflake, atlan, aloudata]
```

正文：对比结论先行 → 逐家分析 → 矩阵表格（尽量用 Dataview 自动聚合，主观评级列手工维护）。

### insight（洞见）

```yaml
type: insight
status: draft
date: 2026-07-31
topics: ["[[Metrics Layer]]"]
```

正文：判断/推论 → 依据（链接 source 和产品页）→ 置信度与待验证点。
命名：`YYYY-MM-DD <判断短语>.md`。

### tool（工具页）

```yaml
type: tool
status: draft
tool: PageIndex
category: code-index | doc-index | agent-context   # 按需扩展
license: MIT
url: https://...
```

正文：定位 → 核心机制 → 关键设计决策 → 与本库主题的关联（对 "for data" 方向的启示）→ 参考来源。

工具页与产品页的区别：**产品页属于本库的调研对象**（语义层赛道里的厂商产品，进 capability 聚合），**工具页属于调研的参照系**（别的领域解决"给 AI 供上下文"问题的工具，用来启发设计判断）。工具页不带 `capabilities` 字段，不进 Capability Matrix。

## 5. capability 受控词表

产品页 `capabilities` 字段只能取以下值（需要新值时先在此登记，并建对应概念页）：

| capability | 概念页 | 含义 |
|---|---|---|
| semantic-modeling | [[Semantic Model]] | 语义模型定义：实体、维度、关系的建模与表达 |
| metrics-layer | [[Metrics Layer]] | 指标定义、口径管理、指标 API |
| text2sql | [[Text2SQL]] | 自然语言查询 / conversational BI |
| data-catalog | [[Data Catalog]] | 元数据编目、搜索、资产管理 |
| lineage | [[Data Lineage]] | 血缘追踪 |
| governance | [[Data Governance]] | 权限、合规、访问控制 |
| query-rewrite | [[Query Rewrite]] | 语义查询到物理 SQL 的编译/改写 |
| acceleration | [[Query Acceleration]] | 物化、缓存、预计算 |
| headless-bi | [[Headless BI]] | 开放 API 供下游 BI/应用消费语义 |
| noetl | [[NoETL]] | 逻辑数据编织 / 虚拟化，免搬运 |

## 6. 语言与命名约定

- 正文中文；术语、产品名、专有名词保留英文（首次出现可加中文注释）。
- 文件名英文，自然写法（`Unity Catalog.md`、`Metrics Layer.md`），不用 kebab-case——文件名即双链显示名。对比页与洞见页可用中文标题（`上下文层的四条路线.md`），因为它们是本库自产的判断而非外部专名。
- 公司 slug 全库统一小写：`databricks`、`snowflake`、`atlan`、`aloudata`。
- 双链指向页面用 `[[Unity Catalog]]`；正文首次提及相关概念时就建链，宁多勿少。
- **对比页不带日期前缀**（`Capability Matrix.md`），**洞见页带日期前缀**（`2026-07-31 <判断短语>.md`）。前者是持续维护的活页面，后者是某个时点的判断。

### 并购与更名的处理

赛道并购频繁（如 dbt Labs 于 2026-06 并入 Fivetran），约定如下：

1. **页面重命名为合并后的官方名称**，用 `git mv` 保留历史。
2. **slug 换为合并后的形式**（`dbt-labs` → `fivetran-dbt-labs`），并**同步更新全库所有 `company:` / `companies:` 字段**，包括并购前发布的旧材料——slug 标识的是本库中的**实体**，不是材料发表时的公司名。否则公司页的 Dataview 会漏掉旧来源。
3. 旧名进 `aliases`，使指向旧名的双链仍可解析。
4. 公司页顶部加**命名说明块**，交代新旧名称的时间分界、slug 约定，以及本库跟踪范围（合并后的实体往往含大量超出本库主题的业务）。
5. 笔记正文按材料的时间点使用当时的名称，不做时代错置的改写。

改完后跑一次链接与重名检查（见第 11 节）。

## 7. Dataview 视图

依赖 Dataview 插件（需在 Obsidian 中安装）。核心视图：

- **Home.md**：全库统计、最近更新的笔记、`status: draft` 待修订清单。
- **概念页内**：按 capability 拉出实现该能力的产品列表
  （`TABLE company, ga_status FROM "2-companies" WHERE contains(capabilities, "metrics-layer")`）。
- **公司页内**：该公司的产品列表、相关 source 列表。
- **对比页内**：能力矩阵的客观部分自动聚合，主观评级手工写。

**全域查询必须排除 `_templates`**：写 `FROM "" AND !"_templates"`。六个模板都带 `type: <类型>` 与 `status: draft`（这是必要的——模板的 frontmatter 会成为新笔记的 frontmatter，不能塞 `type: template` 之类的占位值让人每次去改），所以任何 `FROM ""` 的查询都会把它们算进去：「待修订」列表里混进 `tpl-*`，「全库统计」每一类计数虚高 1。按目录限定的查询（`FROM "2-companies"` 等）不受影响。

`_archive` 的处理不同：`type: archive` 使它可用 `WHERE type != "archive"` 排除，「最近更新」已排除，「全库统计」保留（存档数量本身是有用信息）。

## 8. Obsidian 设置

**Vault 根目录必须是 `semantic_survey/`，不是上层的 `huawei/`。** 上层目录含 `bird_data/`、`yunhe_table_cluster/`、`DG_nl2sql/` 等数据目录，作为 vault 打开会被全部索引。

**配置已随库提交，无需手工点选。** `.obsidian/` 被 git 跟踪（仅 `workspace*.json` 被忽略），下列设置以配置文件形式固化，clone 后打开即生效：

| 文件 | 设置 | 值 | 作用 |
|---|---|---|---|
| `app.json` | `attachmentFolderPath` | `_assets` | 粘贴的图片写入 `_assets`，不散落到 vault 根目录 |
| `app.json` | `newFileLocation` / `newFileFolderPath` | `folder` / `0-inbox` | 新建笔记落 inbox |
| `app.json` | `alwaysUpdateLinks` | `true` | 重命名笔记时自动改全库双链，防止改名造成断链 |
| `templates.json` | `folder` | `_templates` | 模板插件的模板目录 |
| `appearance.json` | `enabledCssSnippets` | `["hide-infrastructure-folders"]` | 启用隐藏下划线目录的 CSS 片段 |

改这些设置时，**在 Obsidian 界面里改比直接编辑文件安全**——Obsidian 运行时以内存状态为准，外部修改需重载 vault（Cmd+R）或重启才读取，且可能被 Obsidian 的下一次写入覆盖。

### 唯一需要手工做的一步：安装 Dataview

**设置 → 第三方插件 → 关闭安全模式 → 浏览 → 搜索 Dataview → 安装并启用。** 当前使用的版本记录在 `.obsidian/plugins/dataview/manifest.json`（0.5.68）。

插件的**设置**随库提交（`data.json` 中 `enableDataviewJs: true`，`Capability Matrix` 的按能力反查用到 dataviewjs），所以装完不需要再逐项配。插件**代码**不提交——`main.js` 是 1.3MB 的压缩产物，每次升版都会在 git 历史里留一份新 blob。`.gitignore` 的规则是只忽略 `main.js` 与 `styles.css`，保留 `manifest.json` 与 `data.json`。

**没装 Dataview 的症状**：Home 页的「洞见」「横向对比」「待修订」「最近更新」「全库统计」、`Capability Matrix` 的自动聚合、各公司页的「相关来源」全部显示为原始代码块而不是表格。若只有 `Capability Matrix` 的「按能力反查」报错、其余正常，说明插件在但 JavaScript Queries 没开。这两个症状是判断配置是否生效最快的方式。

### 侧边栏整洁度

以点开头的目录（`.git`、`.obsidian`、`.specstory`）Obsidian 一律不显示，无需处理。

下划线开头的四个基础设施目录会显示。CSS 片段 `hide-infrastructure-folders.css` 把它们全部隐藏（`_templates`、`_assets`、`_archive`、`_meta`），已在 `appearance.json` 中启用。它只影响侧边栏显示，不影响搜索、图谱、Dataview、模板插件、附件写入；这些目录里的笔记仍可通过 Cmd+O、全局搜索、双链正常访问。`_meta` 的入口在 Home 页的双链上。

另有一个作用范围不同的手段可叠加：**设置 → 文件与链接 → 排除的文件**。它降低搜索排序权重、从图谱中移除，但侧边栏中只变灰不消失。若把大量原文存档放进 `_archive`，建议在这里加入 `_archive`，避免全文搜索被存档内容淹没。

### 为什么不把内容移进子文件夹

一个常见的想法是把 `0-5` 内容目录移进 `vault/`、让下划线目录留在外层、Obsidian 只打开 `vault/`。**这个方案不可行**：

- `_templates/` 与 `_assets/` **必须在 vault 内**——模板文件夹与附件路径都是 vault 相对路径，vault 外的图片无法用 `![[...]]` 嵌入。所以它们挪不出去，侧边栏照样显示，CSS 片段仍然必需。
- `_archive/` 与 `_meta/` 被正文双链引用（source 笔记的 `[[... (raw)]]`、Home 的 `[[2026-07-31-vault-design]]`）。双链只在 vault 内解析，移出去即断链——而存档能从 source 笔记点进去正是它存在的意义。

结论：vault 根目录保持 `semantic_survey/`，显示问题用 CSS 解决。

## 9. 工作流

1. **调研**：AI 围绕一个主题/公司调研，原始产出落 `0-inbox/`。
2. **结构化**：AI 将产出拆为 source 笔记（进 `1-sources/`），并创建/更新对应产品页、公司页、概念页，建双链，全部标 `status: draft`。符合存档标准的材料同时存入 `_archive/`。
3. **修订**：人审阅 draft 笔记，修订后改 `status: reviewed`。
4. **沉淀**：积累一定材料后，写对比页和 insight。
5. **维护**：发现过时信息标 `status: stale`，触发重新调研。

`0-inbox/` 中已完成结构化的原料即删除，inbox 保持趋近于空。

## 10. 初始种子内容（首批搭建范围）

- `Home.md` + 全部模板（6 类）+ 本设计文档。
- 概念页：先建受控词表中的 10 个概念页骨架 + `Semantic Layer` 总览页。
- 公司页：Databricks、Snowflake、Atlan、Aloudata 四个骨架。
- 产品页：Unity Catalog、Genie（Databricks）；Cortex Analyst、Semantic Views（Snowflake）；Atlan 平台；Aloudata 产品线（AnyMetrics 等，调研后定）。
- 对比页：先建 `Capability Matrix.md` 一个。
- 种子页先建骨架（frontmatter + 章节标题 + 待调研问题清单），内容由后续调研工作流填充。

## 11. 一致性检查

每轮调研收尾时跑一次，检查两件事：**双链是否都能解析**、**是否有重名笔记**（重名会造成双链歧义，Obsidian 会随机选一个）。

在 vault 根目录执行：

````bash
python3 - <<'EOF'
import re, os, glob
basenames = {}
for p in glob.glob('**/*.md', recursive=True):
    basenames.setdefault(os.path.splitext(os.path.basename(p))[0], []).append(p)
missing = {}
for p in glob.glob('**/*.md', recursive=True):
    if p.startswith('_archive/') or p.startswith('_templates/'): continue
    txt = open(p, encoding='utf-8').read()
    txt = re.sub(r'```.*?```', '', txt, flags=re.S)   # 围栏代码块
    txt = re.sub(r'`[^`\n]*`', '', txt)               # 行内代码
    for m in re.findall(r'\[\[([^\]\|#]+)', txt):
        t = m.strip()
        if t and t not in basenames: missing.setdefault(t, []).append(p)
print("MISSING:", missing if missing else "none")
print("DUPS:", {k:v for k,v in basenames.items() if len(v)>1} or "none")
EOF
````

三处必要的细节，都是踩过的坑：

- `_templates/` 要**排除**（模板里有 `<同名> (raw)` 这类占位符，不是真链接）。
- 围栏代码块与**行内代码都要剥掉**（示例 YAML 和正文里讲解语法用的双链写法不是真链接，Obsidian 也不会渲染成链接）。
- `_archive/` **必须计入 basenames**（虽然不扫描它的正文），否则 source 笔记指向存档的 `(raw)` 链接会被全部误报为断链。

### 表格截断检查

改表格时容易在中间插入空行或段落，把一张表切成"表格 + 一堆纯文本管道行"。Obsidian 阅读视图下才看得出来，编辑视图里不明显。

````bash
python3 - <<'EOF'
import glob, re
bad = []
for p in sorted(glob.glob('**/*.md', recursive=True)):
    if p.startswith('_archive/'): continue
    lines = open(p, encoding='utf-8').read().split('\n')
    infence = False
    for i, ln in enumerate(lines):
        if ln.startswith('```'): infence = not infence; continue
        if infence or not ln.lstrip().startswith('|'): continue
        if (lines[i-1] if i else '').lstrip().startswith('|'): continue   # 接在表格行后，正常
        nxt = lines[i+1] if i+1 < len(lines) else ''
        if re.match(r'^\s*\|[\s:|-]+\|\s*$', nxt): continue              # 是表头，正常
        bad.append((p, i+1, ln[:80]))
print("孤立表格行:" if bad else "孤立表格行: none")
for p, n, ln in bad: print(f"  {p}:{n}  {ln}")
EOF
````

原理是：一个以 `|` 开头的行，要么紧接在另一个表格行之后，要么它自己是表头（下一行为 `|---|` 分隔行）。两者都不满足就是孤立行。

两段脚本的外层围栏都用**四个反引号**，因为脚本正文里含三反引号字面量，三反引号围栏会被提前闭合——症状是后半段脚本渲染成正文、其后的小标题层级错乱。

## 12. 非目标

- 不做自动化剪藏管道（Readwise 之类），来源笔记由调研工作流产生。
- 不全量存档网页快照，只按第 4 节的三条标准选择性存档。
- 不引入 Dataview 之外的复杂插件依赖（Templater 可选，核心模板够用）。
- 不预先穷举所有厂商，按调研节奏逐步扩展。
