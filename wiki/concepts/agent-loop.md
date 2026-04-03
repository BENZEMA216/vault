# Agent 循环架构（Agent Loop）

> Agent 的核心运行机制：感知、思考、行动、反馈的持续循环，驱动任务从输入到完成的全过程。

## 核心要点

- 基本循环：感知（Perceive）→ 思考（Think）→ 行动（Act）→ 反馈（Feedback）
- Claude Code 采用单循环 + 智能委托（sub-agent delegation）模式，主循环保持简洁
- Creative CoWork 在标准循环基础上增加创意模式：发散（Diverge）与收敛（Converge）阶段
- 循环的终止条件、错误恢复、用户介入点是架构设计的关键决策

## 详细说明

Agent Loop 是所有 Agent 系统的运行核心。不同于传统的请求-响应模式，Agent 通过持续循环来处理复杂任务：先感知环境和用户输入，然后进行推理和规划，接着调用工具执行动作，最后评估结果并决定是否继续循环。这个循环会持续进行直到任务完成或需要用户介入。

Claude Code 的架构分析揭示了一种优雅的实现：单主循环 + 智能委托。主 Agent 保持一个简洁的循环，当遇到可以并行或独立处理的子任务时，委托给 sub-agent 执行。这避免了多 Agent 协调的复杂性，同时保持了任务分解的灵活性。对比 4 种 Agent System Prompt 的研究（Claude Code、Cursor、Devin、Manus）显示，循环架构的选择直接影响了 Agent 的能力边界和用户体验。

Creative CoWork 在此基础上做了关键扩展：引入创意循环模式。标准 Agent Loop 偏向收敛——尽快找到正确答案。但创意工作需要发散阶段（生成多种可能性、探索非常规方向）和收敛阶段（筛选、精炼、确定最终方案）。Creative Agent SP 模板中定义了这两种模式的切换触发条件和行为差异。情书 Agent（core-sp.md）是这种创意循环的早期实现案例。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/Claude-Code架构分析]] | 分析了单循环 + 委托模式的实现细节和设计取舍 |
| [[raw/Agent-System-Prompt研究]] | 对比 4 种主流 Agent 的循环架构差异 |
| [[raw/Creative-Agent-SP模板]] | 定义了创意循环的发散/收敛模式及切换条件 |
| [[raw/core-sp]] | 情书 Agent 作为创意循环的实际实现案例 |

## 关联概念

- [[concepts/sub-agent-architecture]] — Agent Loop 中的智能委托机制，子任务分发与结果收集
- [[concepts/modular-prompt-architecture]] — 循环中每个阶段的 Prompt 构建方式
- [[concepts/creative-agent-design]] — 在标准循环上叠加的创意发散/收敛机制

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
