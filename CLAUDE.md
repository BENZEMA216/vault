# CLAUDE.md

> Schema for this LLM Wiki. Defines structure, conventions, and workflows.
> Pattern: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## Vault 概述

Dongzhe 的个人知识库（Obsidian），采用 **LLM Wiki** 模式。

Obsidian 是 IDE，LLM 是 programmer，wiki 是 codebase。

GitHub remote: https://github.com/BENZEMA216/vault

## 三层架构

```
raw/     原始素材（immutable，LLM 只读不改）
wiki/    LLM 编译产物（LLM 拥有，人只读）
output/  查询产出（可回流 wiki/）
```

## 目录结构

```
vault/
├── raw/                           # Layer 1: Raw Sources
│   ├── papers/                    # 论文 PDF + 笔记（按方向分类）
│   ├── projects/                  # 项目文档（creative-cowork, jimeng-video-agent, etc.）
│   └── articles/                  # 研究文章、学习笔记、深度研究
│       ├── claude-code-research/
│       ├── harness-engineering/
│       ├── agent-communication/
│       ├── world-model/
│       ├── learning-notes/
│       └── ...
│
├── wiki/                          # Layer 2: The Wiki
│   ├── _index.md                  # 按领域分类的内容索引（LLM 查询入口）
│   ├── _summaries.md              # 所有 raw/ 文件的一行摘要
│   ├── log.md                     # Append-only 时间线（可 grep 解析）
│   ├── concepts/                  # 概念文章（23 篇）
│   ├── maps/                      # 主题地图（6 篇）
│   └── connections/               # 跨主题关联发现（6 篇）
│
├── output/                        # Query 产出
│   ├── reports/
│   └── slides/                    # Marp 格式
│
├── .claude/commands/              # Slash commands（/kb-ingest, /kb-query, etc.）
├── active_context.md              # 热记忆：当前优先级
└── CLAUDE.md                      # Layer 3: This Schema
```

## 操作流程

### Ingest（收录新素材）

1. 素材存入 `raw/` 对应子目录（不修改原始内容）
2. 讨论关键 takeaways
3. 写 summary → 更新 `_summaries.md`
4. 更新 `_index.md`（加入对应领域分类）
5. 更新相关 `concepts/`、`maps/`、`connections/`（一个源可能触及 10-15 个 wiki 页面）
6. Append 到 `log.md`：`## [YYYY-MM-DD] ingest | 标题`

### Query（查询研究）

1. 读 `_index.md` 定位相关页面
2. 读取相关 concepts/maps，按需回溯 raw/
3. 合成答案（markdown / 对比表 / Marp 幻灯片 / matplotlib）
4. **有价值的答案归档回 wiki/**（Query 结果不应消失在聊天记录里）
5. Append 到 `log.md`：`## [YYYY-MM-DD] query | 问题摘要`

### Lint（健康检查）

检查项：
- 矛盾：不同页面间的矛盾陈述
- 过时：被新来源取代的旧结论
- 孤儿：没有 inbound links 的页面
- 缺失：被频繁提及但没有独立页面的概念
- 断链：`[[wiki-link]]` 指向不存在的文件
- 数据缺口：可以通过 web search 补充的信息

Append 到 `log.md`：`## [YYYY-MM-DD] lint | 发现 N 个问题`

## 页面格式

### Concept 文章

```markdown
# [概念名称]

> 一句话定义

## 核心要点
## 详细说明
## 在知识库中的出现
| 来源 | 上下文 |
## 关联概念

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
```

### Map 文章

```markdown
# [主题] 全景

> 一句话概述

## 概览
## 关键组件（链接 concepts）
## 数据来源（链接 raw/）
## 开放问题
```

### Log 条目（可 grep 解析）

```markdown
## [2026-04-06] ingest | Article Title
- Source: URL or path
- New/updated pages: ...
- Wiki state: N concepts, N maps, N connections
```

## 编辑规范

- 中文为主，技术术语保持英文
- Obsidian `[[wiki-link]]` 语法
- raw/ 中的文件**只增不改**（immutable source of truth）
- wiki/ 中所有文件底部标注 `*由 LLM 从 raw/ 数据编译，请勿手动编辑*`
- User Interview 访谈原文**禁止修改**
- 论文 PDF 放 `raw/papers/` 对应子目录
- 论文索引需包含：标题、作者、年份、会议、引用数、arXiv URL、质量评级

## 访谈总结规范

- 命名：`{原文件名} - 总结.md`
- 必须包含：公司概况、团队组织、制作流程、工具矩阵、核心痛点、质量标准、金句摘录

## Obsidian Tips

- **Graph View** 查看 wiki 结构和孤儿页面
- **Web Clipper** 快速抓取网页文章到 raw/
- **Marp** 插件渲染 output/slides/
- **Dataview** 插件查询 YAML frontmatter
- Wiki 是 git repo，有版本历史和协作
