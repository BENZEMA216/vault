# Portable Identity Layer for AI — 竞品深度研究

> 来源: 知识库 Query 讨论 (2026-04-08)
> 触发: 在确定 "Identity Layer + Knowledge Layer 组合" framing 之前先 sanity check 竞品
> 结论: **比 Personal Knowledge Card 那一轮更糟。这个赛道在 H2 2025 已经爆发**。

---

## TL;DR

之前以为 "Personal Knowledge Card" 红海。
但 "Portable Identity for AI" **更红**。

- 至少 **8 个直接竞品**在过去半年集中冒出
- 其中 **2 个**（**Epitome**、**limitless-ai.dev**）在 6 维评分上达到 5-5.5/6，**几乎和我们想 build 的一模一样**
- **Anthropic 自己**做了 **Claude Import Memory**（2026-03 上线），从厂商一侧夹击
- **mem0 OpenMemory MCP**（2025-05）已经从协议层占位
- **国产生态**几乎空白（豆包/Kimi/通义/智谱都做了厂商内 memory，没人做 portable）

**这不再是 "找 wedge" 的问题，是 "fast follower 还是 different angle" 的问题。**

---

## 评分维度

| # | 维度 | 含义 |
|---|------|------|
| 1 | 结构化 identity | 不只是 memory log，是 typed sections（about / projects / preferences） |
| 2 | MCP exposed | 开放协议，不绑定单一 vendor |
| 3 | 跨工具 | Claude + Cursor + Cline + Codex + ChatGPT 都能看到同一个 identity |
| 4 | User curated | 用户主控，不是从对话自动 extract |
| 5 | + Knowledge layer | identity + research/notes/concepts 在同一 MCP |
| 6 | 自部署 | 不只是 SaaS，能 self-host |

6/6 = 直接竞品。0-2/6 = 邻接，不算竞争。

---

## Top 10 竞品矩阵

| # | 产品 | 1.结构化 | 2.MCP | 3.跨工具 | 4.User curated | 5.+Knowledge | 6.自部署 | **总分** |
|---|------|---|---|---|---|---|---|---|
| 1 | **Epitome** [epitome.fyi] | ✅ | ✅ | ✅ | ✅ | 部分 | ✅ MIT/Docker | **5.5/6** |
| 2 | **limitless-ai.dev** | ✅ about/projects/preferences/people/decisions（与 BENZEMA 5 维**逐字对应**） | ✅ | ✅ + system prompt snippet | ✅ confirm flag | ❌ | ✅ Cloudflare fork | **5/6** |
| 3 | **Base Layer** [base-layer.ai] | ✅ axioms/context modes | ✅ | ✅ Claude/GPT/Cursor/Windsurf/Cline/Aider | 半自动 | ❌ | ✅ Apache 2.0 | **4.5/6** |
| 4 | **OpenMemory (mem0)** | 半结构 | ✅ | ✅ Cursor/Claude/Windsurf/Cline | 可手动 | ❌ | ✅ Docker | **4/6** |
| 5 | **Letta Memory MCP** | ✅ 双层 (user memory + vector) | ✅ | ✅ MCP 原生 | 半自动 | ❌ | ✅ OSS | **4/6** |
| 6 | **MemPalace** [mempalace.tech] | 半结构 (19 MCP tools) | ✅ | ✅ Claude/ChatGPT | 可手动 | ❌ | ✅ 本地优先 | **4/6** |
| 7 | **Identiti** | ✅ Memory Card (skills/goals/values/traits) | ❌ 仅 copy-paste | ✅ "any AI" | ✅ 编辑式 | ❌ | ❌ SaaS | **3/6** |
| 8 | **Plurality Network** | 半结构 (preferences/style/knowledge) | 不明 | ✅ ChatGPT/Claude/Gemini | ✅ | ❌ | ❌ SaaS | **3/6** |
| 9 | **Heirloom** [yourheirloom.ai] | 未知 (presale) | 未知 | 宣称跨工具 | 未知 | ❌ | 未知 | **2/6** |
| 10 | **Claude Import Memory** (Anthropic 官方) | ❌ 非结构 | ❌ Claude 内 | 单向 (只 import 进 Claude) | ❌ | ❌ | ❌ | **0.5/6**（**威胁极大**） |

### 关键观察

- **Epitome 5.5/6**：MIT + Docker + PostgreSQL + 9 个 MCP tools + USDC pay-per-call。**几乎是我们想做的**。
- **limitless-ai.dev 5/6**：5 个 identity 维度 (about/projects/preferences/people/decisions) **跟 Dongzhe 列出的一模一样**。
- **Anthropic Import Memory** 0.5/6 但威胁巨大：它在告诉用户 "**不需要第三方，我们厂商之间互相导入就行**"。OpenAI / Google 一旦跟进，第三方层的存在意义会被严重削弱。

---

## 邻接威胁

### 1. Skills/Prompt Managers — 最大的横向威胁

| 产品 | 威胁 |
|------|------|
| Promptzy ($5 一次性) | 同步 prompt template 跨 ChatGPT/Claude/Cursor |
| Promptlight | 同上 |
| sidambili/prompt-manager | OSS |

**这些产品只要加一个 "identity skill" preset 就能 80% 覆盖**。它们已经有 distribution + 用户基础 + Mac native UX。

### 2. 文件标准（机会但没人做产品化）

| 标准 | 状态 |
|------|------|
| **context.json** (davidkimai, 2025-06, MIT) | 完整 spec，没 hosting / SaaS |
| **AGENTS.md** (Linux Foundation) | issue #91 在讨论 `~/.config/agents/AGENTS.md` 全局配置 |
| **CLAUDE.md** | Anthropic 鼓励，无 sync |
| **humans.txt / human.json** | 老标准 |
| **llms.txt** | 网站级，不是个人级 |

**机会**: 把 context.json 或 AGENTS.md 全局版做成 hosted MCP 实现。这是 Issue #91 的 SaaS 化答案。

### 3. Agent Memory Infra（auto-extract 路线）

mem0 / Letta / MemPalace / Zep — 核心是"对话日志的智能压缩"，**不是用户主动 curated 的 identity**。Auto-extract 路线和 user-curated 路线是两种哲学。

### 4. Identity-as-a-Service（语义陷阱）

**重要**: `mcp-agent-identity` (Smithery), `PersonaNexus` 这些都是 **agent identity**（"这个 agent 是谁"），**不是 user identity**（"我是谁"）。这是常见误解，positioning 时需要明确区分。

---

## 还空着的精确 wedge

经过 brutal 检查，**还有一个角落没人占满**：

> **"Knowledge layer + Identity layer 在同一个 self-hosted MCP 里 + 用户完全 curated + 中文生态原生"**

具体差异化：

1. **Identity + Knowledge 联合 MCP**（维度 5）—— Epitome / limitless 都没做 knowledge layer
2. **中文场景 + 国产模型客户端**（豆包/Kimi 桌面/Cherry Studio/Chatbox）—— 完全空白
3. **"Decisions in flight"作为 first-class type**—— 只有 limitless-ai.dev 列了，但实现很轻
4. **与 AGENTS.md / CLAUDE.md 全局自动 sync**（生成 + 维护，不是用户复制粘贴）—— 没人做
5. **集成 agent economy 主线**——把 identity / knowledge 作为 agent 经济的"个人 IP 入口"

---

## 失败/已死的产品（证明难度）

| 产品 | 状态 | 教训 |
|------|------|------|
| **Inflection Pi** | 2024-03 团队被 Microsoft 收编 | 闭环个人 AI 没有网络效应 |
| **Rewind.ai** | 2024 pivot Limitless → 2025-12 被 Meta 收购 | 把 memory 锁在硬件 = 死路；用户要 portability |
| **Personal.ai** | 5 年仍 niche | "我有自己的 AI" 不是产品形态 |
| **Friend.com pendant** | 发货延期，未成主流 | 硬件路线 2025 已死 |
| **Heirloom** | 仍是 presale 页面 | 多数 portable memory 项目卡在 demo 阶段 |

---

## 真正的认知翻转

之前我建议 build "Identity Layer 加到 Wiki"。
**现在数据告诉我：这正好是市场上最拥挤的方向**。

**Epitome 已经做到 5.5/6**。如果我帮 Dongzhe 在 wiki-mcp-server 上加 identity 层，**最好的结果就是 6/6 平 Epitome**。但 Dongzhe 没有 Epitome 的 head start, 没有先发优势, 没有他们已经积累的 USDC 商业模式。

**Fast follower 而非 first mover 是可行的，但条件极苛刻**：
- 6 个月内做到 6/6
- 找到一个 Epitome 没碰的角度
- 接受 Anthropic Import Memory 在 12 个月内会进一步压缩第三方层的价值

---

## 三个可选的 path

### Path A: Fast Follower (做 6/6 + 中文 wedge)

定位: "**中文生态的 portable identity + knowledge MCP**"

具体差异:
- 国产模型客户端原生支持（豆包 / Kimi / Cherry Studio / Chatbox）
- 中文 onboarding
- AGENTS.md / CLAUDE.md 双向 sync
- Knowledge layer 集成（Dongzhe 已有的 wiki）

风险: 跟 Epitome 正面竞争。Epitome 团队加个中文版就能反扑。

### Path B: Pivot 到 Agent Economy 入口

定位: "**Knowledge/Identity Card 是 Agent Economy 的入口资产**"

把 identity 不当成产品本身，而是当成**用户进入"AI 知识经济"的第一份资产**:
- v0.1: identity + knowledge MCP（不卖钱）
- v0.2: 加 marketplace（按 query 计费）
- v0.3: agent 经济结算层

跟 Dongzhe memory 里的 `project_agent_economy.md` 完美对齐。

风险: 产品故事抽象了，难解释。

### Path C: 不做产品，做 Tooling

定位: "**给已有 wiki/note 用户一个 30 秒部署 MCP 的 SaaS**"

不跟 Epitome 竞争 identity layer，而是变成 "**Obsidian → MCP**" / "**Notion → MCP**" / "**公众号 → MCP**" 的桥。

第一个用户群: Obsidian 重度用户、Notion 重度用户、Substack 作者。

风险: 是工具，不是产品；客单价低；容易被 Obsidian 自己加的 native MCP support 覆盖。

---

## 给 Dongzhe 的诚实建议

### 1. 必须放弃 "我先发现这个空白" 的叙事

任何 5 分钟做尽调的人都会找到 Epitome / limitless-ai.dev。继续这么说会丢信任。

### 2. 必须重新选择定位

三个 path 各有取舍。我个人认为：

- **Path A** 最现实，但需要 fast execution
- **Path B** 跟 Dongzhe 长期主线对齐度最高，但故事抽象
- **Path C** 最容易冷启动，但天花板最低

### 3. 必须先用现有产品

在 build 之前，**把 Epitome 跑一遍**。看它实际是不是真的 5.5/6。如果它确实做对了，可能直接用、贡献代码、或 fork 它，比从零 build 快 10 倍。

### 4. 必须直面 Anthropic Import Memory

Anthropic 已经动手了。OpenAI / Google 一定会跟进。**12 个月后，第三方层只剩"中立托管 + 用户控制"这一条护城河**。

在 build 之前问自己：当 ChatGPT / Claude / Gemini 互相导入 memory 之后，我的产品还有什么不可替代的价值？

如果答案是 "**我让用户主控、不让任何厂商占有数据**"，那 path A 成立。
如果答案是 "**我跟 Knowledge Layer / Agent Economy 联动**"，那 path B 成立。
如果答案是 "**我帮已有内容用户接入 AI 工具**"，那 path C 成立。

如果三个答案都没有，那这个产品**不该 build**。

---

## 关键参考链接

### 直接竞品
- Epitome — https://epitome.fyi（**最强**）
- limitless-ai.dev — https://limitless-ai.dev（**维度命中度最高**）
- Base Layer — https://www.base-layer.ai
- Identiti — https://identiti.nandanadileep.com
- Plurality Network — https://plurality.network
- Heirloom — https://yourheirloom.ai
- mem0 OpenMemory MCP — https://mem0.ai/blog/introducing-openmemory-mcp
- Letta — https://letta.com
- MemPalace — https://www.mempalace.tech

### 标准/协议
- context.json — https://github.com/davidkimai/context.json
- AGENTS.md global config — https://github.com/agentsmd/agents.md/issues/91
- humans.txt — https://humanstxt.org
- human.json — https://codeberg.org/robida/human.json

### 威胁/邻接
- **Claude Import Memory** — https://claude.com/import-memory（Anthropic 官方动作）
- Promptzy — https://promptzy.app
- mcp-agent-identity — https://smithery.ai/server/@faalantir/mcp-agent-identity

---

## Bottom Line

> **这不是空白市场，是 H2-2025 已经被发现、目前有 2-3 个 5/6 分水平竞品的市场。**
>
> 之前所有定位（Personal Knowledge Card / Cross-Agent Memory / Portable Self / Identity Layer）都已经被 占住或正在被占住。
>
> **唯一仍可能成立的角度**:
> 1. 中文生态 + 国产模型客户端（Path A）
> 2. Identity + Knowledge + Agent Economy 三合一（Path B）
> 3. 不做产品做工具（Path C）
>
> 在 build 之前**必须先跑 Epitome 试试**。
> 在动笔写代码之前**必须想清楚 Anthropic Import Memory 的威胁**。
