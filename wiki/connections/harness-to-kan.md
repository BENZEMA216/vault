# Agent Harness → Knowledge Agent Network 的设计借鉴

> 把 RC 双线工程（kimi-cli + kosong + agent-vault + tldr-vscode + Wire）和 Claude Code 中已经成熟的设计模式，逐一映射到 [[concepts/knowledge-agent-network|KAN]] 的协议、节点、查询、安全四层。

## 为什么这条 connection 值得做

KAN 不需要从零造协议、并发模型、信任边界——这些都已经被 [[maps/agent-harness-implementations|production agent harness]] 啃过最难的部分。直接借鉴可以把 KAN MVP 的工程量从 6 个月压到 4-6 周。

下面 8 条都是**具体可执行的迁移建议**，每条对应一份 RC/Anthropic 已有的代码作为参考实现。

---

## 1. Wire Protocol → KAN 节点通信

**RC 实现**：`src/kimi_cli/wire/types.py` 698 行，JSON-RPC over stdio，17 种事件 + 4 种反向 request。`initialize` 阶段做 capability negotiation，agent 反向 RPC（agent 反向问 client）已实现。

**KAN 借鉴**：把 KAN 节点设计成 **wire-compatible**——立刻获得 (1) ACP 兼容；(2) 可被 Kimi CLI / Claude Code 调用；(3) 可被 IDE 嵌入。不需要 reinvent JSON-RPC、cancellation、replay 这些都已经做完。

**具体实现**：
- 节点初始化时 `initialize.capabilities` 增加 `node_role: "knowledge_node"` + `knowledge_topics: [...]`
- 节点之间通信走 wire 的 `request` / `event` 双向通道
- 节点查询 = `NodeQuery` event，结果 = `NodeQueryResult` event

## 2. agent-vault placeholder → KAN cross-node references

**RC 实现**：`<agent-vault:openai-key>` placeholder。agent 看到的是 placeholder，磁盘上是真值。`split(value).join(placeholder)` 一行避免 regex escape。UNVAULTED 用 SHA256 prefix 反向匹配。

**KAN 借鉴**：扩展占位符语法到三段 — `<kan:node:claim-id>` 或 `<kan:type:agent:id>`。让节点 A 引用节点 B 的事实，**不暴露原文**——既减少 token，也保留出处可追溯。

**注意**：现有 agent-vault 正则 `<agent-vault:[a-z0-9-]+>` 只支持单段 key，扩展到三段需改正则；但 UNVAULTED 已经用过 `:` 分段，先例存在。

## 3. tldr-vscode stub renderer → WikiStub 格式

**RC 实现**：`tldr-vscode` 1k 行 TS，站在 LSP 肩膀上把代码渲染成 LLM-friendly stub——保留函数签名 + 装饰器 + **完整 docstring**，丢掉函数体和局部变量。`lineNumber: number | null` 双向行号映射。

**KAN 借鉴**：把 KAN 的查询响应定义为 **WikiStub 格式**而不是完整页面：
- heading 全保留 + 每个 H2/H3 第一句话作为 "claim"
- bullet list 合并尾部（"• A; • B; ... (+5 more)"）
- code block: 保留语言 + 头三行 + "..."
- 表格: 保留表头 + 行数（"5 rows × 3 cols"）
- 引用块/脚注: 全部丢弃
- 保留 `[[wiki-link]]` 作为知识图边

**收益**：把 KAN 的 token 成本降一个数量级。

## 4. Per-tool isConcurrencySafe → Per-node safety predicate

**Claude Code 实现**：每个 tool 自带 `isConcurrencySafe(input)` 谓词，调度规则"多 read 并发 + write break 队列变栅栏 + 后续 read 再并发"。详见 [[concepts/agent-tool-concurrency]]。

**KAN 借鉴**：跨节点查询用 `isConcurrencySafe(node, query)` 谓词决定能不能并发：
- 多个 **只读查询**（query / search / read_page）→ 并发
- 任何 **写操作**（ingest / update / lint）→ 栅栏
- 同一节点并发数受 `KAN_MAX_CONCURRENT_QUERIES` 限制
- 用 Claude Code 的 `_N8` Promise.race-based generator merge 模式

**为什么必须做对**：KAN 的查询大多数是慢操作（跨节点 wiki query），如果用 LangChain 那种串行模型，体验会非常糟。

## 5. Kimi CLI AGENTS.md leaf-first 32KB → KAN metadata merge

**RC 实现**：`load_agents_md()` 候选优先级 `.kimi/AGENTS.md` > `AGENTS.md` > `agents.md`，**32 KiB 总预算 leaf-first 分配**——深目录永远不会被浅目录挤掉。每段加 `<!-- From: <path> -->` 注解。

**KAN 借鉴**：节点之间的 metadata 也用同款合并机制：
- 全局 metadata（org-level）
- 中级 metadata（topic-level）
- 节点 metadata（per-agent）
- 合并时 **leaf-first**，保证最具体的不被挤掉
- 每段加来源注解便于 debug

## 6. Dynamic injection → KAN periodic reminders

**RC 实现**：kimi-cli `_step()` 在每 turn 投递 dynamic injections（plan_mode/yolo_mode reminders），按 full vs sparse 节奏避免疲劳，注入 `<system-reminder>`。

**KAN 借鉴**：[[concepts/llm-wiki-pattern|LLM Wiki Pattern]] 现在有 ingest/query/lint 三个操作，但**没有 reminder 机制**。借鉴 dynamic injection：
- 每 N 个查询后注入"你的 wiki 还有这些孤儿页面"
- 每 M 个 ingest 后注入"这些 concept 缺少最新论文"
- Full vs sparse 节奏避免疲劳
- 以 `<system-reminder>` 形式注入到下一轮 query 的 system prompt

## 7. kosong sync-handle/async-future → KAN query 并发原语

**RC 实现**：`Toolset.handle()` 是同步函数，立即返回 future，注释强制 `# MUST NOT do any blocking operations`。详见 [[concepts/agent-tool-concurrency]]。

**KAN 借鉴**：KAN 的节点查询接口直接抄这个 Protocol：
```python
class KANNode(Protocol):
    def handle_query(self, query: NodeQuery) -> QueryFuture: ...
    # MUST NOT do any blocking operations
```
- streaming 路径里 `addQuery()` 立即 dispatch
- 多个 `Future` 通过 `asyncio.gather` 后台并行 resolve
- LLM 还在生成下一步推理时，前一个 query 已经在跑

**额外好处**：用 kosong 重写 wiki-mcp-server 的下一版会比 MCP SDK 更灵活——窄抽象、长生命周期 daemon 友好、MCP 桥接已有（`convert_mcp_content`）、Provider 是 Protocol 无需注册。

## 8. Hooks system → KAN event-based extensibility

**Claude Code 实现**：`PreToolUse / PostToolUse / UserPromptSubmit / Stop / SessionStart / Notification / SubagentStop`，spawn 用户 shell，stdin 喂 tool input，stdout 解析 `behavior: "allow"|"deny"|"ask"|"passthrough"`。

**KAN 借鉴**：KAN 节点暴露 hooks 接口，让运营者注入策略：
- `PreNodeQuery` — 鉴权、限流、审计
- `PostNodeQuery` — 计费、缓存、统计
- `OnIngest` — webhook、CI、Slack 通知
- `OnLint` — 自动 PR、错误上报
- `BeforeFederate` — 节点之间的 federation 准入

每个 hook 用 `behavior: "allow"|"deny"|"transform"|"passthrough"` 决定下一步——这是 KAN 走向 enterprise 部署必须的层。

---

## 接近 RC 的具体动作

带一份基于他自己生态的 **RFC 草案**：

> **"Wire Protocol Extension: Knowledge Node Discovery and Cross-Node Query"**
>
> 1. **Motivation**: Wire 当前是 host ↔ agent 单向通信。如果 agent 是知识节点，需要 agent ↔ agent 的对等通信
> 2. **New initialize capability**: `node_role: "knowledge_node"` + `knowledge_topics: [...]`
> 3. **New event types**: `NodeQuery` / `NodeQueryResult`
> 4. **占位符引用**: 借鉴 agent-vault 定义 `<kan:node:claim-id>` 让节点引用而不传输
> 5. **WikiStub 作为标准 response 格式**: 借鉴 tldr-vscode
> 6. **Reference implementation**: 基于 wiki-mcp-server 改造

**为什么这个 RFC 会被 RC 看重**：用他自己的协议（Wire），解决他没碰过的问题（cross-node knowledge sharing），借鉴他自己的设计模式（占位符、能力协商、stub 渲染），直接 fit 他的 botiverse 哲学（agents and humans collaborate as equals）。

## 关联

- [[concepts/knowledge-agent-network]] — 这条 connection 是 KAN 工程实现的设计指南
- [[concepts/agent-tool-concurrency]] — #4、#7 的详细原理
- [[concepts/llm-wiki-pattern]] — #6 dynamic injection 直接补 LLM Wiki 缺的 reminder 操作
- [[concepts/agent-native-im]] — slock.ai 是 RC 这套 harness 的产品 surface
- [[maps/agent-harness-implementations]] — 完整的 harness 横向对照
- [[connections/llm-wiki-to-agent-network]] — KAN 演化路径（这条 connection 是其工程支撑）

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
