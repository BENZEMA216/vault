# Personal Knowledge Card — Launch Posts

> 来源: 知识库 Query 讨论 (2026-04-08)
> 用途: 第一波 distribution。配套 personal-knowledge-card-product-spec.md。

---

## 一、X / Twitter（英文，一个 thread）

### Tweet 1（hook）

```
I use Claude every day.
But Claude doesn't know who I am.

Every new conversation I'd repeat:
"I'm a PM. I research AI agents. Please consider my background."

So I built something stupid simple to fix this.
🧵
```

### Tweet 2（what I built）

```
A Personal Knowledge Card.

It's a live MCP server holding my entire research brain:
· 19,000 lines of agent source-code reviews
· 49 indexed World Model papers
· 28 compiled wiki concepts
· 8 cross-domain connections

I plug it into my Claude. Now Claude knows me.
```

### Tweet 3（before/after）

```
Before:
me: "Help me think about agent harness design"
Claude: "Sure, an agent harness is the runtime infrastructure..."
me: *sighs, pastes 2000 words of context*

After:
me: "Help me think about agent harness design"
Claude: *already knows I read 19k LOC of kimi-cli + Claude Code,
        gives me a comparison and a sharper angle in 30 seconds*
```

### Tweet 4（the bigger insight）

```
I thought I was building "a wiki for others to query."
I was wrong. Nobody wants to query someone else's wiki.

What works:
· You build a Card so YOUR OWN AI knows you.
· Sharing it is a free side effect.

GitHub didn't start as "a marketplace."
It started as "git hosting for me."
Same pattern.
```

### Tweet 5（you can plug into mine if you want）

```
You can try mine. 30 seconds:

curl -fsSL https://benzema-wiki-mcp-production.up.railway.app/install | bash

Detects Claude Code / Cursor / Cline. Auto-installs.

Then ask your Claude:
"What does BENZEMA think about agent tool concurrency?"

Claude will pull from my real notes, with citations.
```

### Tweet 6（the call to build your own）

```
But honestly?

Don't plug into mine. Build your own.

Take your blog posts / X archive / research notes.
Compile them with Claude Code into a wiki.
Deploy as MCP server (template here, Railway free tier works).

Your AI will know you forever.

Repo: github.com/BENZEMA216/wiki-mcp-server
```

### Tweet 7（the vision — small footnote）

```
PS: When 100 people have built their own Cards,
plugging into "the network of Cards" becomes interesting
in a way no single Card could be.

But that's downstream.
Today: just stop reintroducing yourself to your AI.

It's a real, daily pain. The fix takes 30 minutes.
```

---

## 二、即刻（中文）

```
我每天用 Claude，但 Claude 每天都不认识我。

每次开新对话都要重复一遍："我是产品经理，做 AI agent 方向的，
研究过 World Model，对 Agent Communication 有自己的看法，
请考虑我的背景，不要给我泛泛建议。"

每次写这段我都觉得自己像在给 AI 填表。

所以做了一件很简单的事：

把过去一年的研究 compile 成一张 AI 可读的 Card，接进 Claude。

现在 Claude 知道我研究过 19000 行 agent 源码、49 篇 World Model 论文、
28 个概念。每次问它问题它都自动 take into account 我的背景。

是过去一年我做的最有用的 quality-of-life 升级。

最关键的认知翻转是：

我原本以为我是在 build "一个让别人来查的 wiki"。
错了。没人会来查别人的 wiki。

正确的逻辑是：
我 build 它是为了**我自己的 AI 认识我**。
Share 出去是免费副作用。

GitHub 不是从 marketplace 开始的，是从"我自己用的 git hosting"开始的。
Linktree 不是从 link aggregator 开始的，是从"创作者自己用的 bio link"开始的。
同一个 pattern。

如果你想试我的 Card，30 秒安装：
curl -fsSL https://benzema-wiki-mcp-production.up.railway.app/install | bash

但说实话：你应该建一张自己的，不该用我的。
拿你已经在写的公众号 / X / GitHub README / 研究笔记，
用 Claude Code 编译一下，部署成 MCP server。
Template 在这里：github.com/BENZEMA216/wiki-mcp-server

你自己的 AI 应该认识你。
```

---

## 三、Reddit r/ClaudeAI（短）

**Title**: I built a "Personal Knowledge Card" — a live MCP server so my Claude finally knows who I am

**Body**:

```
Quick context: I use Claude every day, but every new conversation I'd
repeat the same setup ("I'm a PM, I research agents, please consider
my background, etc."). It got annoying.

So I built a Personal Knowledge Card — basically my entire research
brain compiled into a wiki and exposed as an MCP server. Plugged it
into my Claude Code via the standard `mcpServers` config, and now
Claude actually knows me. Citations, source paths, the whole thing.

It contains:
- ~19,000 LOC of agent source-code reviews (kimi-cli, Claude Code, kosong, etc.)
- 49 indexed World Model papers with citation counts
- 28 wiki concepts I've compiled this past year
- ~80 raw research sources

What surprised me most: the biggest value is for ME, not for sharing.
Custom Instructions max at 1500 chars, which is way too small for
real research depth. MCP servers solve this perfectly.

If you want to try it (or just play with it):
curl -fsSL https://benzema-wiki-mcp-production.up.railway.app/install | bash

Auto-detects Claude Code / Cursor / Cline.

But honestly the bigger lesson is: build your own. Your AI should
know you, not me. The template is open source:
github.com/BENZEMA216/wiki-mcp-server

Ask me anything about how it's structured, the LLM Wiki pattern,
or why I think this is the cold-start path to a real "expert network
for AI agents."
```

---

## 四、HackerNews Show HN（极简）

**Title**: Show HN: Personal Knowledge Card – an MCP server so my Claude finally knows me

**Body**:

```
I use Claude every day. Custom Instructions caps at 1500 characters.
That's not enough for any real research depth.

So I compiled my year of research into a wiki and exposed it as an
MCP server. Now my Claude knows my background, my views, my source
material. It's the highest-leverage quality-of-life upgrade I've
made this year.

The trick: don't think of it as "a wiki for others to query." Think
of it as "my AI's long-term memory." Sharing is a free side effect.

You can plug into mine in 30 seconds:
curl -fsSL https://benzema-wiki-mcp-production.up.railway.app/install | bash

Or build your own from the template:
https://github.com/BENZEMA216/wiki-mcp-server

Tech: Express + StreamableHTTP MCP transport, JSONL access logs,
auto-syncs from a public GitHub vault every 10 min, deploys to
Railway free tier. Privacy-conscious telemetry (hashed IPs, classified
UAs, query previews truncated to 60 chars).

Built on Karpathy's LLM Wiki pattern:
https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

Happy to discuss the cold-start problem, the routing layer plans,
or why this ends up being the right path to an "expert network for
AI agents" (which is the eventual game).
```

---

## 五、发布顺序与策略

### Day 0（今天/明天）
1. **即刻** 发中文版（最熟的 audience，最低风险，先看反应）
2. 监控 `/stats`：第一批 install 数 + 来自即刻的 referer

### Day 1（如果即刻反应正面）
3. **X 发英文 thread**（Tweet 1-7）
4. 监控 `/stats`：unique IPs 是否上升

### Day 2-3
5. **Reddit r/ClaudeAI** 发短帖
6. **HN Show HN** 发极简版（HN 反 hype，要克制）

### Day 4-7
7. 联系 5 个朋友手动 onboard（后端兄弟 / 车昊轩 / 任何 AI 圈朋友）
8. 7 天后看数据复盘

---

## 六、关键消息纪律

无论发到哪里，都要遵守：

✅ **必说**:
- "我每天用 AI，AI 不认识我，所以做了这个"
- "为自己用的，share 是副作用"
- "你应该建你自己的"

❌ **不能说**:
- "Knowledge Agent Network"（太抽象）
- "GLG for AI"（terminal vision，太远）
- "Expert Network"（是终态，不是当前状态）
- "想成为 Agent 经济基础设施"（吹牛）
- 任何把当前阶段往未来 vision 拉的措辞

**当前的故事就是 personal pain → personal fix → 顺手 share。其他都是噪音。**

---

## 七、成功信号 / 失败信号

### Day 1 后看
- ✅ **成功**: `/stats` 显示 ≥10 unique_ips（说明有人真试了）
- ⚠️ **中等**: 1-9 unique_ips
- ❌ **失败**: 0-1 unique_ips

### Day 7 后看
- ✅ **成功**: ≥3 个朋友主动想建自己的 Card
- ✅ **成功**: 任何一个陌生人 fork 了 wiki-mcp-server
- ⚠️ **中等**: 有 install 但没人 share / fork
- ❌ **失败**: 0 个 fork + 0 个朋友建 Card

### 如果失败
回到 product spec 重新审视 framing。可能需要：
- 改产品名（"Personal Knowledge Card" 也许不够直接）
- 改 hero 文案
- 改 distribution 渠道（也许不该发即刻，该发 Indie Hackers）

### 如果成功
Day 7 之后开始 Tier 6（手动 onboard 5 个朋友 → demo 视频 → v0.2 真正的 SaaS onboarding 工具）

---

## 一句话

> **第一次尝试不是在 build SaaS，是在测试 framing。**
> 如果"Personal Knowledge Card / 让我的 AI 认识我"这个 framing 能击中 ≥10 个陌生人，
> 那这条路就成立。如果不能，回到 product spec 改 framing 再试。
