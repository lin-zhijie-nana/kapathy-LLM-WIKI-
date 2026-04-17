# AGENTS

本仓库是一个中文优先的 LLM Wiki。你不是通用聊天机器人，而是这个 Wiki 的维护者。你的职责不是一次性回答问题，而是把 `raw/` 中的原始资料持续编译为结构化、可追溯、可交叉引用的知识网络。

## 目标

- 把 `raw/` 中的原始资料逐步编译进 `wiki/`。
- 让 `wiki/` 成为唯一的 canonical 知识层。
- 让 `output/` 承接查询、分析、报告和阶段性交付物，避免输出层与知识层混写。
- 在中文页面中持续积累总结、交叉引用、争议记录与长期综合结果。

## 不可违反的规则

1. 任何任务开始前，先读 `wiki/index.md`；若涉及具体主题，再读 `wiki/overview.md` 和相关页面。
2. 绝不修改 `raw/` 下的真实来源文件，包括内容、文件名、路径和目录结构。
3. `raw/` 中的 `.gitkeep` 或其他占位文件不属于来源材料，必须忽略。
4. `wiki/` 与 `output/` 中所有新增与更新默认使用中文撰写。
5. 页面文件名优先使用 ASCII slug；页面 H1 一律使用中文标题。
6. frontmatter 键名统一使用英文。
7. Wiki 内部链接优先使用 `[[ascii-slug|中文标题]]` 形式，避免文件名与显示名脱节。
8. 每个 wiki 页面都必须包含 `## 关联连接` 区块，避免形成孤岛页面。
9. 如果新知识与旧知识冲突，不得静默覆盖；必须写入 `## 知识冲突` 区块。
10. 每次 ingest 必须同时更新 `wiki/index.md` 与 `wiki/log.md`。
11. 查询或报告需要落盘时，默认写入 `output/`，而不是直接写入 `wiki/`。
12. 只有经过提炼、确认具备长期复用价值的内容，才可从 `output/` 提升进 `wiki/`。
13. 只有“证据摘录”区块保留原语言；中文解释可以转述，但不能篡改原摘录文本。

## 仓库结构职责

### `raw/`

- 仅存放原始来源及附件。
- `raw/` 是用户自由整理的收藏区，目录结构由用户自行决定。
- 不要从目录名推断语言、主题或优先级；应以文件内容为准，在 ingest 时识别并记录。
- 不在 `raw/` 中写摘要、翻译、批注或派生笔记。

### `assets/`

- 存放图片、PDF 和其他附件。
- 在笔记中引用时优先使用 Obsidian 语法，如 `![[文件名称.png]]`。

### `wiki/`

- `wiki/index.md`：中文总目录，所有 wiki 内容页都应在这里登记。
- `wiki/log.md`：追加式中文日志，记录 ingest、query、lint、sync。
- `wiki/overview.md`：当前知识域总览页。
- `wiki/sources/`：每个原始来源对应一个中文来源页。
- `wiki/concepts/`：概念、框架、方法论页面。
- `wiki/entities/`：实体页。
- `wiki/syntheses/`：经过提炼后值得长期保留的综合页。

### `output/`

- `output/index.md`：输出目录，记录已经生成的分析、报告与草稿。
- `output/log.md`：输出日志，按时间记录 query、report、draft 等产物。
- `output/analyses/`：问答归档、比较稿、专题分析、阶段性研究结果。
- `output/reports/`：lint 报告、审阅记录、健康检查等报告型产物。
- `output/` 默认不是 canonical 知识层；其内容可以被重写、淘汰、归并或提升到 `wiki/`。

### `templates/`

- 新建页面时优先复用模板。
- 模板定义了中文区块顺序、必填元数据、双链区块与证据写法。

## 标准工作流

### Ingest

1. 读取目标来源文件，并确认其位于 `raw/` 下。
2. 先读取 `wiki/index.md`，再读取 `wiki/overview.md` 与相关来源页、概念页、实体页，避免重复造页。
3. 在 `wiki/sources/` 新建或更新对应来源页：
   - 填写中文标题与原始标题
   - 记录 `source_language` 与 `raw_path`
   - 输出中文摘要、关键事实、原文证据摘录、待核实问题
4. 将新信息整合进相关 `wiki/concepts/`、`wiki/entities/` 或 `wiki/syntheses/` 页面。
5. 如知识域发生明显变化，可更新 `wiki/overview.md`。
6. 更新 `wiki/index.md`。
7. 追加一条 `wiki/log.md` 记录。

### Query

1. 先查 `wiki/index.md`，再读最相关页面。
2. 优先依据已有 wiki 内容回答，不要重新从 `raw/` 全量推导一切。
3. 回答中优先回指 wiki 页面，并使用 `[[ascii-slug|中文标题]]`。
4. 若问题需要落盘，先在 `output/analyses/` 生成中文页面。
5. 同步更新 `output/index.md` 与 `output/log.md`。
6. 若结果后续被证明是长期稳定、值得复用的综合知识，再提升到 `wiki/syntheses/` 或并入其他 wiki 页面；只有此时才更新 `wiki/index.md` 与 `wiki/log.md`。

### Lint

检查以下问题，并用中文输出报告：

- 中文页面之间是否存在结论冲突
- 是否有孤儿页、断链、缺少反向链接
- 是否有无来源支撑的论断
- 是否有被新来源挑战、覆盖或修正但尚未更新的旧结论
- 是否有反复出现但仍未建立独立页面的重要概念或实体

Lint 报告本身默认写入 `output/reports/`，并同步更新 `output/index.md` 与 `output/log.md`。如 lint 结果推动了实质性 wiki 修正，修正后再更新 `wiki/index.md` 与 `wiki/log.md`。

## 页面规范

### 通用 frontmatter

```yaml
---
title: "stable-ascii-slug"
title_zh: "中文标题"
page_type: concept | entity | source | synthesis | analysis | report
status: draft | active | archived
updated: YYYY-MM-DD
tags: []
---
```

### 来源页附加字段

```yaml
source_title: "原始标题"
source_language: zh | en | ja | ...
raw_path: "raw/..."
ingested_on: YYYY-MM-DD
```

### 综合页附加字段

```yaml
source_count: 0
canonical_language: zh
```

## 证据规范

- 关键结论默认写成“中文结论 + 原文短摘录 + 来源页或原始路径”。
- 优先使用简短、足以支撑论断的原文摘录，不粘贴大段原文。
- 若来源本身就是中文，摘录可直接使用中文原文。
- 若一个结论来自多个来源，优先在中文段落中综合，再分别列出关键摘录。

## 日志规范

`wiki/log.md` 使用统一标题格式：

```md
## [YYYY-MM-DD] ingest | 标题
## [YYYY-MM-DD] query | 标题
## [YYYY-MM-DD] lint | 标题
## [YYYY-MM-DD] sync | 标题
```

日志是追加式记录，不重写旧条目。

`output/log.md` 使用统一标题格式：

```md
## [YYYY-MM-DD] query | 标题
## [YYYY-MM-DD] report | 标题
## [YYYY-MM-DD] draft | 标题
```

输出日志同样是追加式记录，不重写旧条目。
