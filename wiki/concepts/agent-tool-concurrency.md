# Agent Tool 并发模型

> 让 LLM streaming 和 tool execution 在时间上重叠的设计模式。kosong 的 sync handle/async future + Claude Code 的 per-tool isConcurrencySafe 是 2026 年最先进的两种实现。

## 核心要点

- **三种实现路径**：
  1. **串行**（LangChain AgentExecutor / AutoGen / CrewAI）— 等 LLM 流完整、解析、一个个跑 tool
  2. **Post-stream batched**（OpenAI Agents SDK / Claude Code fallback 路径）— 流完后并发批量
  3. **Streaming-concurrent**（kimi-cli / Claude Code streaming 路径，gated by `tengu_streaming_tool_execution2`）— 流式 token 一边收一边并发执行
- **kosong 核心原语**：`Toolset.handle()` 是**同步函数**，立即返回 future。注释 `# MUST NOT do any blocking operations` 的真正意义是保护 streaming pipeline 不被任何 tool 拖住
- **Claude Code 的更细粒度**：每个 tool 自带 `isConcurrencySafe(input)` 谓词（默认看 `annotations.readOnlyHint`）。调度规则：多个只读工具（Read/Grep/Glob）可同时跑，遇到写工具（Bash/Edit/Write）自动 break 队列变成串行栅栏，然后后续 read 又可以并发
- **量化收益**：单 tool 场景 LLM 5s + tool 8s，串行 = 13s，并发 = 8s；多 tool 场景 LLM 5s + 三个 tool (8/6/1s)，串行 = 20s，并发 = 8s（**节省 60%**）
- **并发上限**：Claude Code 默认 10（`CLAUDE_CODE_MAX_TOOL_USE_CONCURRENCY`），用 `_N8` Promise.race-based generator merge

## 详细说明

**为什么 sync handle 是关键**：如果 `handle()` 是 async（用 `await` 实际执行 tool），那么 streaming parser 调用它的时候就会**阻塞 streaming 读 token**。`# MUST NOT do any blocking operations` 强制 handle 立即返回 future，让 streaming 立刻继续读下一个 token，多个 future 通过 `asyncio.gather` 在后台并行 resolve。这种 "**收银员叫号，咖啡机后台并行**" 的设计是 kosong 最优雅的部分。

**Claude Code 的并发安全模型**比 kimi-cli 更精细：在 streaming 路径里，`C68.addTool()` 立即根据 schema 校验后的 input 计算 `isConcurrencySafe`，加进队列，调用 `processQueue()`。`canExecuteTool()` 的逻辑是"队列里所有 executing 的都是 safe，且当前也是 safe，才能并行"——这能精确做到"3 个 Read 并发 + 1 个 Edit 串行栅栏 + 后续 Read 再并发"。kimi-cli 用 SimpleToolset 立即 dispatch，没有这层 read/write 区分。

**为什么主流框架还是串行**：(1) Anthropic 2024 才把 streaming tool use 加进 API；(2) LangChain AgentExecutor 是 2022 年的设计，重写需要 breaking change；(3) 在 tool 都是几百毫秒 API 调用的场景下，体验差不多——**真正的差距出现在 coding agent**（shell 命令秒级、慢测试、长 LLM 输出）和未来的 [[concepts/knowledge-agent-network|跨节点 KAN 查询]] 这种延迟场景。

**之前的判断更正**：早期判断 "Claude Code 是 post-stream parallel" 只对了一半。基于 2026-03-31 sourcemap 泄露后的本地 cli.js v2.1.87 一手分析，Claude Code 同时拥有两条路径，且 streaming-concurrent 路径已经在生产灰度中（telemetry 名 `tengu_streaming_tool_execution2` 的 "2" 后缀说明这是第二代实现）。

## 在知识库中的出现

| 来源 | 上下文 |
|------|------|
| `raw/articles/agent-tool-concurrency-discussion.md` | 三种路径完整讨论 + 时间轴对比 + 主流框架现状表 + KAN 启示 |
| `raw/articles/claude-code-source-analysis.md` | Claude Code v2.1.87 一手代码分析：`C68` streaming executor、`isConcurrencySafe` 谓词、`_N8` generator merge、feature gate |
| `raw/articles/richard-chien/code-review.md` | kosong `handle()` Protocol 完整设计、ToolCall vs ToolCallPart 分离、`asyncio.create_task` future 模式 |

## 关联概念

- [[concepts/agent-runtime]] — 并发模型是 runtime 的核心特性
- [[concepts/harness-engineering]] — Harness 的核心评估维度之一
- [[concepts/sub-agent-architecture]] — Claude Code Task tool 的 `isConcurrencySafe` 默认 true，多 sub-agent 也用同一并发栈
- [[maps/agent-harness-implementations]] — kimi-cli vs Claude Code 完整对比
- [[connections/harness-to-kan]] — 把 per-tool concurrency safety 模型映射到 KAN 节点的 read/write 栅栏

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
