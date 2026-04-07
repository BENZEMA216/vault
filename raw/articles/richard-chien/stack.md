# Richard Chien (余超 / stdrc) 完整技术栈

> 来源: 知识库 Query 讨论 (2026-04-07)
> GitHub: https://github.com/stdrc · X: https://x.com/istdrc
> 关键发现: 他不是在做"一个 Slack 克隆"，是在**搭建一整套 agent 基础设施**

---

## 核心洞察

**"I build ecosystems, not just software."**

这不是一句空话。RC 正在构建一个**从 OS 到 UX 的完整 agent stack**，slock.ai 只是这个 cathedral 最上层的协作介质层。

## 完整技术栈

```
┌─────────────────────────────────────────────────────────────┐
│  UX 层：Agent-Human Collaboration Interface                  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  slock.ai          → Slack-like multi-agent IM         │  │
│  │  hakimi            → Telegram/Slack/Feishu 远程触发     │  │
│  └───────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────┤
│  Agent / SDK 层                                              │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  kimi-cli          → Opinionated AI coding agent      │  │
│  │  kimi-agent-sdk    → SDK to build agents on Kimi CLI  │  │
│  │  kimchi            → Agent harness via stdio (1★)      │  │
│  └───────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────┤
│  抽象层：LLM + OS + Secrets                                  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  kosong (LLM)      → LLM abstraction for agent apps    │  │
│  │  pykaos (OS)       → OS abstraction for controlled     │  │
│  │                       agent file/process access        │  │
│  │  agent-vault ⭐    → Secret-aware file I/O for agents  │  │
│  │                       356 stars in 2 months            │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## ⭐ agent-vault 单独说一下

这个项目最值得 Dongzhe 关注，因为它**直接对应 Knowledge Agent Network 的安全层缺口**。

### 它解决什么

> When AI agents help you set up services, secrets like API keys and tokens flow through LLM provider servers. agent-vault prevents this by acting as a secret-aware file I/O layer — agents see placeholders like `<agent-vault:api-key>`, never real values.

```
┌──────────────────────────────────────────────┐
│  Agent sees:                                 │
│    api_key: <agent-vault:openai-key>         │
│    bot_token: <agent-vault:tg-bot-token>     │
│    port: 3000                                │
├──────────────────────────────────────────────┤
│  agent-vault                                 │
│    read:  real value → <agent-vault:key>     │
│    write: <agent-vault:key> → real value     │
├──────────────────────────────────────────────┤
│  Actual file on disk:                        │
│    api_key: sk-proj-abc123...                │
│    bot_token: 7821345:AAF...                 │
│    port: 3000                                │
└──────────────────────────────────────────────┘
```

**核心机制**:
- 高熵字符串（看起来像 API key）自动检测并替换成 `<agent-vault:UNVAULTED:sha256:XXX>` 占位符
- 加密本地存储 (`~/.agent-vault/`)
- agent 永远看不到/传输真实 secret
- npm 包：`@botiverse/agent-vault`

**Stars**: **356** (创建于 2026-02-19，**2 个月**)

### 为什么对 KAN 重要

Knowledge Agent Network 一旦走向"互联节点"，每个节点都需要：
- 暴露 API 但不暴露 API key
- 跨节点查询时不让查询方看到 secret
- 安全审计

**agent-vault 的"占位符 + 真实值分离"模型可以直接复用到 KAN 的节点间通信**。

## RC 的完整 GitHub 视图

### 在做（active 2026）

| 项目 | 类别 | Stars | 备注 |
|------|------|-------|------|
| **agent-vault** | Agent 安全 | **356** | 2 个月，最快增长 |
| **kimi-cli** | Agent harness | (Moonshot 主仓) | 他是主作者 |
| **slock.ai** | Agent UX | — | 闭源 |
| hakimi | Remote control | 12 | Telegram/Slack/Feishu 控制 Kimi CLI |
| kimchi | Agent harness | 1 | stdio 接口 |

### 经典项目（高 stars）

| 项目 | 类别 | Stars |
|------|------|-------|
| **modern-cmake-by-example** | C++/系统教学 | **661** |
| **purepress** | 静态博客生成器 | 33 |
| qdp | QQ 消息上的 transport protocol | 13 |

### Side projects 体系

| 项目 | 用途 |
|------|------|
| chinese-colors | 中国传统色卡 |
| longshot | 智能截图拼接 |
| tldr-vscode | VSCode 代码结构速览 |
| **my-vibe-coding** | RC 本人的 vibe code 项目集合 |
| reading-list | 阅读列表 |
| macos-init | macOS 初始化脚本 |

## Botiverse 组织

| | |
|---|---|
| Created | **2023-05-05**（已经存在 1 年了！）|
| Description | "Where humans and bots create together." |
| Public repos | 2（agent-vault + kimchi）|

**注意**: 之前研究说 Botiverse 与 slock.ai 无关 —— **错的**。Botiverse 是 RC 自己 2023 就创建的 GitHub org，slock.ai footer 的 © Botiverse 是真的。

可能 Botiverse 是 RC 早就准备好的"agent-bot 生态" brand，最近才开始装载真实项目（agent-vault + slock + kimchi）。

## 完整履历（再次确认）

| 时间 | 经历 |
|---|---|
| 2008– | 5 年级写第一个程序 |
| 2016–2020 | **CQHTTP → OneBot → NoneBot** chatbot 生态（Python，国内 QQ bot 开发圈知名）|
| 2020–2023 | **SJTU IPADS** 硕士，OS kernel & hypervisor |
| 2022–2025 | **RisingWave Labs** core contributor（Rust streaming database）|
| 2023-05 | 创建 GitHub org **Botiverse**（蛰伏 1 年多）|
| 2025– | **Moonshot AI**，主导 **Kimi CLI** + 配套 agent infra (kosong, pykaos, kimi-agent-sdk) |
| 2026-02 | **agent-vault** 发布（2 个月 356 stars）|
| 2026-02 | **hakimi** 发布 |
| 2026-03 | **slock.ai** soft launch |

## 对 Dongzhe 研究方向的关键启示

### 1. RC 的工程哲学和你一致
- "I build ecosystems, not just software."
- 不做单点产品，做完整 stack
- **这恰好是你 Knowledge Agent Network 应该走的路** —— 不只是一个 wiki MCP server，而是 ingest + compile + query + lint + network 的完整 stack

### 2. 他的 stack 直接可用
- **agent-vault** 可以是 KAN 节点的 secrets 层
- **kosong** 的 LLM 抽象可以参考做 KAN 的 LLM 配置
- **kimchi** 的 stdio harness 接口和 MCP 是同构的

### 3. RC 是值得追踪的人
- **十年 chatbot 生态老兵 + 系统底层背景 + Moonshot agent infra 主作者**
- 跨越 NoneBot → IPADS OS → RisingWave → Moonshot Kimi CLI → 个人 agent stack
- 这种"宽度 + 深度"的工程师在 agent 时代是稀缺资源
- 他在 X 上 (@istdrc) 发什么就是 agent infra 方向的领先信号

### 4. slock.ai 在他生态中的位置
slock.ai 是 RC 整个 agent stack 的**最上层 UX 入口**。它不是孤立产品，是 cathedral 的尖顶。当你看 slock.ai 时，应该想到：
- 它背后有 agent-vault 解决 secrets
- 有 kosong 解决 LLM 抽象
- 有 pykaos 解决 OS 接口
- 有 kimi-cli 提供 agent runtime

**这是一个完整闭环，不是一个 Slack 克隆**。

## 关键 URL

- **GitHub Profile**: https://github.com/stdrc
- **Profile README**: https://github.com/stdrc/stdrc
- **Botiverse Org**: https://github.com/botiverse
- **agent-vault**: https://github.com/botiverse/agent-vault
- **kimchi**: https://github.com/botiverse/kimchi
- **kimi-cli**: https://github.com/MoonshotAI/kimi-cli
- **slock.ai**: https://slock.ai
- **个人站**: https://stdrc.cc
- **X**: https://x.com/istdrc

## 我之前研究的两个错误

| | 第一次错误 | 第二次错误 | 真相 |
|---|---|---|---|
| 作者 | Fanqing Meng (SJTU 多模态 PhD) | — | **Richard Chien (余超 / stdrc)** |
| Botiverse | "可能是公司名" | "与 slock.ai 无关" | **是 RC 自己的 GitHub org，2023-05 创建** |
| 工程性质 | "vibe code 个人玩具" | "10 年老兵 side project" | **完整 agent stack 的最上层 UX** |

教训：
1. 不要在第一个匹配处停下
2. 从硬证据（npm 包 owner、GitHub org）反查
3. 看完整生态而不只是单个仓库
