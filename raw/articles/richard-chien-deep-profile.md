# Richard Chien (stdrc) 完整人物档案

> 来源: 知识库 Query 讨论 (2026-04-07)
> 这是对 richard-chien-stack.md 的扩展和深化，覆盖工程哲学、Twitter、GitHub 完整生态、网络关系
> 抓取限制：X 主页和单条 tweet 详情页被反爬阻断，timeline 不完整

---

## 一句话画像

**RC 是一个把"为 bot/agent 建生态"做了 10 年的 builder。**

每一个项目都是"先做协议、再做框架、最后才是产品"。从 2016 的 CQHTTP → NoneBot → OneBot 协议（中文 chatbot 一代标准），到 IPADS OS kernel，到 RisingWave streaming database，到 Moonshot Kimi CLI 主作者，到 botiverse 个人 agent stack。

GitHub tagline：**"I build ecosystems, not just software."** —— 不是吹牛，是事实陈述。

---

## 一、工程哲学（最重要的部分）

### 协议人（protocol person）

| 时期 | 协议产物 |
|---|---|
| 2016-2020 | **CQHTTP → OneBot 协议** + NoneBot 框架（chatbot 跨语言协议标准）|
| 2018 | **qdp** — "把 QQ 消息当 transport protocol" (13 ★) — slock/hakimi 的远古祖先思想 |
| 2025-2026 | **kosong** (LLM 抽象) + **pykaos** (OS 抽象) + **agent-vault** (secret-aware I/O) + **wire mode** (agent 服务化) + **ACP** (Agent Client Protocol，他把 zed 的 ACP fork 进来用) |

OneBot 的作者天然懂"把 N×M 集成问题降到 N+M"。Slock 选 IM 作为 host plane，本质上是赌**"IM 协议会变成 agent 互操作的 substrate"**。

### "Empty layer" 设计美学

- **Kosong** 在马来语里就是"empty"—— 一个故意空的层
- **pykaos** —— OS 抽象但**只暴露受控访问**
- **agent-vault** —— 不让 agent 看到 secret
- **kimchi** —— "agent harness with stdio interface" (botiverse fork)

这是一套 **"give agent less, not more"** 的安全 + 抽象哲学。跟硅谷主流"give agent more tools / longer context / more autonomy"是反向的。

> 在 prompt injection 和 agent 越权问题正在变成 2026 年最大事故源的时间点，这个立场会越来越值钱。

### "Co-designed and evolved together"

Kosong 走红后他自己反应（[tweet](https://x.com/istdrc/status/1987553427409543336)）：

> "Didn't expect that Kosong would get so much attention. In fact, **Kimi CLI and Kosong were co-designed and evolved together**."

**好的 abstraction 不是事后提取出来的，是一开始就跟使用者一起进化的。** 这是给所有把 framework 当作事后封装的人的反例。

### 中文社区里极少数 systems → AI agent 的代表

中文 LLM 圈大部分是 ML 出身或产品出身，systems programmer 出身的不多。RC 的存在让 Kimi CLI 在工程口味上明显不同于 Cursor/Cline/Aider —— **Kimi CLI 默认支持 ACP、MCP、shell mode、ZSH plugin，是 Unix 哲学复刻而非 IDE 哲学复刻。**

### 博客签名：「己所不欲，勿施于人」

把儒家伦理放在博客 footer ([stdrc.cc/about](https://stdrc.cc/about))。这跟他做 agent-vault / secret-aware I/O 的设计取向是同一个东西的两面。

---

## 二、完整 GitHub 生态（38 个 repo + botiverse org）

### A. 当前主战场（agent / vibe coding）

| 仓库 | 描述 | Stars | 备注 |
|---|---|---|---|
| **botiverse/agent-vault** | Secret-aware file I/O | **356** | 2 个月，最快增长 |
| **stdrc/hakimi** | Telegram/Slack/Feishu 远控 Kimi CLI | 12 | qdp 思想的现代版 |
| **botiverse/kimchi** | Agent harness via stdio | 1 | botiverse 自己 fork 的 |
| stdrc/pink | Python TUI 框架，仿 Ink | 2 | "pure vibe engineering" |
| stdrc/tldr-vscode | 把代码渲染成 stub（**LLM-friendly**） | 4 | 直接连到 Kimi CLI 的 context management |
| stdrc/longshot | 智能截图拼接 | 2 | "pure vibe engineering" |
| stdrc/locklock | Next.js 资源管理后台（不是 slock 前身）| 0 | 早期实验 |
| stdrc/reading-list | 个人阅读清单，**README 直接写 "95% by Cursor AI agent and 5% by human"** | 0 | vibe coding 公开宣言 |
| **stdrc/my-vibe-coding** | **元仓库**，把所有 vibe 项目串成 collection | 2 | **AK LLM Wiki / 作品集的同构形态** |
| stdrc/macos-init-trajectory-viz | Codex session 中 macOS 配置对话的可视化 | 0 | "agent trajectory 是独立产物" 的探索 |

### B. Kimi 周边 fork

| 仓库 | 用途 |
|---|---|
| stdrc/kimi-cli (fork) | 个人镜像 |
| stdrc/agent-client-protocol (fork) | zed 的 ACP 协议 |
| stdrc/openai-python (fork) | 给 SDK 提 PR |

### C. RisingWave 时代（2022-2025）

| 项 | 数据 |
|---|---|
| 总贡献 | **375 commits** |
| 角色 | core contributor |
| 写到 | 2025-07，最后一个 commit 是 `add openai_embedding function` |
| 内容 | over window functions, expr/optimizer, dashboard UDFs |

### D. IPADS / OS kernel 时代（2020-2023）

| 仓库 | 注 |
|---|---|
| **stdrc/modern-cmake-by-example** | "IPADS 实验室新人培训第二讲" — **661 ★ / 85 forks，他个人最高** |
| stdrc/qemu-virt-hello | bare metal hello, 9 ★ |
| stdrc/uthread, freertos-raspi3 | 用户态线程, FreeRTOS port |
| stdrc/chbuild-poc | ChCore 新构建系统 PoC |
| stdrc/rust-os-slides | "Why Write OS in Rust? v2.0" 演讲 |

### E. 古老 chatbot 时代（2016-2020，灵魂前史）

| 项 | 数据 |
|---|---|
| **CQHTTP**（酷 Q HTTP API） | 1845 ★（已 archive）|
| **NoneBot** | 2195 ★ |
| **NoneBot2** (fork 出来) | **7467 ★** |
| **OneBot** 标准 | 1995 ★ |
| botuniverse 整个生态 | ~3000 ★ |
| **合计** | **~13,000 ★ 的中文 chatbot 生态由他主导** |
| **stdrc/qdp** | **"把 QQ 消息当 transport protocol"** — 13 ★，2018，灵魂原点 |

### Moonshot 时代（2025-2026）

| 项 | 数据 |
|---|---|
| **MoonshotAI/kimi-cli** | **7643 ★**（创建于 2025-10-15，他是主作者）|
| MoonshotAI/kosong | 513 ★ |
| MoonshotAI/kimi-agent-sdk | 382 ★ |
| Kimi CLI 1.0 → 1.9 | **一个月内连发 9 个版本** |

---

## 三、双线工程：Moonshot in-house + botiverse solo

```
┌──────────────────────────────────────────────────┐
│  Moonshot 内部 (官方 agent harness)                │
│  ┌──────────────────────────────────────────┐    │
│  │  Kimi CLI                                 │    │
│  │  ↓ 抽象出                                  │    │
│  │  Kosong (LLM)  pykaos (OS)                │    │
│  │  ↓                                         │    │
│  │  Kimi Agent SDK · wire mode · ACP         │    │
│  └──────────────────────────────────────────┘    │
├──────────────────────────────────────────────────┤
│  botiverse org (个人侧 agent 用户态)               │
│  ┌──────────────────────────────────────────┐    │
│  │  agent-vault (信任边界)                     │    │
│  │  hakimi (远控)                             │    │
│  │  slock.ai (协作 IM)                        │    │
│  │  kimchi (个人 fork harness)                │    │
│  └──────────────────────────────────────────┘    │
└──────────────────────────────────────────────────┘
```

**Moonshot 那条**：给所有 agent 用的官方 harness
**botiverse 那条**：给个人 agent 用户的 IM、保险箱、远控

**他在搭一个完整的 Agent OS 用户态生态，且两条线互补。**

---

## 四、最值得引用的瞬间

### 1. 自我介绍 ([tweet 2040862482622026076](https://x.com/istdrc/status/2040862482622026076))

> "Hi, I'm RC. I previously built Kimi CLI at Moonshot AI. Now I'm building Slock, an agent-human collaboration platform for modern builders and teams."

一句话把 credential、转身、目标全部交代清楚。从 big tech solo build 的标准动作，但没有任何"我离职了"的 melodrama。

### 2. Kosong 走红后 ([tweet 1987553427409543336](https://x.com/istdrc/status/1987553427409543336))

> "Didn't expect that Kosong would get so much attention. In fact, Kimi CLI and Kosong were co-designed and evolved together."

### 3. Slack workspace wedge ([tweet 2039960951789961401](https://x.com/istdrc/status/2039960951789961401))

> "被 Slack 删除 workspace 的中国公司可以试试 Slock，我们正在定义的下一代生产力协作平台，还在疯狂迭代打磨，不过已经初具可用性～"

Cold-open distribution play，对地缘政治 + 产品定位的精确拿捏。

### 4. 防 scam 公告 ([tweet 2012205878112309416](https://x.com/istdrc/status/2012205878112309416))

> "Kimi CLI 也是，不要相信任何跟 Kimi CLI 有关的虚拟货币信息！"

Open source maintainer 的责任反射弧。

### 5. Codex CLI 集成 ([tweet 2028403518244270328](https://x.com/istdrc/status/2028403518244270328))

让 Slock 支持 "agents powered by Codex CLI"——**他不把 Slock 当 Kimi CLI 的封闭 UI，而是当 multi-harness 的 host**。OpenAI / Anthropic / Moonshot 的 CLI agent 都可以在 Slock 里互通。

---

## 五、网络分析

### 他 follow 的人（389 人）

**Kernel/Rust OS 圈**：
- torvalds, ojeda (Rust for Linux), phil-opp (Writing an OS in Rust), MaskRay (LLD), andrewrk (Zig 作者), chyyuu (rCore)

**数据库/系统圈**：
- apavlo (CMU DB), c4pt0r (PingCAP), liurenjie1024 (RisingWave), Folyd, breezewish

**C++/编程语言**：
- hsutter, dtolnay, matklad, gvanrossum, yyx990803

**AI infra 新一代**：
- tiangolo (FastAPI), willmcgugan (Rich), Keats

**最关键的连接：Xuanwo**
- Apache committer (OpenDAL)
- 同时在做 xurl, xlaude, acp-claude-code
- **两人在同一个"给 agent 做基础设施"的圈子，互相 star，互相沿用 ACP 协议**

### 关注他的人（1323 followers）

包含 **skyzh (Alex Chi, Neon/CMU DB)**——中文系统圈核心人物之一。

### 他最近 star 的趋势（极强信号）

全是"agent 工具基建"：
- xurl, opencli, hermes-agent, bub, opencode, agent-browser
- kimi-agent-sdk, kimi-agent-rs, fastmcp
- ratatui, ink, gum, prompt-toolkit, rich （TUI 框架）
- **langfuse** → 在想 LLM 调用的 observability/eval
- **anthropics/skills** → 直接对应 kimi-cli 里大量 `feat(skills)/docs(skills)` commits
- **nonebot/nonechat** → **十年了他还在跟自己的老生态保持联系**

---

## 六、Hidden Gems（最值得细看的小项目）

### 1. qdp (2018, 13 ★)

> "A transport protocol based on QQ messages."

这是 hakimi + slock 的远古祖先。**"把 IM 当成 agent 与人之间的通用通道"是他从 2018 年就在想的事。**

### 2. modern-cmake-by-example (661 ★)

他个人最高 star 的项目居然是教学材料。说明他有教育/布道天分，会把内部知识公开化（对应他做协议的本能）。

### 3. tldr-vscode

把代码渲染成 stub。这是一个 **"为 LLM 上下文优化"的开发者工具**——LLM 读 stub 不读实现就够了。**直接连到 Kimi CLI 怎么做 context management。**

### 4. pink (Python TUI 框架)

他在 Kimi CLI 用 Python prompt-toolkit/rich 写 shell，副业又自己抄 Ink，说明他对 **TUI 组件化** 不满意现状，可能 Kimi CLI 的下一代 UI 会切到 pink。

### 5. macos-init-trajectory-viz

把 Codex session 里"配置 macOS 的对话流"做成可视化轨迹——他在思考**"agent 跑步过程的 trajectory 是一种独立的产物"**。

### 6. reading-list 的 README "95% by Cursor"

**他在公开宣告 vibe coding 流水线是他的开发主力。这不是边角料，是他工程哲学的公开化。**

### 7. agent-vault 的完整 trust boundary 模型

agent 看到 `<agent-vault:openai-key>` placeholder，磁盘上是真值，**TTY-only 的 sensitive command** 防止 agent 自己 execute。**这是一个完整的 agent threat model 被代码化了。**

### 8. my-vibe-coding 元仓库

把所有 vibe 项目串成一个 collection 的元 README——**这就是 AK LLM Wiki 想做的"个人作品集 + 给 agent 读的索引"的同构形态**，而且 RC 已经用最朴素的 markdown 做出来了。

---

## 七、与 Dongzhe 工作的具体连接点

### 1. Knowledge Agent Network ↔ slock.ai

slock 的 tagline = "Agent-native IM where AI agents and humans collaborate as equals"。**这正是 Dongzhe 在想的"agent-as-citizen"的 IM 形态**。slock 现在有产品但代码大概率不开源——可以注册试用、用 hakimi 反向研究他的 IM↔agent 对接思路。

### 2. Agent Economy ↔ agent-vault

Dongzhe 思考"agent 经济变现基础设施"时，**信任边界（secrets / 授权 / 不可信 agent）是绕不开的层**。agent-vault 已经把 trust boundary 做成完整的 file I/O 抽象 + TTY-only sensitive command 模型——这是**可以直接抄的设计模式**。这套模型可以演化成"agent 经济"里的支付授权层。

### 3. AK LLM Wiki ↔ tldr-vscode + my-vibe-coding

- **tldr-vscode** 把代码渲染成 LLM-friendly stub —— 这个 idea 可以挪到"知识库/卡片是 LLM stub form"的设计上
- **my-vibe-coding** 是 AK LLM Wiki 想做的同构形态 —— 直接借鉴他的归档结构
- **reading-list** 仓库里有完整的"个人内容数据 + 自动渲染"模式，可以直接 fork 当 starting point

### 4. Agent Deck ↔ kimchi/agent-client-protocol

**Agent Deck 应该 audit 自己是不是该 native 支持 ACP/stdio**——如果支持，立刻能跑 kimi-cli/Claude Code/任何 ACP 兼容 agent，社区面爆炸式扩大。

### 5. agent-friendly 设计 ↔ botiverse 整套哲学

RC 的 "agents and humans collaborate as equals" 跟 BENZEMA 笔记里的 "agent-friendly 作为核心交互思路" 是同一个北极星。RC 已经在产品/协议层把这个北极星具象化。

### 6. 可能的合作角度

RC 的兴趣半径（OneBot 协议 → Kimi wire mode → ACP → slock 的 agent IM）和 Dongzhe 的"Agent Economy + Knowledge Agent Network"高度重合。

**最自然的接触面是协议层**——比如：以 ACP/wire/OneBot 任一方为底，提一个"agent 之间的支付/授权/服务发现"扩展。比起空对空聊"合作"，**带一份基于他自己生态的 RFC 草案过去**。

---

## 八、Twitter 抓取限制

X.com 主页、单条 tweet 详情页、xcancel 镜像在抓取时分别返回 402/503/404。除了 GitHub profile + 博客 + 个别能从 Google 索引拿到的 tweet snippet 外，**无法获得他完整 timeline 与回复网络**。

如果要做"他与谁互动 / 引用谁 / 在哪条 viral 上下注 karpathy 风评"等分析，需要登录态的 X API 或 nitter 私有部署补完。

---

## 关键 URL

- **GitHub Profile**: https://github.com/stdrc
- **GitHub README**: https://github.com/stdrc/stdrc
- **Botiverse Org**: https://github.com/botiverse
- **agent-vault**: https://github.com/botiverse/agent-vault
- **kimchi**: https://github.com/botiverse/kimchi
- **Kimi CLI**: https://github.com/MoonshotAI/kimi-cli
- **Kosong**: https://github.com/MoonshotAI/kosong
- **slock.ai**: https://slock.ai
- **个人站**: https://stdrc.cc
- **博客 about**: https://stdrc.cc/about
- **X**: https://x.com/istdrc
- **TG**: https://t.me/istdrc
- **LinkedIn**: https://linkedin.com/in/stdrc
