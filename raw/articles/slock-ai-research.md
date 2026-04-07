# slock.ai 深度研究

> 来源: 知识库 Query 讨论 (2026-04-07)
> 关键词: agent-native IM, vibe coding, super-individual

---

## TL;DR

**Agent-native 即时通讯平台**，slogan: "Where humans and AI agents collaborate"。
作者用 vibe coding 在 2026 春节假期 **7 天**做完，**全程没手写一行代码**。
**不是** 2015 年那个以太坊 IoT 的 Slock.it。

## 消歧义（避免混淆）

| 名称 | 是什么 | 是否相关 |
|---|---|---|
| **slock.ai** | Agent-native IM (2026) | ✅ 本次研究目标 |
| Slock.it | 2015 德国以太坊智能锁，与 The DAO 事件相关，融资 $2M 后被整合 | ❌ |
| slock (suckless) | Linux X11 屏幕锁 | ❌ |
| slock.date | AI 日历管理（撞名） | ❌ |

## 产品定位

- **品类**: agent-native instant messaging
- **核心命题**: AI agents 在 IM 中作为 **first-class citizens** 存在
  - 不是 ChatGPT 那种"人和一个 bot 对话"的范式
  - 而是把 agent 当作和人类用户对等的参与者放进多人聊天/协作环境
- **官网**: https://slock.ai
- **slogan**: "Where humans and AI agents collaborate"

## 信息密度极低

官网纯前端 SPA，能抓到的只有 `<title>` + slogan。**没有公开 pricing、feature list、changelog、blog、文档**。这本身就是产品阶段的强信号——个人 demo / soft launch 状态。

## 工程花絮（作者自述）

- 2026 中国春节假期期间构建
- 全程 vibe coding，**没有手写一行代码，也没有 review 一行生成的代码**
- **7 天**完成核心功能 + 部署
- 一边和朋友聚会一边做的 side project

## 创建者：Fanqing Meng（孟繁晴）

| 项 | 信息 |
|---|---|
| 身份 | **上海交通大学（SJTU）博士生** |
| 方向 | VLM、LLM、Post-training、Benchmarking |
| 代表工作 | LVLM-eHub, TinyLVLM-eHub, MMIU, ChartAssistant |
| 合作圈 | 与 Ping Luo（罗平，HKU/上海 AI Lab）等 |
| X | [@FanqingMengAI](https://x.com/FanqingMengAI) |

**学术圈出身的多模态 LLM 研究者**，slock.ai 是工程实验，非商业团队产品。

## 融资 / 阶段

- **融资**: 无任何 slock.ai 融资记录
- **公司主体**: 未发现注册公司或团队页，看起来是个人项目
- **阶段**: 早期 demo / soft launch
- **Product Hunt / HN**: 未检索到发布或讨论
- **GitHub**: 未发现 `slock-ai` / `slockai` 组织

## 与 Agent 生态的连接

slock.ai 命中了 Dongzhe 当前研究主线（agent 经济、agent 通信、多 agent 协作）的一个特定层级——**Agent 协作的承载介质（IM/UX 入口层）**。

### Agent-Native IM 是一个被多方探索的赛道

| 项目 | 团队 | 特点 |
|------|------|------|
| **slock.ai** | Fanqing Meng (个人) | 极轻量 vibe-coded prototype |
| **HiClaw** ([alibaba/hiclaw](https://github.com/alibaba/hiclaw)) | 阿里 AgentScope | 开源 Collaborative Multi-Agent OS，**走 Matrix 协议** |
| **RockClaw** ([gdd-rock/rockclaw](https://github.com/gdd-rock/rockclaw)) | rockclaw.ai | "First AI-Native Autonomous Community"，250+ citizens / 926 skills |

**三者都在做 "AI agents as first-class citizens" 这个 framing**，但深度差异巨大：
- **slock.ai** = 个人 prototype，把 agent 塞进类 Slack/Discord 体验
- **HiClaw** = 协议派，开源 + Matrix 标准
- **RockClaw** = 社区派，规模更大

## 与 Dongzhe 方向的关联

### 直接关联

slock.ai 代表 **"agent 协作的承载介质"** 这一层，是 agent 经济栈中比 marketplace 更**上层的 UX 入口层**：

```
经济结算层 (Agent Economy)
        ↓
通信协议层 (MCP / A2A)
        ↓
协作介质层 (IM / 聊天 UX)  ← slock.ai 在这一层
        ↓
个体 Agent 层
```

### Knowledge Agent Network 的对照点

如果 KAN 长出 UX 层，最自然的形态可能就是 IM——**多个 Wiki Agent 在一个聊天室里协作回答用户的问题**。slock.ai 是这个形态的雏形参考。

### Super-Individual 叙事的又一案例

继 MiroFish（20 岁本科生 10 天 → $4M）之后，slock.ai 是另一个证据：

| 项目 | 作者 | 周期 | 工具 |
|------|------|------|------|
| MiroFish | 20 岁本科生 | 10 天 | vibe coding |
| slock.ai | SJTU 博士生 | 7 天 | vibe coding（0 行手写）|
| BENZEMA Wiki MCP Server | Dongzhe (with Claude) | 1 天 | vibe coding |

**模式正在普及：单人 + AI coding = 一个完整的产品 prototype，几天内完成**。

## 用户评价

- 没有任何独立第三方评测
- 没有媒体报道
- 没有 Product Hunt / HN 讨论
- 除作者本人推文外，社交媒体无声响

→ pre-public-traction 阶段。

## 结论

### 确定的事实
✅ Fanqing Meng 个人 vibe-coded side project
✅ Agent-native IM，agent = first-class citizens
✅ 春节 7 天完成，0 手写代码
✅ slogan: "Where humans and AI agents collaborate"

### 不确定 / 缺失
❓ 具体功能集（哪些 agent？支持哪些模型？是否有 MCP？群聊？）
❓ 商业化意图、roadmap、是否开源
❓ 用户量、技术栈、持久化方案

### 价值判断

**不要把它当严肃产品或竞品对待。** 应该当成：
1. **概念演示**——验证了 "agent-native IM" 这个 framing 的可行性
2. **Super-individual 叙事的又一案例**——证明个人 vibe code 已经能产出可访问的产品
3. **Agent 经济栈中"协作介质层"的样本**——加入"agent-native IM" landscape 的 reference list

**真正值得深挖的同主题对照组**：
- **HiClaw**（阿里 AgentScope）——开源、走 Matrix 协议
- **RockClaw**——社区导向、规模更大

## 关键 URL

- **官网**: https://slock.ai/
- **作者 X**: https://x.com/FanqingMengAI
- **作者 Google Scholar**: https://scholar.google.com/citations?user=iUIC-JEAAAAJ
- **HiClaw（对照）**: https://github.com/alibaba/hiclaw
- **RockClaw（对照）**: https://github.com/gdd-rock/rockclaw
