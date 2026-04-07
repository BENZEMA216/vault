# Richard Chien 完整生态深度代码评审

> 来源: 知识库 Query 讨论 (2026-04-07)
> 这是真正的源码级评审，覆盖 7 个项目（kimi-cli + kosong + agent-vault + OneBot + kimchi + tldr-vscode + pink）
> 总分析量：~19,000 LOC 的源码 + spec 文档

---

## 总览

| 项目 | 类型 | 规模 | 角色 |
|---|---|---|---|
| **kimi-cli** | Python agent harness | ~25-35k LOC | RC 主战场（Moonshot） |
| **kosong** | Python LLM 抽象层 | ~4,200 LOC | kimi-cli 的 LLM 层 |
| **agent-vault** | TypeScript secrets layer | ~1,100 LOC | botiverse 已发布产品 |
| **OneBot** | Spec only | Markdown | 2020 年 chatbot 协议标准 |
| **kimchi** | Fork (空) | 0 独有 commits | botiverse 的 kimi-cli 占位 |
| **tldr-vscode** | TypeScript VSCode 扩展 | ~1k LOC | LLM-friendly stub renderer |
| **pink** | Python TUI framework | ~500 LOC | Ink 的 Python 移植 |

---

## 一、最大的发现：RC 是「协议人」+「Empty Layer 美学」的统一者

把所有 7 个项目放一起看，**同一种设计哲学贯穿了 10 年**：

### 协议优先（10 年没变的本能）

| 项目 | 协议产物 | 设计模式 |
|---|---|---|
| **OneBot 12** (2020) | chatbot 跨平台标准 | 4 种通信方式 + 3 种数据协议 + meta/message/notice/request 4 种事件 + `<prefix>.field` 命名空间扩展 |
| **Kimi Wire** (2026) | agent harness over JSON-RPC | initialize/prompt/replay/steer/cancel + 17 种 event + 反向 RPC + capability negotiation |
| **kosong** | LLM provider Protocol | 5 个方法的 ChatProvider Protocol，无注册表 |
| **agent-vault** | `<agent-vault:key>` 占位符协议 | placeholder + restoration |

**最关键的发现**: OneBot 和 Kimi Wire 几乎是**同一份设计的两次实现**：

| 维度 | OneBot 12 | Kimi Wire 1.7 |
|---|---|---|
| 解决问题 | N 平台 × M 框架 | N LLM × M UI/IDE |
| 错误码 | retcode 分段（10xxx/20xxx/30xxx + 36xxx "I Am Tired"）| JSON-RPC 标准段 + 业务段（-32000 turn already running 等）|
| 异步关联 | `echo` 字段 | JSON-RPC `id` |
| 能力发现 | `get_supported_actions` | `initialize.capabilities` + `slash_commands` + `supported_events` |
| 双向 RPC | event push + action request | event push + 双向 request（agent 反向问 client）|
| 扩展机制 | `<prefix>.field` 命名空间 | `external_tools` + `hooks` + `client_capabilities` |

**RC 在 2020 年就把 2026 年 agent 协议要解决的问题想清楚了。**

### Empty Layer 美学（命名都是字面的）

- **Kosong** = 印尼语「空」→ LLM 抽象层故意不做 prompt template / memory / agent loop / retry policy
- **pykaos** = "kaos" = "OS abstraction" 反过来读 → OS 接口故意只暴露受控访问
- **agent-vault** → 故意不让 agent 看到 secret
- **kimchi** → 把 kimi-cli 重定位为 "agent harness with programmatic interface via stdio"，不加任何东西

**"Give agent less, not more"** 的哲学不是口号，是每个项目的代码层面强制约束。

---

## 二、kimi-cli 完整架构（最核心的发现）

### 主循环 `_step()` 的真正实现（kimisoul.py:780-935，~150 行）

```
_step():
  1. 投递 pending notifications 到 LLM context
  2. 收集 dynamic injections（plan_mode/yolo_mode reminders）→ 注入 user message
  3. normalize_history(context)
  4. 用 tenacity 装饰的 await kosong.step(provider, system_prompt, toolset, history)
     ↓ inner: connection_recovery wrapper
     ↓ inner: kosong.step 真正调 LLM（streaming + tool 并发）
  5. await result.tool_results() — tool 已经在 LLM token stream 里并发执行
  6. asyncio.shield 保护 grow_context（避免 cancel 导致半截写入）
  7. 检查 ToolReject + has_feedback：无 feedback 则停 turn
  8. 检查 D-Mail（DenwaRenji）→ 抛 BackToTheFuture 异常 → 主循环 revert checkpoint
  9. 无 tool_calls → return StepOutcome("no_tool_calls")
```

**这是我看过的最成熟的 agent harness 主循环**。比 Claude Code 多了几个关键机制：

### 7 个核心机制

| 机制 | 实现 | 价值 |
|---|---|---|
| **D-Mail 物理时光机** | append-only JSONL + `_checkpoint` 行 + revert_to() 重写文件 | LLM 自己说"我刚才走错了" → 物理回滚到 checkpoint，重跑一条伪 user 消息 |
| **Ralph loop** | 用 Flow 状态机实现（BEGIN → R1 task → R2 decision → CONTINUE/STOP）| Geoffrey Huntley 的"反复喂回去"技巧产品化 |
| **Steer**（v1.4） | `_steer_queue` + `_consume_pending_steers` | 在 LLM 流式输出中注入新 user 消息，下一步消化 |
| **持久化 sub-agent** | `SubagentStore` 把每个子 agent 持久化到 `session/subagents/<agent_id>/` | **可 resume 的子 agent**，比 Claude Code 的 Task tool 强 |
| **Wire external tool** | client 在 initialize 时注册工具 → LLM 调用时反向 wire request | **IDE 可以反向给 agent 注入工具**，不止是消费事件 |
| **Dynamic injection** | 周期性注入 `<system-reminder>`（plan mode 每 5 turn，full/sparse 节奏）| 防漂移 + 保持 prompt cache |
| **Token 双轨计数** | LLM usage（精确）+ pending estimate（粗）= compaction 决策 | 解决 "tool result 还没回 LLM 就 overflow" 死角 |

### Compaction 是 Claude Code 学派但更结构化

`prompts/compact.md` 输出 schema：
```
<current_focus>
<environment>
<completed_tasks>
<active_issues>
<code_state>
<important_context>
```

明显借鉴 Claude Code 的 compact，但更细致。

### AGENTS.md 处理是教科书级别

`load_agents_md()`：
- 候选优先级：`.kimi/AGENTS.md` > `AGENTS.md` > `agents.md`
- **32 KiB 总预算，leaf-first 分配**（深目录永远不会被浅目录挤掉）
- 每段加 `<!-- From: <path> -->` 注解
- system prompt 明确告诉 LLM "deeper directories take precedence"

**RC 故意选 `AGENTS.md` 而不是 `CLAUDE.md`**——这是个公开声明：agent 文档应该跨品牌中立。

### Skill 跨品牌兼容（最大胆的设计）

```python
def _get_user_brand_skills_dir_candidates():
    return (
        KaosPath.home() / ".kimi" / "skills",
        KaosPath.home() / ".claude" / "skills",
        KaosPath.home() / ".codex" / "skills",
    )
```

**Kimi CLI 直接读 `~/.claude/skills` 和 `~/.codex/skills`**。开源敌商兼容姿态——RC 把 Anthropic 的 skill 生态当公共池子。

### Wire 是产品级协议

`src/kimi_cli/wire/types.py` 698 行，定义 17 种事件 + 4 种反向 request：
- `TurnBegin/TurnEnd`, `StepBegin/StepInterrupted`, `StatusUpdate`, `ToolCall/ToolResult`, `CompactionBegin/End`, `MCPLoadingBegin/End`, `ApprovalRequest/Response`, `QuestionRequest/Response`, `HookRequest/Response`, `SubagentEvent`, `BtwBegin/End`, `PlanDisplay`, `Notification`

**soul/ 完全不知道 UI 的存在**。所有 UI（shell/print/acp/web/vis）都是 wire event 的消费者。Claude Code 是 stdout 中心化路径，Kimi 是真协议。

---

## 三、kosong 是「故意不做 framework 的 SDK」

### Public API 只有 4 个名字

```python
__all__ = [
    "generate", "GenerateResult",
    "step", "StepResult",
]
```

整个顶层只暴露 **2 个函数 + 2 个 dataclass**。

### 12 件「故意不做」的事

1. ❌ Agent 类
2. ❌ prompt 模板系统
3. ❌ retry policy / model fallback
4. ❌ callback / tracing 框架
5. ❌ token counting / cost computation
6. ❌ streaming text aggregator
7. ❌ RAG / vector store
8. ❌ structured output (JSON mode)
9. ❌ multi-agent orchestration
10. ❌ session / history management
11. ❌ in-process tool registry
12. ❌ provider SDK re-export

### Provider 抽象用 Protocol 而非注册表

```python
@runtime_checkable
class ChatProvider(Protocol):
    name: str
    @property
    def model_name(self) -> str: ...
    async def generate(self, system_prompt, tools, history) -> StreamedMessage: ...
    def with_thinking(self, effort) -> Self: ...
```

加新 provider = 实现 5 个方法的类。**没有注册，没有 entry point，没有 plugin 系统**。

### 政治表态：目录就是政策

- `kosong/chat_provider/` → Kimi（first-party）
- `kosong/contrib/chat_provider/` → **Anthropic、Google、OpenAI 都在这里**

**Kimi 在 first-party，OpenAI 在 contrib**。一个 Moonshot 自家工程师，把 OpenAI SDK 标记为 "contrib"。

### Sync handle, async future（最优雅的设计）

```python
class Toolset(Protocol):
    def handle(self, tool_call: ToolCall) -> HandleResult: ...
    # MUST NOT do any blocking operations
```

`handle()` 是同步函数，立即返回 future。**工具开始执行的时机和 LLM 输出 tool_call 的时机一致**——LLM 后续 token 和 tool 执行**并发**。

这对长延迟 tool（shell command、wiki query）的体验是革命性的。

### Message 抽象：可注册 ContentPart union

```python
class ContentPart(BaseModel, ABC, MergeableMixin):
    __content_part_registry: ClassVar[dict[str, type["ContentPart"]]] = {}
```

子类通过 `type` 字段注册。内置：`TextPart`, `ThinkPart` (encrypted for Anthropic), `ImageURLPart`, `AudioURLPart`, `VideoURLPart`。

**ToolCall 与 ToolCallPart 分离**——完整的第一帧 + 后续 delta 帧，避开 OpenAI `index` 字段语义。

---

## 四、agent-vault 是「最巧妙的小项目」

### 4 个文件 1100 行的全部代码

```
src/cli.ts        533 行   commander CLI
src/redact.ts     334 行   redaction + restoration + 熵检测
src/vault.ts      168 行   AES-GCM + JSON 持久化
src/tty.ts         89 行   TTY 守门
```

### 3 个最巧妙的设计

#### 1. Bigram word-likeness 检测（区分品牌词 vs 随机字符串）

硬编码 110 个英文最常见 bigram (`th`, `he`, `in`, ...)，对 segment 滑窗算命中率，≥30% 算英文。

测试覆盖：
- `kimi-for-coding` → 不 flag（命中率高）
- `xqzmjvftpkwlrn` → flag（命中率低）

**用 110 个字符串检测整个英语语义，便宜到极致**。

#### 2. `split(value).join(placeholder)` 替换

```typescript
result = result.split(value).join(`<agent-vault:${key}>`);
```

**避免 regex escape 的所有问题，一行解决**。还配 longest-first 排序避免 substring 冲突。

#### 3. UNVAULTED placeholder 用 SHA256 prefix 反向匹配

agent 看到 `<agent-vault:UNVAULTED:sha256:abc12345>`，照样写回。工具 restore 时从磁盘老文件里找出原值放回——**让 agent 修改未 vault 的文件成为可能**。

### 2 道闸门检测高熵

```typescript
if (matchesSecretPattern(trimmed) ||
    (isHighEntropy(trimmed) && !looksLikeNonSecret(trimmed)))
```

- Pattern 命中（17 条 regex）→ 强制 redact
- Shannon entropy ≥3.0 + 不像英文 → redact

### 安全模型诚实

- master key 是明文 hex 文件，仅 0600 权限
- 没有 KDF / passphrase
- DESIGN.md 明确："threat model 是阻止 secrets 被发到 LLM provider，不是抵御 root"

### TTY boundary 是约定式的

```typescript
export function requireTTY(commandName: string): void {
  if (!process.stdin.isTTY) {
    process.exit(1);
  }
}
```

**只阻止"agent 通过 CLI 拿 secret"这一条路径**——这正是 LLM/MCP 场景里最实际的攻击面。

### 测试覆盖出色

- `tests/unit/redact.test.ts` 431 行
- `tests/unit/vault.test.ts` 362 行
- `tests/e2e/cli-safe.test.ts` 309 行（spawn 子进程跑真实 CLI）
- `tests/e2e/cli-sensitive.test.ts` 242 行（TTY 拦截校验）

---

## 五、kimchi 的真相：0 commits，纯占位 fork

```bash
$ gh api repos/botiverse/kimchi/compare/MoonshotAI:main...botiverse:main
"status": "behind", "ahead_by": 0, "behind_by": 91
```

**kimchi 截至 2026-04-03 是 0 个独有 commit 的占位 fork**。RC 只做了三件事：
1. fork 到 botiverse org
2. 重命名为 `kimchi`（食物命名风格）
3. 改 description 为 "agent harness with programmatic interface via stdio"

**这是个意图声明，不是改造**。所谓 "stdio interface" 就是 kimi-cli 已有的 Wire mode。

**目的推测**：给 botiverse / slock.ai 锁定一个不会被 Moonshot breaking change 的稳定 distribution。

---

## 六、tldr-vscode：LLM-friendly stub renderer 的范本

### 最聪明的设计：站在 LSP 肩膀上

```typescript
let symbols = await vscode.commands.executeCommand<DocumentSymbol[]>(
    'vscode.executeDocumentSymbolProvider', docUri
);
if (!symbols) return this.parseWithRegex(lines, structure);  // fallback
```

**不写 AST parser，复用 VSCode 已有的 Language Server**。1k 行 TS 覆盖 Python/TS/Rust 三种语言。

### 渲染算法（Python 版最纯粹）

```typescript
renderFunction(func, indent): CodeLine[] {
    // 1. Decorators (preserved)
    // 2. async def name(params) -> return_type:
    // 3. Docstring (verbatim, NOT truncated)
    // 4. Body → ellipsis
}
```

### 双向行号映射

```typescript
type CodeLine = { lineNumber: number | null, code: string }
```

`null` 表示合成行（关闭的 `}`、`...` 占位符）。**保留行号映射**，让 stub 可以双向跳回原文件。

### 保留 vs 丢弃的取舍

| 保留 | 丢弃 |
|---|---|
| 函数名 + 签名 + 类型 | 函数体 |
| 装饰器 | 局部变量 |
| **Docstring 完整保留** | inline 注释 |
| Class field 名 + 类型 | private helper |
| Module-level docstring | |

**关键设计**：docstring 完整保留，没有截断。"保留 intent，丢弃 mechanism"——这正是 LLM context 优化的核心思路。

### 对 LLM Wiki Pattern 的直接启发

把 tldr 的三层换一下输入：

```
parsers/   → wikiParser:    Markdown AST + frontmatter + section tree
renderers/ → wikiStubRenderer:
    - heading 全保留
    - 每个 H2/H3 第一句话保留作为 "claim"
    - bullet list 合并尾部（"• A; • B; ... (+5 more)"）
    - code block: 保留语言 + 头三行 + "..."
    - 表格: 保留表头 + 行数（"5 rows × 3 cols"）
    - 引用块/脚注: 全部丢弃
diffUtils  → 知识 diff: 比较两个版本，给出新增/删除的 claim
```

**得到的产出 = WikiStub**：100% 信号、0 fluff 的 LLM context payload，同时充当人类可读的 outline。

**这是 Dongzhe 的 Knowledge Agent Network 应该立刻借鉴的设计**。

---

## 七、pink：Python 版的 Ink

### 22 KB / 4 个 Python 文件

```
pink/app.py          # App 基类 + compose_static/liveview
pink/components.py   # Static + Input
pink/runtime.py      # Renderer（三种渲染模式）
pink/__init__.py
main.py              # demo
```

### Three-section 固定布局

```python
class App:
    def compose_liveview(self) -> Renderable: ...  # 当前流式内容
    def compose_static(self) -> Renderable: ...    # 已完成的快照
    def commit(self): ...                          # liveview → static
```

### 三种渲染模式

1. **Inline mode**：内容 ≤ 终端高度 → CSI 上移 + 重绘（Ink 模式，利用 scrollback）
2. **Fullscreen mode**：超出终端 → 退化全清屏
3. **Tail update**：static 稳定 → 只重绘屏幕底部 live+input

### 不是 Textual，是 Python 版的 Ink

| | Ink (TS) | Textual | pink |
|---|---|---|---|
| 渲染目标 | 主缓冲（scrollback 友好）| alternate screen | **主缓冲（抄 Ink）** |
| 代码量 | 数千行 | 几万行 | **~500 行** |
| Diff 粒度 | vdom node | widget 子树 | **行级文本** |

### 它的 USP

LLM CLI 必须：
- 用户能 scroll 回去看历史 ✓
- 能 copy 历史 ✓
- 能 pipe 输出 ✓
- 不接管整个终端 ✓

**Textual 不擅长这些，pink 填了这个坑**。

### Three-section 完美匹配 LLM CLI

- Static = 已完成的对话历史（不可变）
- Live = 当前流式 turn（spinner、token 流、tool call 状态）
- Input = 用户下一条消息

**`commit()` 语义** 正好对应"一个 turn 完成 → 冻结渲染 → 清空 input"的状态机。

### 预测：pink 将成为 Kimi CLI 下一代 UI

证据：
1. 时间点对（2025-11，正是 Kimi CLI 讨论 prompt-toolkit vs Textual 之后）
2. Python + Ink-style 这两个约束的组合**只有 LLM CLI 这个 use case**
3. Three-section 布局完美匹配 Claude Code 的视觉模型
4. RC 风格是先把 model 跑通再加 surface area

---

## 八、OneBot 的设计原则一比一映射到 Wire

### 5 个一致的设计模式

| 设计 | OneBot | Wire |
|---|---|---|
| **N×M → N+M** | N 平台 × M 框架 → N OneBot 实现 + M 应用 | N LLM × M UI → N agent kernel + M wire client |
| **能力发现** | `get_supported_actions` | `initialize.capabilities` + `slash_commands` + `supported_events` |
| **命名空间扩展** | `<prefix>.field`（`qq.anonymous`, `dc.url`） | `external_tools` + `hooks` + `client_capabilities` |
| **错误码分段** | retcode 10xxx/20xxx/30xxx + 36xxx "I Am Tired" | JSON-RPC 标准段 + -32000～-32003 业务段 |
| **双向 RPC** | event push + action request | event push + 双向 request |
| **异步关联** | `echo` 字段 | JSON-RPC `id` |
| **Self 标识** | `(platform, user_id)` 二元组 | session_id + agent_id |
| **协议无官方实现** | 多语言 LibOneBot | `kimi-agent-rs`（Rust 版 Wire server） |

### 时间维度（Wire 比 OneBot 更进一步）

- **Replay**：把每个 session 的 wire 消息持久化到 `wire.jsonl`，client 任何时候可以重建状态
- **Steer**：在 LLM 还在执行时把新消息塞进 context queue
- **Cancel**：取消进行中的 turn
- **Hooks**：client 订阅事件，agent 触发时反向问 client allow/block

**这些都是把 agent 从"一次性 RPC"升级成"持续可操控的交互流"**。

---

## 九、对 Dongzhe 的具体启示

### 1. Knowledge Agent Network 的协议层应该直接借鉴 Wire

**KAN 节点之间通信不应该 reinvent**。Wire 已经做了：
- JSON-RPC over stdio（进程边界做隔离）
- Initialize + capability negotiation
- 双向 RPC（节点 A 可以反向问节点 B）
- Event stream + cancellation
- Hooks（让节点之间可以注入策略）

**做法**：把 KAN 节点设计成 wire-compatible，立刻获得 ACP 兼容、可被 Kimi CLI 调用、可被 IDE 嵌入。

### 2. agent-vault 的占位符模型可以扩展到 KAN

当前：`<agent-vault:openai-key>`
扩展：`<kan:agent-name:claim-id>` 或 `<kan:type:agent:id>`

KAN agent A 可以引用 agent B 的事实，**不暴露原文**。

**注意**：现有正则 `<agent-vault:[a-z0-9-]+>` 只支持单段 key，要扩展到三段需改正则（但 UNVAULTED 已经用过 `:` 分段，先例存在）。

### 3. tldr-vscode 的 stub 渲染应该用到 wiki

Knowledge Agent Network 的查询响应应该返回 **WikiStub** 而不是完整页面：
- heading + 第一句 claim + bullet 头 + code block 头 3 行 + 表格元数据
- 保留 [[wiki link]] 作为知识图边
- 100% 信号，0 fluff

**这能把 KAN 的 token 成本降一个数量级**。

### 4. kosong 是 Wiki MCP Server / KAN 节点的最佳 LLM 层

理由：
- ✅ 窄抽象，不绑死
- ✅ Sync handle async future 模式天然支持并发 agent 调用
- ✅ 长生命周期 daemon 友好（每个 request new SimpleToolset，无全局状态）
- ✅ MCP 桥接已有（`convert_mcp_content`）
- ✅ Provider 是 Protocol，无需注册
- ⚠️ 没有 multi-agent orchestration，必须自己写
- ⚠️ 没有 history 管理，必须自己加（但有 `LinearContext` contrib 模块）

**用 kosong 重写 wiki-mcp-server 的下一版会比 MCP SDK 更灵活**。

### 5. AGENTS.md 32 KiB leaf-first 算法值得抄

KAN 节点之间的 metadata 也应该有这种合并机制：
- 全局 metadata（org-level）
- 中级 metadata（topic-level）
- 节点 metadata（per-agent）

合并时 **leaf-first**，保证最具体的不被挤掉。

### 6. Dynamic injection 是 LLM Wiki pattern 的缺失片

AK Wiki 现在有 ingest/query/lint 三个操作，但**没有 reminder 机制**。

借鉴 kimi-cli 的 plan-mode reminder：
- 每 N 个查询后注入"你的 wiki 还有这些孤儿页面"
- 每 M 个 ingest 后注入"这些 concept 缺少最新论文"
- Full vs sparse 节奏避免疲劳

### 7. Wire external tool 模式 = KAN 节点反向工具注入

**最有意思的连接**：

slock.ai 通过 wire 让 IDE 反向给 agent 注入工具。
KAN 也可以让节点 A 通过类似机制 **反向给节点 B 注入查询能力**。

这比 MCP 的"agent 调用 server"模型更对称——KAN 应该是**对等网络**，每个节点既是 server 又是 client。

---

## 十、合作角度：怎么接近 RC 不是空对空

之前的研究说"带一份 RFC 草案"——现在有了源码理解，**这个 RFC 可以非常具体**：

### 草案标题
"Wire Protocol Extension: Knowledge Node Discovery and Cross-Node Query"

### 内容大纲
1. **Motivation**: Wire 当前是 host ↔ agent 单向通信。如果 agent 是知识节点，需要 agent ↔ agent 的对等通信。
2. **New initialize capability**: `node_role: "knowledge_node"` + `knowledge_topics: [...]`
3. **New event types**:
   - `NodeQuery` (agent → wire → other agent)
   - `NodeQueryResult` (other agent → wire → agent)
4. **占位符引用**: 借鉴 agent-vault 的 placeholder，定义 `<kan:node:claim-id>` 让节点引用而不传输
5. **WikiStub 作为标准 response 格式**: 借鉴 tldr-vscode 的 stub 渲染
6. **Reference implementation**: 基于 wiki-mcp-server 改造

### 为什么这个 RFC 会被 RC 看重
- 用他自己的协议（Wire）
- 解决他没碰过的问题（cross-node knowledge sharing）
- 借鉴他自己的设计模式（占位符、能力协商、stub 渲染）
- 直接 fit 他的 botiverse 哲学（agents and humans collaborate as equals）

---

## 关键文件路径速查

### kimi-cli
- `src/kimi_cli/soul/kimisoul.py` (1307 行) — **主循环，最重要**
- `src/kimi_cli/soul/agent.py` (529 行) — Runtime + Agent + AGENTS.md 合并
- `src/kimi_cli/soul/toolset.py` (661 行) — KimiToolset + WireExternalTool
- `src/kimi_cli/soul/context.py` (339 行) — JSONL + checkpoint
- `src/kimi_cli/soul/compaction.py` (189 行)
- `src/kimi_cli/soul/denwarenji.py` — D-Mail 时光机
- `src/kimi_cli/wire/types.py` (698 行) — 17 种事件
- `src/kimi_cli/skill/__init__.py` (382 行) — 跨品牌 skill 兼容

### kosong
- `src/kosong/__init__.py` — 顶层 step() / generate()
- `src/kosong/_generate.py` — 流式合并循环
- `src/kosong/message.py` — 可注册 ContentPart
- `src/kosong/chat_provider/__init__.py` — Protocol
- `src/kosong/tooling/__init__.py` — Tool / Toolset / CallableTool2
- `src/kosong/contrib/chat_provider/anthropic.py` — provider 范本

### agent-vault
- `src/cli.ts` (533 行)
- `src/redact.ts` (334 行) — bigram 词检测在这里
- `src/vault.ts` (168 行) — AES-GCM
- `tests/unit/redact.test.ts` (431 行) — 测试覆盖范本

### tldr-vscode
- `src/parsers/typescriptParser.ts` — LSP-based 解析
- `src/renderers/pythonRenderer.ts` — 最纯净的渲染算法

### pink
- `pink/app.py` — Three-section App
- `pink/runtime.py` — 三种渲染模式
