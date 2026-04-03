# Skills 系统（Skills System）

> 模块化能力加载机制，基于 SKILL.md + YAML frontmatter 标准，允许用户按需加载/卸载 Agent 的能力模块。

## 核心要点

- 遵循 Anthropic Agent Skills 标准：每个 Skill 由 SKILL.md 描述文件 + YAML frontmatter 元数据定义
- 用户可按需加载/卸载，Agent 能力不再是固定的，而是可组合的
- Creative CoWork 扩展了标准，增加 prompts/（提示词模板）和 presets/（预设配置）目录
- 三层推荐机制（init 阶段）确保用户在项目启动时获得合适的 Skills 组合

## 详细说明

Skills 系统是 Creative CoWork 的能力基础设施。传统 AI 工具的能力是硬编码的——你能做什么取决于开发者预设了什么。Skills 系统将能力模块化：每个 Skill 是一个独立的能力单元，包含描述、触发条件、所需工具、提示词模板等，Agent 在运行时按需加载。

Anthropic 在其 Agent Skills 标准中定义了基本规范：SKILL.md 文件描述能力的用途和使用方式，YAML frontmatter 包含元数据（名称、版本、依赖、触发条件等）。0201 技术研究对这一标准进行了深度分析。Superpowers 的 Skills 架构提供了另一种实现参考，其核心差异在于 Skill 的发现和加载机制。jimeng-video-agent 的 Skills 则展示了创意领域 Skill 的实际案例。

Creative CoWork 在标准基础上做了关键扩展。04 MVP 需求的第 6 节定义了 Creative Skills 的额外结构：prompts/ 目录存放该 Skill 专用的提示词模板，presets/ 目录存放预设配置（如特定风格的参数组合）。0205 init 机制设计了三层推荐系统——项目类型推荐、用户历史推荐、社区热门推荐——确保用户在创建项目时能快速获得合适的 Skills 组合，而不需要手动逐个搜索和配置。

"为 Claude 构建 Skills 完整指南" 提供了从零构建一个 Skill 的完整流程，是理解 Skills 系统设计哲学的最佳入口。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/0201-技术研究]] | 深度分析 Anthropic Agent Skills 标准及其扩展方向 |
| [[raw/0205-init机制]] | 设计三层推荐系统，解决 Skills 发现和初始化问题 |
| [[raw/04-MVP需求]] | 第 6 节定义了 Creative Skills 的扩展结构 |
| [[raw/Creative-CoWork-Skills架构启发]] | 从多个 Skills 实现中提炼的架构设计启发 |
| [[raw/Superpowers-Skills架构分析]] | 对比分析另一种 Skills 加载和管理机制 |
| [[raw/为Claude构建Skills完整指南]] | 从零构建 Skill 的完整流程和最佳实践 |
| [[raw/jimeng-video-agent-skills]] | 创意领域 Skill 的实际实现案例 |

## 关联概念

- [[concepts/init-mechanism]] — Skills 的初始化加载和三层推荐机制
- [[concepts/progressive-disclosure]] — Skills 按需加载是渐进式披露在能力层面的应用
- [[concepts/modular-prompt-architecture]] — Skills 内部的 Prompt 组织和构建方式

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
