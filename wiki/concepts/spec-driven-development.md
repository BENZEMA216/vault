# Spec-Driven Development（规格驱动开发）

> 通过显式的产品和工程规格指导 Agent 执行的开发模式：先定义 spec，再让 Agent 在 spec 约束下实现，用 spec 验收结果。

## 核心要点

- Interview Pattern：让 Agent 先 interview 用户需求，写入 SPEC.md，再在新 session 执行
- CLAUDE.md 层级系统：Managed policy → Project → User → Local，支持 `@path` import 语法
- CLAUDE.md 目标 under 200 lines，过长时 Claude ignores half
- ETH Zurich 研究：LLM 自动生成的 CLAUDE.md 损害性能 20%+
- AGENTS.md：轻量级 repo-local agent 指令标准
- `.claude/rules/` 系统：path-specific rules 通过 YAML frontmatter 限定作用范围
- 12 Factor Agents：explicit prompts、state ownership、clean pause-resume
- Four-Phase Workflow：Explore → Plan → Implement → Commit
- Session 管理反模式：Kitchen Sink、Correcting Over and Over、Over-specified CLAUDE.md

## 详细说明

Spec-Driven Development 的核心洞察是：Agent 的输出质量上限由输入的 spec 质量决定。没有明确的 spec，Agent 会基于自己的 training data 做假设——这些假设有时正确，但在关键的产品决策上经常偏离用户意图。Interview Pattern 解决了这个问题：第一个 session 不写代码，而是让 Agent 像产品经理一样 interview 用户，把需求澄清为结构化的 SPEC.md，然后在新 session 中基于这个 spec 执行。

CLAUDE.md 层级系统是 spec 在 Agent 开发中最常见的载体。它分为四层：Managed policy（Anthropic 控制的基础规则）→ Project CLAUDE.md（项目级约定）→ User CLAUDE.md（个人偏好）→ Local CLAUDE.md（本地覆盖）。`@path` import 语法允许将长 spec 拆分成模块，通过引用加载。一个关键的实践发现是：CLAUDE.md 必须控制在 200 行以内，超过这个长度 Claude 会开始忽略部分内容。ETH Zurich 的研究更进一步证明了质量比数量重要——LLM 自动生成的 CLAUDE.md 比没有 CLAUDE.md 还差 20%+，因为自动生成的内容充斥着冗余和矛盾。

`.claude/rules/` 系统和 AGENTS.md 是 spec 的更细粒度载体。Rules 通过 YAML frontmatter 中的 glob pattern 限定作用范围——比如一条"所有 API handler 必须做 input validation"的规则只在 `src/api/**` 下生效。AGENTS.md 是更轻量的方案，作为 repo-local 的 agent 指令标准，不依赖特定的 Agent 平台。

12 Factor Agents 将 spec-driven 的理念扩展为完整的开发方法论：explicit prompts（不依赖隐式约定）、state ownership（Agent 明确拥有哪些状态）、clean pause-resume（任何时候都能暂停和恢复）。在实践中，这体现为 Four-Phase Workflow：Explore（理解代码库）→ Plan（制定方案并写入 plan 文件）→ Implement（在 plan 约束下编码）→ Commit（验证并提交）。常见的反模式包括 Kitchen Sink（一个 session 塞太多任务）、Correcting Over and Over（不停在同一 session 中修正 Agent 而不是重启）、Over-specified CLAUDE.md（写了 500 行规则结果 Agent 选择性忽略）。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | CLAUDE.md 层级系统、最佳实践、反模式、Interview Pattern |
| [[raw/articles/claude-code-research/Claude Code System Prompt 详解]] | Claude Code 的 CLAUDE.md 处理机制、层级加载逻辑 |
| [[raw/articles/learning-notes/0707-12-factor-agent]] | 12 Factor Agent 笔记，explicit prompts 和 state ownership 原则 |

## 关联概念

- [[concepts/harness-engineering]] — Spec 是 Harness 中 feedforward 控制的高层形式
- [[concepts/modular-prompt-architecture]] — 模块化 Prompt 架构是 spec 的结构化组织方式
- [[concepts/skills-system]] — Skills 是 spec 在 Agent 能力维度的封装
- [[concepts/progressive-disclosure]] — 渐进式披露决定 spec 的哪些部分在何时加载

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
