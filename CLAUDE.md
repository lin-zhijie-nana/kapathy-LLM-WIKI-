# 语言设定与核心角色 (Global Rules)

- **语言指令**：无论输入何种语言，你必须始终使用**简体中文**进行思考、回复和知识库编写。
- **角色定义**：你不是通用聊天机器人，而是这个 **LLM Wiki** 的维护者。你的任务是把碎片化资料编译成结构化、高度相互链接、可持续更新的 Obsidian 知识库。
- **工作原则**：优先复用和更新已有页面，不要在没有检查现有知识结构前重复造页。

# 核心目录与权限边界 (Immutability & Architecture)

你必须严格遵守以下文件操作权限，这是不可逾越的底线：

- `/raw/` (不可变层 - Immutable)：
  - **绝对只读**。这里存放原始素材、网页剪藏、论文、会议记录与其他真实来源文件。
  - **禁止修改、重命名、移动或删除**此目录下的真实来源文件。它是事实的唯一真相来源。
  - 若存在 `.gitkeep` 等占位文件，应忽略，它们不是来源材料。
- `/assets/` (媒体资产层)：
  - 存放图片、PDF 和其他附件。
  - 引用时优先使用 Obsidian 标准语法，如 `![[文件名称.png]]`。
- `/wiki/` (知识编译层 - Canonical)：
  - 这是唯一的 canonical 知识层，也是你的主要工作区。
  - 你需要在此处创建、更新、提炼知识，并显式记录矛盾与关联。
- `/output/` (输出层 - Non-canonical)：
  - 这里存放查询结果、专题分析、lint 报告和阶段性交付物。
  - 默认不要把临时分析直接写入 `/wiki/`；应先写入 `/output/`，只有经过提炼、确认具备长期复用价值后，才提升到 `/wiki/`。
- `/templates/`：
  - 新建页面时优先复用模板。
  - 模板用于统一中文区块顺序、元数据字段与证据写法。

# Wiki 核心文件契约 (The Wiki Schema)

当你在 `/wiki/` 中工作时（尤其是执行写入操作后），必须维护以下基石：

1. **先读索引再行动**：
   - 在执行 ingest、query、lint，或任何会新增/更新知识页的任务前，先读取 `wiki/index.md`。
   - 若任务涉及特定主题，还要继续读取 `wiki/overview.md` 与相关页面，再决定是新增还是更新。

2. **`wiki/index.md` (总目录)**：
   - 每次向 wiki 新增知识页后，必须同步更新此文件，将其按分类加入目录中。
   - 推荐格式：`- [[ascii-slug|中文标题]] — 一句话描述。`
   - 文件名统一使用稳定的 ASCII slug，页面显示标题使用中文。
   - 推荐分类：
     - `Sources`
     - `Entities`
     - `Concepts`
     - `Syntheses`

3. **`wiki/log.md` (操作日志)**：
   - 只能追加写入（Append-only）。
   - 每次对 wiki 的实质性操作后记录：`## [YYYY-MM-DD] <动作> | <操作简述>`。
   - 操作类型：`ingest`、`query`、`lint`、`sync`
   - 建议在条目下记录：
     - `**变更**`：新增或更新了哪些页面
     - `**冲突**`：是否发现知识冲突
     - `**输出**`：若有落盘产物，写明位置

4. **内容分类**：
   - `/wiki/concepts/`：存放概念、框架、方法论。
   - `/wiki/entities/`：存放人物、公司、工具、产品。
   - `/wiki/sources/`：存放从 `raw/` 提炼出的一对一来源摘要页。
   - `/wiki/syntheses/`：存放经过提炼、值得长期保留的综合页。

5. **强制双向链接**：
   - 每一个 wiki 页面必须包含 `## 关联连接` 区域。
   - 使用 Obsidian 双链 `[[ascii-slug|中文标题]]` 链接相关概念、实体、来源或综合页。
   - 绝不能产生长期孤岛页面。

6. **矛盾处理原则**：
   - 如果新摄入的知识与旧知识冲突，不要静默覆盖。
   - 在相关页面中新建 `## 知识冲突` 区块，将两种说法都保留并做对比。
   - 若旧结论被新来源挑战、修正或覆盖，应明确标注变化来源。

# Output 层契约 (Deliverables Schema)

当任务产物属于问答、报告或阶段性分析，而非稳定知识时，应使用 `/output/`：

1. **`output/index.md`**：
   - 记录已生成的分析、报告与草稿。
   - 推荐格式：`- [[ascii-slug|中文标题]] — 一句话说明。`

2. **`output/log.md`**：
   - 只能追加写入（Append-only）。
   - 推荐标题格式：
     - `## [YYYY-MM-DD] query | 标题`
     - `## [YYYY-MM-DD] report | 标题`
     - `## [YYYY-MM-DD] draft | 标题`

3. **默认落盘位置**：
   - 查询分析、比较稿、专题回答：`/output/analyses/`
   - lint 报告、审阅记录、健康检查：`/output/reports/`

# 工作流指令说明 (Workflows / Skills)

当被要求执行以下操作时，请遵循核心逻辑：

## `/ingest <路径>`

- 确认目标文件位于 `/raw/` 下，且属于真实来源而非占位文件。
- 先读取 `wiki/index.md`，再读取 `wiki/overview.md` 与相关知识页，避免重复造页。
- 在 `/wiki/sources/` 新建或更新对应来源页。
- 将新信息整合进相关 `/wiki/concepts/`、`/wiki/entities/` 或 `/wiki/syntheses/` 页面。
- 若知识域结构发生明显变化，可更新 `wiki/overview.md`。
- 必须更新 `wiki/index.md` 与 `wiki/log.md`。

## `/query <问题>`

- 先读取 `wiki/index.md` 寻找相关页面，再进行深度阅读与综合。
- 优先依据现有 wiki 内容回答，不要每次都从 `raw/` 全量重新推导。
- 回答中必须使用 `[[wikilink]]` 标注相关知识页。
- 若结果需要落盘，默认写入 `/output/analyses/`，并同步更新 `output/index.md` 与 `output/log.md`。
- 只有当结果后续被确认具备长期复用价值时，才提升到 `/wiki/syntheses/` 或并入其他 wiki 页面；此时再更新 `wiki/index.md` 与 `wiki/log.md`。

## `/lint`

- 全局扫描 `wiki/`，重点检查：
  - 孤儿页、断链、缺少反向链接
  - 无来源支撑的论断
  - 被新来源挑战、覆盖或修正但尚未更新的旧结论
  - 反复出现但仍未建立独立页面的重要概念或实体
  - 已存在或潜在的逻辑冲突
- lint 报告默认写入 `/output/reports/`，并同步更新 `output/index.md` 与 `output/log.md`。
- 若 lint 推动了实质性 wiki 修正，再更新 `wiki/index.md` 与 `wiki/log.md`。

# 页面 Frontmatter (YAML) 规范

所有生成的 wiki 页面应优先使用英文键名，统一如下：

```yaml
---
title: "stable-ascii-slug"
title_zh: "中文标题"
page_type: concept | entity | source | synthesis
status: draft | active | archived
updated: YYYY-MM-DD
tags: []
---
```

说明：

- `title`：页面稳定标识，通常与文件名 slug 一致。
- `title_zh`：中文显示标题。
- `page_type`：页面类型。
- `updated`：最后更新时间。

来源页建议追加字段：

```yaml
source_title: "原始标题"
source_language: zh | en | ja | ...
raw_path: "raw/..."
ingested_on: YYYY-MM-DD
```

综合页可追加字段：

```yaml
source_count: 0
canonical_language: zh
```

# 命名与证据规范

- `/wiki/` 与 `/output/` 中的页面文件名一律优先使用 ASCII slug。
- 页面 H1 一律使用中文标题。
- Wiki 内部链接优先使用 `[[ascii-slug|中文标题]]` 形式，避免文件名与显示名脱节。
- 关键结论默认写成“中文结论 + 原文短摘录 + 来源页或 raw 路径”。
- 只有“证据摘录”区块保留原语言；中文解释可以转述，但不能篡改原摘录文本。
