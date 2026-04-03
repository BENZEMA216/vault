# Agent 记忆系统（Agent Memory）

> Agent 跨会话保持状态的机制，使 Agent 能够积累经验、维护长期上下文、在多次交互间保持连贯性。

## 核心要点

- 三种主流实现方案：Beads（DAG 结构化任务追踪）、OpenClaw（文件系统 + 向量搜索）、Claude Code（分层 CLAUDE.md + 社区插件）
- 核心挑战：什么值得记住、怎么组织记忆、何时检索、如何遗忘过时信息
- 研究前沿：记忆元进化（MemEvolve）、反馈蒸馏（Distilling Feedback）
- 实践中的分层设计：hot memory（活跃上下文）vs cold memory（长期存储）

## 详细说明

Agent 记忆是将 AI 助手从"无状态工具"升级为"有经验的合作者"的关键技术。没有记忆的 Agent 每次对话都从零开始，用户需要反复解释偏好、项目背景、历史决策。有效的记忆系统让 Agent 能够积累对用户和项目的理解，随时间推移变得越来越有用。

AI Agent 记忆方案对比研究详细分析了三种主流方案。Beads 采用 DAG（有向无环图）结构追踪任务和决策链，擅长表达任务之间的依赖和因果关系。OpenClaw 走文件系统 + 向量搜索的路线，将记忆存储为结构化文件并通过语义搜索检索，优势在于简单且可人工审计。Claude Code 的方案最为务实：分层 CLAUDE.md 文件（项目级、目录级、用户级）作为结构化记忆，社区插件扩展自动记忆能力。每种方案都有其适用场景和权衡取舍。

研究前沿正在探索更智能的记忆机制。MemEvolve 论文提出了记忆元进化的概念——记忆系统本身能够学习如何更好地记忆，通过元学习优化记忆的存储、检索和更新策略。Distilling Feedback 研究了反馈蒸馏——将用户的隐式和显式反馈提炼为持久化的行为调整，让 Agent 从每次交互中持续改进。

在实践层面，active_context.md 展示了 hot memory 的设计：维护一个活跃上下文文件，记录当前正在进行的任务、最近的决策、待解决的问题。CLAUDE.md 的温度分层设计则体现了 cold memory 的组织方式：高频更新的信息（如当前 sprint 目标）和低频更新的信息（如项目架构决策）分层存放，各有不同的更新和清理策略。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/AI-Agent记忆方案对比]] | 系统性对比 Beads、OpenClaw、Claude Code 三种记忆方案 |
| [[raw/MemEvolve论文笔记]] | 记忆元进化研究，记忆系统的自我优化能力 |
| [[raw/Distilling-Feedback]] | 反馈蒸馏研究，从用户反馈中提炼持久化的行为调整 |
| [[raw/active_context]] | Hot memory 的实践设计，活跃上下文文件结构 |
| [[raw/CLAUDE.md]] | 温度分层设计，cold memory 的组织和更新策略 |

## 关联概念

- [[concepts/context-engineering]] — 记忆系统是上下文工程在时间维度上的延伸
- [[concepts/progressive-disclosure]] — 记忆的检索和加载遵循渐进式披露原则

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
