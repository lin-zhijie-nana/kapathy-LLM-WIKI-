# LLM Wiki 知识库

本项目是一个基于 [Karpathy 的 LLM Wiki 理念](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 构建的 Obsidian 知识库。它不是一个临时问答仓库，而是一套让 LLM 持续维护、逐步累积、长期可用的知识编译系统。

## 核心理念

将碎片化的信息编译成**结构化、高度相互链接、可追溯**的知识网络，便于 AI 辅助学习、研究与长期沉淀。

核心区别不在于“能不能检索原文”，而在于：

- `raw/` 保存不可变的原始来源。
- `wiki/` 保存已经整理、交叉引用、持续更新的 canonical 知识。
- `output/` 保存查询、分析、报告等阶段性交付物，避免把临时产物直接混入知识层。

## Git 与隐私

- `raw/` 通常包含个人收藏、网页剪藏、会议记录或私有资料，建议默认不纳入公开版本控制。
- `wiki/` 中的知识页可能带有个人判断、研究脉络与长期积累，是否公开应由你自行决定。
- `output/` 更适合存放阶段性分析、草稿、专题报告与 lint 报告，通常也不建议默认公开。

## 目录结构

```text
🏛️ 你的知识库文件夹 (LLM-Wiki-Vault)
├── assets/                      ← 统一媒体资源层：图片、PDF、附件，供 Obsidian 引用
├── raw/                         ← 原始资料收件箱：事实来源层，默认只读、不可变
│   ├── 01-articles/             ← 网页剪藏、技术文章、博客
│   ├── 02-papers/               ← 论文、研报、PDF 文档
│   ├── 03-transcripts/          ← 视频/播客转录文本、会议记录
│   ├── 04-meeting_notes/        ← 头脑风暴、访谈、内部纪要
│   └── ...
├── wiki/                        ← 知识编译层：LLM 维护的 canonical 知识库
│   ├── index.md                 ← 全局内容目录：记录所有 wiki 页面及一句话说明
│   ├── log.md                   ← 追加式操作日志：记录 ingest/query/lint/sync
│   ├── overview.md              ← 当前知识域总览页
│   ├── concepts/                ← 概念、框架、方法论
│   ├── entities/                ← 人物、公司、工具、项目
│   ├── sources/                 ← 对应 raw 来源的一对一摘要页
│   └── syntheses/               ← 经过沉淀后值得长期保留的综合页
├── output/                      ← 输出层：查询结果、专题分析、报告、草稿
│   ├── index.md                 ← 输出目录
│   ├── log.md                   ← 输出日志
│   ├── analyses/                ← 问答归档、比较稿、专题分析
│   └── reports/                 ← lint 报告、审阅记录、健康检查
├── templates/                   ← 页面模板：来源页、概念页、实体页、报告页
├── CLAUDE.md                    ← Agent 行为规范：语言、权限、Schema、工作流
└── .claude/                     ← Claude Code 配置目录
    └── skills/                  ← Agent Skill 中心
        ├── ingest/              ← 编译 raw 来源到 wiki
        ├── query/               ← 检索 wiki 并生成回答/输出
        ├── lint/                ← 知识体检、死链检查、冲突发现
        ├── obsidian-cli/        ← 调用 Obsidian 原生 API
        └── defuddle/            ← 将网页 URL 清洗为 Markdown 存入 raw/
```

## 分层职责

### `raw/`

- 只存放原始来源及其附件。
- 目录结构由用户自行决定，不应从目录名推断主题、语言或优先级。
- LLM 只能读取，不能修改、重命名、移动或删除真实来源文件。
- 若存在 `.gitkeep` 等占位文件，应忽略，它们不属于来源材料。

### `wiki/`

- 这是唯一的知识编译层与 canonical 知识层。
- LLM 在这里维护来源摘要、概念页、实体页、综合页、索引与日志。
- 页面应尽量复用和更新现有内容，而不是无节制新增重复页面。

### `output/`

- 这是查询和交付产物层，不默认视为 canonical 知识。
- 查询结果、比较稿、专题分析、lint 报告优先写入这里。
- 只有经过提炼、确认具备长期复用价值的内容，才提升进 `wiki/`。

### `CLAUDE.md`

- 这是 Agent 的行为规范与工作流说明。
- 当你用 Claude Code 或其他 Agent 维护这个仓库时，应先读取该文件，再执行 ingest、query 或 lint。

## 最短工作流

### `/ingest <路径>`

1. 读取 `raw/` 下的来源文件。
2. 先检查 `wiki/index.md`、`wiki/overview.md` 与相关页面，避免重复造页。
3. 在 `wiki/sources/` 新建或更新对应来源页。
4. 将新信息整合进相关 `wiki/concepts/`、`wiki/entities/` 或 `wiki/syntheses/`。
5. 同步更新 `wiki/index.md` 与 `wiki/log.md`。

### `/query <问题>`

1. 先从 `wiki/index.md` 定位相关页面。
2. 基于现有 wiki 内容回答问题，并用 `[[wikilink]]` 回指相关知识页。
3. 若需要落盘，默认写入 `output/analyses/`，并更新 `output/index.md` 与 `output/log.md`。
4. 只有当结果经过提炼、确认具备长期复用价值时，才提升进 `wiki/`。

### `/lint`

1. 检查孤儿页、断链、缺少反向链接的页面。
2. 检查无来源支撑的论断与被新来源挑战但尚未更新的旧结论。
3. 将 lint 报告默认写入 `output/reports/`。
4. 若 lint 推动了实质性 wiki 修正，再更新 `wiki/index.md` 与 `wiki/log.md`。

## 页面规范

- `wiki/` 与 `output/` 中的页面默认使用中文撰写。
- 页面文件名建议统一使用稳定的 ASCII slug，例如 `model-context-window.md`。
- 页面标题与正文使用中文，必要时通过别名链接显示中文标题。
- frontmatter 键名统一使用英文，减少工具兼容问题。
- 关键论断优先采用“中文结论 + 原文短摘录 + 来源路径”的写法，方便回溯。

## 常用命令

- `/query <问题>`：在知识库中搜索并综合回答问题
- `/ingest <路径>`：将新的原始资料编译到知识库
- `/lint`：检查知识库健康度，输出结构化问题清单

## 知识来源

- Google Gemini API 官方文档
- Anthropic Claude 最佳实践
- 各机构发布的 Prompt Engineering 白皮书
- 学术论文（如 5C Prompt Contracts）
