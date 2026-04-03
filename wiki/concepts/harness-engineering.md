# Harness Engineering（驾驭工程）

> 塑造 AI Agent 运行环境使其可靠工作的工程实践：Agent = Model + Harness，Harness 是 Agent 中除 Model 之外的一切——system prompt、tools、sandbox、memory、orchestration、verification。

## 核心要点

- Agent = Model + Harness，模型提供智能，Harness 让智能可靠
- 两类控制：Feedforward（前馈/引导，如 CLAUDE.md）vs Feedback（反馈/传感，如 tests）
- 两种执行：Computational（确定性，如 linters）vs Inferential（推理性，如 LLM-as-judge）
- Harnessability（可驾驭性）：不同代码库对 harness 的亲和度不同
- 每个 harness component 编码了对 model 能力的假设，这些假设会过期
- "Anytime you find an agent makes a mistake, you engineer a solution so it never makes that mistake again" — Mitchell Hashimoto

## 详细说明

Harness Engineering 是 2025-2026 年 Agent 开发中浮现的核心工程学科。它的核心洞察是：让 Agent 可靠的关键不是更强的模型，而是更好的运行环境。Model 提供原始智能——推理、生成、判断；Harness 提供结构——约束、引导、验证、恢复。两者缺一不可，但工程师能直接控制的只有 Harness。

Harness 的控制手段分为两个维度。第一个维度是方向：Feedforward 控制在 Agent 执行前引导它（system prompt、CLAUDE.md、few-shot examples），Feedback 控制在 Agent 执行后纠正它（tests、linters、human review）。第二个维度是执行方式：Computational 控制是确定性的（regex 检查、AST 分析），Inferential 控制是概率性的（LLM-as-judge、embedding similarity）。最强的 harness 同时使用四个象限。

一个容易被忽视的概念是 Harnessability——代码库对 harness 的亲和度。有清晰类型系统、完善测试套件、模块化架构的代码库天然更容易被 harness 驯服。反之，全局状态满天飞、没有测试的代码库会让最精巧的 harness 也失效。这意味着 harness engineering 不只是"给 Agent 加护栏"，还包括"让代码库本身更适合 Agent 操作"。

Claude Code 本身就是一个成熟的 harness 实现：它的 system prompt 是 feedforward，hooks 是 feedback，permission system 是 computational control，而 model 的自我纠正能力是 inferential control。Superpowers Skills 在此基础上增加了 discipline layer——通过标准化的 workflow pattern（brainstorm → plan → execute → verify）进一步约束 Agent 行为。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/articles/harness-engineering/awesome-harness-engineering]] | 完整资源列表，汇集 harness engineering 领域的工具、论文和实践 |
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | 深度研究报告，定义 feedforward/feedback、computational/inferential 四象限框架 |
| [[raw/articles/claude-code-research/Claude Code 架构分析]] | Claude Code 本身就是一个成熟的 harness 实现 |
| [[raw/articles/claude-code-research/Superpowers Skills 架构分析]] | Superpowers 是 harness 的 discipline layer |

## 关联概念

- [[concepts/context-engineering]] — Context Engineering 是 Harness 中 feedforward 控制的核心手段
- [[concepts/agent-loop]] — Agent Loop 是 Harness 编排的执行骨架
- [[concepts/sub-agent-architecture]] — Sub-Agent 架构是 Harness 的 orchestration 维度
- [[concepts/self-verification]] — Self-Verification 是 Harness 中最高杠杆的 feedback 控制
- [[concepts/safe-autonomy]] — Safe Autonomy 是 Harness 在安全维度的设计目标
- [[concepts/tool-routing]] — Tool Routing 是 Harness 中工具层的编排逻辑

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
