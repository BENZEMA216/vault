# Personal Knowledge Card — 产品定义文档

> 来源: 知识库 Query 讨论 (2026-04-08)
> 状态: ✅ Committed direction
> 这是把过去一周所有 KAN / Expert Network / Wiki MCP Server 讨论收敛到的最终产品 framing

---

## 核心 Insight（决定一切的认知翻转）

**错误的 framing**：
> "我 build 一个 Wiki，让别人来查"

→ 死亡螺旋：别人没动机来 → 我没动机维护 → 内容陈腐 → 更没人来。
→ 所有"个人页面"产品（About.me / Polywork / Read.cv 大多数变体）都死在这里。

**正确的 framing**：
> "我 build 一张 Card，让**我自己的 AI** 认识我。Share 是副作用。"

→ 自用即动机 → 我天天会用 → Card 持续更新 → Share 时是 alive 的 → 接收方有 cool feature 体验。

---

## 一、真实痛点

每个 ChatGPT/Claude 重度用户每天都在重复：

> "我是 PM，做 AI agent 产品的"
> "我之前研究过 World Model 这个领域"
> "我对 Agent Communication 有自己的看法"
> "请考虑我的背景，不要给泛泛建议"

**问题**：AI 每天都不知道你是谁。

### 现有解决方案有多烂

| 方案 | 问题 |
|------|------|
| ChatGPT Custom Instructions | 1500 字符上限，装不下任何深度 |
| Claude Project + System Prompt | 每个 project 一份，无法跨工具共享 |
| Mem0 / Letta | 给开发者的，普通人不会用 |
| 复制粘贴 markdown 文件 | 每次都要做，烦 |
| 公众号/博客 + AI 阅读 | AI 没主动读你博客的动机 |

**没有一个产品解决"让我的 AI 真正了解我"这件事。**

### MCP 是天然解法

```
[你的 Knowledge Card MCP Server]
        ↓
[你自己的 Claude Code / Cursor / ChatGPT]
        ↓
你跟 AI 对话时它自动知道你是谁
```

**Dongzhe 已经在用这套基础设施**——wiki-mcp-server 就是这个东西，只是他没意识到第一个用户应该是他自己。

---

## 二、产品定义

**A SaaS that auto-builds your Personal Knowledge Card from existing content and exposes it as an MCP endpoint for both your own AI and anyone you share it with.**

### Tagline 候选

- "Stop reintroducing yourself to ChatGPT every day."
- "Plug your brain into your AI."
- "Your AI should know who you are."
- "30 seconds: turn your blog into your AI's memory."

### 30 秒 Onboarding

```
1. 输入内容来源:
   ☐ 个人博客 URL
   ☐ 公众号 RSS
   ☐ X handle
   ☐ GitHub username
   ☐ Notion 公开页面
   ☐ Substack
   ☐ Medium

2. 系统自动 ingest 所有内容

3. LLM 自动 compile 成结构化 wiki:
   - 概念
   - 主题地图
   - 核心观点

4. 自动部署成 MCP endpoint:
   https://[yourname].cards/mcp

5. 自动给你安装到 Claude / Cursor:
   一键复制 config

完成。从此你的 AI 知道你是谁。
```

---

## 三、价值主张分层

**永远不要在 Tier 1 验证之前讲 Tier 3 的故事。**

### Tier 1：自用价值（10/10 强，第一年只卖这个）

- "Stop reintroducing yourself to ChatGPT every day."
- "Your AI now knows your research, your views, your context."
- 用户付费意愿：高（每天用 AI 的人 = 重度需求）

### Tier 2：Share 价值（7/10，第二年）

- "Send your card to anyone who wants to know you deeper."
- "Investors can DD you in 5 minutes via their own AI."
- 适用场景：投资 DD / 招聘 / 合作 / 公众号读者

### Tier 3：Network 价值（4/10，第三年）

- "Plug into the Expert Network to monetize queries."
- 这是 KAN 的终态形态

---

## 四、冷启动 — 内容创作者作为火种

### Early Adopter 画像（同时满足 3 个条件）

1. **已经在公开写内容**（公众号/博客/X/播客/论文）
2. **自己重度用 AI**（每天用 Claude/ChatGPT）
3. **有少量 audience**（哪怕 100 个）

### 为什么这群人 = 火种

| 普通人 | 内容创作者 |
|--------|----------|
| 没有现成内容 → 要从零写 | 已有 1 万字内容可以 ingest |
| 没有 distribution | 自带 X / 公众号 audience |
| 不爱用 AI | 已经在用，痛点真实 |
| 动机模糊 | 动机两条：自用 + 给读者 cool feature |

### 第一批 100 人 — 触手可及

- AI 圈写公众号的人（量子位作者、机器之心作者、AI 评论员）
- 写 Substack 的产品经理
- X 上发 thread 的 builder
- AI 创业者
- 写 newsletter 的 VC

**总数估计**：中文圈 ~500 人，英文圈 ~5000 人。100 个 early adopter 是触手可及的目标。

### Dongzhe 的 5 人种子网络

1. **Dongzhe 自己**（已有 wiki-mcp-server）
2. **后端兄弟 xz1220**（ai-startup repo）
3. **车昊轩**（GameGen-X 学术成果）
4. **第 4 个朋友**（待定）
5. **第 5 个朋友**（待定）

---

## 五、由果倒因 — 6 个 Milestone

```
Year 5  ← Expert Network 终态 (1000+ cards, marketplace)
   ↑
Year 3  ← Card Marketplace + 投资人 DD 工具
   ↑
Year 2  ← Card 间互查 + Routing Layer (KAN 形成)
   ↑
Year 1  ← 1000 张 card,免费,自用价值为主
   ↑
Month 6 ← 100 张 card,内容创作者圈测试
   ↑
Month 3 ← 10 张 card,Dongzhe + 后端兄弟 + 8 个朋友
   ↑
Month 1 ← 1 张 card (Dongzhe),手动 build (已完成)
   ↑
Today  ← wiki MCP server (已上线)
```

**每一步都不需要"突破"，只需要"加一个 card + 加一个 user"。**

---

## 六、产品形态

### v0.1（这周）
- 一个 web landing page (复用 install.html 现有的)
- 重写 README，把 framing 从"BENZEMA's wiki"换成"BENZEMA's Personal Knowledge Card"
- 加 "ask my AI" 的 share-friendly 角度
- 第一篇发布推文

### v0.2（这个月）
- 让 5 个朋友 build 自己的 card（手动 onboarding，不是 SaaS）
- 每张 card 都用同样的 wiki-mcp-server template
- 5 张 card 各自部署到 Railway 免费层
- 录一个 demo 视频展示 "5 个真人，5 张 card，问同一个问题得 5 个视角"

### v0.3（下个月）
- 写一个 30 秒 onboarding CLI / Web 工具
- 输入：博客 URL / X handle / GitHub
- 输出：一个 deployed Card endpoint
- **此时才是真正的 SaaS**

### v1.0（3-6 个月）
- 100 张 card
- 一个简单的 directory（EXPERTS.md 升级版）
- Routing layer 雏形（按主题路由到相关 card）

### v2.0（6-12 个月）
- Card 间互查
- 经济层（按 query 计费 + expert 分成）
- Expert Network 形成

---

## 七、跟之前所有 framing 的对比

| Framing | 冷启动可行性 | 原因 |
|---------|------------|------|
| 个人 LLM Wiki | ❌ 0 | 别人没动机 |
| Knowledge Agent Network | ❌ 0 | 需要先有 100 个节点 |
| Expert Cards for AI（投资 DD 场景）| ⚠️ 弱 | 频次太低 |
| Routing Layer | ⚠️ 中 | 需要先有 N 个 expert |
| **Personal Knowledge Card (for self use)** | **✅ 强** | **每个用户独立有动机，不依赖网络效应** |

**关键差异**：所有失败的 framing 都依赖 "别人来用我"，唯一可行的 framing 是 **"我用我自己"，share 是副作用**。

---

## 八、产品名候选

需要尽快定一个：

| 名字 | 优势 | 劣势 |
|------|------|------|
| **MyMind.cards** | 直接表达"我的大脑变成卡片" | 域名可能贵 |
| **Brain.bio** | 短，bio 暗示个人身份 | 已被占用 |
| **Persona.dev** | dev 友好 | 偏开发者 |
| **CardBase** | 简洁 | 太通用 |
| **Bio.ai** | 极简 | 已被占用 |
| **MeAsContext.io** | 精准描述"我作为 context" | 长 |
| **Think.bio** | 优雅 | 模糊 |
| **AI-Brief.cards** | 描述性 | 长 |

**临时使用**：先继续叫 wiki-mcp-server / BENZEMA's Knowledge MCP，product launch 时再定名字。

---

## 九、决策时刻 — Dongzhe 的 commit

✅ **2026-04-08 commit**：
- 终极态：Expert Network for AI Agents
- 冷启动路径：Personal Knowledge Card（自用驱动）
- 第一个 mile：让 Dongzhe 自己的 wiki MCP 重新 framing 为 Personal Knowledge Card
- 第二个 mile：找 5 个朋友 build 各自的 card
- 第三个 mile：录 demo + 写 launch 推文

**放弃的选项**：
- ❌ 继续优化 wiki distribution（Tier 2/4/5 的 MCP registry / demo video）
- ❌ 直接 build routing layer（太早，没 expert）
- ❌ 直接 build SaaS onboarding（太早，没 PoC）

**保留但延迟的选项**：
- ⏳ Routing Layer（v1.0 之后）
- ⏳ MCP Registry 提交（v0.2 之后）
- ⏳ Expert Network 经济层（v2.0 之后）

---

## 十、立即可做的 3 件事（按优先级）

### Today（30 分钟）
**重写 wiki-mcp-server README**：
- 从 "Plug another person's brain" 改为 "**Plug your brain into your AI**"
- 第一段不再说"BENZEMA 的 wiki"，说"我每天用 AI，但 AI 不知道我是谁，所以我做了这个"
- 加 "ask my AI" share-friendly 角度
- v0.1 example 还是 BENZEMA 自己的 card，但 framing 是 "for self use"

### This Week（1-2 小时）
**写第一篇 launch 推文**：

> 我每天用 Claude，但 Claude 每天都不知道我是谁。
>
> 所以我做了一件事：把我过去一年的研究 compile 成一张
> AI 可读的卡片，接进我的 Claude。
>
> 现在 Claude 知道我研究过 19000 行 agent 源码，
> 49 篇 World Model 论文，28 个概念。
>
> 每次我问问题它都 take into account 我的背景。
> 这是我做的最有用的 quality-of-life 提升。
>
> 想试试? 30 秒 install:
> curl -fsSL ... | bash

发到 X / 即刻 / Reddit r/ClaudeAI / HN Show HN。

### Next Week（半天）
**找 3 个朋友手动 build 第二批 card**：
- 后端兄弟 xz1220（已有 ai-startup repo，零成本）
- 车昊轩（学术成果可 ingest）
- 任何一个写公众号的 AI 圈朋友

每个用 wiki-mcp-server template，半小时部署到自己的 Railway 账号。

---

## 一句话 commit

> **Personal Knowledge Card = 让我的 AI 认识我（自用）+ Share 是副作用（被动 distribution）+ 5 个朋友 = 第一个 network 雏形。**
>
> **不是"build 一个让别人用的产品"，是"build 一个让自己用的产品，碰巧别人也能用"。**

这是 GitHub / Linktree / Cursor 都成功过的路径。
所有"为别人 build"的个人产品都死了。

**Today's commit: walk this path. Stop optimizing the dead one.**
