# 知识库索引

> 由 LLM 自动维护，请勿手动编辑
> 最后更新：2026-04-03

---

## 概念文章 (Concepts)

- [[concepts/context-container]] — 上下文容器：Creative CoWork 核心抽象，将项目状态、资产、对话封装为可持久化的工作单元
- [[concepts/agent-loop]] — Agent 循环架构：感知-思考-行动的核心执行循环，Claude Code 单循环 + 智能委托模式
- [[concepts/skills-system]] — Skills 系统：可插拔的能力模块标准，Anthropic 官方规范 + Superpowers 实践
- [[concepts/progressive-disclosure]] — 渐进式披露：按需暴露功能复杂度，降低用户认知负荷的交互设计原则
- [[concepts/genui]] — 动态生成界面（GENUI）：Agent 根据上下文实时生成 UI 组件，替代静态模板
- [[concepts/super-creators]] — 超创用户画像：独立导演/小团队创作者，追求全链路 AI 协作的核心目标用户
- [[concepts/context-engineering]] — 上下文工程：管理 LLM 可用信息的系统方法，包括裁剪、摘要、动态加载等技术
- [[concepts/agent-memory]] — Agent 记忆系统：短期/长期/元进化记忆架构，Beads vs OpenClaw vs Claude Code 对比
- [[concepts/modular-prompt-architecture]] — 模块化 Prompt 架构：将 System Prompt 拆分为 identity/tools/skills/reminders 等独立模块
- [[concepts/video-agent-workflow]] — 视频 Agent 工作流：即梦视频 Agent 5 阶段创作流程（构思→剧本→素材→视频→配乐）
- [[concepts/manga-drama-production]] — 漫剧制作流程：角色一致性、上下文分散、"抽卡"效率等行业核心痛点
- [[concepts/creative-agent-design]] — 创意 Agent 设计：面向创作场景的 Agent 架构，强调情境理解与审美判断
- [[concepts/human-in-the-loop]] — 人机协作：创作流程中人类审核/干预的机制设计，平衡自动化与控制感
- [[concepts/tool-routing]] — 工具路由：Agent 根据意图动态选择和调用工具的机制，含 Tool Search 延迟加载
- [[concepts/sub-agent-architecture]] — 子 Agent 架构：主 Agent 委托专业子 Agent 执行特定任务的分层设计
- [[concepts/init-mechanism]] — /init 机制：Skill Focus Profile + 三层推荐 + 干扰规避的会话初始化设计
- [[concepts/harness-engineering]] — Harness Engineering: Agent 运行环境的可靠性工程
- [[concepts/self-verification]] — Self-Verification: Agent 自验证机制（single highest-leverage intervention）
- [[concepts/safe-autonomy]] — Safe Autonomy: 减少审批摩擦同时保持行为可控
- [[concepts/agent-runtime]] — Agent Runtime: 状态管理、持久化、重试的基础设施层
- [[concepts/spec-driven-development]] — Spec-Driven Development: 规格驱动的 Agent 开发模式
- [[concepts/agent-communication]] — Agent Communication（Agent 通信）：Agent 间交换信息、协调行为的机制和协议，MCP+A2A 双协议栈 + 7 种通信模式
- [[concepts/world-model]] — World Model（世界模型）：AI 系统学习的世界内部表征，五大技术路线 + $80亿+ 融资 + Agent 规划核心组件

---

## 主题地图 (Maps)

- [[maps/creative-cowork-product]] — Creative CoWork 产品全景：从愿景到 MVP 的完整产品脉络，"创作者的 Claude Code"
- [[maps/agent-infrastructure]] — Agent 基础设施研究：Agent 循环、记忆、上下文、工具、Prompt 等底层技术全景
- [[maps/user-research-insights]] — 用户研究洞察：4 组漫剧团队访谈 + 产品分析汇总的用户需求图谱
- [[maps/harness-engineering]] — Harness Engineering 全景：六大支柱 + Benchmarks
- [[maps/agent-communication]] — Agent Communication 全景：协议栈（MCP→A2A→ANP→AGNTCY→UCP）、学术脉络、产品三层格局、论文索引
- [[maps/world-model]] — World Model 全景：五大技术路线、学术演进（2018→2026）、产品三层、39 篇论文索引、$80亿+ 投资格局

---

## 关联发现 (Connections)

- [[connections/user-pain-to-product]] — 用户痛点 → 产品特性映射：访谈痛点如何转化为 Creative CoWork 功能设计
- [[connections/claude-code-to-creative]] — Claude Code → Creative CoWork 架构迁移：从编码 Agent 到创作 Agent 的改造路径
- [[connections/memory-to-context]] — 记忆系统 → 上下文工程关联：持久化记忆如何服务于上下文窗口管理
- [[connections/harness-to-creative]] — Harness Engineering → Creative CoWork 架构映射
- [[connections/communication-to-economy]] — Agent Communication → Agent Economy 关联：协议/学术模式到经济基础设施的映射
- [[connections/world-model-to-agent]] — World Model → Agent 关联：world model 作为 Agent 规划内核、训练环境、服务 API 空白分析

---

## Raw 数据源索引

### papers/ (论文)

#### reasoning/
- `raw/papers/reasoning/Multi-Persona Thinking 论文笔记.md` — 多角色 Prompt 消除 LLM 偏见（target + counter + neutral 视角）
- `raw/papers/reasoning/DrZero_论文笔记.md` — 无训练数据的自进化搜索 Agent（Proposer-Solver 双 Agent）

#### context-engineering/
- `raw/papers/context-engineering/SWE-Pruner 论文笔记.md` — 0.6B 轻量模型做动态上下文裁剪，节省 23-54% token
- `raw/papers/context-engineering/Context Management 技术方案汇总.md` — 上下文管理技术综述：滑动窗口、LLM 摘要、观察屏蔽等
- `raw/papers/context-engineering/Claude Context Awareness 机制.md` — Claude 原生 token 预算感知机制

#### visual-generation/
- `raw/papers/visual-generation/VisPainter 论文笔记.md` — 基于 MCP 的多 Agent 科学插图生成框架
- `raw/papers/visual-generation/PaperBanana 论文笔记.md` — 学术论文插图自动化，292 例 benchmark

#### agent-infrastructure/
- `raw/papers/agent-infrastructure/LLM-in-Sandbox 论文笔记.md` — 将 LLM 放入虚拟沙盒以激发通用 Agent 能力
- `raw/papers/agent-infrastructure/Tool Search Tool 笔记.md` — Claude API 的 Tool Search 机制（50+ 工具延迟加载）

#### agent-memory/
- `raw/papers/agent-memory/MemEvolve_论文笔记.md` — Agent 记忆系统的元进化，EvolveLab 框架（12 种记忆系统 + 双循环进化）
- `raw/papers/agent-memory/Distilling Feedback into Memory-as-a-Tool.md` — 将评估反馈转化为持久化记忆工具

#### generative-models/
- `raw/papers/generative-models/Generative Modeling via Drifting - 笔记.md` — MIT 论文：将多步迭代从推理转移到训练，实现单步生成

### projects/ (项目)

#### creative-cowork/
- `raw/projects/creative-cowork/01 Creative CoWork - DEMO思路.md` — 产品愿景：Creative CoWork 定位为"创作者的 Claude Code"，Agent + Skills + GENUI 核心架构
- `raw/projects/creative-cowork/02 Creative CoWork - 产品深化.md` — 产品深化：上下文容器概念、Studio 模板、9 个关键战略问题
- `raw/projects/creative-cowork/0201 - 技术研究.md` — 技术调研：OpenClaw、Agent Skills 标准、浏览器控制、视频生成 API、上下文管理策略
- `raw/projects/creative-cowork/0205 - init 机制设计.md` — /init 机制设计：Skill Focus Profile（主动/被动层）、三层推荐、干扰规避
- `raw/projects/creative-cowork/03 Creative CoWork - 用户手册.md` — 面向导演的用户手册：工具添加、UI 定制、Studio 概念
- `raw/projects/creative-cowork/04 Creative CoWork - MVP需求.md` — MVP 需求：双栏布局 + 浮动对话框、文件管理、GENUI、分层 Agent、Skills 系统、视频生成技能
- `raw/projects/creative-cowork/05 AI Explorer - PRD.md` — 独立产品：VLM 感知 + LLM 决策 + Playwright 的自动化 Web 探索工具
- `raw/projects/creative-cowork/06 Creative CoWork - 侧边栏设计方案.md` — V6 绿色主题设计：Sandbox + Agent + GENUI 架构，完整设计 token
- `raw/projects/creative-cowork/CLAUDE.md` — Creative CoWork 项目的 Claude Code 配置文件
- `raw/projects/creative-cowork/reference/Claude Code Raw Prompt.md` — Claude Code SP 与 Creative CoWork 的逐段对比
- `raw/projects/creative-cowork/reference/Creative Agent System Prompt 模板.md` — 创意 Agent 完整 SP 模板

#### user-interview/
- `raw/projects/user-interview/漫剧访谈 LSW - 总结.md` — 30 人漫剧团队访谈总结：角色一致性和上下文分散是核心痛点
- `raw/projects/user-interview/漫剧访谈 LSW 0123.md` — LSW 团队完整访谈记录原文（2 小时）
- `raw/projects/user-interview/漫剧访谈 三界动画 - 总结.md` — 100-200 人团队总结：单人全链路模式，角色识别和"抽卡"效率是痛点
- `raw/projects/user-interview/漫剧访谈 三界动画.md` — 三界动画完整访谈记录原文（3 小时）
- `raw/projects/user-interview/漫剧访谈 艺柏AILab - 总结.md` — 5 人北京工作室：AIGC 广告，"剪辑优先"制作理念
- `raw/projects/user-interview/漫剧访谈-福州畅读 - 总结.md` — 福州团队：漫剧出海，"赛马策略"，自建 AI 制作管理系统

#### jimeng-video-agent/
- `raw/projects/jimeng-video-agent/视频AGENT 主SP.md` — 即梦视频 Agent 主 SP：视觉导演角色，7 个 MCP 工具，5 阶段工作流
- `raw/projects/jimeng-video-agent/视频AGENT 主SP（优化版）.md` — 优化版：全局约束（无真人面孔、15s 限制）、Skill 加载映射表
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_构思.md` — Skill：故事短片创意构思，从模糊想法到完整剧本
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_创意与剧本（优化版）.md` — Skill：创意与剧本优化版，Phase 2 分镜设计触发
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_参考素材生成.md` — Skill：角色和道具参考素材设计
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_参考素材生成（优化版）.md` — Skill 优化版：白色背景硬性约束 + 素材规范
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_故事板生成.md` — Skill：故事板生成，从剧本到分镜视觉化
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_bgm.md` — Skill：配乐技巧，为视频片段添加音频层
- `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_配乐方案（优化版）.md` — Skill 优化版：仅输出推荐方案，不控制音频生成
- `raw/projects/jimeng-video-agent/[视频Agent]配乐_搜索词提取.md` — Skill：配乐搜索关键词提取，生成 3-5 个检索词
- `raw/projects/jimeng-video-agent/[视频Agent]配乐_搜索词提取（优化版）.md` — Skill 优化版：字数约束 + 用户偏好优先

#### xian-home/
- `raw/projects/xian-home/弦的形象设计：从零到9.5的完整过程.md` — AI Agent "弦" 的视觉形象设计：弦/波隐喻、Three.js 技术架构

### articles/ (文章和研究)

#### claude-code-research/
- `raw/articles/claude-code-research/Agent System Prompt 研究.md` — 4 大 Agent SP 对比：Manus、Claude Code、Cursor、Devin
- `raw/articles/claude-code-research/Claude Code System Prompt 详解.md` — Claude Code 116 文件模块化 Prompt 系统完整拆解
- `raw/articles/claude-code-research/Claude Code 架构分析.md` — 架构分析：单循环 + 智能委托、分层 Agent、18 工具、成本优化
- `raw/articles/claude-code-research/Creative CoWork Skills 架构启发.md` — 将 Anthropic Skills 标准适配创意领域
- `raw/articles/claude-code-research/Superpowers Skills 架构分析.md` — Superpowers 框架分析（47K stars）：纪律执行、反合理化、Session hook
- `raw/articles/claude-code-research/为 Claude 构建 Skills 完整指南.md` — Anthropic 官方 Skills 构建指南笔记

#### claude-code-research/agent-prompt-design/
- `raw/articles/claude-code-research/agent-prompt-design/core-sp.md` — "情书" 创意 Agent 核心 SP 草稿
- `raw/articles/claude-code-research/agent-prompt-design/modular-architecture.md` — 模块化 Prompt 目录结构设计
- `raw/articles/claude-code-research/agent-prompt-design/Creative CoWork SP 改动计划.md` — 基于 Claude Code 架构改造为创作版本的完整改动清单
- `raw/articles/claude-code-research/agent-prompt-design/tool-descriptions.md` — 工具描述模板：用途、参数、使用场景、注意事项规范
- `raw/articles/claude-code-research/agent-prompt-design/prompts/README.md` — Prompt 模块目录说明文档
- `raw/articles/claude-code-research/agent-prompt-design/prompts/agents/general.md` — 通用 Agent 角色 Prompt
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/identity.md` — 系统模块：Agent 身份定义
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/agent-loop.md` — 系统模块：Agent 循环行为规范
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/communication.md` — 系统模块：沟通风格与表达规范
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/limitations.md` — 系统模块：能力边界与限制声明
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/professional-objectivity.md` — 系统模块：专业客观性原则
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/self-correction.md` — 系统模块：自我纠错机制
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/thinking.md` — 系统模块：思考过程规范
- `raw/articles/claude-code-research/agent-prompt-design/prompts/system/tool-policy.md` — 系统模块：工具使用策略
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/execute.md` — 工具定义：命令执行
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-edit.md` — 工具定义：文件编辑
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-read.md` — 工具定义：文件读取
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-write.md` — 工具定义：文件写入
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/search-glob.md` — 工具定义：文件搜索（glob）
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/search-grep.md` — 工具定义：内容搜索（grep）
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/skill-activate.md` — 工具定义：技能激活
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/skill-deactivate.md` — 工具定义：技能停用
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/task.md` — 工具定义：任务管理
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/todo-update.md` — 工具定义：待办更新
- `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/todo-write.md` — 工具定义：待办写入
- `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/code-style/SKILL.md` — Skill 定义：代码风格
- `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/prompt-engineering/SKILL.md` — Skill 定义：Prompt 工程
- `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/story-writing/SKILL.md` — Skill 定义：故事写作
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/creation-in-progress.md` — Reminder：创作进行中提示
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/file-modified-externally.md` — Reminder：外部文件修改通知
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/long-conversation.md` — Reminder：长对话提示
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/skill-activated.md` — Reminder：技能激活通知
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/task-idle.md` — Reminder：任务空闲提示
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/token-limit-approaching.md` — Reminder：token 上限预警
- `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/tool-call-failed.md` — Reminder：工具调用失败处理
- `raw/articles/claude-code-research/agent-prompt-design/skills/code-style.md` — Skill 实现：代码风格完整规则集
- `raw/articles/claude-code-research/agent-prompt-design/skills/prompt-engineering.md` — Skill 实现：Prompt 工程完整规则集
- `raw/articles/claude-code-research/agent-prompt-design/skills/story-writing.md` — Skill 实现：故事写作完整规则集

#### memory-research/
- `raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code.md` — 三种 Agent 记忆方案对比分析

#### learning-notes/
- `raw/articles/learning-notes/README.md` — 学习笔记目录说明
- `raw/articles/learning-notes/0310-agent-knowledge.md` — MCP 协议深度、Cursor Agent、OpenAI Agent 框架
- `raw/articles/learning-notes/0311-karpathy-autoresearch.md` — Karpathy AutoResearch 开源项目学习
- `raw/articles/learning-notes/0312-harness-engineering-cybernetics.md` — Harness Engineering 即控制论，Agent 工程范式思考
- `raw/articles/learning-notes/0314-open-manus.md` — Open Manus 项目源码学习（核心工具与思路）
- `raw/articles/learning-notes/0324-claude-think-tool.md` — Claude Think Tool 与 Extended Thinking 推理技巧
- `raw/articles/learning-notes/0325-multi-turn-training.md` — 多轮训练让智能体学会协作推理 + 多 Agent 问题归因
- `raw/articles/learning-notes/0326-sora-feed-agent.md` — 4o 图像生成发布观察 + Agent 方向思考
- `raw/articles/learning-notes/0327-creative-module.md` — 创意模块可能是创作 Agent 最重要的差异化能力
- `raw/articles/learning-notes/0327-tracing-thoughts.md` — Anthropic 归因图研究：追踪大语言模型的思维过程
- `raw/articles/learning-notes/0407-define-agent-requirement.md` — Agent 该怎么搞？思路与坑的系统整理
- `raw/articles/learning-notes/0410-agent2agent.md` — Google A2A（Agent-to-Agent）协议学习
- `raw/articles/learning-notes/0416-ai-second-half.md` — 姚顺雨定义的 AI 下半场思考
- `raw/articles/learning-notes/0417-o3-o4-mini.md` — O3/O4 mini 发布：推理模型首次 agentic 使用所有工具
- `raw/articles/learning-notes/0509-paper-bench.md` — OpenAI PaperBench：新 Agent 评估方法
- `raw/articles/learning-notes/0513-lovart.md` — Lovart 产品研究：设计场景 + Agent 结合的实践
- `raw/articles/learning-notes/0513-metamorph.md` — MetaMorph：通过质量训练获取多模态理解与生成能力
- `raw/articles/learning-notes/0517-health-bench.md` — OpenAI HealthBench：健康领域 AI 评估研究
- `raw/articles/learning-notes/0521-why-we-think.md` — Lilian Weng "Why We Think" 综述笔记
- `raw/articles/learning-notes/0527-beyond-agent-tech.md` — Agent 技术实现之外的产品思路（Lovart + Youware 创始人访谈）
- `raw/articles/learning-notes/0615-agent-architectures.md` — 多种 Agent 架构对比：Devin、Anthropic、Google 的不同实践
- `raw/articles/learning-notes/0620-anthropic-agent-cookbook.md` — Anthropic 官方 Agent Cookbook 学习笔记
- `raw/articles/learning-notes/0621-ak-software-changing.md` — AK 演讲 "Software is Changing Again" 笔记
- `raw/articles/learning-notes/0701-era-of-exploration.md` — "The Era of Exploration" 长文阅读笔记
- `raw/articles/learning-notes/0702-scaling-test-time-compute.md` — Noam Brown: Scaling Test Time Compute to Multi-Agent Civilizations
- `raw/articles/learning-notes/0707-12-factor-agent.md` — 12 Factor Agent 项目学习
- `raw/articles/learning-notes/0709-websailor.md` — WebSailor：阿里巴巴开源 Web Agent + 训练方法
- `raw/articles/learning-notes/0710-agent-how.md` — Agent 该怎么搞？思路与坑的持续整理
- `raw/articles/learning-notes/0719-manus.md` — Manus 上下文工程实践文章笔记
- `raw/articles/learning-notes/0804-oppo-agent.md` — OPPO Agent-KB：用户数据利用与 Agent 增强
- `raw/articles/learning-notes/0807-cc-prompt.md` — Claude Code Prompt 实现深度分析
- `raw/articles/learning-notes/0814-plan-mode-auto-accept.md` — Plan Mode / Auto Accept 的思考与想法
- `raw/articles/learning-notes/0823-multimodal-agent-long-term-memory.md` — 多模态 Agent 长期记忆论文阅读（结论：无新知识）
- `raw/articles/learning-notes/0826-filmaster.md` — Filmaster 论文学习笔记
- `raw/articles/learning-notes/0830-memory-r1.md` — Memory-R1：强化学习的上下文管理思路
- `raw/articles/learning-notes/0917-how-people-use-gpt-claude.md` — OpenAI + Anthropic 用户使用行为报告对比
- `raw/articles/learning-notes/0922-deep-researcher.md` — Google Deep Researcher with Test-Time Diffusion
- `raw/articles/learning-notes/0929-claude-release.md` — Claude 新版本发布内容学习
- `raw/articles/learning-notes/1009-openai-dev-day.md` — OpenAI Dev Day：Apps in GPT（Booking、Canva、Figma 等）
- `raw/articles/learning-notes/agent-test-sets.md` — Agent 过程评估测试集研究：对过程的评价比结果更合理
- `raw/articles/learning-notes/ai-native-coding.md` — Cursor 两篇 Problems Blog 深度分析
- `raw/articles/learning-notes/deepseek-v3-r1.md` — DeepSeek-V3/R1 推理系统概览（降本思路学习）
- `raw/articles/learning-notes/miaoshua-analysis.md` — 妙刷产品分析：AI 玩具的情绪价值
- `raw/articles/learning-notes/opusclip-analysis.md` — OpusClip ClipAnything：GPT-4o 长转短视频
- `raw/articles/learning-notes/xiaolong-product-course.md` — 张小龙产品课笔记

#### blog/
- `raw/articles/blog/AI Agent 产品设计 Blog 阅读清单.md` — AI Agent 产品设计博客阅读清单

#### harness-engineering/
- `raw/articles/harness-engineering/awesome-harness-engineering.md` — Harness Engineering 资源大全（walkinglabs/awesome-harness-engineering）
- `raw/articles/harness-engineering/harness-engineering-deep-research.md` — Harness Engineering 深度研究报告（10 篇核心文章的系统性分析）

#### agent-communication/
- `raw/articles/agent-communication/protocols-and-standards.md` — Agent 通信协议深度研究：MCP/A2A/ANP/AGNTCY/UCP 等 15+ 协议分析、FIPA ACL 历史、协议对比矩阵
- `raw/articles/agent-communication/academic-research.md` — Agent 通信学术研究：7 种通信模式、25+ 篇论文分析、Benchmark 评估体系
- `raw/articles/agent-communication/product-landscape.md` — Agent 通信商业产品全景：40+ 产品/平台格局、市场数据、商业模式分类、中国生态

#### world-model/
- `raw/articles/world-model/paper-index.md` — World Model 论文索引：39 篇论文按引用量和领域分类，覆盖 Games/RL、Video、Driving、Robotics、Planning、JEPA 六大领域
- `raw/articles/world-model/deep-research.md` — World Model 深度研究：五大技术路线对比、LLM 辩论、JEPA Vision、$80亿+ 产品格局、Scaling Laws、创意应用、2026 趋势

#### 其他
- `raw/articles/设计原则.md` — StoryCode Studio CSS 设计 token（暗色主题 + 青色强调）
