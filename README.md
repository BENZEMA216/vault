# Dreamina AGENT 2026

> Creative CoWork 产品设计文档库

## 项目简介

Creative CoWork 是面向专业创作者（"超创"）的 AI 创作工作台，定位为 **"Claude Code 的创作版"**。

核心理念：**上下文聚合 + AGENT + 可定制 Studio**

## 目录结构

```
├── .prompt/                    # Prompt 管理（AGENT 的模块化 Prompt）
│   ├── README.md               # Prompt 组装说明
│   ├── system/                 # 基础 System Prompt
│   ├── agents/                 # Agent 角色描述
│   ├── skills/                 # Skill 描述模板
│   ├── modes/                  # 模式 Prompt（创作/探索）
│   └── reminders/              # 系统提醒
│
├── The Best Idea/              # 核心产品文档
│   ├── 01 Creative CoWork - DEMO思路.md      # 原始想法和架构设计
│   ├── 02 Creative CoWork - 产品深化.md      # 产品讨论和关键决策
│   ├── 03 Creative CoWork - 用户手册.md      # 用户试用指南
│   ├── 04 Creative CoWork - MVP需求.md       # ⭐ MVP 功能需求（核心文档）
│   ├── 05 AI Explorer - PRD.md               # AI Explorer 产品需求
│   ├── 0201 - 技术研究.md                    # 技术调研笔记
│   ├── 0205 - init 机制设计.md               # /init 机制设计方案
│   └── resources/                            # 设计稿图片
│
├── Study/                      # 学习笔记与研究
│   ├── Claude Code 研究/       # Claude Code 深度分析
│   │   ├── Agent Prompt 设计/  # 模块化 Prompt 架构设计
│   │   ├── Claude Code 架构分析.md
│   │   └── Claude Code System Prompt 详解.md
│   ├── Creative CoWork/        # Creative CoWork 相关研究
│   │   └── Creative Agent System Prompt 模板.md
│   ├── Memory 研究/            # AI Agent 记忆机制研究
│   │   └── Agent Memory/       # Memory 论文笔记
│   ├── Blog/                   # Blog 阅读笔记
│   └── 论文/                   # 论文笔记 + PDF（按主题分类）
│       ├── Agent Infrastructure/
│       ├── Context Engineering/
│       ├── Generative Models/
│       ├── Reasoning/
│       └── Visual Generation/
│
├── User Interview/             # 用户访谈记录
│   └── 漫剧访谈 *.md           # 访谈原文和总结
│
└── 设计原则.md                 # 设计原则文档
```

## 核心文档

| 文档 | 说明 | 建议阅读顺序 |
|------|------|-------------|
| [01 DEMO思路](./The%20Best%20Idea/01%20Creative%20CoWork%20-%20DEMO思路.md) | 产品背景、核心架构 | 1 |
| [02 产品深化](./The%20Best%20Idea/02%20Creative%20CoWork%20-%20产品深化.md) | 目标用户、关键决策 | 2 |
| [04 MVP需求](./The%20Best%20Idea/04%20Creative%20CoWork%20-%20MVP需求.md) | **功能需求清单（研发必读）** | 3 |
| [Claude Code 架构分析](./Study/Claude%20Code%20研究/Claude%20Code%20架构分析.md) | 技术参考 | 4 |

## 核心概念

- **超创**：能用 AIGC 赚钱的专业创作者（目标用户）
- **上下文容器**：聚合项目所有上下文（素材、生成结果、参考、对话历史）
- **AGENT**：自然语言交互，具备 L3-L4 智能（上下文利用 + 多步编排）
- **Studio**：场景化工作空间模板（工具 + 界面 + 配置）
- **Skills**：用户可自由加载的模块化能力
- **GENUI**：基于 Skills/上下文动态生成的界面

## 里程碑

- **0215**：完成可试用版本，3-5 个导演朋友能用它完成真实项目

## 问题反馈

如果对文档有任何疑问或建议，请 [提交 Issue](../../issues/new)。

常见问题类型：
- 需求不清晰
- 功能点有歧义
- 缺少技术细节
- 优先级疑问

---

*文档持续更新中*
