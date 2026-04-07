# Agent Harness 实现全景

> 把 kimi-cli (Richard Chien / Moonshot)、Claude Code (Anthropic)、kosong (RC 的 LLM 抽象层)、agent-vault (RC 的 secrets 层) 放在一起的横向对照地图。

## 概览

2025-2026 是 production-grade agent harness 的成熟期。两条最值得研究的路线是 **Anthropic Claude Code**（cli.js bundle 16,750 行 + native binary，2026-03-31 sourcemap 泄露后基本公开）和 **Richard Chien 在 Moonshot 的 kimi-cli + 双线 botiverse 个人栈**。这两条路线**架构高度同构**（都用 async generator 主循环 + streaming 中并发 dispatch tool），但在 5 个维度上各有所长。

把两个生态拆开看，得到一张完整的 agent harness anatomy：**主循环 → LLM 抽象 → 工具并发 → 子 agent → 上下文压缩 → hooks → 协议 → skills → 时光机**。

## 关键组件

### 主循环（async generator）

- **kimi-cli `_step()`** (`src/kimi_cli/soul/kimisoul.py:780-935`，~150 行)：投递 pending notifications → 收集 dynamic injections → normalize_history → tenacity 装饰的 `await kosong.step()` → `await result.tool_results()` → asyncio.shield 保护 grow_context → 检查 ToolReject + D-Mail → return StepOutcome
- **Claude Code `mainLoop()`**（cli.js 提取）：feature gate 决定 streaming executor `C68` → `for await` 流式收 LLM SSE → 每收到 `assistant` 消息立即 `addTool()` → `getCompletedResults()` drain → telemetry → `getRemainingResults()` 或 `zN8` batched dispatch
- **共同点**：都用 async generator 作骨架；都把 LLM call、tool exec、UI 流通过 yield 管线串起来
- 详细见 [[concepts/agent-loop]] 和 [[concepts/agent-tool-concurrency]]

### Streaming Tool Execution（两条路径并存）

| 维度 | kimi-cli | Claude Code v2.1.87 |
|---|---|---|
| Timing | **In-stream concurrent**（kosong sync handle / async future）| **两条路径**：streaming-concurrent (gated by `tengu_streaming_tool_execution2`) + post-stream batched (fallback) |
| 并发安全 | dispatch 立即并发，不区分 read/write | 每个 tool 自带 `isConcurrencySafe(input)` 谓词，readOnly 默认 safe |
| 写工具栅栏 | 没有特殊处理 | **自动 break 队列**：3 read 并发 + 1 write 串行栅栏 + 后续 read 再并发 |
| 并发上限 | unlimited (asyncio gather) | `CLAUDE_CODE_MAX_TOOL_USE_CONCURRENCY=10`，`_N8` Promise.race-based generator merge |

详细对比见 [[concepts/agent-tool-concurrency]]。

### LLM 抽象层（kosong = empty layer）

- Public API 只有 4 个名字：`generate / GenerateResult / step / StepResult`
- **12 件故意不做的事**：Agent 类、prompt 模板、retry policy、callback/tracing、token counting、streaming text aggregator、RAG、structured output、multi-agent orchestration、session/history 管理、in-process tool registry、provider SDK re-export
- Provider 用 **Protocol** 而非注册表（5 个方法的 ChatProvider Protocol，无 entry point/plugin）
- 政治表态：`kosong/chat_provider/` 放 Kimi（first-party），`kosong/contrib/chat_provider/` 放 **Anthropic / Google / OpenAI**——一个 Moonshot 工程师把 OpenAI 标记为 contrib
- Claude Code 没有等价物——LLM 调用直接绑死 Anthropic API

### Sub-agent 设计

- **kimi-cli**：`SubagentStore` 把每个子 agent 持久化到 `session/subagents/<agent_id>/`，**可 resume**
- **Claude Code Task tool**：主循环的**递归调用**，传新 `agentId` 和独立 message 数组。父子只交换最终文本，**clean context recursion**——中间所有 tool calls/思考都被丢弃
- 详细见 [[concepts/sub-agent-architecture]]

### Context Compaction

- **kimi-cli**：单一 compaction 阶段，`prompts/compact.md` 输出结构化 schema (`<current_focus>` / `<environment>` / `<completed_tasks>` / `<active_issues>` / `<code_state>` / `<important_context>`)
- **Claude Code 三档**：
  - `microcompact` (Zg) — 每 turn 静默检查，增量摘要旧 tool_result
  - `autocompact` (BJK) — token 接近上限，完整 summarize
  - `reactive compact` (Mj6.tryReactiveCompact) — API 返回 `prompt_too_long` 错误的兜底
- 详细见 [[concepts/context-engineering]]

### Hooks 系统

- **Claude Code**：231 个 `hooks?` 匹配。事件：`PreToolUse / PostToolUse / UserPromptSubmit / Stop / SessionStart / Notification / SubagentStop`。spawn 用户 shell，stdin 喂 tool input，stdout 解析 `behavior: "allow"|"deny"|"ask"|"passthrough"`
- **kimi-cli**：server-side + wire-side 双路 hooks，client 通过 wire 反向给 agent 注入 hooks

### Wire Protocol vs stdout-centric

- **kimi-cli Wire** (`src/kimi_cli/wire/types.py` 698 行)：JSON-RPC over stdio，17 种事件 + 4 种反向 request。所有 UI（shell/print/acp/web/vis）都是 wire event 的消费者，**soul/ 完全不知道 UI 的存在**
- **Claude Code**：stdout 中心化路径，UI 与 main loop 较紧耦合
- Wire 是 OneBot (2020) 协议的 2026 年再实现——RC 在 2020 年就把 2026 年 agent 协议要解决的问题想清楚了

### Skills 系统

- **Claude Code**：`~/.claude/skills/<name>/SKILL.md` 在 session 启动时只扫描名字+description 注入 system prompt，实际内容在 `Skill` 工具显式调用时才读
- **kimi-cli 跨品牌兼容（最大胆设计）**：`_get_user_brand_skills_dir_candidates()` 直接读 `~/.kimi/skills` + **`~/.claude/skills`** + **`~/.codex/skills`**——开源敌商兼容
- 详细见 [[concepts/skills-system]]

### D-Mail / Time Machine

- **kimi-cli**：append-only JSONL + `_checkpoint` 行 + `revert_to()` 重写文件。LLM 自己说"我刚才走错了"→ 物理回滚到 checkpoint → 重跑一条伪 user 消息（抛 `BackToTheFuture` 异常）
- **Claude Code**：无等价机制
- 这是 kimi-cli 比 Claude Code "多"的最独特机制

### 信任边界（agent-vault）

- 4 个文件 1100 行的全部代码，**356 stars 2 个月**
- 3 个最巧妙的设计：(1) Bigram word-likeness 检测区分品牌词 vs 随机字符串（硬编码 110 个英文最常见 bigram，命中率 ≥30% 算英文）；(2) `split(value).join(placeholder)` 一行避免 regex escape；(3) UNVAULTED placeholder 用 SHA256 prefix 反向匹配
- 安全模型诚实：master key 是明文 hex 文件 0600 权限，没有 KDF/passphrase，"threat model 是阻止 secrets 被发到 LLM provider，不是抵御 root"
- TTY-only sensitive command 防止 agent 自己 execute——agent 完整 trust boundary 模型被代码化

## AGENTS.md 处理（教科书级别）

- 候选优先级：`.kimi/AGENTS.md` > `AGENTS.md` > `agents.md`
- **32 KiB 总预算，leaf-first 分配**——深目录永远不会被浅目录挤掉
- 每段加 `<!-- From: <path> -->` 注解
- system prompt 明确告诉 LLM "deeper directories take precedence"
- RC 故意选 `AGENTS.md` 而不是 `CLAUDE.md`——是个公开声明：agent 文档应该跨品牌中立

## 完整对照表

| 维度 | kimi-cli | Claude Code |
|---|---|---|
| 主循环 | async generator (`_step`) | async generator (`mainLoop`) |
| LLM 层 | kosong (~4,200 LOC empty layer) | 直接绑 Anthropic SDK |
| Tool 并发 | in-stream concurrent | streaming + batched 双路径 |
| 并发安全 | dispatch 立即 | per-tool `isConcurrencySafe` 谓词 |
| Sub-agent | **可 resume 持久化** | clean context recursion |
| Compaction | 单档结构化 | **三档**（micro/auto/reactive） |
| Session | JSONL + checkpoint 行 | JSONL append-only |
| Hooks | server + wire 双路 | settings.json + shell + JSON IPC |
| Wire 协议 | ✅ 完全解耦 UI | ❌ stdout 中心 |
| Time machine | ✅ D-Mail | ❌ |
| Steer (流中注入 user msg) | ✅ `_steer_queue` | ❌ |
| Cross-brand skills | ✅ 读 `~/.claude/skills` | ❌ |
| 信任边界 | agent-vault placeholder | 无显式 vault |
| Token 计数 | 双轨（精确 + estimate） | 单一精确 |

## 数据来源

- `raw/articles/richard-chien-deep-profile.md` — RC 完整人物档案（38 个 repo + botiverse org + 工程哲学）
- `raw/articles/richard-chien-code-review.md` — 7 个项目 ~19,000 LOC 源码级评审
- `raw/articles/richard-chien-stack.md` — RC 技术栈早期研究
- `raw/articles/claude-code-source-analysis.md` — Claude Code v2.1.87 cli.js 一手分析（修正了 streaming vs batched 的判断）
- `raw/articles/agent-tool-concurrency-discussion.md` — 三种并发路径完整讨论
- `raw/articles/slock-ai-research.md` — RC 的 botiverse 侧产品 slock.ai
- `raw/articles/claude-code-research/` — Claude Code SP 详解、架构分析、Skills 分析等（6 篇）

## 开放问题

1. **Bun-compiled binary 反向**：Claude Code v2.1.91+ 已切到 Bun-compiled native binary (198MB Mach-O arm64)，需要 `bun build --compile` 反向才能继续读
2. **`tengu_streaming_tool_execution2` 灰度比例**：bundle 里看不出 streaming 路径在普通用户身上默认开还是关，要看 Statsig 灰度分组
3. **Telemetry 名 "2" 后缀**：说明这是第二代实现，第一代可能已下线或失败回滚——值得追溯
4. **`pink` 是否会成为 Kimi CLI 下一代 UI**：RC 自己写 Python 版 Ink，Three-section App 完美匹配 LLM CLI，时间点对——预测但未确认
5. **kimchi 的真实意图**：截至 2026-04-03 是 0 commits 的占位 fork，推测是给 botiverse / slock.ai 锁定一个不会被 Moonshot breaking change 的稳定 distribution

## 关联

- [[concepts/agent-tool-concurrency]] — 详细的并发模型对比
- [[concepts/harness-engineering]] — 上层概念
- [[concepts/agent-loop]] / [[concepts/sub-agent-architecture]] / [[concepts/agent-runtime]] — 各组件
- [[concepts/agent-native-im]] — RC botiverse 侧的 slock.ai 是这套 harness 的产品 surface
- [[concepts/knowledge-agent-network]] — Wire 协议 + agent-vault placeholder 是 KAN 协议层的直接灵感
- [[connections/harness-to-kan]] — 8 个具体设计模式 → KAN 的迁移
- [[maps/harness-engineering]] — 抽象六大支柱版的 harness 全景

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
