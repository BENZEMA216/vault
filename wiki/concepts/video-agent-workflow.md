# 视频 Agent 工作流

> 即梦视频 Agent 的 5 阶段制作流程，每阶段按需加载对应 Skill，通过 MCP 工具与生成平台交互。

## 核心要点

- 5 阶段流程：需求分析 → 故事板 → 参考素材生成 → 视频生成 → 配乐
- 每阶段有独立的 Skill 文件，按需加载而非全量注入
- 使用 7 个 MCP 工具与即梦等生成平台交互
- 关键约束：无真人面孔、单片段时长 ≤15s
- 每阶段结束需要用户确认后才进入下一阶段（Human in the Loop）

## 详细说明

视频 Agent 工作流是模块化 Prompt 架构和 Skills 系统在视频制作场景的完整落地。整个流程分为 5 个明确阶段，每个阶段对应一个独立的 Skill 文件：构思 Skill 负责需求分析和创意发散，故事板 Skill 将创意结构化为分镜，素材生成 Skill 调用图片生成工具产出参考画面，视频生成 Skill 驱动实际的视频合成，BGM Skill 负责配乐选择和匹配。

在技术实现上，Agent 通过 MCP（Model Context Protocol）工具与外部生成平台交互。优化版的主 SP 引入了全局约束声明和 Skill 映射表，使 Agent 能根据当前阶段自动加载正确的 Skill，同时维持全局约束（如无真人面孔、片段时长限制）的一致性。这种设计避免了将所有阶段的指令一次性塞入上下文。

工作流中的平台路由也是关键设计点。不同的视频生成需求可能适合不同的平台（如 Sora、Minimax、Runway、Kling），Agent 需要根据画面风格、时长、质量要求等因素智能选择最合适的生成平台。这一机制与 Tool Routing 概念紧密相关。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/video-agent-main-sp]] | 视频 Agent 主 SP，定义了 5 阶段流程和 7 个 MCP 工具 |
| [[raw/video-agent-main-sp-v2]] | 优化版主 SP，增加了全局约束声明和 Skill 映射表 |
| [[raw/video-agent-skill-ideation]] | 构思阶段 Skill，负责需求分析和创意发散 |
| [[raw/video-agent-skill-storyboard]] | 故事板阶段 Skill，将创意结构化为分镜 |
| [[raw/video-agent-skill-reference]] | 素材生成阶段 Skill，调用图片生成工具 |
| [[raw/video-agent-skill-bgm]] | BGM 阶段 Skill，负责配乐选择 |

## 关联概念

- [[concepts/skills-system]] — 视频工作流是 Skills 系统的典型应用，每阶段对应一个 Skill
- [[concepts/tool-routing]] — 视频生成平台的智能选择依赖工具路由机制
- [[concepts/creative-agent-design]] — 视频 Agent 是创意 Agent 在视频领域的具体实现
- [[concepts/human-in-the-loop]] — 每阶段的用户确认机制体现了人机协作原则

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
