# CLAUDE.md

This file provides guidance to Claude Code when working in this Obsidian vault.

## Vault 概述

这是 Dongzhe 的个人知识库（Obsidian），采用 **AK 知识库模式**：raw 数据 → LLM 编译 → wiki → CLI 操作 → Obsidian 浏览。

GitHub remote: https://github.com/BENZEMA216/vault

## 目录结构

```
vault/
├── active_context.md          # 热记忆：当前工作状态和优先级
│
├── raw/                       # 原始素材（只增不改，LLM 的数据源）
│   ├── papers/                # 论文 PDF + 笔记（按方向分类）
│   │   ├── reasoning/
│   │   ├── context-engineering/
│   │   ├── visual-generation/
│   │   ├── agent-infrastructure/
│   │   ├── agent-memory/
│   │   └── generative-models/
│   ├── projects/              # 项目文档
│   │   ├── creative-cowork/   # Creative CoWork 产品（01→06 迭代文档 + 设计稿）
│   │   ├── jimeng-video-agent/ # 即梦视频 Agent SP 和 Skill
│   │   ├── xian-home/         # 弦的形象设计
│   │   └── user-interview/    # 漫剧用户访谈（原文禁止修改）
│   └── articles/              # 研究文章和学习笔记
│       ├── claude-code-research/ # Claude Code 深度拆解
│       ├── learning-notes/    # 飞书/外部同步的学习笔记
│       ├── memory-research/   # Agent 记忆方案研究
│       └── blog/              # 阅读清单
│
├── wiki/                      # LLM 编译产物（人不直接编辑，LLM 维护）
│   ├── _index.md              # 全局索引
│   ├── _summaries.md          # 所有 raw/ 文档的摘要
│   ├── concepts/              # 概念文章（16 篇）
│   ├── maps/                  # 主题地图（3 篇）
│   └── connections/           # 跨主题关联发现（3 篇）
│
├── output/                    # 查询产出（可回流 wiki）
│   ├── reports/
│   └── slides/
│
├── .prompt/                   # Prompt 管理（Agent 的模块化 Prompt）
└── CLAUDE.md                  # 本文件
```

## AK 知识库模式

### 核心原则

1. **raw/ 只增不改** — 原始素材是 ground truth，LLM 只读
2. **wiki/ 由 LLM 维护** — 人不直接编辑 wiki，所有内容由 LLM 从 raw/ 编译
3. **output/ 回流增强** — 查询产出可归档回 wiki，知识持续积累
4. **索引自维护** — LLM 自动维护 `_index.md` 和 `_summaries.md`

### 操作流程

```
新素材 → raw/ → LLM 增量编译 → wiki/ 更新
                                    ↓
查询 → LLM 研究 wiki → output/ → 可选回流 wiki/
                                    ↓
Lint → LLM 健康检查 → 发现不一致/缺失 → wiki/ 修复
```

### 使用 Knowledge Base Skill

本 vault 配套 `knowledge-base` Skill，提供以下命令：
- **ingest**: 将新素材收录到 raw/ 并增量编译到 wiki/
- **query**: 对 wiki 进行复杂查询，产出 output/
- **lint**: 对 wiki 进行健康检查（不一致、缺失、新关联）
- **compile**: 全量重新编译 wiki/

## 编辑规范

- 文档使用中文，技术术语保持英文
- 使用 Obsidian 风格的 Markdown（支持 `[[链接]]` 语法）
- 论文 PDF 统一放 `raw/papers/` 对应子目录
- User Interview 中的访谈原文**禁止修改**，只能新建总结文件
- wiki/ 中的所有文件底部标注 `*由 LLM 从 raw/ 数据编译，请勿手动编辑*`

## 访谈总结规范

- 命名：`{原文件名} - 总结.md`
- 头部包含元信息（原文链接、访谈对象、时长、日期）
- 必须包含：公司概况、团队组织、制作流程、工具矩阵、核心痛点、质量标准、金句摘录
