# 创意 Agent 设计

> 将 Agent 从代码助手转型为创意协作伙伴，核心在于增加发散/收敛模式和专业客观性。

## 核心要点

- 核心差异：创意 Agent 需要发散模式（探索多种可能性）和收敛模式（聚焦执行），代码 Agent 只需收敛
- 专业客观性：不盲目赞美用户作品，提供真实的创意反馈和改进建议
- 创意标准意识：Agent 需要理解什么是"好的"创意，具备审美判断能力
- 多模态能力：理解图片、视频、音频，而非仅处理文本和代码
- 从 Claude Code 的架构出发适配，而非从零设计

## 详细说明

创意 Agent 设计是将成熟的代码 Agent 架构（如 Claude Code）迁移到创意领域的系统性工程。代码助手的核心模式是"理解需求 → 执行实现 → 验证结果"，这是一个高度收敛的流程。但创意工作有本质区别：在创意初期需要大量发散思考，探索多种可能方向，然后才收敛到具体执行。Creative Agent 需要同时支持这两种模式，并能在两者之间流畅切换。

专业客观性是创意 Agent 区别于通用聊天机器人的关键特质。大多数 LLM 应用倾向于迎合用户、过度赞美，但在创意场景中这是有害的。一个真正有价值的创意伙伴需要像专业导演或资深设计师一样，能够指出作品的不足、提供具体的改进方向。Creative Agent System Prompt 模板中明确要求了这种专业态度。

从 Claude Code 到 Creative Agent 的适配路径已经在 Creative CoWork SP 改动计划中详细规划。核心改造包括：将代码工具模块替换为创意工具模块（图片生成、视频生成、音频处理）、增加发散/收敛模式切换机制、注入创意领域的专业知识和评判标准。情书 Agent 的 core-sp.md 实现展示了这种适配的具体案例，而 06 侧边栏的 UI 设计则定义了创意 Agent 的交互界面。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/creative-agent-sp-template]] | Creative Agent System Prompt 完整模板，定义了发散/收敛模式和专业客观性要求 |
| [[raw/claude-code-raw-prompt]] | Claude Code 原始 Prompt，作为 Creative Agent 适配的基础参照 |
| [[raw/creative-cowork-sp-plan]] | Creative CoWork SP 改动计划，详细的模块改造方案 |
| [[raw/core-sp]] | 情书 Agent 实现，展示了创意 Agent 设计的具体落地 |
| [[raw/06-sidebar]] | 侧边栏 UI 设计，定义了创意 Agent 的实际交互界面 |

## 关联概念

- [[concepts/agent-loop]] — 创意 Agent 的发散/收敛模式需要在 Agent Loop 中实现模式切换
- [[concepts/modular-prompt-architecture]] — 创意 Agent 基于模块化架构进行适配改造
- [[concepts/super-creators]] — 创意 Agent 是 Super Creator 的核心赋能工具
- [[concepts/genui]] — GenUI 为创意 Agent 提供动态生成的交互界面

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
