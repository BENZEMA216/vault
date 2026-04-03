# Agent Runtime（Agent 运行时）

> Agent 的状态管理、持久化、重试和生命周期控制基础设施——"Your agent needs a harness, not a framework"，可靠性是基础设施问题而非模型问题。

## 核心要点

- "Your agent needs a harness, not a framework" — 可靠性是基础设施问题
- Event-driven durability：每个 LLM/tool call 是独立可重试的 step
- Initializer + Coding Agent 架构：第一个 session 创建 init.sh/feature list/progress file，后续 session 读取并继续
- Singleton concurrency：每个 conversation 只有一个 agent run，新消息 cancel 之前的
- Filesystem memory 是跨 session 核心记忆层：progress files + feature list (JSON) + git history
- JSON > Markdown for feature tracking（model 更不容易 overwrite JSON）
- Multi-Agent 实测对比：Solo agent ($9, 20min, broken) vs Full harness ($200, 6h, playable)
- Compaction 不够用——Sonnet 4.5 有 "context anxiety"（过早收尾），需要 context reset

## 详细说明

Agent Runtime 解决的核心问题是：LLM 调用本质上是无状态的函数调用，但 Agent 任务需要跨越多次调用、多个 session、甚至多天的有状态执行。没有可靠的 runtime 基础设施，Agent 会在中途失败后丢失进度、在长任务中迷失方向、在并发场景中产生冲突。

Event-driven durability 是现代 Agent Runtime 的核心架构模式。以 Inngest 为代表的方案将 Agent 的每一步（LLM 调用、工具执行、人工审批）建模为独立的、可重试的 event step。任何一步失败都可以从该步重试，而不需要重跑整个 workflow。这比传统的"try-catch + retry loop"强大得多，因为它天然支持持久化——每个 step 的输入输出都被记录，crash recovery 变成了基础设施提供的能力而非应用层需要实现的逻辑。

Initializer + Coding Agent 架构解决了跨 session 的状态延续问题。第一个 session 专门用于初始化：分析需求、生成 feature list（JSON 格式）、创建 init.sh、写入 progress file。后续每个 session 启动时先读取这些文件，了解项目状态和下一步任务，然后继续执行。之所以用 JSON 而非 Markdown 做 feature tracking，是因为 model 更不容易意外 overwrite JSON 的结构——Markdown 的自由格式反而成了隐患。

实测数据说明了 runtime 基础设施的价值：同一个项目，Solo agent 花费 $9、耗时 20 分钟，产出 broken 的结果；配备完整 harness（包括 initializer、progress tracking、multi-agent orchestration、verification）的方案花费 $200、耗时 6 小时，但产出了 playable 的结果。成本增加了 20 倍，但从"不能用"到"能用"的差距是质变。另一个值得注意的发现是 Sonnet 4.5 的 "context anxiety"——当 context window 接近满时，model 会表现出过早收尾的倾向，跳过剩余任务直接输出总结。Compaction（上下文压缩）在这种情况下不够用，更有效的做法是 context reset——开新 session 并传入 progress file。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | Inngest event-driven 架构、Initializer Agent 模式、Solo vs Full harness 成本对比 |
| [[raw/articles/claude-code-research/Claude Code 架构分析]] | Claude Code 的 session 管理、singleton concurrency、compaction 机制 |

## 关联概念

- [[concepts/harness-engineering]] — Agent Runtime 是 Harness 的基础设施层
- [[concepts/context-engineering]] — Context Reset 和 Compaction 是 Runtime 与 Context Engineering 的交汇点
- [[concepts/agent-memory]] — Filesystem memory 是 Runtime 提供的跨 session 记忆机制
- [[concepts/sub-agent-architecture]] — Multi-Agent orchestration 依赖 Runtime 的并发和状态管理能力

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
