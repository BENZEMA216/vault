# CLAUDE.md

This file provides guidance to Claude Code when working in this Obsidian vault.

## Vault 概述

这是 Dongzhe 的个人知识库（Obsidian），同时也是 vault 项目的本地仓库。

GitHub remote: https://github.com/BENZEMA216/vault

## 目录结构

```
Fighting/
├── active_context.md     # 热记忆：当前工作状态和优先级
├── Inbox/                # 快速捕获：待整理的碎片内容
│
├── Projects/             # 项目文档（温记忆）
│   ├── Creative-CoWork/  # Creative CoWork 产品（01→05 迭代文档）
│   ├── jimeng-video-agent/ # 即梦视频 Agent SP 和技能 Prompt
│   ├── xian-home/        # 弦的形象设计过程
│   └── User-Interview/   # 漫剧用户访谈记录
│
├── Study/                # 研究笔记（冷记忆）
│   ├── Claude Code 研究/ # System Prompt、架构、Skills 深度拆解
│   ├── Memory 研究/      # Agent Memory 论文和方案对比
│   ├── Learning-Notes/   # 学习笔记（从飞书/外部同步，Study 的 inbox）
│   ├── 论文/             # 按方向分类的论文笔记和 PDF
│   ├── Blog/             # 阅读清单
│   └── 设计原则.md
│
└── CLAUDE.md             # 本文件
```

## 记忆温度分层

- **热**（active_context.md）：当前优先级、关键决策、阻塞项
- **温**（Projects/）：项目文档、迭代记录、访谈
- **冷**（Study/）：论文、研究、方法论、长期参考

详见 GitHub 上的 [MEMORY-GUIDE](https://github.com/BENZEMA216/BENZEMA216/blob/main/MEMORY-GUIDE.md)。

## 核心概念（Creative CoWork）

- **超创**：能用 AIGC 赚钱的专业创作者（目标用户）
- **上下文容器**：聚合项目所有上下文（素材、生成结果、参考、对话历史）
- **AGENT**：自然语言交互，具备 L3-L4 智能（上下文利用 + 多步编排）
- **Studio**：场景化工作空间模板（工具 + 界面 + 配置）
- **Skills**：用户可自由加载的模块化能力

## 编辑规范

- 文档使用中文，技术术语保持英文
- 使用 Obsidian 风格的 Markdown（支持 `[[链接]]` 语法）
- 下载的论文 PDF 统一放 `Study/论文/` 对应子目录
- User Interview 中的访谈原文**禁止修改**，只能新建总结文件
- 对话中形成的结论主动写入 active_context.md 或对应项目文档

## 访谈总结规范

- 命名：`{原文件名} - 总结.md`
- 头部包含元信息（原文链接、访谈对象、时长、日期）
- 必须包含：公司概况、团队组织、制作流程、工具矩阵、核心痛点、质量标准、金句摘录
