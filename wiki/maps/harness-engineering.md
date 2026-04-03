# Harness Engineering 全景

> 塑造 AI Agent 运行环境使其可靠工作的工程实践

## 定义

Agent = Model + Harness。Harness 是 Agent 中除 Model 之外的一切——System Prompt、工具定义、上下文管理、评估机制、运行时架构、记忆系统、权限边界。Model 是"大脑"，Harness 是"身体 + 环境"。当模型能力趋于同质化，Harness Engineering 成为 Agent 产品差异化的核心战场。

## 核心框架

### Feedforward vs Feedback

来自 Birgitta Bockeler / Thoughtworks 的控制论框架，将 Harness 的所有机制分为两类：

- **Feedforward（前馈控制）**：在 Agent 执行之前施加的约束。System Prompt 指令、工具白名单、权限边界、输入校验——这些在 Agent 开始推理之前就已确定，不依赖运行时反馈。类似恒温器的预设温度。
- **Feedback（反馈控制）**：在 Agent 执行过程中或之后的检查和修正。Self-verification、trajectory critics、用户 approve、错误重试——这些基于实际输出动态调整行为。类似恒温器的温度传感器。

最佳 Harness 设计同时使用两种控制：Feedforward 设定边界降低出错概率，Feedback 捕获漏网之鱼并持续改进。

### Computational vs Inferential

Agent 的执行可以分为两种模式：

- **Computational（计算执行）**：确定性的、可验证的操作。运行测试、执行 linter、编译代码、格式校验——结果非对即错，无需 LLM 判断。
- **Inferential（推理执行）**：需要 LLM 判断的操作。理解需求、生成代码、设计方案、创意发散——结果有灰度，需要评估。

Harness 的关键设计原则：尽可能将任务从 Inferential 转化为 Computational。让 LLM 做判断，用确定性工具做验证。"Verification > Generation" 的本质就是将评估从 Inferential 推向 Computational。

## 六大支柱

### 1. Context Engineering

> 系统性管理 LLM 可用的所有上下文

- [[concepts/context-engineering]] — 上下文工程的完整概念
- [[concepts/progressive-disclosure]] — 三层渐进式披露（L1/L2/L3）

**关键文章**：
- Anthropic "Effective context engineering"：Context 是 Agent 的第一约束，管理上下文比优化 prompt 更重要
- Manus context engineering：Context 编排的工程实践，KV-Cache 效率优化
- HumanLayer backpressure：当上下文过载时的反压机制，避免 Agent 被信息淹没

**核心技术**：Context Budget 管理、KV-Cache 命中率优化、Compaction 策略（摘要 + 截断）、Just-in-Time Retrieval（按需检索替代预加载）

### 2. Constraints & Safe Autonomy

> 定义 Agent 的行为边界和安全自主范围

- [[concepts/safe-autonomy]] — 安全自主的概念和设计模式
- [[concepts/tool-routing]] — 工具路由与权限控制

**关键文章**：
- Anthropic permissions：三层权限模型（允许 / 需确认 / 禁止），用户可配置 Agent 的自主边界
- ACI (Agent-Computer Interface) design：工具设计原则——最小权限、明确语义、防误操作
- Hooks：Harness 层的事件钩子，在 Agent 执行前后注入确定性检查

**核心技术**：Permission boundaries、Tool allowlists、Pre/post-execution hooks、Dangerous command detection

### 3. Evaluation & Observability

> 评估 Agent 输出质量并保持可观测性

- [[concepts/self-verification]] — Agent 自我验证机制

**关键文章**：
- GAN-inspired eval：Generator Agent 生成，Evaluator Agent 评估，形成对抗式质量提升循环
- Sprint Contract：将 Agent 任务拆解为可评估的 sprint，每个 sprint 有明确的验收标准
- Trajectory critics：不只评估最终输出，还评估 Agent 的推理轨迹和决策路径

**核心技术**：Automated scoring、Trajectory analysis、Regression benchmarks、"Silence on success; errors only on failure" 原则

### 4. Runtime Architecture

> Agent 的运行时基础设施和执行模型

- [[concepts/agent-runtime]] — Agent 运行时架构
- [[concepts/agent-loop]] — Agent 循环机制
- [[concepts/sub-agent-architecture]] — 子 Agent 分层委托

**关键文章**：
- Inngest durability：持久化执行——Agent 长时间运行时的断点续传、状态恢复、错误重试
- Initializer agent：启动时的初始化 Agent，负责环境检测、上下文加载、工具注册
- Multi-agent systems：多 Agent 协作的编排模式——串行 pipeline、并行 fan-out、层级委托

**核心技术**：Durable execution、Checkpoint/resume、Sub-agent context firewall、Agent loop lifecycle

### 5. Specs & Workflow Design

> 用结构化规范驱动 Agent 的行为

- [[concepts/spec-driven-development]] — 规范驱动开发
- [[concepts/modular-prompt-architecture]] — 模块化 Prompt 架构

**关键文章**：
- CLAUDE.md：项目级配置文件，定义 Agent 的行为规范、代码风格、禁止操作——Harness 的核心 feedforward 控制
- AGENTS.md：目录级配置，让不同目录有不同的 Agent 行为——粒度化的 harness 分区
- 12 Factor Agents：构建可靠 Agent 的 12 条原则，类比 12-Factor App 的工程方法论

**核心技术**：Layered config（项目/目录/用户级）、Modular system prompt、Spec-first workflow

### 6. Memory & Working State

> Agent 的记忆系统和工作状态管理

- [[concepts/agent-memory]] — Agent 记忆系统
- [[concepts/context-engineering]] — 记忆与上下文的交叉领域

**关键文章**：
- Filesystem memory：文件系统作为跨 session 的核心记忆层，JSON > Markdown（结构化优先）
- Compaction：上下文压缩——当对话过长时自动摘要历史，保留关键信息丢弃细节
- Context reset：主动清空上下文重新开始，避免"上下文腐化"（累积噪声导致性能下降）

**核心技术**：Progress files、Scratchpad patterns、Hot/cold memory 分层、Auto-compaction triggers

## Benchmarks 速览

| Benchmark | 评估维度 | 说明 |
|-----------|---------|------|
| SWE-bench Verified | 真实 GitHub issue 修复 | 最主流的 coding agent benchmark，500 个人工验证样本 |
| Terminal-Bench | 终端操作能力 | 评估 Agent 在真实终端环境中的操作能力 |
| OSWorld | 桌面 GUI 操作 | 评估 Agent 操作完整操作系统界面的能力 |
| WebArena | Web 浏览和交互 | 在真实网站上完成复杂任务 |
| τ-Bench | 工具使用可靠性 | 测试 Agent 在多轮工具调用中的鲁棒性 |
| GAIA | 通用 AI 助手能力 | 需要多步推理、工具使用、信息检索的综合任务 |
| MCPMark | MCP 协议工具调用 | 评估 Agent 通过 MCP 协议使用外部工具的能力 |

## 数据来源

- [[raw/articles/harness-engineering/awesome-harness-engineering]]
- [[raw/articles/harness-engineering/harness-engineering-deep-research]]
- [[raw/articles/claude-code-research/Claude Code 架构分析]]
- [[raw/articles/claude-code-research/Claude Code System Prompt 详解]]
- [[raw/articles/claude-code-research/Superpowers Skills 架构分析]]
- [[raw/articles/learning-notes/0707-12-factor-agent]]

## 关键 Takeaways

1. **简单优先** — 最好的 Harness 是最简单的 Harness。Claude Code 证明单循环 + 智能委托优于复杂的多 Agent 编排
2. **Context 是第一约束** — 上下文窗口有限且注意力有衰减，Context Engineering 是 Harness 的第一支柱
3. **Verification > Generation** — 将评估推向 Computational 领域（跑测试、linter）比让 LLM 自己判断更可靠
4. **文件系统是记忆层** — Filesystem memory 简单、可审计、跨 session 持久化，优于复杂的记忆方案
5. **分离关注点** — Sub-agent 作为 context firewall，每个 Agent 只关注自己的任务域
6. **Harness assumptions expire** — 模型能力持续提升，今天的最佳 Harness 设计明天可能过度约束
7. **Incremental > One-shot** — 增量式执行（小步迭代 + 验证）比一次性大规模生成更可靠

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
