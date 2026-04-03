# Raw 数据源摘要速查

> 由 LLM 自动维护，请勿手动编辑
> 最后更新：2026-04-03

---

## papers/

### `raw/papers/reasoning/Multi-Persona Thinking 论文笔记.md`
> 多角色 Prompt 方法消除 LLM 推理偏见，设置 target、counter、neutral 三视角交叉验证
- 关键概念：[[concepts/agent-loop]], [[concepts/modular-prompt-architecture]]
- 约 5900 字

### `raw/papers/reasoning/DrZero_论文笔记.md`
> 无训练数据的自进化搜索 Agent，采用 Proposer-Solver 双 Agent 架构实现自主学习
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]]
- 约 4000 字

### `raw/papers/context-engineering/SWE-Pruner 论文笔记.md`
> 0.6B 参数轻量模型实现动态上下文裁剪，在 SWE-bench 上节省 23-54% token 同时保持性能
- 关键概念：[[concepts/context-engineering]], [[concepts/context-container]]
- 约 2500 字

### `raw/papers/context-engineering/Context Management 技术方案汇总.md`
> 上下文管理技术综述，覆盖滑动窗口、LLM 摘要、观察屏蔽、压缩等主流方案
- 关键概念：[[concepts/context-engineering]], [[concepts/agent-memory]]
- 约 2500 字

### `raw/papers/context-engineering/Claude Context Awareness 机制.md`
> Claude 原生 token 预算感知机制，模型自主根据剩余 token 调整输出策略
- 关键概念：[[concepts/context-engineering]]
- 约 1200 字

### `raw/papers/visual-generation/VisPainter 论文笔记.md`
> 基于 MCP 的多 Agent 科学插图生成框架，多角色协作完成科学可视化任务
- 关键概念：[[concepts/sub-agent-architecture]], [[concepts/tool-routing]]
- 约 4300 字

### `raw/papers/visual-generation/PaperBanana 论文笔记.md`
> 学术论文插图自动化系统，构建 292 例 benchmark 评估生成质量
- 关键概念：[[concepts/creative-agent-design]]
- 约 1800 字

### `raw/papers/agent-infrastructure/LLM-in-Sandbox 论文笔记.md`
> 将 LLM 放入虚拟沙盒环境以激发通用 Agent 能力，探索安全执行边界
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 3500 字

### `raw/papers/agent-infrastructure/Tool Search Tool 笔记.md`
> Claude API 的 Tool Search 机制，支持 50+ 工具延迟加载，按需检索激活
- 关键概念：[[concepts/tool-routing]], [[concepts/skills-system]]
- 约 3600 字

### `raw/papers/agent-memory/MemEvolve_论文笔记.md`
> Agent 记忆系统元进化研究，EvolveLab 框架涵盖 12 种记忆系统 + 双循环进化机制
- 关键概念：[[concepts/agent-memory]], [[concepts/context-engineering]]
- 约 19400 字

### `raw/papers/agent-memory/Distilling Feedback into Memory-as-a-Tool.md`
> 将评估反馈蒸馏转化为持久化记忆工具，Agent 可主动调用历史经验
- 关键概念：[[concepts/agent-memory]], [[concepts/tool-routing]]
- 约 8800 字

### `raw/papers/generative-models/Generative Modeling via Drifting - 笔记.md`
> MIT 论文：将多步迭代过程从推理阶段转移到训练阶段，实现高质量单步生成
- 关键概念：[[concepts/context-engineering]]
- 约 2300 字

---

## projects/

### `raw/projects/creative-cowork/01 Creative CoWork - DEMO思路.md`
> Creative CoWork 产品愿景文档，定位为"创作者的 Claude Code"，Agent + Skills + GENUI 三核心架构
- 关键概念：[[concepts/context-container]], [[concepts/skills-system]], [[concepts/genui]]
- 约 1600 字

### `raw/projects/creative-cowork/02 Creative CoWork - 产品深化.md`
> 产品深化文档：上下文容器概念提出、Studio 模板设计、9 个关键战略问题
- 关键概念：[[concepts/context-container]], [[concepts/progressive-disclosure]], [[concepts/super-creators]]
- 约 5600 字

### `raw/projects/creative-cowork/0201 - 技术研究.md`
> 全面技术调研：OpenClaw、Agent Skills 标准、浏览器控制方案、视频生成 API、上下文管理策略
- 关键概念：[[concepts/skills-system]], [[concepts/context-engineering]], [[concepts/tool-routing]]
- 约 24200 字

### `raw/projects/creative-cowork/0205 - init 机制设计.md`
> /init 机制详细设计：Skill Focus Profile 主动/被动层、三层推荐引擎、干扰规避策略
- 关键概念：[[concepts/init-mechanism]], [[concepts/skills-system]], [[concepts/progressive-disclosure]]
- 约 19700 字

### `raw/projects/creative-cowork/03 Creative CoWork - 用户手册.md`
> 面向导演用户的操作手册：工具添加流程、UI 定制方法、Studio 概念介绍
- 关键概念：[[concepts/genui]], [[concepts/super-creators]], [[concepts/human-in-the-loop]]
- 约 1200 字

### `raw/projects/creative-cowork/04 Creative CoWork - MVP需求.md`
> MVP 完整需求文档：双栏布局 + 浮动对话框、文件管理系统、GENUI 渲染、分层 Agent 架构、Skills 系统、视频生成技能
- 关键概念：[[concepts/genui]], [[concepts/skills-system]], [[concepts/sub-agent-architecture]], [[concepts/agent-loop]]
- 约 11600 字

### `raw/projects/creative-cowork/05 AI Explorer - PRD.md`
> 独立产品 PRD：VLM 视觉感知 + LLM 决策 + Playwright 自动化的 Web 探索工具
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 7000 字

### `raw/projects/creative-cowork/06 Creative CoWork - 侧边栏设计方案.md`
> V6 绿色主题侧边栏设计：Sandbox + Agent + GENUI 架构，附完整 CSS 设计 token
- 关键概念：[[concepts/genui]], [[concepts/context-container]]
- 约 6500 字

### `raw/projects/creative-cowork/CLAUDE.md`
> Creative CoWork 项目的 Claude Code 配置与开发指引
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 1000 字

### `raw/projects/creative-cowork/reference/Claude Code Raw Prompt.md`
> Claude Code 完整 System Prompt 原文，与 Creative CoWork 设计的逐段对比分析
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/agent-loop]], [[concepts/skills-system]]
- 约 23800 字

### `raw/projects/creative-cowork/reference/Creative Agent System Prompt 模板.md`
> 创意 Agent 完整 System Prompt 模板，可直接用于部署
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/creative-agent-design]]
- 约 5300 字

### `raw/projects/user-interview/漫剧访谈 LSW - 总结.md`
> 30 人漫剧团队 LSW 访谈总结：角色一致性和上下文分散是核心痛点
- 关键概念：[[concepts/manga-drama-production]], [[concepts/super-creators]], [[concepts/human-in-the-loop]]
- 约 2000 字

### `raw/projects/user-interview/漫剧访谈 LSW 0123.md`
> LSW 团队完整访谈记录原文（2 小时），覆盖镜头、风格、工具、流程等话题
- 关键概念：[[concepts/manga-drama-production]]
- 约 56600 字

### `raw/projects/user-interview/漫剧访谈 三界动画 - 总结.md`
> 100-200 人三界动画团队访谈总结：单人全链路模式，角色识别和"抽卡"效率是核心痛点
- 关键概念：[[concepts/manga-drama-production]], [[concepts/super-creators]]
- 约 3700 字

### `raw/projects/user-interview/漫剧访谈 三界动画.md`
> 三界动画完整访谈记录原文（3 小时），覆盖三维流程、资产管理、团队协作等话题
- 关键概念：[[concepts/manga-drama-production]]
- 约 74800 字

### `raw/projects/user-interview/漫剧访谈 艺柏AILab - 总结.md`
> 5 人北京工作室艺柏 AILab 访谈总结：AIGC 广告制作，"剪辑优先"制作理念
- 关键概念：[[concepts/manga-drama-production]], [[concepts/video-agent-workflow]]
- 约 3800 字

### `raw/projects/user-interview/漫剧访谈-福州畅读 - 总结.md`
> 福州畅读团队访谈总结：漫剧出海战略，"赛马策略"，自建 AI 制作管理系统
- 关键概念：[[concepts/manga-drama-production]], [[concepts/super-creators]]
- 约 4300 字

### `raw/projects/jimeng-video-agent/视频AGENT 主SP.md`
> 即梦视频 Agent 主 System Prompt：视觉导演角色设定，7 个 MCP 工具，5 阶段创作工作流
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 9700 字

### `raw/projects/jimeng-video-agent/视频AGENT 主SP（优化版）.md`
> 视频 Agent 主 SP 优化版：新增全局约束（无真人面孔、15s 时长限制）、Skill 加载映射表
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]], [[concepts/init-mechanism]]
- 约 10300 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_构思.md`
> 视频 Agent Skill：故事短片创意构思，从模糊想法/图片/视频发展为完整故事剧本
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]], [[concepts/creative-agent-design]]
- 约 2200 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_创意与剧本（优化版）.md`
> 视频 Agent Skill 优化版：创意与剧本生成，Phase 2 分镜设计阶段触发
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]]
- 约 6800 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_参考素材生成.md`
> 视频 Agent Skill：角色设计和道具参考素材的标准照生成规范
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/creative-agent-design]]
- 约 4400 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_参考素材生成（优化版）.md`
> 视频 Agent Skill 优化版：白色背景硬性约束 + 素材一致性规范
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]]
- 约 4800 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_故事板生成.md`
> 视频 Agent Skill：从剧本到分镜的故事板视觉化生成
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/creative-agent-design]]
- 约 4900 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_bgm.md`
> 视频 Agent Skill：创作流收尾环节，为视频片段添加背景音乐
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]]
- 约 4100 字

### `raw/projects/jimeng-video-agent/[视频Agent-skill]故事短片_配乐方案（优化版）.md`
> 视频 Agent Skill 优化版：仅输出配乐推荐文档，不直接控制音频生成流程
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/skills-system]]
- 约 4000 字

### `raw/projects/jimeng-video-agent/[视频Agent]配乐_搜索词提取.md`
> 视频 Agent Skill：根据配乐需求生成 3-5 个背景音乐检索关键词
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/tool-routing]]
- 约 3100 字

### `raw/projects/jimeng-video-agent/[视频Agent]配乐_搜索词提取（优化版）.md`
> 视频 Agent Skill 优化版：字数限制 + 用户配乐偏好优先的搜索词生成
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/tool-routing]]
- 约 3700 字

### `raw/projects/xian-home/弦的形象设计：从零到9.5的完整过程.md`
> AI Agent "弦" 的视觉形象设计全过程：弦/波隐喻、Three.js 粒子技术架构
- 关键概念：[[concepts/creative-agent-design]]
- 约 3600 字

---

## articles/

### `raw/articles/claude-code-research/Agent System Prompt 研究.md`
> 4 大主流 Agent System Prompt 横向对比：Manus、Claude Code、Cursor、Devin 的设计哲学差异
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/agent-loop]], [[concepts/skills-system]]
- 约 11300 字

### `raw/articles/claude-code-research/Claude Code System Prompt 详解.md`
> Claude Code 116 个文件组成的模块化 Prompt 系统完整拆解，逐模块分析设计意图
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/agent-loop]], [[concepts/skills-system]], [[concepts/tool-routing]]
- 约 21800 字

### `raw/articles/claude-code-research/Claude Code 架构分析.md`
> Claude Code 架构深度分析：单循环 + 智能委托模式、分层 Agent 体系、18 个工具、成本优化策略
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]], [[concepts/tool-routing]]
- 约 5600 字

### `raw/articles/claude-code-research/Creative CoWork Skills 架构启发.md`
> 将 Anthropic 官方 Skills 标准适配创意领域的实践思路
- 关键概念：[[concepts/skills-system]], [[concepts/creative-agent-design]]
- 约 4100 字

### `raw/articles/claude-code-research/Superpowers Skills 架构分析.md`
> Superpowers 框架分析（47K stars）：纪律执行、反合理化机制、Session hook 设计
- 关键概念：[[concepts/skills-system]], [[concepts/modular-prompt-architecture]]
- 约 6900 字

### `raw/articles/claude-code-research/为 Claude 构建 Skills 完整指南.md`
> Anthropic 官方 Skills 构建指南的完整笔记，覆盖 Skill 生命周期与最佳实践
- 关键概念：[[concepts/skills-system]], [[concepts/init-mechanism]]
- 约 17900 字

### `raw/articles/claude-code-research/agent-prompt-design/core-sp.md`
> "情书" 创意 Agent 核心 System Prompt 草稿
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/creative-agent-design]]
- 约 1100 字

### `raw/articles/claude-code-research/agent-prompt-design/modular-architecture.md`
> 模块化 Prompt 目录结构设计：system/tools/skills/reminders 四层分离
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 4600 字

### `raw/articles/claude-code-research/agent-prompt-design/Creative CoWork SP 改动计划.md`
> 基于 Claude Code 架构改造为创作版本的完整改动清单和实施计划
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/creative-agent-design]]
- 约 9300 字

### `raw/articles/claude-code-research/agent-prompt-design/tool-descriptions.md`
> 工具描述模板规范：用途、参数、使用场景、注意事项的标准化格式
- 关键概念：[[concepts/tool-routing]]
- 约 1900 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/README.md`
> Prompt 模块目录结构说明文档，定义 system/tools/skills/reminders 各目录职责
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 3200 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/agents/general.md`
> 通用 Agent 角色 Prompt 模板
- 关键概念：[[concepts/agent-loop]], [[concepts/modular-prompt-architecture]]
- 约 480 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/identity.md`
> 系统 Prompt 模块：Agent 身份与角色定义
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 170 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/agent-loop.md`
> 系统 Prompt 模块：Agent 感知-思考-行动循环行为规范
- 关键概念：[[concepts/agent-loop]], [[concepts/modular-prompt-architecture]]
- 约 260 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/communication.md`
> 系统 Prompt 模块：沟通风格与用户交互表达规范
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/human-in-the-loop]]
- 约 430 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/limitations.md`
> 系统 Prompt 模块：Agent 能力边界与限制声明
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 330 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/professional-objectivity.md`
> 系统 Prompt 模块：专业客观性原则
- 关键概念：[[concepts/modular-prompt-architecture]]
- 约 270 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/self-correction.md`
> 系统 Prompt 模块：自我纠错与回溯修正机制
- 关键概念：[[concepts/agent-loop]], [[concepts/modular-prompt-architecture]]
- 约 300 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/thinking.md`
> 系统 Prompt 模块：思考过程与推理链规范
- 关键概念：[[concepts/agent-loop]], [[concepts/modular-prompt-architecture]]
- 约 250 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/system/tool-policy.md`
> 系统 Prompt 模块：工具使用策略与安全约束
- 关键概念：[[concepts/tool-routing]], [[concepts/modular-prompt-architecture]]
- 约 470 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/execute.md`
> 工具 Prompt 定义：命令执行工具的参数与使用规范
- 关键概念：[[concepts/tool-routing]]
- 约 340 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-edit.md`
> 工具 Prompt 定义：文件编辑工具
- 关键概念：[[concepts/tool-routing]]
- 约 300 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-read.md`
> 工具 Prompt 定义：文件读取工具
- 关键概念：[[concepts/tool-routing]]
- 约 230 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/file-write.md`
> 工具 Prompt 定义：文件写入工具
- 关键概念：[[concepts/tool-routing]]
- 约 210 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/search-glob.md`
> 工具 Prompt 定义：文件名模式搜索工具
- 关键概念：[[concepts/tool-routing]]
- 约 260 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/search-grep.md`
> 工具 Prompt 定义：内容正则搜索工具
- 关键概念：[[concepts/tool-routing]]
- 约 270 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/skill-activate.md`
> 工具 Prompt 定义：技能激活工具
- 关键概念：[[concepts/skills-system]], [[concepts/tool-routing]]
- 约 240 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/skill-deactivate.md`
> 工具 Prompt 定义：技能停用工具
- 关键概念：[[concepts/skills-system]], [[concepts/tool-routing]]
- 约 190 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/task.md`
> 工具 Prompt 定义：任务创建与管理工具
- 关键概念：[[concepts/tool-routing]], [[concepts/agent-loop]]
- 约 370 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/todo-update.md`
> 工具 Prompt 定义：待办事项更新工具
- 关键概念：[[concepts/tool-routing]]
- 约 320 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/tools/todo-write.md`
> 工具 Prompt 定义：待办事项写入工具
- 关键概念：[[concepts/tool-routing]]
- 约 370 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/code-style/SKILL.md`
> Skill Prompt 定义：代码风格检查与规范执行
- 关键概念：[[concepts/skills-system]], [[concepts/modular-prompt-architecture]]
- 约 1000 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/prompt-engineering/SKILL.md`
> Skill Prompt 定义：Prompt 工程方法论与最佳实践
- 关键概念：[[concepts/skills-system]], [[concepts/modular-prompt-architecture]]
- 约 1100 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/skills/story-writing/SKILL.md`
> Skill Prompt 定义：故事写作与叙事结构
- 关键概念：[[concepts/skills-system]], [[concepts/creative-agent-design]]
- 约 1200 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/creation-in-progress.md`
> Reminder Prompt：创作任务进行中的状态提醒
- 关键概念：[[concepts/agent-loop]]
- 约 140 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/file-modified-externally.md`
> Reminder Prompt：外部文件修改通知，触发 Agent 重新读取
- 关键概念：[[concepts/agent-loop]]
- 约 170 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/long-conversation.md`
> Reminder Prompt：长对话上下文管理提醒
- 关键概念：[[concepts/context-engineering]], [[concepts/agent-loop]]
- 约 140 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/skill-activated.md`
> Reminder Prompt：技能成功激活的状态通知
- 关键概念：[[concepts/skills-system]]
- 约 120 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/task-idle.md`
> Reminder Prompt：任务空闲超时提醒
- 关键概念：[[concepts/agent-loop]]
- 约 160 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/token-limit-approaching.md`
> Reminder Prompt：token 上限接近预警
- 关键概念：[[concepts/context-engineering]]
- 约 90 字

### `raw/articles/claude-code-research/agent-prompt-design/prompts/reminders/tool-call-failed.md`
> Reminder Prompt：工具调用失败后的错误处理引导
- 关键概念：[[concepts/tool-routing]], [[concepts/agent-loop]]
- 约 120 字

### `raw/articles/claude-code-research/agent-prompt-design/skills/code-style.md`
> Skill 完整实现：代码风格规则集（命名、缩进、注释等规范）
- 关键概念：[[concepts/skills-system]]
- 约 2800 字

### `raw/articles/claude-code-research/agent-prompt-design/skills/prompt-engineering.md`
> Skill 完整实现：Prompt 工程规则集（结构、变量、测试等方法论）
- 关键概念：[[concepts/skills-system]], [[concepts/modular-prompt-architecture]]
- 约 2300 字

### `raw/articles/claude-code-research/agent-prompt-design/skills/story-writing.md`
> Skill 完整实现：故事写作规则集（叙事结构、角色塑造、节奏控制）
- 关键概念：[[concepts/skills-system]], [[concepts/creative-agent-design]]
- 约 1300 字

### `raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code.md`
> 三种主流 Agent 记忆方案横向对比：Beads（结构化）、OpenClaw（开源）、Claude Code（文件系统）
- 关键概念：[[concepts/agent-memory]], [[concepts/context-engineering]]
- 约 5500 字

### `raw/articles/learning-notes/README.md`
> 学习笔记目录说明与个人简介
- 关键概念：无
- 约 2000 字

### `raw/articles/learning-notes/0310-agent-knowledge.md`
> MCP 协议深度学习、Cursor Agent 模式分析、OpenAI Agent 框架调研
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 5000 字

### `raw/articles/learning-notes/0311-karpathy-autoresearch.md`
> Karpathy AutoResearch 开源项目学习（GitHub 8000+ stars），自动化研究 Agent
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]]
- 约 1900 字

### `raw/articles/learning-notes/0312-harness-engineering-cybernetics.md`
> Harness Engineering 本质是控制论，Agent 工程的反馈环路范式思考
- 关键概念：[[concepts/agent-loop]], [[concepts/context-engineering]]
- 约 3700 字

### `raw/articles/learning-notes/0314-open-manus.md`
> Open Manus 项目源码阅读学习，核心工具链与 Agent 架构分析
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]], [[concepts/sub-agent-architecture]]
- 约 7100 字

### `raw/articles/learning-notes/0324-claude-think-tool.md`
> Claude Think Tool 与 Extended Thinking 学习：推理过程中的小 trick 集合
- 关键概念：[[concepts/agent-loop]], [[concepts/context-engineering]]
- 约 2700 字

### `raw/articles/learning-notes/0325-multi-turn-training.md`
> 多轮训练让智能体学会协作推理 + 多 Agent 问题归因方法
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]]
- 约 3700 字

### `raw/articles/learning-notes/0326-sora-feed-agent.md`
> OpenAI 4o 图像生成发布观察 + Agent 方向的战略思考
- 关键概念：[[concepts/creative-agent-design]]
- 约 1400 字

### `raw/articles/learning-notes/0327-creative-module.md`
> 创意模块是创作 Agent 最重要的差异化能力的核心判断
- 关键概念：[[concepts/creative-agent-design]], [[concepts/skills-system]]
- 约 5300 字

### `raw/articles/learning-notes/0327-tracing-thoughts.md`
> Anthropic 归因图研究 "Tracing the Thoughts of a LLM" 学习笔记
- 关键概念：[[concepts/agent-loop]]
- 约 2400 字

### `raw/articles/learning-notes/0407-define-agent-requirement.md`
> Agent 该怎么搞？所有思路和坑的系统化整理
- 关键概念：[[concepts/agent-loop]], [[concepts/skills-system]], [[concepts/context-engineering]]
- 约 4100 字

### `raw/articles/learning-notes/0410-agent2agent.md`
> Google A2A（Agent-to-Agent）协议学习：多 Agent 互操作标准
- 关键概念：[[concepts/sub-agent-architecture]], [[concepts/tool-routing]]
- 约 2800 字

### `raw/articles/learning-notes/0416-ai-second-half.md`
> 姚顺雨对 AI 下半场的定义与思考
- 关键概念：[[concepts/agent-loop]]
- 约 1600 字

### `raw/articles/learning-notes/0417-o3-o4-mini.md`
> O3/O4 mini 发布笔记：推理模型首次 agentic 使用 ChatGPT 所有工具
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 950 字

### `raw/articles/learning-notes/0509-paper-bench.md`
> OpenAI PaperBench 新 Agent 评估方法学习
- 关键概念：[[concepts/agent-loop]]
- 约 420 字

### `raw/articles/learning-notes/0513-lovart.md`
> Lovart 产品深度研究：设计场景如何与 Agent 结合的实践案例
- 关键概念：[[concepts/creative-agent-design]], [[concepts/genui]]
- 约 4300 字

### `raw/articles/learning-notes/0513-metamorph.md`
> MetaMorph 论文学习：通过质量训练同时获取多模态理解与生成能力
- 关键概念：[[concepts/creative-agent-design]]
- 约 1700 字

### `raw/articles/learning-notes/0517-health-bench.md`
> OpenAI HealthBench 学习：健康领域 AI 评估的三大问题与方案
- 关键概念：[[concepts/agent-loop]]
- 约 1500 字

### `raw/articles/learning-notes/0521-why-we-think.md`
> Lilian Weng "Why We Think" 综述笔记：从 Think 角度展开的推理研究全景
- 关键概念：[[concepts/agent-loop]], [[concepts/context-engineering]]
- 约 710 字

### `raw/articles/learning-notes/0527-beyond-agent-tech.md`
> Agent 技术实现之外的产品思路：Lovart 创始人陈冕 + Youware 创始人明超平的访谈
- 关键概念：[[concepts/creative-agent-design]], [[concepts/super-creators]]
- 约 3300 字

### `raw/articles/learning-notes/0615-agent-architectures.md`
> 多种 Agent 架构横向对比：Devin（反对多 Agent）、Anthropic、Google 的不同设计哲学
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]]
- 约 3000 字

### `raw/articles/learning-notes/0620-anthropic-agent-cookbook.md`
> Anthropic 官方 Agent Cookbook 深度学习笔记
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]], [[concepts/context-engineering]]
- 约 8800 字

### `raw/articles/learning-notes/0621-ak-software-changing.md`
> AK 演讲 "Software is Changing Again" 笔记
- 关键概念：[[concepts/agent-loop]]
- 约 390 字

### `raw/articles/learning-notes/0701-era-of-exploration.md`
> "The Era of Exploration" 长文阅读笔记：探索时代的 AI 研究方向
- 关键概念：[[concepts/agent-loop]]
- 约 3200 字

### `raw/articles/learning-notes/0702-scaling-test-time-compute.md`
> Noam Brown 博客学习：Scaling Test Time Compute 扩展到多 Agent 文明
- 关键概念：[[concepts/agent-loop]], [[concepts/sub-agent-architecture]]
- 约 750 字

### `raw/articles/learning-notes/0707-12-factor-agent.md`
> 12 Factor Agent 项目学习笔记
- 关键概念：[[concepts/agent-loop]]
- 约 400 字

### `raw/articles/learning-notes/0709-websailor.md`
> WebSailor 学习：阿里巴巴开源的超人推理 Web Agent + 训练方法
- 关键概念：[[concepts/agent-loop]], [[concepts/tool-routing]]
- 约 2100 字

### `raw/articles/learning-notes/0710-agent-how.md`
> Agent 该怎么搞？思路与坑的持续整理与迭代
- 关键概念：[[concepts/agent-loop]], [[concepts/skills-system]], [[concepts/context-engineering]]
- 约 4100 字

### `raw/articles/learning-notes/0719-manus.md`
> Manus "Context Engineering for AI Agents" 博客文章笔记
- 关键概念：[[concepts/context-engineering]]
- 约 580 字

### `raw/articles/learning-notes/0804-oppo-agent.md`
> OPPO Agent-KB 项目学习：用户数据利用与 Agent 增强的实践
- 关键概念：[[concepts/agent-memory]], [[concepts/agent-loop]]
- 约 1300 字

### `raw/articles/learning-notes/0807-cc-prompt.md`
> Claude Code Prompt 实现的深度解析，主要聚焦 Prompt 层面的工程细节
- 关键概念：[[concepts/modular-prompt-architecture]], [[concepts/agent-loop]], [[concepts/context-engineering]]
- 约 15700 字

### `raw/articles/learning-notes/0814-plan-mode-auto-accept.md`
> Plan Mode 与 Auto Accept 的产品设计思考和个人体会
- 关键概念：[[concepts/human-in-the-loop]], [[concepts/agent-loop]]
- 约 1800 字

### `raw/articles/learning-notes/0823-multimodal-agent-long-term-memory.md`
> 多模态 Agent 长期记忆论文阅读（结论：缺少新知识，不推荐）
- 关键概念：[[concepts/agent-memory]]
- 约 490 字

### `raw/articles/learning-notes/0826-filmaster.md`
> Filmaster 论文学习：两个核心启发点
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/creative-agent-design]]
- 约 2000 字

### `raw/articles/learning-notes/0830-memory-r1.md`
> Memory-R1：用强化学习优化上下文管理的思路探索
- 关键概念：[[concepts/agent-memory]], [[concepts/context-engineering]]
- 约 270 字

### `raw/articles/learning-notes/0917-how-people-use-gpt-claude.md`
> OpenAI + Anthropic 分别发布的用户行为分析报告对比
- 关键概念：[[concepts/super-creators]]
- 约 300 字

### `raw/articles/learning-notes/0922-deep-researcher.md`
> Google Deep Researcher with Test-Time Diffusion 学习笔记
- 关键概念：[[concepts/agent-loop]]
- 约 320 字

### `raw/articles/learning-notes/0929-claude-release.md`
> Claude 新版本发布内容简要学习
- 关键概念：[[concepts/agent-loop]]
- 约 300 字

### `raw/articles/learning-notes/1009-openai-dev-day.md`
> OpenAI Dev Day：Apps in GPT 功能发布（Booking、Canva、Figma、Spotify 等合作伙伴）
- 关键概念：[[concepts/tool-routing]], [[concepts/skills-system]]
- 约 480 字

### `raw/articles/learning-notes/agent-test-sets.md`
> Agent 过程评估测试集深度研究：对过程的评价比对结果的评价更合理
- 关键概念：[[concepts/agent-loop]]
- 约 11100 字

### `raw/articles/learning-notes/ai-native-coding.md`
> Cursor 两篇 Problems Blog 深度分析：定义了 AI Native Coding 的核心问题
- 关键概念：[[concepts/agent-loop]], [[concepts/context-engineering]], [[concepts/human-in-the-loop]]
- 约 12600 字

### `raw/articles/learning-notes/deepseek-v3-r1.md`
> DeepSeek-V3/R1 推理系统概览：降本思路与架构学习（皮毛级理解）
- 关键概念：[[concepts/context-engineering]]
- 约 8000 字

### `raw/articles/learning-notes/miaoshua-analysis.md`
> 妙刷产品分析：AI 玩具赛道的情绪价值与用户粘性
- 关键概念：[[concepts/creative-agent-design]], [[concepts/super-creators]]
- 约 5800 字

### `raw/articles/learning-notes/opusclip-analysis.md`
> OpusClip ClipAnything 深度分析：GPT-4o 驱动的长视频转短视频方案
- 关键概念：[[concepts/video-agent-workflow]], [[concepts/creative-agent-design]]
- 约 11000 字

### `raw/articles/learning-notes/xiaolong-product-course.md`
> 张小龙产品课核心笔记
- 关键概念：[[concepts/progressive-disclosure]], [[concepts/human-in-the-loop]]
- 约 3500 字

### `raw/articles/blog/AI Agent 产品设计 Blog 阅读清单.md`
> AI Agent 产品设计领域的博客与文章阅读清单
- 关键概念：[[concepts/agent-loop]], [[concepts/creative-agent-design]]
- 约 4700 字

### `raw/articles/设计原则.md`
> StoryCode Studio CSS 设计 token：暗色主题 + 青色强调色完整设计规范
- 关键概念：[[concepts/genui]]
- 约 3600 字
