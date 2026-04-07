# Agent Tool 并发模型完整讨论

> 来源: 知识库 Query 讨论 (2026-04-07)
> 起源: 阅读 RC 完整代码评审中"Sync handle, async future"段落引出的连环讨论
> 核心问题: agent 框架如何让 LLM streaming 和 tool execution 在时间上重叠

---

## 讨论起点

在阅读 kosong 评审里这段时：

> ### Sync handle, async future（最优雅的设计）
>
> ```python
> class Toolset(Protocol):
>     def handle(self, tool_call: ToolCall) -> HandleResult: ...
>     # MUST NOT do any blocking operations
> ```
>
> `handle()` 是同步函数，立即返回 future。**工具开始执行的时机和 LLM 输出 tool_call 的时机一致**——LLM 后续 token 和 tool 执行**并发**。

提的问题：**为什么是革命性的？claude code 也是这么实现的吗？已经是主流了？**

---

## Part 1: 为什么"sync handle / async future"是革命性的

### 传统串行流程

```
时刻 T0  ────────────────────────────────────►
         LLM 开始 streaming 输出
         "我需要查一下数据库..."
         (token 流)
         "...所以我要调用 query_db 这个工具"
         tool_call 出现
时刻 T1  ────────────────────────────────────►
         LLM 输出结束（流关闭）
         framework 解析出 tool_call
         开始执行 query_db
         ⏳ query_db 跑了 8 秒
时刻 T9  ────────────────────────────────────►
         tool result 拿到
         framework 把 result 拼回 history
         开始下一个 LLM step
```

**关键问题**: tool 必须**等 LLM 把整段输出说完**才能开始执行。

### 时间轴对比（单 tool 例子）

LLM 输出 80 token = 3 秒。`query_db` = 8 秒。

```
传统串行: 0s ─── 3s (LLM 说完) ─── 11s (工具跑完)  → 11 秒
kosong:   0s ──► 3s
              └─► 工具开始执行 (并发)
                 tool 跑 8s
                              ↑
                  LLM 流和工具同时结束
                                                 → 8 秒
```

### 多 tool 才是真正的革命

LLM 一次说要调三个工具：

```
LLM 输出: 5 秒
工具 1 (查 DB): 8 秒
工具 2 (查 web): 6 秒
工具 3 (读文件): 1 秒
```

**传统串行**: 5 + 8 + 6 + 1 = **20 秒**
**kosong 并发**: max(5, 8, 6, 1) = **8 秒**
**节省 60%**

而且这是**自然的并发**——你不需要在 prompt 里告诉 LLM "请并发调用工具"，不需要写 `Promise.all`，不需要任何编排代码。

### 为什么 sync handle 是关键

**问题**: 如果 `handle()` 是 async（用 await），那 streaming parser 调用它的时候就会**阻塞 streaming**——等 handle 返回才能继续读 LLM 的下一个 token。

```python
# ❌ 错误设计
async def handle(self, tool_call):
    result = await actually_run_tool(tool_call)  # 阻塞了！
    return result

async for token in llm_stream:
    if is_tool_call(token):
        result = await toolset.handle(tool_call)  # 卡住整个 streaming
```

**kosong 的做法**: handle 必须是 sync，立刻返回 future：

```python
def handle(self, tool_call) -> Future:
    future = ToolResultFuture()
    asyncio.create_task(self._real_work(tool_call, future))  # 后台跑
    return future  # 0 延迟返回

async for token in llm_stream:
    if is_tool_call(token):
        future = toolset.handle(tool_call)  # 不阻塞！
        pending_futures.append(future)
        # streaming 立刻继续读下一个 token

results = await asyncio.gather(*pending_futures)
```

`# MUST NOT do any blocking operations` 这条注释的真正含义：**保护 streaming pipeline 不被任何 tool 拖住**。

### 生活类比

**传统 agent** = 收银员亲自做咖啡，一杯一杯做，队伍很长
**kosong** = 收银员叫号（返回 future），后台咖啡机并行，立刻服务下一位

**收银员 = streaming loop。绝不能让她亲自去做咖啡。她只能叫号。**

---

## Part 2: 各主流框架的现状

| 框架 | 是否并发执行 tool？ | 怎么做的 |
|------|------|------|
| **kosong / kimi-cli** | ✅ 流式中并发 | sync handle / async future |
| **Claude Code** | ⚠️ **两条路径并存** | feature gate 灰度（详见 Part 3）|
| **Cursor** | ⚠️ 部分（不公开） | 推测有，但实现不公开 |
| **OpenAI Agents SDK** | ⚠️ 多 tool 并发，但不在 stream 中 | `Runner.run` 等流结束后并发 |
| **LangChain AgentExecutor** | ❌ 串行 | 等 LLM 输出完整 → 解析 → 一个个跑 |
| **LangGraph** | ⚠️ 你自己写并发 | 用 `parallel` 节点，但要手动编排 |
| **AutoGen** | ❌ 串行 | 一次一个 |
| **CrewAI** | ❌ 串行 | 一次一个 |

### 为什么这件事还不主流？

**原因一：API 层支持得晚**
Anthropic 是 2024 年才把 streaming tool use 加进 API。框架层要等 API 层稳定才能往上做。

**原因二：实现复杂度高**
要做对这件事，framework 需要：
1. 流式 parser 识别 tool_call 边界
2. 并发模型（async + future）不被 streaming 阻塞
3. ToolCall vs ToolCallPart 分离
4. 错误处理、cancellation、回滚机制

LangChain 的 AgentExecutor 是 2022 年的设计，要重写需要 breaking change。

**原因三：在大多数场景下"够用了"**
如果你的 tool 都是几百毫秒的 API 调用，串行和并发的体验差不多。**真正的差距出现在 coding agent**——shell 命令（秒级）、数据库查询、慢测试、长 LLM 输出。

---

## Part 3: Claude Code 的实际情况（重要修正）

### ⚠️ 之前判断错误

之前说："Claude Code 是 post-stream parallel"——**只对了一半**。

### 实际真相（基于本地 cli.js v2.1.87 一手分析）

**Claude Code 同时拥有两条 tool execution 路径**，由 Statsig feature gate `tengu_streaming_tool_execution2` 控制：

1. **Streaming-concurrent 路径**（开关开启时）—— 和 kimi-cli 同构
2. **Post-stream batched 路径**（fallback）—— 等 turn 结束才批量执行

且 Claude Code 发送 telemetry：
- `tengu_streaming_tool_execution_used`
- `tengu_streaming_tool_execution_not_used`

**说明 Anthropic 在 A/B 灰度切换，streaming 路径已经在生产中跑**。

### Claude Code 比 kimi-cli 更细的设计：per-tool concurrency safety

```js
class C68 {
  addTool(q, K) {
    let _ = findToolDef(q.name);
    let parsed = _.inputSchema.safeParse(q.input);
    let isConcurrencySafe = parsed?.success
      ? Boolean(_.isConcurrencySafe(parsed.data))   // ★ 每个 tool 自己说能否并发
      : false;
    this.tools.push({...});
    this.processQueue();
  }

  canExecuteTool(q) {
    let executing = this.tools.filter(t => t.status === "executing");
    return executing.length === 0 ||
           (q && executing.every(t => t.isConcurrencySafe));
  }
}
```

**调度规则**:
- 每个 tool 上有 `isConcurrencySafe(input)` 谓词，由 tool 定义提供
- 只读工具（Read/Grep/Glob/WebFetch）默认 safe → 可同时跑
- 写工具（Bash/Edit/Write）→ 自动栅栏，串行执行
- 能精确到"3 个 Read 并发 + 1 个 Edit 串行 + 后续 Read 再并发"

**这个粒度是 kimi-cli 没有的**（kimi-cli 用 SimpleToolset 立刻 dispatch，不区分 read/write）。

### 完整对比表（修正版）

| 维度 | kimi-cli | Claude Code |
|---|---|---|
| Streaming tool execution | ✅ in-stream concurrent | ✅ in-stream concurrent (gated) + batched fallback |
| 并发安全粒度 | dispatch 立即并发 | per-tool `isConcurrencySafe(input)` 动态分组 |
| 写工具栅栏 | 没有特殊处理 | **自动 break 队列** |
| 并发上限 | 默认 unlimited | `CLAUDE_CODE_MAX_TOOL_USE_CONCURRENCY=10` |
| Sub-agent | **可 resume 持久化** | clean context recursion |
| Compaction | 单一阶段 | **三档**: micro / auto / reactive |
| Session | JSONL + checkpoint 行 | JSONL append-only |
| 时光机回滚 | ✅ D-Mail | ❌ |
| Wire 协议 | ✅ 完全解耦 UI | ❌ stdout 中心 |

---

## Part 4: 三个之前判断需要更正

1. **"Claude Code 是 post-stream parallel"** —— ❌ 不准确。两条路径都有，streaming 路径已生产灰度。
2. **"Claude Code 闭源所以只能猜"** —— ❌ 已过时。2026-03-31 sourcemap 泄露后基本公开；cli.js 是 minified-not-obfuscated，所有 prompt/工具/控制流明文可读。
3. **架构差异本质** —— 不是"流式 vs 后处理"，而是 **并发安全模型** 的差异：Claude Code 用 per-tool 谓词动态分组。

---

## Part 5: 对 Knowledge Agent Network 的启示

KAN 的查询大多数是慢操作（跨节点 wiki query），如果用 LangChain 那种串行模型，体验会非常糟。

### 应该借鉴的设计

1. **kosong 的 sync handle / async future**——核心并发原语
2. **Claude Code 的 per-tool `isConcurrencySafe(input)`**——粒度更细
3. **写操作栅栏**——KAN 的"写知识"操作必须串行避免冲突
4. **Generator merge 模式**（Claude Code `_N8`）——并发上限可配

### 设计建议

KAN 节点查询应该：
- 多个**只读查询**（query / search / read_page）→ 并发
- 任何**写操作**（ingest / update / lint）→ 栅栏
- 跨节点查询用 `isConcurrencySafe(node, query)` 谓词决定能不能并发

---

## 一句话总结

**"sync handle, async future" 不是 kosong 独有，但确实是少数派。** Claude Code 在 streaming 路径上和 kimi-cli 同构，且更进一步做了 per-tool 并发安全粒度。LangChain / AutoGen / CrewAI 这些主流框架基本都是串行的。**在 coding agent / 长延迟 tool 场景下，并发原语决定了体验是"卡顿"还是"流畅"**——KAN 必须从一开始就做对这件事。

---

## 关键文件路径

- 本地 Claude Code bundle: `/opt/homebrew/lib/node_modules/@anthropic-ai/claude-code/cli.js`
- 本地 Claude Code native: `/Users/benzema/.local/share/claude/versions/2.1.92`
- 完整 Claude Code 源码分析: `raw/articles/claude-code-source-analysis.md`
- RC 完整代码评审: `raw/articles/richard-chien-code-review.md`
