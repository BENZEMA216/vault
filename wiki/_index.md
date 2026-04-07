# 知识库索引

> 由 LLM 自动维护，请勿手动编辑
> 最后更新：2026-04-06
> 操作记录：[[wiki/log]]

---

## Agent 基础设施

### 概念
- [[concepts/agent-loop]] — Agent 循环架构：感知-思考-行动的核心执行循环
- [[concepts/sub-agent-architecture]] — 子 Agent 架构：主 Agent 委托专业子 Agent 的分层设计
- [[concepts/agent-memory]] — Agent 记忆系统：Beads vs OpenClaw vs Claude Code 三方案对比
- [[concepts/agent-communication]] — Agent 通信：MCP+A2A 协议栈 + 7 种通信模式 + 40+ 产品
- [[concepts/agent-runtime]] — Agent Runtime：状态管理、持久化、重试的基础设施层
- [[concepts/tool-routing]] — 工具路由：Agent 动态选择和调用工具的机制
- [[concepts/world-model]] — 世界模型：五大技术路线 + $80亿+ 融资 + Agent 规划核心组件
- [[concepts/knowledge-agent-network]] — KAN：从个人 LLM Wiki 演化到互联的知识 Agent 网络
- [[concepts/agent-tool-concurrency]] — Agent Tool 并发：sync handle/async future + per-tool isConcurrencySafe
- [[concepts/agent-native-im]] — Agent-Native IM：slock.ai / HiClaw / RockClaw 三种实现
- [[concepts/multi-agent-simulation]] — Multi-Agent Simulation：OASIS 1M agents + MiroFish 产品化

### 地图
- [[maps/agent-infrastructure]] — Agent 基础设施研究全景
- [[maps/agent-communication]] — Agent 通信全景：协议栈、学术脉络、产品三层格局
- [[maps/world-model]] — World Model 全景：技术路线、学术演进、投资格局
- [[maps/agent-harness-implementations]] — Agent Harness 实现全景：kimi-cli / Claude Code / kosong / agent-vault 横向对照

### 关联
- [[connections/communication-to-economy]] — Agent 通信 → Agent Economy：协议到经济基础设施
- [[connections/world-model-to-agent]] — World Model → Agent：规划内核、训练环境、服务空白
- [[connections/memory-to-context]] — 记忆系统 → 上下文工程
- [[connections/harness-to-kan]] — Agent Harness 设计模式 → KAN 的 8 条迁移建议
- [[connections/llm-wiki-to-agent-network]] — LLM Wiki 范式 → KAN 四阶段演化路径

### 数据源
- `raw/articles/agent-communication/` — 协议研究、学术论文、产品格局（3 篇）+ 论文索引（30 篇）
- `raw/articles/world-model/` — 深度研究、论文索引（39 篇）、产品格局
- `raw/articles/memory-research/` — Agent 记忆方案对比
- `raw/papers/agent-infrastructure/` — LLM-in-Sandbox、Tool Search Tool
- `raw/papers/agent-memory/` — MemEvolve、Distilling Feedback 等 9 篇论文+PDF
- `raw/articles/learning-notes/0310-agent-knowledge.md` — MCP 协议、Cursor Agent
- `raw/articles/learning-notes/0410-agent2agent.md` — A2A 协议学习
- `raw/articles/learning-notes/0615-agent-architectures.md` — 多种 Agent 架构对比
- `raw/articles/learning-notes/0620-anthropic-agent-cookbook.md` — Anthropic Agent Cookbook
- `raw/articles/learning-notes/0707-12-factor-agent.md` — 12 Factor Agent
- `raw/articles/learning-notes/0702-scaling-test-time-compute.md` — Scaling Test Time Compute
- `raw/articles/richard-chien/stack.md` — RC 早期技术栈研究
- `raw/articles/richard-chien/deep-profile.md` — RC 完整人物档案（38 repo + botiverse）
- `raw/articles/richard-chien/code-review.md` — 7 个项目 ~19,000 LOC 源码级评审
- `raw/articles/claude-code-source-analysis.md` — Claude Code v2.1.87 cli.js 一手分析
- `raw/articles/agent-tool-concurrency-discussion.md` — Tool 并发模型三种路径完整讨论
- `raw/articles/richard-chien/slock-ai-research.md` — slock.ai agent-native IM 深度研究
- `raw/articles/mirofish-research.md` — MiroFish 多 agent 仿真产品化案例
- `raw/articles/oasis-camel-ai-research.md` — OASIS 1M agents 社会模拟框架
- `raw/articles/oasis-followup-discussion.md` — OASIS 后续讨论与 Dongzhe-fit 角度

---

## LLM Wiki Pattern & 知识库基础设施

### 概念
- [[concepts/llm-wiki-pattern]] — Karpathy 提出的个人知识库范式：raw → LLM 编译 → wiki
- [[concepts/knowledge-agent-network]] — KAN：互联的 Wiki Agent 网络（详见 Agent 基础设施）

### 关联
- [[connections/llm-wiki-to-agent-network]] — LLM Wiki → KAN 四阶段演化路径
- [[connections/harness-to-kan]] — RC harness 设计模式 → KAN 工程实现

### 数据源
- `raw/articles/llm-wiki-pattern.md` — Karpathy gist 的完整文档
- `raw/articles/llm-wiki-product-opportunity.md` — 产品化为 Obsidian 插件的机会分析（评分 23/30）
- `raw/articles/knowledge-agent-network-idea.md` — KAN 完整愿景与四阶段演进
- `raw/articles/knowledge-agent-network-design.md` — 网络生效机制：冷启动、激励、拓扑、Step 0-4 启动路径

---

## Harness Engineering & 开发实践

### 概念
- [[concepts/harness-engineering]] — 驾驭工程：Agent = Model + Harness
- [[concepts/self-verification]] — 自验证：GAN-inspired Generator + Evaluator 分离
- [[concepts/safe-autonomy]] — 安全自治：三层权限 + ACI 设计 + Hooks
- [[concepts/spec-driven-development]] — 规格驱动开发：CLAUDE.md ≤200 行、Interview Pattern
- [[concepts/context-engineering]] — 上下文工程：管理 LLM 上下文的系统方法
- [[concepts/progressive-disclosure]] — 渐进式披露：按需逐层展开信息
- [[concepts/modular-prompt-architecture]] — 模块化 Prompt 架构：116 文件系统

### 地图
- [[maps/harness-engineering]] — Harness Engineering 全景：六大支柱 + Benchmarks

### 关联
- [[connections/harness-to-creative]] — Harness Engineering → Creative CoWork 架构映射

### 数据源
- `raw/articles/harness-engineering/` — awesome list + 深度研究（2 篇）
- `raw/articles/claude-code-research/` — Claude Code SP 详解、架构分析、Skills 分析等（6 篇）
- `raw/articles/claude-code-research/agent-prompt-design/` — 模块化 Prompt 完整参考（5 篇）
- `raw/papers/context-engineering/` — SWE-Pruner、Context Management、Claude Awareness
- `raw/papers/reasoning/` — Multi-Persona Thinking、DrZero

---

## Creative CoWork 产品

### 概念
- [[concepts/context-container]] — 上下文容器：项目级上下文聚合器
- [[concepts/skills-system]] — Skills 系统：模块化能力加载机制
- [[concepts/genui]] — GENUI：Agent 根据上下文动态生成界面
- [[concepts/super-creators]] — 超创：能用 AIGC 赚钱的专业创作者
- [[concepts/creative-agent-design]] — 创意 Agent 设计：发散/收敛模式
- [[concepts/human-in-the-loop]] — 人机协作：人定方向、AI 出活
- [[concepts/init-mechanism]] — /init 机制：Skill 三层推荐 + 干扰规避
- [[concepts/video-agent-workflow]] — 视频 Agent 工作流：5 阶段制作流程
- [[concepts/manga-drama-production]] — 漫剧制作流程：行业痛点与趋势

### 地图
- [[maps/creative-cowork-product]] — Creative CoWork 产品全景
- [[maps/user-research-insights]] — 用户研究洞察

### 关联
- [[connections/user-pain-to-product]] — 用户痛点 → 产品特性映射
- [[connections/claude-code-to-creative]] — Claude Code → Creative CoWork 架构迁移

### 数据源
- `raw/projects/creative-cowork/` — 产品文档 01-06 + 设计稿 + 参考资料（11 篇）
- `raw/projects/user-interview/` — 4 组漫剧团队访谈（原文 + 总结）
- `raw/projects/jimeng-video-agent/` — 视频 Agent SP + 9 个 Skill（11 篇）
- `raw/projects/xian-home/` — 弦的形象设计
- `raw/articles/learning-notes/miaoshua-analysis.md` — 妙刷产品分析
- `raw/articles/learning-notes/opusclip-analysis.md` — OpusClip 分析

---

## 学习笔记 & 其他

### 数据源
- `raw/articles/learning-notes/` — 40+ 篇飞书/外部同步的学习笔记
- `raw/articles/blog/` — AI Agent 产品设计博客阅读清单
- `raw/articles/设计原则.md` — StoryCode Studio CSS 设计 token
- `raw/papers/visual-generation/` — VisPainter、PaperBanana
- `raw/papers/generative-models/` — Generative Modeling via Drifting

---

## Wiki 统计

| 类别 | 数量 |
|------|------|
| 概念文章 | 28 |
| 主题地图 | 7 |
| 关联发现 | 8 |
| Raw 数据源 | ~80 篇 |
| 论文索引 | 69 篇（Agent Comm 30 + World Model 39） |
