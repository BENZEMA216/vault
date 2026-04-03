# AI Agent 产品设计 Blog 阅读清单

> 整理于 2026-01-26，聚焦 AI Agent 产品设计、PM 方法论、核心人物观点

---

## 核心概念速览

### 传统 PM vs AI Agent PM

| 传统 PM | AI Agent PM |
|---------|-------------|
| 定义确定性功能 | 设计不确定性交互 |
| 写 PRD 交给工程师 | 做 Context Engineering |
| 关注 UI/UX | 关注 Prompt/工具编排 |
| 验收功能完成度 | 评估 AI 输出质量 |

### Agent 的定义（Simon Willison）

> "An LLM agent runs tools in a loop to achieve a goal."

### PM 的 20-60-20 工作法则

- **前 20%**：PM 定义背景、约束、边界
- **中间 60%**：AI 处理框架性输出
- **后 20%**：PM 注入业务洞察、人性化调整

---

## 公司官方 Engineering Blog

### Anthropic

| 文章 | 链接 | 说明 |
|------|------|------|
| Engineering Blog 主页 | https://www.anthropic.com/engineering | 官方工程博客 |
| Effective Context Engineering for AI Agents | https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents | 2025年9月，Context Engineering 核心方法论 |
| Claude Code Best Practices | https://www.anthropic.com/engineering/claude-code-best-practices | 官方最佳实践 |
| How Anthropic teams use Claude Code | https://www.anthropic.com/news/how-anthropic-teams-use-claude-code | 内部使用案例 |

**核心观点**：
- Context Engineering > Prompt Engineering
- 三原则：保持架构简单、让推理过程可见、确保工具交互可靠
- Claude Code 设计哲学：低层级、不预设观点、可扩展

### Manus

| 文章 | 链接 | 说明 |
|------|------|------|
| Context Engineering for AI Agents | https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus | ⭐ 必读，核心方法论 |
| Wide Research: Beyond Context Window | https://manus.im/blog/manus-wide-research-solve-context-problem | 解决 Context 限制 |
| Understanding Manus Sandbox | https://manus.im/blog/manus-sandbox | 沙盒架构 |
| What We Saw - Future Vision | https://manus.im/blog/what-we-saw-in-the-past-three-months-and-what-we-see-in-the-future | 产品愿景 |
| Blog 主页 | https://manus.im/blog | 全部文章 |

**核心观点**：
> "If model progress is the rising tide, we want Manus to be the boat, not the pillar stuck to the seabed."

- 工具命名设计：所有浏览器工具以 `browser_` 开头，便于状态控制
- 上下文压缩策略：保留 URL/路径，内容可恢复
- 团队重建了 4 次 agent framework，称之为 "Stochastic Graduate Descent"

### Cursor

| 文章 | 链接 | 说明 |
|------|------|------|
| Cursor Blog 主页 | https://cursor.com/blog | 官方博客 |
| Team 介绍 | https://cursor.com/blog/team | 了解团队背景 |
| How Cursor Grows | https://www.news.aakashg.com/p/how-cursor-grows | 增长分析 |

**核心观点**（CEO Michael Truell）：
> "We didn't want to just add AI to existing tools. We wanted to reimagine the entire development environment with AI at its core."

- 汽车比喻：OpenAI/Anthropic 是引擎，Cursor 是整车
- 2025年11月：$2.3B Series D，估值 $29.3B，ARR 超 $1B

### LangChain

| 文章 | 链接 | 说明 |
|------|------|------|
| LangChain Blog 主页 | https://www.blog.langchain.com/ | 官方博客 |
| Reflections on Three Years of Building LangChain | https://www.blog.langchain.com/three-years-langchain/ | Harrison Chase 三年反思 |
| Not Another Workflow Builder | https://www.blog.langchain.com/not-another-workflow-builder/ | 为什么不做可视化 workflow |

**核心观点**（Harrison Chase）：
- Workflows 给你更多可预测性，牺牲自主性
- Agents 给你更多自主性，牺牲可预测性
- 三个关键发展领域：Planning、UX、Memory

### Replit

| 文章 | 链接 | 说明 |
|------|------|------|
| Replit Blog 主页 | https://blog.replit.com/ | 官方产品博客 |

**核心观点**（CEO Amjad Masad）：
> "We don't care about professional coders anymore."

- 2025年战略转向：面向非程序员
- AI Agent 已创建超过 200 万个应用
- ARR 从 $2.7M（2024.4）到 $150M（2025），增长 55x

### OpenAI

| 文章 | 链接 | 说明 |
|------|------|------|
| OpenAI for Developers 2025 | https://developers.openai.com/blog/openai-for-developers-2025/ | 开发者年度总结 |

---

## 核心人物个人 Blog

### Simon Willison ⭐⭐⭐

> Andrej Karpathy 推荐，23年持续高质量输出

| 文章 | 链接 | 说明 |
|------|------|------|
| 主站 | https://simonwillison.net/ | 主博客 |
| Newsletter | https://simonw.substack.com/ | Substack |
| 2025: The Year in LLMs | https://simonwillison.net/2025/Dec/31/the-year-in-llms/ | ⭐ 2025年度总结 |
| The Lethal Trifecta for AI Agents | https://simonw.substack.com/p/the-lethal-trifecta-for-ai-agents | Agent 安全风险 |
| Agent 定义讨论 | https://simonw.substack.com/p/i-think-agent-may-finally-have-a | 什么是 Agent |

**核心观点**：
- "The coding agents pattern is a much bigger deal. The most impactful event of 2025 happened in February, with the quiet release of Claude Code."
- Agent 安全的致命三角：私有数据访问 + 不可信内容暴露 + 外部通信能力
- METR 研究：AI 能完成的任务时长每 7 个月翻倍

### Andrej Karpathy ⭐⭐⭐

| 文章 | 链接 | 说明 |
|------|------|------|
| 主站 | https://karpathy.ai/ | 个人主页 |
| Blog | https://karpathy.bearblog.dev/ | 博客 |
| 2025 LLM Year in Review | https://karpathy.bearblog.dev/year-in-review-2025/ | ⭐ 年度回顾 |

**核心观点**：
> "2025 is not the year of agents. But 2025–2035 will be the decade of agents."

- Claude Code 是第一个令人信服的 LLM Agent 示范
- Anthropic 做对了：本地优先（localhost），而非 OpenAI 的云端容器
- 当前 Agent 局限：缺乏记忆、多模态不足、无持续学习能力

### Swyx (Shawn Wang) ⭐⭐

> AI Engineer 社区核心人物

| 文章 | 链接 | 说明 |
|------|------|------|
| 主站 | https://www.swyx.io/ | 个人主页 |
| Latent Space | https://www.latent.space/ | ⭐ Newsletter + Podcast |
| Agent Engineering | https://www.latent.space/p/agent | Agent 工程方法论 |
| The Tiny Teams Playbook | https://www.latent.space/p/tiny | AI 时代的小团队 |

**核心观点**：
- "Tiny Teams" = ARR（百万美元）> 员工数
- Replit $150M ARR 时只有 70 人，传统 SaaS 需要 700 人

### Harrison Chase (LangChain)

| 文章 | 链接 | 说明 |
|------|------|------|
| 作者页面 | https://www.blog.langchain.com/author/harrison/ | LangChain Blog |

---

## Newsletter / Substack

### Lenny's Newsletter

| 文章 | 链接 | 说明 |
|------|------|------|
| How AI will impact product management | https://www.lennysnewsletter.com/p/how-ai-will-impact-product-management | AI 对 PM 的影响 |
| Make product management fun again with AI agents | https://www.lennysnewsletter.com/p/make-product-management-fun-again-9f6 | PM 的 Agent 指南 |
| The Rise of Cursor | https://www.lennysnewsletter.com/p/the-rise-of-cursor-michael-truell | ⭐ Michael Truell 专访 |
| State of the product job market in 2025 | https://www.lennysnewsletter.com/p/state-of-the-product-job-market-in | PM 就业市场 |

**核心观点**（引用 Andrew Ng）：
> "Product management is becoming the new bottleneck."

- PM 不会消失，但会成为瓶颈
- 软技能（影响力、创造力、同理心）将更重要

---

## VC 投资视角

### Sequoia Capital

| 文章 | 链接 | 说明 |
|------|------|------|
| Konstantine Buhler: Agent Economy Thesis | https://digidai.github.io/2025/11/24/konstantine-buhler-sequoia-capital-agent-economy-trillion-dollar-bet-deep-analysis/ | 万亿美元 Agent 经济 |
| Training Data Podcast - Harrison Chase | https://sequoiacap.com/podcast/training-data-harrison-chase/ | LangChain 创始人访谈 |

**核心观点**（Konstantine Buhler）：
AI 演进三阶段：
1. **Phase 1**：AI 助手（ChatGPT、Copilot）- 响应式
2. **Phase 2**：Agent Swarms - 协作完成复杂任务（2025 年兴起）
3. **Phase 3**：Agent Economy - 完整经济体系

### a16z (Andreessen Horowitz)

| 文章 | 链接 | 说明 |
|------|------|------|
| Big Ideas 2026 | https://a16z.com/newsletter/big-ideas-2026-part-1/ | 2026 大趋势 |
| State of AI: 100 Trillion Token Study | https://a16z.com/state-of-ai/ | AI 状态报告 |

---

## 播客推荐

| 播客 | 链接 | 说明 |
|------|------|------|
| Latent Space Podcast | https://www.latent.space/ | Swyx 主持，AI Engineer 视角 |
| Sequoia Training Data | https://sequoiacap.com/podcast/training-data-harrison-chase/ | Harrison Chase 专访 |
| YC - Amjad Masad | https://www.ycombinator.com/library/Mi-replit-ceo-amjad-masad-coding-agents-autonomy-and-the-future-of-work | Replit CEO 谈 Agent |

---

## 推荐阅读顺序

如果时间有限，按这个顺序读：

1. ⭐ **Manus Context Engineering** - 最实操的 Agent 设计方法
2. ⭐ **Anthropic Effective Context Engineering** - 官方方法论
3. ⭐ **Simon Willison 2025 Year in LLMs** - 全景回顾
4. **Karpathy 2025 LLM Year in Review** - 技术视角
5. **Lenny's Rise of Cursor** - 产品增长视角
6. **Latent Space Agent Engineering** - AI Engineer 视角

---

## 相关内部文档

- [[01-产品概述]] - Creative CoWork 产品定位
- [[02-核心概念]] - 上下文容器、Skills、GENUI 等概念

---

## 更新日志

- 2026-01-26：初版整理
