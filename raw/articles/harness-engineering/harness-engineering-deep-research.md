# Harness Engineering 深度研究

> Sources: Anthropic, LangChain, Thoughtworks/Martin Fowler, HumanLayer, Inngest
> Ingested: 2026-04-03

## 一、What is Harness Engineering

### 核心定义

**Harness = Agent 中除 Model 之外的一切。**

LangChain 给出最简洁的公式:
```
Agent = Model + Harness
```

Harness 包括: system prompt、tools、filesystem、sandbox、memory、context management、orchestration logic、verification mechanisms。Model 提供智能，Harness 让智能变得可靠。

**Birgitta Bockeler (Thoughtworks)** 将 harness 分为两类控制:
- **Feedforward (前馈/Guides)**: 在 agent 行动前引导行为，提高首次成功率。包括 CLAUDE.md、typed schemas、architectural constraints。
- **Feedback (反馈/Sensors)**: 在 agent 行动后观测输出并触发修正。包括 tests、linters、type checkers、LLM-as-judge。

她还区分了两种执行模式:
- **Computational controls (确定性)**: tests、linters、type checkers — 毫秒级、可靠
- **Inferential controls (推理性)**: code review agents、LLM-as-judge — 更慢、更贵、不确定

### 与 Framework 的关系

Inngest 明确提出 **"Your agent needs a harness, not a framework"**。Framework 封装了 LLM 调用逻辑，但 harness 关注的是基础设施层 — retry logic、state persistence、job queues、event routing。Agent 的可靠性是基础设施问题，不是 AI 问题。

Anthropic "Building Effective Agents" 也警告: framework 创造额外抽象层，obscure underlying prompts and responses，让调试更难。建议直接使用 LLM API。

### Harnessability (可驾驭性)

Bockeler 提出了 **harnessability** 概念: 不同代码库对 harness 的亲和度不同。强类型语言、明确的 module boundaries、成熟的 framework 天然提供更好的控制面。这些被称为 **"ambient affordances"**。

---

## 二、Context Engineering Patterns

### Context 是最稀缺资源

Anthropic 核心原则: **"Find the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome."**

Claude Code Best Practices: **"Most best practices are based on one constraint: Claude's context window fills up fast, and performance degrades as it fills."**

### Context Budget 管理

**Anthropic 三要素策略:**
1. **System Prompts**: 在"正确的高度"平衡 specificity 与 flexibility
2. **Tools**: 设计 minimal、non-overlapping 工具集，返回 token-efficient 信息
3. **Examples**: 策展 diverse、canonical examples，而非 exhaustive edge-case lists

**HumanLayer 的 Progressive Disclosure:**
- 只在需要时提供信息/工具
- 每条无关指令都消耗 context window
- **Silence on success; errors only on failure**

### KV-Cache 与 Context 效率

Context rot 研究证实 recall accuracy 随 context window 扩大而下降。Chroma 研究: **低 semantic similarity 的 context 会导致更陡峭的性能退化。**

### Runtime Context Retrieval (Just-in-Time)

不要预加载所有数据。使用 **lightweight identifiers** (file paths, URLs, queries) 进行即时检索。Claude Code: agent 写 targeted queries，用 grep 和 head 分析大数据集。

### Compaction (压缩) 策略

- `/clear` — 不相关任务之间完全重置 context
- `/compact <instructions>` — 带指令的压缩
- CLAUDE.md **完整保留** compaction — 压缩后从磁盘重新读取
- Sonnet 4.5 即使使用 compaction 仍有 **"context anxiety"** (过早收尾)，所以 context reset 是必要的

### Filesystem Memory (文件系统记忆)

多篇文章一致认为 filesystem 是跨 session 的核心记忆层:

**Anthropic 的具体实现:**
- `claude-progress.txt` — 记录 agent 做了什么
- Feature list (JSON 格式) — 200+ features 列表，初始 "failing"
- Git history — 变更追踪和回退
- `init.sh` — 启动开发服务器

**为什么用 JSON 不用 Markdown**: Model 更不容易 inappropriately change or overwrite JSON files。

### Sub-Agent 作为 Context Firewall

**HumanLayer 关键洞察**: sub-agent 是 **"context firewalls"**，隔离离散任务，防止 intermediate noise 在 parent thread 中积累。

Anthropic: specialized agents 处理 focused tasks 并返回 **condensed summaries (1,000-2,000 tokens)** 给 lead agent。

---

## 三、Constraints & Safe Autonomy

### Sandboxing

**Claude Code 三层权限:**
- **Auto mode** — classifier model 审查命令
- **Permission allowlists** — 允许已知安全的特定工具
- **OS-level sandboxing** — 限制 filesystem 和 network access

### Tool Design (ACI 原则)

**Anthropic "Building Effective Agents" — ACI (Agent-Computer Interface):**
- 给 model 足够 tokens 去 "think"
- 保持格式与 model 在 internet text 中遇到的 pattern 一致
- 问: "tool usage 是否仅从 descriptions 和 parameters 就 self-evident?"
- 包括 example usage、edge cases、input format requirements

**实际案例**: SWE-bench agent 在 directory changes 后 struggle with relative filepaths。解决: **exclusively require absolute filepaths** — 简单改变消除整个 error class。

**HumanLayer 工具管理:**
- 优先使用 CLI tools (在 training data 中有出现) 而非 MCP servers
- 多个工具创造 **"the dumb zone"** — 膨胀 context window
- 使用 experimental MCP tool search 实现 progressive disclosure

### Hooks (确定性控制)

与 CLAUDE.md instructions (advisory) 不同，hooks 是 **deterministic** 的，guarantee action happens。
- Success should be silent; only failures produce output

### Guardrails 层级 (Bockeler)

1. **Maintainability harness** — 内部代码质量
2. **Architecture fitness harness** — fitness functions 定义性能/可观测/架构约束
3. **Behavior harness** — 功能规范通过 skills 和 test validation（目前最弱）

---

## 四、Evaluation & Observability

### Self-Verification

**Claude Code Best Practices 第一条**: **"Include tests, screenshots, or expected outputs so Claude can check itself. This is the single highest-leverage thing you can do."**

### GAN-inspired Evaluation

**Anthropic "Harness Design"** 核心创新 — 将 generation 与 evaluation 分离:
- **Generator** — 生成实际输出
- **Evaluator** — 用具体标准提供批判性外部反馈
- 分离原因: model grade 自己的工作时倾向于 **"confident praising"**

**Frontend Design 四维评估:**
1. Design Quality (色彩、typography、layout)
2. Originality (避免 template defaults 和 "AI slop")
3. Craft (hierarchy, spacing, contrast)
4. Functionality (独立于美学的可用性)

### Sprint Contract Pattern

Generator 和 evaluator 在每个 sprint 前 negotiate:
- What "done" looks like
- How success will be verified
- Specific testable behaviors

### Load-Bearing Assumptions

**关键原则**: "Every component in a harness encodes an assumption about what the model can't do on its own." 这些假设随 model 进步而 stale。

实测: Sprint decomposition 在 Claude 4.5 时必要，在 4.6 时已不再必要。

---

## 五、Runtime Architecture

### Event-Driven Durability (Inngest)

每个 LLM 和 tool call 成为 discrete, independently retryable step。第 5 次迭代失败时，前 4 次保持 persisted。

### Initializer + Coding Agent 架构

**Anthropic "Effective Harnesses":**

**Initializer Agent** (第一个 session):
1. 创建 `init.sh` 脚本
2. 创建 `claude-progress.txt`
3. 创建 initial git commit
4. 生成 comprehensive feature list (JSON)

**Coding Agent** (后续 sessions):
1. `pwd` 确认工作目录
2. Read git logs + progress files
3. 选择最高优先级未完成 feature
4. Incremental development (一次一个 feature)
5. Commit + update progress

### Multi-Agent Architecture

**Anthropic "Harness Design" 三 Agent 系统:**

| Agent | 职责 |
|-------|------|
| **Planner** | 将 1-4 句 prompt 转为 comprehensive product spec |
| **Generator** | Sprint-by-sprint 执行 spec |
| **Evaluator** | 像 end user 一样测试 running app (Playwright MCP) |

### Cost-Performance 实测

| 模式 | 时间 | 成本 | 结果 |
|------|------|------|------|
| Solo agent | 20 min | $9 | 界面 ok 但核心 gameplay broken |
| Full harness | 6 hours | $200 | 16 features，实际 playable |

---

## 六、Practical Patterns

### CLAUDE.md 最佳实践

**该写什么:** Claude 猜不到的 commands、非默认的 code style rules、测试指令、架构决策
**不该写什么:** Claude 能从代码推断的、标准 conventions、详细 API docs

**关键数据:**
- 目标 **under 200 lines** per CLAUDE.md
- CLAUDE.md 过长时 Claude **ignores half of it**
- ETH Zurich 研究: LLM 自动生成的 CLAUDE.md **损害性能 20%+**

### CLAUDE.md 层级

| Scope | Location | 共享 |
|-------|----------|------|
| Managed policy | `/Library/Application Support/ClaudeCode/CLAUDE.md` | 组织全员 |
| Project | `./CLAUDE.md` | 团队 (via git) |
| User | `~/.claude/CLAUDE.md` | 仅自己 (所有项目) |
| Local | `./CLAUDE.local.md` (.gitignore) | 仅自己 (当前项目) |

### Session 管理反模式

| 反模式 | 修复 |
|--------|------|
| Kitchen Sink Session | `/clear` between tasks |
| Correcting Over and Over | 两次修正后 `/clear` + better prompt |
| Over-specified CLAUDE.md | Ruthlessly prune to <200 lines |
| Trust-then-Verify Gap | Always provide verification |
| Infinite Exploration | Scope narrowly or use sub-agents |

---

## 跨文章共识 (7 条)

1. **简单优先** — start simple, add complexity only when it demonstrably improves outcomes
2. **Context 是第一约束** — context window 的有效利用决定 agent 成败
3. **Verification > Generation** — 给 agent 自验证能力是 single highest-leverage intervention
4. **文件系统是记忆层** — progress files, feature lists, CLAUDE.md, git history 构成 persistent state
5. **分离关注点** — generation vs evaluation, planning vs execution 应在独立 context window
6. **Harness assumptions expire** — 每个 component 编码了对 model 能力的假设，需定期重检
7. **Incremental > One-shot** — 一次做一个 feature，留下 clean state
