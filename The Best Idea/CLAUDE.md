# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个 Obsidian 知识库，包含 **Creative CoWork** 产品的设计文档——一个面向专业创作者（"超创"）的 AI 创作工作台。

## 文档结构

- `The Best Idea/` - 核心产品文档
  - **项目文档**（编号命名，持续更新）：
    - `01 Creative CoWork - DEMO思路.md` - 原始产品想法和架构设计
    - `02 Creative CoWork - 产品深化.md` - 产品讨论和关键决策
    - `03 Creative CoWork - 用户手册.md` - 用户试用指南
    - `04 Creative CoWork - MVP需求.md` - MVP 功能需求
    - `05 AI Explorer - PRD.md` - AI Explorer 产品需求
  - **日期文档**（MMDD 命名，单次研究/记录）：
    - `0201 - 技术研究.md` - Skills 架构、浏览器控制、视频生成集成

## 核心概念

- **超创**：能用 AIGC 赚钱的专业创作者
- **上下文容器**：聚合项目的所有上下文（素材、生成结果、参考、对话历史）
- **AGENT**：自然语言交互，具备 L3-L4 智能（上下文利用 + 多步编排）
- **Studio**：场景化工作空间模板（工具 + 界面 + 配置）
- **Skills**：用户可自由加载的模块化能力
- **GENUI**：基于 skills/上下文动态生成的界面

## 编辑规范

- 所有文档使用中文
- 使用 Obsidian 风格的 Markdown
- 更新文档时保持现有结构和编号
- 添加新内容时注意与相关章节的交叉引用

## 命名规范

- **项目文档**：`XX 产品名 - 文档类型.md`（如 `04 Creative CoWork - MVP需求.md`）
  - 持续更新的核心文档
  - 编号递增
- **日期文档**：`MMDD - 标题.md`（如 `0201 - 技术研究.md`）
  - 单次研究、会议记录、体验报告等
  - 以创建日期命名
