# Agent 基础设施研究全景

> 从 Agent 循环到记忆系统，从 Prompt 工程到工具路由——构建 AI Agent 的完整技术栈。

## Agent 循环架构

Agent 的核心运行机制：感知-思考-行动-反馈的持续循环。

| 概念 | 说明 | 来源 |
|------|------|------|
| **[[concepts/agent-loop]]** | 单循环 + 智能委托，Claude Code 验证的架构范式 | [[raw/articles/claude-code-research/Claude Code 架构分析]] |
| **[[concepts/sub-agent-architecture]]** | 分层 Agent 委托：Main Agent → Explore/Plan/General Sub-Agent，最多一层嵌套 | [[raw/articles/claude-code-research/Claude Code 架构分析]] |
| **[[concepts/creative-agent-design]]** | 在标准循环上叠加发散/收敛模式，适配创意工作 | [[raw/projects/creative-cowork/reference/Creative Agent System Prompt 模板]] |

### 关键论文

- [[raw/papers/agent-infrastructure/LLM-in-Sandbox 论文笔记]] — Agent 沙盒执行环境设计
- [[raw/papers/agent-infrastructure/Tool Search Tool 笔记]] — 工具发现与路由的自动化

### 学习笔记

- [[raw/articles/learning-notes/0615-agent-architectures]] — Agent 架构综述
- [[raw/articles/learning-notes/0620-anthropic-agent-cookbook]] — Anthropic 官方 Agent 构建指南
- [[raw/articles/learning-notes/0707-12-factor-agent]] — 12-Factor Agent 设计原则
- [[raw/articles/learning-notes/0710-agent-how]] — Agent 实现方法论
- [[raw/articles/learning-notes/0719-manus]] — Manus Agent 架构分析
- [[raw/articles/learning-notes/0804-oppo-agent]] — OPPO Agent 实践

## Prompt 工程

从单体 Prompt 到模块化架构的演进。

| 概念 | 说明 | 来源 |
|------|------|------|
| **[[concepts/modular-prompt-architecture]]** | 110+ 文件模块化 SP 系统，按需动态组装 | [[raw/articles/claude-code-research/Claude Code System Prompt 详解]] |
| **[[concepts/creative-agent-design]]** | 创意 Prompt 模块化：system/ + tools/ + agents/ + skills/ + reminders/ | [[raw/projects/creative-cowork/reference/Creative Agent System Prompt 模板]] |

### 对标研究

- [[raw/articles/claude-code-research/Agent System Prompt 研究]] — Claude Code / Cursor / Devin / Manus 四大 Agent SP 对比
- [[raw/articles/claude-code-research/Claude Code System Prompt 详解]] — 116 文件完整拆解
- [[raw/articles/learning-notes/0807-cc-prompt]] — Claude Code Prompt 深度分析
- [[raw/articles/learning-notes/0407-define-agent-requirement]] — Agent 需求定义方法

## 上下文管理

Agent 能力的上限取决于上下文质量。

| 概念 | 说明 | 来源 |
|------|------|------|
| **[[concepts/context-container]]** | 项目级上下文聚合器，Creative CoWork 的核心 | [[raw/projects/creative-cowork/01 Creative CoWork - DEMO思路]] |
| **[[concepts/context-engineering]]** | Context Engineering > Prompt Engineering，Anthropic/Manus 共识 | [[raw/articles/blog/AI Agent 产品设计 Blog 阅读清单]] |
| **[[concepts/progressive-disclosure]]** | 信息按需分层加载，避免上下文过载 | [[raw/projects/creative-cowork/0205 - init 机制设计]] |

### 论文前沿

- [[raw/papers/context-engineering/Claude Context Awareness 机制]] — Token 预算感知：模型知道自己还剩多少 token
- [[raw/papers/context-engineering/SWE-Pruner 论文笔记]] — 0.6B 轻量模型动态裁剪代码上下文，节省 23-54% token
- [[raw/papers/context-engineering/Context Management 技术方案汇总]] — 滑动窗口、LLM 摘要、压缩、分层检索方案对比

### 学习笔记

- [[raw/articles/learning-notes/0702-scaling-test-time-compute]] — Test-time Compute 与上下文管理的关系
- [[raw/articles/learning-notes/0709-websailor]] — Web 场景下的上下文管理

## 记忆系统

跨会话记忆是 Agent 最强烈的未满足需求之一。

| 概念 | 说明 | 来源 |
|------|------|------|
| **[[concepts/agent-memory]]** | 三种记忆范式：任务编排（Beads）、知识事实（OpenClaw）、规范指令（Claude Code） | [[raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code]] |

### 论文前沿

- [[raw/papers/agent-memory/MemEvolve_论文笔记]] — 记忆系统的元进化：不仅积累经验，还动态调整记忆架构本身
- [[raw/papers/agent-memory/Distilling Feedback into Memory-as-a-Tool]] — 将评估反馈转化为可持久复用的记忆工具（RAG + 自动评估闭环）

### 学习笔记

- [[raw/articles/learning-notes/0823-multimodal-agent-long-term-memory]] — 多模态 Agent 长期记忆
- [[raw/articles/learning-notes/0830-memory-r1]] — Memory R1 研究
- [[raw/articles/learning-notes/0310-agent-knowledge]] — Agent 知识管理

## 工具系统

Agent 的能力边界由工具决定。

| 概念 | 说明 | 来源 |
|------|------|------|
| **[[concepts/skills-system]]** | SKILL.md + YAML frontmatter 标准，模块化能力加载 | [[raw/articles/claude-code-research/Superpowers Skills 架构分析]] |
| **[[concepts/init-mechanism]]** | /init 三层推荐：项目类型 + 用户历史 + 社区热门 | [[raw/projects/creative-cowork/0205 - init 机制设计]] |
| **[[concepts/tool-routing]]** | 工具发现与路由，自动匹配最适合当前任务的工具 | [[raw/papers/agent-infrastructure/Tool Search Tool 笔记]] |

### Skills 实现参考

- [[raw/articles/claude-code-research/Creative CoWork Skills 架构启发]] — 多 Skills 实现的架构启发
- [[raw/articles/claude-code-research/Superpowers Skills 架构分析]] — Superpowers 的 Skill 发现和加载机制
- [[raw/articles/claude-code-research/为 Claude 构建 Skills 完整指南]] — 从零构建 Skill 的完整流程

## 视频 Agent 实例

即梦视频 Agent 是创意 Agent 的核心实践案例。

| 文件 | 内容 |
|------|------|
| [[raw/projects/jimeng-video-agent/视频AGENT 主SP]] | 视频 Agent 主 System Prompt |
| [[raw/projects/jimeng-video-agent/视频AGENT 主SP（优化版）]] | 优化后版本 |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_构思]] | 构思阶段 Skill |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_创意与剧本（优化版）]] | 创意与剧本 Skill |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_参考素材生成（优化版）]] | 参考素材生成 Skill |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_故事板生成]] | 故事板生成 Skill |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_配乐方案（优化版）]] | 配乐方案 Skill |
| [[raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_bgm]] | BGM 搜索 Skill |

### 学习笔记

- [[raw/articles/learning-notes/0326-sora-feed-agent]] — Sora Feed Agent 分析
- [[raw/articles/learning-notes/0826-filmaster]] — Filmaster 视频 Agent 分析
- [[raw/articles/learning-notes/0513-lovart]] — Lovart 创意 Agent 分析

## 论文前沿（按方向）

### 推理与规划

- [[raw/papers/reasoning/DrZero_论文笔记]] — DrZero 推理研究
- [[raw/papers/reasoning/Multi-Persona Thinking 论文笔记]] — 多角色思维方法

### 视觉生成

- [[raw/papers/visual-generation/PaperBanana 论文笔记]] — PaperBanana 视觉生成
- [[raw/papers/visual-generation/VisPainter 论文笔记]] — VisPainter 视觉绘画
- [[raw/papers/generative-models/Generative Modeling via Drifting - 笔记]] — 生成式漂移建模

### 行业动态

- [[raw/articles/learning-notes/0416-ai-second-half]] — AI 下半场
- [[raw/articles/learning-notes/0527-beyond-agent-tech]] — 超越 Agent 技术
- [[raw/articles/learning-notes/0701-era-of-exploration]] — 探索时代
- [[raw/articles/learning-notes/0621-ak-software-changing]] — 软件正在改变
- [[raw/articles/learning-notes/1009-openai-dev-day]] — OpenAI Dev Day
- [[raw/articles/learning-notes/0929-claude-release]] — Claude 新版本发布

## 核心洞察

1. **架构简单性优先**：Claude Code 用单循环 + 委托胜过复杂的多 Agent 系统
2. **上下文 > 模型**：Context Engineering 成为新共识，好的上下文比更强的模型更有效
3. **记忆分层是必须的**：热数据全量注入、冷数据按需检索，无限积累会淹没上下文窗口
4. **工具模块化**：Skills 系统让 Agent 能力可组合、可进化，是平台化的关键
5. **创意循环需要发散**：标准 Agent Loop 偏收敛，创意工作需要显式的发散阶段

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
