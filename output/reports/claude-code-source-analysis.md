# Claude Code 实际源码深度分析

> 来源: 知识库 Query 讨论 (2026-04-07)
> 调研基础: 本地实际安装的 cli.js bundle (v2.1.87, 12.9MB, 16,750 行) + 2026-03-31 sourcemap 泄露事件后的多个开源 RE 仓库
> ⚠️ **修正了之前关于 Claude Code 是 "post-stream parallel" 的判断**

---

## 重要更正

之前在讨论 sync handle / async future 时，我说：

> **"Claude Code 默认 post-stream parallel，kimi-cli 是 in-stream concurrent"**

这个说法**只对了一半**。实际情况：

**Claude Code v2.1.87 同时拥有两条 tool execution 路径**，由 Statsig feature gate `tengu_streaming_tool_execution2` 控制：

1. **Streaming-concurrent 路径**（开关开启时）—— 和 kimi-cli 的 `_step()` 思路一样，流式 SSE 一边收一边并发执行 tools
2. **Post-stream batched 路径**（开关关闭/fallback）—— 等 turn 结束才批量执行

而且 Claude Code 还会发送 `tengu_streaming_tool_execution_used` / `tengu_streaming_tool_execution_not_used` telemetry —— 说明 Anthropic 仍在 A/B 灰度切换，**streaming 路径已经实现完成并在生产中跑**。

**架构相似度**：Claude Code 在 streaming 路径上和 kimi-cli `_step()` 是同构的 —— 都是 async generator + 流式过程中 dispatch tool。

## 一手证据：Main Agent Loop（从本地 cli.js 提取）

```js
// 主 turn 循环（伪代码，从 cli.js 提取并去混淆命名）
async function* mainLoop(...) {
  let c = D.gates.streamingToolExecution     // ★ feature gate
            ? new C68(tools, canUseTool, ctx) // streaming executor
            : null;

  while (looping) {
    N3("query_api_streaming_start");

    // 1. 流式调用模型
    for await (let Z6 of callModel({...})) {
      if (Z6.type === "assistant") {
        assistantMessages.push(Z6);
        const toolUses = Z6.message.content.filter(b => b.type === "tool_use");
        if (toolUses.length > 0) pendingToolUses.push(...toolUses);

        // ★★★ streaming 路径下，每收到一个 assistant 消息
        //          立刻把里面的 tool_use 喂给 streaming executor
        if (c && !aborted) {
          for (let block of toolUses) c.addTool(block, Z6);
        }
      }
      yield Z6;

      // ★★★ 边流边 drain 已完成的 tool 结果
      if (c && !aborted) {
        for (let r of c.getCompletedResults()) {
          if (r.message) yield r.message;
        }
      }
    }
    N3("query_api_streaming_end");

    // 2. Tool execution 阶段
    if (c) telemetry("tengu_streaming_tool_execution_used", {...});
    else   telemetry("tengu_streaming_tool_execution_not_used", {...});

    // ★★★ 二选一：drain streaming executor 剩余结果，要么走 batched
    let R6 = c ? c.getRemainingResults() : zN8(pendingToolUses, ...);
    for await (let A6 of R6) { yield A6.message; ... }
  }
}
```

**关键发现**：streaming-tool-execution 路径下，`addTool()` 是在外层 `for await` 流式循环里调用的 —— **模型还在生成 token、SSE 流还没结束，tools 就已经开始执行了**。

## Streaming Tool Executor `C68` 的真正巧妙之处

```js
class C68 {
  tools = [];                    // {id, block, status, isConcurrencySafe, ...}

  addTool(q, K) {
    let _ = findToolDef(q.name);
    let parsed = _.inputSchema.safeParse(q.input);
    let isConcurrencySafe = parsed?.success
      ? Boolean(_.isConcurrencySafe(parsed.data))
      : false;
    this.tools.push({...});
    this.processQueue();         // 立即 kick off
  }

  canExecuteTool(q) {
    let executing = this.tools.filter(t => t.status === "executing");
    return executing.length === 0 ||
           (q && executing.every(t => t.isConcurrencySafe));
  }

  async processQueue() {
    for (let t of this.tools) {
      if (t.status !== "queued") continue;
      if (this.canExecuteTool(t.isConcurrencySafe)) {
        await this.executeTool(t);
      } else if (!t.isConcurrencySafe) {
        break;                    // 遇到不安全 tool 就停队列
      }
    }
  }
}
```

### Per-tool 并发安全谓词（Claude Code 比 kimi-cli 更细的设计）

**这是 Claude Code 最巧妙的地方**：每个 tool 上有 `isConcurrencySafe(input)` 方法，由 tool 定义提供（默认看 `annotations.readOnlyHint`）

调度规则：
- 多个**只读**工具（Read/Grep/Glob/WebFetch）可以同时跑
- 一旦遇到**写工具**（Bash/Edit/Write）就 break 队列、变成串行栅栏
- 模型还在流式吐字时，已经收到的 tool_use 就开始执行
- 等流结束时大概率结果已经回来，UI 几乎"零延迟"

**这能精确到"同一批里 3 个 Read 并发 + 1 个 Edit 串行栅栏 + 后面的 Read 再并发"**。

## Post-stream Batched 路径（fallback）

当 feature gate 关闭，走传统 batched 模式：

```js
// 把一批 tool_use blocks 按 concurrency-safe 分段
function $5Y(toolUses, ctx) {
  return toolUses.reduce((acc, Y) => {
    let safe = parsed?.success ? Boolean(tool?.isConcurrencySafe(parsed.data)) : false;
    if (safe && acc[acc.length-1]?.isConcurrencySafe)
      acc[acc.length-1].blocks.push(Y);  // 连续 safe 合并到一组
    else
      acc.push({ isConcurrencySafe: safe, blocks: [Y] });
    return acc;
  }, []);
}

// 派发器
async function* zN8(toolUses, ...) {
  for (let {isConcurrencySafe, blocks} of $5Y(toolUses, ctx)) {
    if (isConcurrencySafe) yield* j5Y(blocks, ...);  // 并发
    else                   yield* w5Y(blocks, ...);  // 串行 for-loop
  }
}

// 通用 generator merge（max concurrency 默认 10）
async function* _N8(generators, K = Infinity) { /* Promise.race-based merge */ }
```

环境变量控制并发上限：`CLAUDE_CODE_MAX_TOOL_USE_CONCURRENCY`（默认 10）

## Task (Sub-agent) Tool

bundle 里 `name:"Task"`：

- 子 agent 是 main loop 的**递归调用**，传一个新的 `agentId` 和**独立的 message 数组**
- 父 context 不直接共享 —— 子 agent 收到的是 Task 工具 input 里的 prompt（"clean context"）
- 子 agent 完成时**只把最终文本结果返回给父** —— 中间所有 tool calls/思考都被丢弃，是 context 隔离的核心机制
- 多个并行 Task 通过 `_N8` 同样的 generator-merge 实现并行（Task tool 的 `isConcurrencySafe` 默认 `true`）

`hitmux/HitCC` 文档把这套叫 "Sidechain / Subagent / Forked agent"。

## Context Compaction 三档

| 类型 | 函数 | 触发 | 行为 |
|---|---|---|---|
| **microcompact** | `Zg` | 每 turn 静默检查 | 增量摘要旧 tool_result，不打断用户 |
| **autocompact** | `BJK` | token 接近模型 ctx 上限 | 完整 summarize 整段对话，保留最近 N 轮原文 |
| **reactive compact** | `Mj6.tryReactiveCompact` | API 返回 `prompt_too_long` 错误 | 兜底，立刻 compact 并重试 |

bundle 里的 `BJK` 入口：
```js
async function BJK(q, K, _, Y, z, A) {
  if (env.DISABLE_COMPACT) return {wasCompacted: false};
  if (z?.consecutiveFailures >= SXK) return {wasCompacted: false};
  let O = K.options.mainLoopModel;
  if (!await checkShouldCompact(...)) return {wasCompacted: false};
  // 跟踪 isRecompactionInChain / turnsSincePreviousCompact
  ...
}
```

**Session 文件格式 = JSONL append-only**（在 `~/.claude/sessions/` 和 `~/.claude/projects/` 里能直接看到）

## Hooks 系统

bundle 里有 231 个 `hooks?` 匹配。事件类型：
- `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, `SessionStart`, `Notification`, `SubagentStop`

实现：每次工具执行前后，主循环调用 hook runner，spawn 用户配置的 shell 命令，stdin 喂 tool input，stdout 解析 JSON 决定 `behavior: "allow"|"deny"|"ask"|"passthrough"`。

## Skill 系统

不在主循环里 —— Skills 是**懒加载的 system prompt 注入**：
- `~/.claude/skills/<name>/SKILL.md` 在 session 启动时被扫描
- 只有名字+description 注入 system prompt
- 实际内容只有当 Claude 通过 `Skill` 工具显式调用时才会读取并注入到 context
- **这就是为什么 Skill 比"全部塞进 system prompt"省 token**

## Claude Code vs kimi-cli 对比（修正版）

| 维度 | kimi-cli `_step()` | Claude Code v2.1.87 |
|---|---|---|
| Tool execution timing | **In-stream concurrent** | **两条路径**：streaming-concurrent (gated) + post-stream batched (fallback) |
| 并发安全判断 | （Tool 由 SimpleToolset.handle 立即 dispatch）| 每个 tool 自带 `isConcurrencySafe(input)`，readOnly 默认 safe |
| 并发栅栏 | （需核对）| **写工具自动栅栏，能精确到"3 read 并发 + 1 write 栅栏 + 后续 read 再并发"** |
| 并行原语 | asyncio.create_task + Future | `_N8` Promise.race-based generator merge，max=10 |
| Sub-agent | 持久化 + resumable | Task tool = 主循环递归 + clean context + 父子只交换最终文本 |
| Context 管理 | compaction | **三档**：micro / auto / reactive |
| Session 格式 | JSONL append-only + checkpoint 行 | JSONL append-only |
| Hooks | server + wire-side（双路）| settings.json + shell + JSON IPC |

**架构相似点**：两者都用 async generator 作主循环骨架；都把 LLM call、tool exec、UI 流通过 yield 管线串起来。

**Claude Code 比 kimi-cli 多**：feature gate 灰度、Statsig、reactive/auto/micro 三档 compact、forked-agent task budget、stop hook 拦截 + 重试、模型 fallback (Opus→Sonnet)、prompt-too-long 自动 recover、output-token-limit 自动续写

**kimi-cli 比 Claude Code 多**：D-Mail 物理时光机、Steer (流中注入 user msg)、持久化 sub-agent、Wire 协议 (UI 完全解耦)、跨品牌 skill 兼容

## 三个需要纠正的之前判断

1. **"Claude Code 是 post-stream parallel"** —— ❌ **不准确**。它**两条路径都有**，且 streaming 路径已经在生产灰度。说 "Claude Code 默认 post-stream，但灰度中的新路径是 in-stream concurrent" 才准确。

2. **"Claude Code 闭源所以只能猜"** —— ❌ **已经过时**。
   - 2026-03-31 sourcemap 泄露后基本是公开的
   - cli.js 一直就是 minified-not-obfuscated，所有 prompt/工具描述/控制流都明文可读
   - **本地装的 Claude Code 可以直接 grep cli.js**

3. **架构差异本质** —— 不是"流式 vs 后处理"的差异，而是 **并发安全模型** 的差异：Claude Code 用 **per-tool `isConcurrencySafe(input)` 谓词** 来动态分组。kimi-cli 是不是有同等粒度的判断需要核对。

## 仍未确认 / Unknown

- v2.1.91+ 已切到 **Bun-compiled native binary**（198MB Mach-O arm64），需要 `bun build --compile` 反向才能继续读
- `streamingToolExecution` gate 在普通用户身上**默认开还是关**：bundle 里看不出，要看 Statsig 灰度分组
- telemetry 名称 `tengu_streaming_tool_execution2` 的 "2" 后缀说明这是第二代实现，第一代可能已下线或失败回滚
- dev.to 那篇说 "5.4% orphaned tool calls" 因为 `_N8` 用裸 `Promise.race` —— 实现确实是裸 race，但 orphan 是不是真因这个还需细致复现

## 关键文件路径

- 本地 cli.js bundle (v2.1.87): `/opt/homebrew/lib/node_modules/@anthropic-ai/claude-code/cli.js`
- 本地 native binary (v2.1.92): `/Users/benzema/.local/share/claude/versions/2.1.92`
- session 数据: `~/.claude/sessions/`, `~/.claude/projects/`
- skills: `~/.claude/skills/`

## 关键开源 RE 仓库

- [hitmux/HitCC](https://github.com/hitmux/HitCC) — Complete reverse-engineering of v2.1.84
- [ghuntley/claude-code-source-code-deobfuscation](https://github.com/ghuntley/claude-code-source-code-deobfuscation) — Cleanroom deobfuscation
- [Yuyz0112/claude-code-reverse](https://github.com/Yuyz0112/claude-code-reverse) — Behavioral analysis
- [777genius/claude-code-working](https://github.com/777genius/claude-code-working) — Working reverse-engineered rebuild
- [shareAI-lab/learn-claude-code](https://github.com/shareAI-lab/learn-claude-code) — Nano implementation
- [hangsman/claude-code-source](https://github.com/hangsman/claude-code-source) — Raw cli.js.map mirror
- [DEV: We Reverse-Engineered 12 Versions of Claude Code](https://dev.to/kolkov/we-reverse-engineered-12-versions-of-claude-code-then-it-leaked-its-own-source-code-pij)
