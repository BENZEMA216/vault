# Agent-Native IM

> 把 AI agents 当作一等公民放进聊天室的产品形态。slock.ai / HiClaw / RockClaw 是当前三种代表实现，介于通信协议层和经济结算层之间的"协作介质层"。

## 核心要点

- **Slogan**：slock.ai 的 "Where humans and AI agents collaborate. **Not as tools. As teammates.**" 是这个品类的口号
- **三种实现对照**：
  | 项目 | 团队 | 路线 | 背景 |
  |---|---|---|---|
  | **slock.ai** | Richard Chien (前 Moonshot Kimi CLI 主作者) | Slack 风格 + 本地 daemon | 个人/小团队 |
  | **HiClaw** ([alibaba/hiclaw](https://github.com/alibaba/hiclaw)) | 阿里 AgentScope | Matrix 协议 + 开源 | 大厂 |
  | **RockClaw** ([gdd-rock/rockclaw](https://github.com/gdd-rock/rockclaw)) | rockclaw.ai | 社区派，250+ citizens | 社区 |
- **slock.ai 三大差异化**：
  1. **Agents That Remember** — 持久化记忆，跨 session 记得 codebase / 偏好
  2. **One Conversation** — 频道 + DM，人和 agent 平等参与，无 context switching
  3. **Your Machines, Your Agents** ⭐ — agent 在用户**自己的机器上执行**，通过 lightweight daemon (`npx @slock-ai/daemon`)，full privacy
- **核心命题**：local-first daemon + Slack UX，区别于 OpenAI Agents / ChatGPT 的云执行
- **状态机**：Online / Thinking… / Working… / Offline。namespace `/s/{username}/{page}` 是 per-user workspace 概念

## 详细说明

**为什么 IM 是 agent 协作的承载介质**：[[concepts/agent-communication|MCP/A2A 协议层]] 解决"agent 之间能不能通话"，但用户体验需要一个"agent 和人混在同一个房间"的 surface。Discord/Slack 的群聊比邮件、issue tracker、IDE 都更适合长期、异步、多参与者的协作流。RC 从 2018 年的 [qdp](https://github.com/stdrc/qdp)（13 stars，"把 QQ 消息当 transport protocol"）到 2026 年的 slock.ai，是同一条思想线的演化——**IM 协议会变成 agent 互操作的 substrate**。

**在 Agent Economy 栈中的位置**：

```
经济结算层 (Agent Economy) ← Dongzhe 最关心
        ↓
通信协议层 (MCP / A2A) ← 已研究
        ↓
协作介质层 (IM / 聊天 UX) ← slock.ai 等 IM 项目在这层
        ↓
本地执行层 (daemon) ← slock.ai 也覆盖这层
        ↓
个体 Agent 层
```

slock.ai 同时占了**协作介质 + 本地执行**两层，这是它的工程野心，也是它和云端 agent 路线的根本区别。

**冷启动的地缘政治 wedge**：RC 的推文 ["被 Slack 删除 workspace 的中国公司可以试试 Slock"](https://x.com/istdrc/status/2039960951789961401) 是一个非常精确的 distribution play——选了一个明确的、被 incumbent 抛弃的细分群体作为入口。

**与 [[concepts/knowledge-agent-network|KAN]] 的连接**：如果 KAN 长出 UX 层，slock.ai 是最直接的参考。Wiki Agent 可以以"团队成员"形式出现在 channel 里，跨节点查询变成 channel 内的 @mention，命名空间 `/s/{user}/` 概念可以直接借鉴。

## 在知识库中的出现

| 来源 | 上下文 |
|------|------|
| `output/reports/richard-chien/slock-ai-research.md` | slock.ai 完整研究：定位、daemon 模式、URL namespace、三种 named agents、与 HiClaw/RockClaw 对照 |
| `output/reports/richard-chien/deep-profile.md` | slock.ai 是 RC 双线工程（Moonshot in-house + botiverse solo）的 botiverse 侧旗舰 |
| `output/reports/richard-chien/code-review.md` | RC "co-designed and evolved" 哲学：slock 与 Wire 协议 / agent-vault 是同一套 trust boundary 的不同切面 |

## 关联概念

- [[concepts/agent-communication]] — IM 是协议之上的 UX 表达
- [[concepts/knowledge-agent-network]] — KAN 的 UX 层可以借鉴 slock 的频道 + daemon 模型
- [[concepts/agent-runtime]] — local daemon 是 runtime 在用户机器侧的形态
- [[connections/communication-to-economy]] — IM 是协议层到经济层之间的 missing layer
- [[connections/harness-to-kan]] — RC 的 Wire 协议是 slock 之下的 substrate

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
