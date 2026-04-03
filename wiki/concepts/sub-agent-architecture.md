# 子 Agent 架构

> 分层 Agent 系统，主 Agent 委托专门子 Agent 处理特定任务，实现能力分治和上下文隔离。

## 核心要点

- Claude Code 模式：Main Agent + Explore Agent（只读搜索）+ Plan Agent（方案设计），三层分工
- Manus 模式：29 个工具全部集中在单一 Agent 中，不做子 Agent 拆分
- 子 Agent 的核心价值：上下文隔离（子 Agent 的临时上下文不污染主 Agent）和能力聚焦
- Creative CoWork 采用 Claude Code 的委托模式，适配创意场景的子 Agent 角色
- 子 Agent 之间通过结构化消息传递结果，而非共享上下文

## 详细说明

子 Agent 架构是解决单一 Agent 复杂度爆炸问题的核心方案。当 Agent 需要处理的任务涉及多个领域（搜索、规划、执行、验证）时，将所有能力塞进一个 Agent 会导致 System Prompt 过长、行为不可预测、上下文窗口被填满。分层架构通过"主 Agent 调度 + 子 Agent 执行"的模式，让每个 Agent 只关注自己擅长的领域。

Claude Code 的三层架构是最清晰的参考实现。Main Agent 负责理解用户意图、编排任务、执行代码修改。当需要搜索代码库时，它委托 Explore Agent（只读权限，不能修改文件）进行探索；当需要设计方案时，它委托 Plan Agent 进行思考和规划。关键设计：子 Agent 完成任务后只返回结构化结果给 Main Agent，而非将整个搜索过程的上下文传回，这实现了有效的上下文隔离。

相比之下，Manus 的 Agent System Prompt 研究显示其采用了完全不同的策略：29 个工具全部注入单一 Agent，不做子 Agent 拆分。这种方案在工具数量较少时更简单直接，但随着能力扩展会遇到上下文瓶颈。Creative CoWork 的 MVP 需求文档（sec 5.1 分层 Agent 设计）选择了 Claude Code 的委托模式，并将子 Agent 角色适配到创意场景：例如"素材探索 Agent"负责搜索和筛选参考素材，"创意规划 Agent"负责方案构思，"执行 Agent"负责调用生成工具。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/claude-code-architecture]] | Claude Code 架构分析：Main/Explore/Plan 三层子 Agent 设计 |
| [[raw/agent-system-prompt-research]] | 4 种 Agent 对比：Manus 29 工具单 Agent vs Claude Code 委托模式 |
| [[raw/04-mvp-requirements]] | sec 5.1 分层 Agent 设计，Creative CoWork 采用委托模式的决策 |
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | Sub-agent 作为 "context firewall"，隔离任务防止 noise 积累 |

## 关联概念

- [[concepts/agent-loop]] — 子 Agent 各自运行独立的 Agent Loop，由主 Agent 的 Loop 触发
- [[concepts/tool-routing]] — 子 Agent 架构与工具路由配合，不同子 Agent 持有不同工具集
- [[concepts/modular-prompt-architecture]] — 每个子 Agent 加载不同的模块组合
- [[concepts/harness-engineering]] — Sub-agent 是 harness 管理 context 的核心工具

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
