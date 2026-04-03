# BENZEMA216 Knowledge Base

> AK-style personal knowledge base: raw data → LLM compilation → markdown wiki → Obsidian

## Architecture

```
raw/          → 原始素材（论文、项目文档、学习笔记、访谈）
wiki/         → LLM 编译产物（概念文章、主题地图、关联发现）
output/       → 查询产出（报告、幻灯片）
```

## 核心原则

- **raw/ 只增不改** — 原始数据是 ground truth
- **wiki/ 由 LLM 维护** — 人不直接编辑，LLM 从 raw/ 编译
- **output/ 回流增强** — 查询结果归档回 wiki，知识持续积累

## Quick Stats

| 类别 | 数量 |
|------|------|
| 论文 | 12 篇（6 方向） |
| 项目 | 4 个（Creative CoWork, 即梦视频Agent, 弦, 用户访谈） |
| 研究文章 | 20+ 篇 |
| Wiki 概念 | 16 篇 |
| 主题地图 | 3 篇 |
| 关联发现 | 3 篇 |

## 研究方向

- **Agent 基础设施** — 循环架构、Prompt 工程、上下文管理、记忆系统、工具路由
- **Creative AI** — 创意 Agent 设计、视频生成工作流、漫剧制作
- **产品设计** — Creative CoWork 产品、用户研究、设计系统

## 使用方式

1. 在 Obsidian 中打开此 vault
2. 从 `wiki/_index.md` 开始浏览
3. 使用 Claude Code + `knowledge-base` Skill 进行 ingest/query/lint/compile

---

*Knowledge base maintained by LLM, inspired by [Karpathy's LLM Knowledge Bases](https://x.com/karpathy/status/2039805659525644595)*
