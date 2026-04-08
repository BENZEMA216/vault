# Personal Knowledge Card 竞品深度研究

> 来源: 知识库 Query 讨论 (2026-04-08)
> 触发: Dongzhe 质疑"没有产品做这件事"的断言
> 结论: **断言错了。空间比想象的拥挤，但有一个非常具体的 wedge 仍然空着。**

---

## 核心修正

之前的 product spec 写的是：

> "没有一个产品解决"让我的 AI 真正了解我"这件事"

**这是错的。** 任何懂行的人 30 秒 Google 就能找到 sage-wiki / Mem0 / alanagoyal/mcp-server，说这话会丢信任。

正确表述应该是：

> "有 5+ 个产品在尝试解决这件事，但没有一个同时做对 5 个关键维度。**真正空着的 wedge 是 share-side，不是 self-use side**。"

---

## 一、5 个测试维度

| 维度 | 含义 |
|------|------|
| **D1 自用优先** | 自用是 build 动机,share 是副作用 |
| **D2 MCP/开放协议** | 暴露给任意 AI,不绑定 Claude/ChatGPT |
| **D3 Compiled wiki** | LLM 预编译的结构化知识,不是 RAG raw text |
| **D4 Long-form depth** | 远超 Custom Instructions 的 1500 字符 |
| **D5 Auto-ingest existing assets** | 从 blog/X/GitHub 自动摄取,不需要手动 entry |

---

## 二、Top 10 直接/邻近竞品

| # | 产品 | D1 | D2 | D3 | D4 | D5 | 相似度 |
|---|------|----|----|----|----|----|----|
| 1 | **sage-wiki** (xoai/sage-wiki, Karpathy LLM Wiki 开源实现) | ✅ | ✅ 14 MCP tools | ✅ LLM-compiled | ✅ | ❌ 手动 ingest | **9/10** |
| 2 | **alanagoyal/mcp-server** (从 alanagoyal.com/basecase.vc 用 Stagehand 自动抓) | ✅ | ✅ MCP | ⚠️ 网站抽取非 wiki | ✅ | ✅ | **8/10** |
| 3 | **Jay Ozer "I Built an MCP About Myself"** (mcp_jozer, 14 tools, FastMCP Cloud) | ✅ | ✅ | ⚠️ JSON 非 wiki | ⚠️ | ❌ | **7/10** |
| 4 | **Mem0 / OpenMemory MCP** (2025-06 Cloud, 9 MCP tools) | ✅ | ✅ | ❌ raw memories | ✅ | ❌ | **6/10** |
| 5 | **Obsidian + Smart Connections + MCP bridge** | ✅ | ✅ | ❌ RAG | ✅ | ⚠️ | **6/10** |
| 6 | **Khoj** (YC, 开源 second brain) | ✅ | ⚠️ 自有协议 | ❌ RAG | ✅ | ⚠️ Notion/Obsidian sync | **5/10** |
| 7 | **Pieces LTM-2** (9 个月本地记忆 + MCP) | ✅ | ✅ | ❌ OS capture | ✅ | ❌ | **5/10** |
| 8 | **Letta / MemGPT** (stateful agent framework) | ⚠️ dev-facing | ⚠️ REST 非 MCP-first | ⚠️ memory blocks | ✅ | ❌ | **4/10** |
| 9 | **Personal.ai** (Model-1 Twin, $40/mo) | ❌ 封闭自有 AI | ❌ | ⚠️ | ✅ | ⚠️ | **3/10** |
| 10 | **Claude Projects / CLAUDE.md / ChatGPT Memory** | ✅ | ❌ 锁定单一 AI | ⚠️ | ⚠️ | ❌ | **3/10** |

### 其他被排除但应该知道的

| 产品 | 为什么不算直接竞品 |
|------|----------------|
| **Limitless / Rewind** | 已被 Meta 2025-12 收购，硬件停产；capture-everything 路线 |
| **DollhouseMCP** | AI persona/skills marketplace，不是个人上下文 |
| **Day One Journal MCP / Knowledge Graph MCP** | 暴露已有数据源，不涉及 compiled wiki |
| **llms.txt** | 给"AI 爬取网站"用的，不是个人级、不是 MCP |
| **Alejandro Aboy's Substack MCP** (2025-10) | **方向接近但做反了**：reader-side（任何读者抓任何作者），不是 author-side（作者主动维护权威 card） |

---

## 三、关键发现：D3 + D5 是真空地带

**没有一个产品同时命中 D3（compiled wiki）+ D5（auto-ingest existing assets）+ MCP 暴露**。

- D2 MCP 暴露：**已饱和**，不是差异点
- D4 long-form：**已饱和**，不是差异点
- D1 self-use：**已饱和**，不是差异点
- D3 compiled wiki：**只有 sage-wiki 真做到**，但需要手动 ingest
- D5 auto-ingest：**只有 alanagoyal/mcp-server 做到**，但内容不 compile，是抽取

**这就是 Karpathy 在 2026-04 那篇 LLM Wiki gist 里说的"incredible new product"还没人 build 出来的位置。**

---

## 四、最重要的发现：self-use 不是差异点，share-side 才是

**这彻底推翻了昨晚的 product spec。**

之前的 framing："Personal Knowledge Card for self-use, share is side effect."

**问题**：self-use 个人 MCP 已经被 10+ 个开发者 hack 出来了：
- sage-wiki 已经 215 stars，开源
- Mem0/OpenMemory Cloud 已经商业化
- Pieces LTM-2 已经在卖
- alanagoyal/jozer/evangstav 都有自己的个人 MCP demo
- Karpathy 的 LLM Wiki gist 公开后引爆了一个小型浪潮
- 任何命令行 hacker 1 小时用 FastMCP 就能搞一个

**所以 self-use first 这个定位 = 加入红海**。

### 真正空着的 wedge

> **"让任何人的 AI 在谈论你的话题时，自动调用你写过的东西、你的观点、你的研究"**
>
> = author-controlled, compiled-wiki 形态, 从已有创作资产自动 build 的 MCP endpoint
>
> = **创作者的 AI 引用源 / Personal IP 在 AI-native 时代的分发协议**

不是 "Personal Knowledge Card for my own AI"，是 **"Personal Knowledge Endpoint for the world's AI"**。

### 翻转后的 framing

**Old framing (red ocean)**:
- Self-use first
- Share is side effect
- "让我的 AI 认识我"

**New framing (blue ocean)**:
- **Share-first, self-use is bonus**
- "**让世界的 AI 在谈到你时引用你**"
- "Karpathy 说 LLM Wiki 是下个伟大产品。sage-wiki 做到了 self-use 部分。**但 share-side 没人做。**"

### 类比解释

| 旧 framing 类比 | 新 framing 类比 |
|----------------|----------------|
| "GitHub: Git hosting for me" | "GitHub: Public code so the world can find and use it" |
| "Linktree: bio link for me" | "Linktree: my links for my followers" |
| **强调"我自己用"** | **强调"世界引用我"** |

实际上 GitHub / Linktree **从一开始就是 share-first**。Self-use 是动机，但**产品形态是 public-by-default**。我之前误读了它们。

---

## 五、Substack MCP 反向案例

`Alejandro Aboy's Substack MCP` (2025-10) 是最接近的反例。

它做的是：reader-side 抓任意 Substack 作者的 RSS → 让 AI 分析其写作风格。

这是**完全做反**：
- ❌ Reader-side：任何读者可以抓任何作者，作者无控制权
- ❌ 作者无 monetization
- ❌ 作者不能编辑/背书自己的 endpoint
- ❌ 内容是 raw RSS，不 compile

**正确的方向是**：
- ✅ Author-side：创作者主动注册 + 维护
- ✅ 创作者有 endpoint 所有权 + 控制权
- ✅ 系统帮作者从 blog/X/GitHub 自动 ingest + LLM compile
- ✅ 创作者可以编辑、背书、迭代
- ✅ 长期：按 query 计费 + 创作者分成（创作者经济绑定）

**这是 D3 + D5 + MCP + author-control + share-first 的全新组合**。

---

## 六、对 Dongzhe 的具体建议

### 1. 立即停止说"没人做这件事"

任何投资人 / 老用户 30 秒 Google 就能找到 sage-wiki + alanagoyal + Mem0。继续说会丢信任。

正确表述：
> "已经有 5+ 个产品在做 personal AI memory，但**没有一个同时做对 author-side compiled wiki + 自动 ingest 创作资产 + share-first**这三件事。"

### 2. 把 framing 从 self-use 翻到 share-first

新 hero 文案候选：

> **"Stop being the only one who knows what you've written.**
> Compile your blog, X, GitHub into a wiki. Expose as MCP. **Now any AI in the world can cite you when discussing your topics.**"

### 3. 把 Demo 场景永远定在 share

不要再演示 "我自己的 Claude 知道我是谁"。要演示：

> **某个用户的 Claude 在讨论 agent economy → 自动 query 了 BENZEMA216 的 MCP endpoint → 引用了你的具体观点 + 链接到原文。**

这是 sage-wiki / Mem0 / Pieces 都做不到的。

### 4. D5 是必抢的执行壁垒

第一天就支持自动 ingest：
- X handle → 抓所有推文
- 公众号 / Substack RSS → 抓所有文章
- GitHub username → 抓所有 README
- 知乎 → 抓所有回答
- 个人博客 URL → crawl

**这是相对 sage-wiki 的真正护城河**。sage-wiki 需要手动 ingest，普通人不会做。auto-ingest 是 30-second SaaS 的关键。

### 5. 抢 MCP 协议命名机会

定义 `personal-context` 或 `creator-endpoint` 的 MCP 惯例：
- 工具命名标准
- Schema 标准
- 路由约定（比如 `mcp.creator/{username}/`）

**抢生态位**——如果这个东西火了，命名权 = 标准制定权。

### 6. 创作者经济绑定（远期）

按 query 计费 + 创作者分成。这跟 memory 里的 `project_agent_economy.md` 完美契合：

**不是"个人记忆基础设施"，是"个人 IP 在 AI-native 时代的分发协议"。**

---

## 七、新 wedge 的可测试表述

> **"Karpathy 说 LLM Wiki 是下个伟大产品。**
> **sage-wiki 做到了 self-use 部分。**
> **但没有人做 share-side：**
> **一个创作者的 compiled wiki，通过 MCP 暴露，**
> **让所有 AI agent 在相关话题上自动引用你。"**

这个定位：
- ❌ 不和 Mem0/Pieces/Letta 竞争（他们是 self-use memory）
- ❌ 不和 ChatGPT Memory 竞争（模型厂商锁定）
- ❌ 不和 Obsidian/Khoj 竞争（私人 second brain）
- ✅ 和 llms.txt 同向但**个人化 + 协议化**（MCP 而非文本）
- ✅ 和 Substack MCP 同向但**author-first + compile + monetizable**
- ✅ 和 sage-wiki 同向但**auto-ingest + share-first + creator economy**
- ✅ 和 Agent Economy 研究方向天然契合

---

## 八、需要回写的文档

1. ✏️ `personal-knowledge-card-product-spec.md` — 加 "Competitive Reality" 章节，承认 self-use 是红海，pivot 到 share-first
2. ✏️ `personal-knowledge-card-launch-posts.md` — 改所有 "我每天用 AI，AI 不认识我" 的开头，改成 "我写过的东西，世界的 AI 不知道"
3. ✏️ `wiki-mcp-server/README.md` — hero 从 "Plug a brain into your AI" 改成 share-first 表述
4. ✏️ `wiki-mcp-server/public/install.html` — landing page 同步
5. ✏️ `wiki/log.md` — 记录这次 framing 修正

---

## 一句话

> **昨晚的 commit 是错的。Self-use 是红海，share-side 才是真正的蓝海。**
>
> **新 commit**：Personal Knowledge Card 的产品形态 = "**creator's AI-callable endpoint**"，价值是"**让世界的 AI 在谈到你的话题时引用你**"，护城河是"**author-side + auto-ingest + LLM-compile + creator-economy 绑定**"。
>
> 这是过去 4 天里第一次找到一个**同时不和别人竞争**的定位。
