# slock.ai 深度研究（已修正作者）

> 来源: 知识库 Query 讨论 (2026-04-07)
> 关键词: agent-native IM, agent collaboration, Moonshot ex, Kimi CLI
> ⚠️ **2026-04-07 修正**: 作者不是 Fanqing Meng，是 Richard Chien (余超)。详见末尾"修正记录"。

---

## TL;DR

**Agent-native IM / 协作平台**，slogan: "Where humans and AI agents collaborate. Not as tools. As teammates."

由 **Richard Chien (余超 / @stdrc / @istdrc)** 构建。他之前在 **Moonshot AI 主导构建 Kimi CLI**，更早是国内 chatbot 生态 **NoneBot / OneBot / CQHTTP** 的作者，做过 SJTU IPADS 系统方向硕士、RisingWave Labs core contributor。

**这不是一个学术圈 vibe code prototype，而是一个十年 chatbot/agent 生态老兵 + Moonshot agent infra 主作者的下一个项目。**

## 消歧义

| 名称 | 是什么 | 是否相关 |
|---|---|---|
| **slock.ai** | Agent-native IM (2026, RC 作品) | ✅ 本次目标 |
| Slock.it | 2015 德国以太坊智能锁，The DAO 事件 | ❌ |
| slock (suckless) | Linux X11 屏幕锁 | ❌ |
| slock.date | AI 日历管理（撞名）| ❌ |
| Botiverse | 埃及开发者的 Python chatbot 包，与 slock.ai 无关 | ❌（之前误以为是公司主体）|

## 产品定位

- **品类**: agent-native instant messaging / collaboration workspace
- **核心命题**: AI agents 在 IM 中作为 **first-class citizens** —— "Not as tools. As teammates."
- **官网**: https://slock.ai
- **slogan**: "Where humans and AI agents collaborate"
- **footer**: © 2026 Botiverse（**注**: 这个 Botiverse 不是公司名，目前没有公开证据 slock.ai 有注册公司主体）

## 三个核心差异化（marketing 上明确提的）

### 1. Agents That Remember
持久化记忆，跨 session 记得 codebase / 偏好 / 历史对话。

### 2. One Conversation
频道 + DM，人和 agent 平等参与，无 context switching，无 copy-paste。

### 3. Your Machines, Your Agents ⭐
**Agent 在你自己的机器上执行**，通过 lightweight daemon。Full control over compute, full privacy over your code and data。

**这是最大的差异化点**：区别于 OpenAI Agents / ChatGPT 的云执行，slock.ai 是 **local-first**。

## 4 步上手流程

```
01 Create a Server          (Slack 风格)
02 Connect a Machine        (npx @slock-ai/daemon)
03 Spawn Agents             (用一段描述定义 agent 角色)
04 Collaborate              (聊天，agent 持续工作)
```

## 产品工程细节（从官网/截图）

- 4 个 marketing 用 named agents：**Atlas / Luna / Nova / Echo**
- Agent 状态机：**Online / Thinking… / Working… / Offline**
- 设计风格：**黄黑粉 Y2K Notion 美学**（不是 Discord/Slack 的标准蓝绿）
- URL namespace 结构：`/s/{username}/{page}` —— per-user workspace 概念
- npm 包：`@slock-ai/daemon`
- "Always on, always ready"：hibernate when idle, wake on new messages, full context restored, memory carries over automatically

## 创建者：Richard Chien (余超)

| 项 | 信息 |
|---|---|
| 英文名 | Richard Chien |
| 中文名 | **余超** |
| 别名 | RC, stdrc, istdrc |
| GitHub | https://github.com/stdrc |
| X | https://x.com/istdrc |
| 个人站 | https://stdrc.cc |
| LinkedIn | https://linkedin.com/in/stdrc |
| 座右铭 | "I build ecosystems, not just software." |

### 完整履历

| 时间 | 经历 |
|---|---|
| 2016–2020 | 开源 chatbot 生态 **CQHTTP → OneBot → NoneBot**（Python chatbot 框架，国内 QQ/IM bot 开发者圈知名）|
| 2020–2023 | **SJTU IPADS** 硕士，做 microkernel OS 和 hypervisor |
| 2022–2025 | **RisingWave Labs** core contributor，PostgreSQL 兼容的 streaming database (Rust) |
| 2025–至今 | **Moonshot AI**，主导 **Kimi CLI**，做 agent infra (`kosong` LLM 抽象层、`kimi-agent-sdk`) |
| 2026 春节起 | 个人项目 **slock.ai** |

### 技术 DNA

**他从 2016 年就是 chatbot/agent 框架作者**。slock.ai 是这条线的自然演化：
- **NoneBot 时代** = chatbot 生态思维
- **Kimi CLI 时代** = agent harness 思维
- **slock.ai** = agent-human collaboration UX 思维

**横跨 chatbot 历史 + agent harness + 协作介质三层，是 agent-native IM 这个 framing 最有资格的人之一。**

## 自述

来自 RC 的 X 推文：

> "Hi, I'm RC. I previously built Kimi CLI at Moonshot AI. Now I'm building Slock, an agent-human collaboration platform for modern builders and teams."

> "被 Slack 删除 workspace 的中国公司可以试试 Slock，我们正在定义的下一代生产力协作平台"

(注意第二条用了"我们"——可能在拉团队，但目前无公开 team 信息)

## 融资 / 阶段

- **融资**: 无任何 slock.ai 融资记录
- **公司主体**: 无（footer "Botiverse" 来源不明，可能是个人 brand）
- **阶段**: soft launch / 小范围测试
- **Product Hunt / HN**: 未检索到发布

## 与 Agent 生态的关系

### Agent-Native IM 同赛道项目对比

| 项目 | 团队 | 路线 | 作者背景 |
|------|------|------|---------|
| **slock.ai** | Richard Chien (个人/小团队) | Slack 风格 + 本地 daemon | **Moonshot Kimi CLI 主作者**，10 年 chatbot 生态 |
| **HiClaw** ([alibaba/hiclaw](https://github.com/alibaba/hiclaw)) | 阿里 AgentScope | 开源 + Matrix 协议派 | 大厂 |
| **RockClaw** ([gdd-rock/rockclaw](https://github.com/gdd-rock/rockclaw)) | rockclaw.ai | 社区派，250+ citizens | — |

**slock.ai 的差异化**：
- 不像 HiClaw 那样押 Matrix 协议（更主流但更重）
- 不像 RockClaw 那样做大社区（更野心但更分散）
- 走 **local-first daemon + Slack UX** 路线，技术敏感度极高（典型 Kimi CLI 作者的思路）

## 在 Dongzhe Agent Economy 栈中的位置

slock.ai 命中的是 **"Agent 协作的承载介质"** —— 比 marketplace 更上层的 UX 入口层：

```
经济结算层 (Agent Economy) ← Dongzhe 最关心
        ↓
通信协议层 (MCP / A2A) ← Dongzhe 已研究
        ↓
协作介质层 (IM / 聊天 UX) ← slock.ai 在这层
        ↓
本地执行层 (daemon) ← slock.ai 也覆盖这一层
        ↓
个体 Agent 层
```

**slock.ai 同时占了协作介质 + 本地执行两层** —— 这是它的工程野心。

## 关键判断

### 修正后的判断

之前框架："学术圈博士的 vibe coding 玩具"——**完全错误，需要降权**

实际情况：
- **Moonshot Kimi CLI 主作者 + 10 年 chatbot 生态老兵的下一个项目**
- 信号强度：**高**
- 技术成熟度：**高**（不是 vibe code，是有 daemon、namespace、状态机的真产品）

### 与 Knowledge Agent Network 的对照

如果 Dongzhe 的 KAN 长出 UX 层，slock.ai 是最直接的参考：

| Dongzhe KAN | slock.ai | 启示 |
|---|---|---|
| Wiki Agent (MCP server) | Agent in channel | KAN agent 可以以"团队成员"形式出现 |
| 跨节点查询 | Atlas/Luna 跨频道协作 | 需要类似的 IM-style coordination |
| 节点暴露为 endpoint | local daemon | 也许 KAN 节点也该走 local-first |
| 知识 namespace | `/s/{user}/` | 命名空间概念可以借鉴 |

## 修正记录

**2026-04-07**：之前的研究错误地将作者归为 Fanqing Meng (SJTU 多模态 LLM 博士生)。这是错误归因。

正确作者是 **Richard Chien (余超 / @stdrc)**，前 Moonshot AI Kimi CLI 主作者，更早是 NoneBot 等 chatbot 框架作者。两人的共同点都跟 SJTU 有关（Fanqing 是在读博士；Richard 是 IPADS 已毕业硕士），可能是误判源头。

之前研究中关于"7 天 vibe code"的描述也需降权——可能只是早期 v0.1，现在的 slock.ai 是有 daemon、有 namespace、有完整产品架构的成熟项目。

## 关键 URL

- **官网**: https://slock.ai/
- **作者 GitHub**: https://github.com/stdrc
- **作者 X**: https://x.com/istdrc
- **作者个人站**: https://stdrc.cc
- **Kimi CLI**: https://github.com/MoonshotAI/kimi-cli
- **关键推文**（自述）: https://x.com/istdrc/status/2040862482622026076
- **HiClaw（同赛道对照）**: https://github.com/alibaba/hiclaw
- **RockClaw（同赛道对照）**: https://github.com/gdd-rock/rockclaw
