# 模块化 Prompt 架构

> 将 System Prompt 拆分为可组合的模块文件，按需组装成最终 Prompt，实现 Prompt 的工程化管理。

## 核心要点

- System Prompt 不再是单一大文件，而是拆分为独立模块（如 Claude Code 的 116 文件系统）
- 模块分类：system/（基础身份）、tools/（工具描述）、agents/（角色）、skills/（能力）、reminders/（提醒）
- 模块按需加载，根据当前任务动态组装，避免上下文浪费
- 支持版本管理和独立迭代，单个模块的修改不影响整体系统
- 目录结构本身即是架构文档，新成员可通过文件树理解系统设计

## 详细说明

模块化 Prompt 架构的核心思想来自软件工程的模块化原则：将复杂的 System Prompt 分解为职责单一、可独立维护的文件。Claude Code 的实现是目前最成熟的参考案例，其 116 个文件按功能分层组织，从基础身份定义到工具描述再到具体技能，形成清晰的层次结构。

在实际设计中，模块通常分为几个层级。system/ 目录包含 Agent 的基础身份和行为准则，是所有场景都会加载的核心模块。tools/ 目录描述可用工具的接口和使用规范。agents/ 定义不同角色的行为模式（如 Explore Agent、Plan Agent）。skills/ 包含特定能力的详细指令。reminders/ 则是运行时动态注入的上下文提醒。

这种架构的关键优势在于组合性。同一套基础模块可以搭配不同的 skills/ 和 agents/ 组合，快速派生出面向不同场景的 Agent。Creative CoWork 的模块改造计划正是基于这一思路，将原本单体的 System Prompt 重构为可复用的模块集合，使创意场景和代码场景可以共享底层架构。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/claude-code-system-prompt]] | Claude Code 116 文件拆解，展示了最完整的模块化 Prompt 实现 |
| [[raw/modular-architecture]] | 目录结构设计，定义了 system/tools/agents/skills/reminders 分类体系 |
| [[raw/agent-system-prompt-research]] | 4 种 Agent 的 System Prompt 对比，分析不同架构选择的利弊 |
| [[raw/creative-cowork-sp-plan]] | Creative CoWork 的模块改造计划，将单体 SP 重构为模块化架构 |
| [[raw/agent-prompt-design/prompts]] | 模块化 Prompt 的完整实现代码 |

## 关联概念

- [[concepts/agent-loop]] — Agent Loop 是模块化 Prompt 的运行时载体，决定模块何时被加载和执行
- [[concepts/skills-system]] — Skills 是模块化架构中最细粒度的能力单元
- [[concepts/progressive-disclosure]] — 渐进式披露决定了模块的加载时机和顺序
- [[concepts/creative-agent-design]] — 创意 Agent 设计是模块化架构在创意场景的具体应用

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
