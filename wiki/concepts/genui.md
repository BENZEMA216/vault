# GENUI（动态生成界面）

> Agent 根据当前任务和加载的 Skills 实时生成的 HTML 界面，不是预设的固定 UI，而是上下文驱动的动态渲染。

## 核心要点

- 核心理念：UI 不是预先设计的，而是 Agent 根据上下文动态生成的
- 基于 HTML 渲染方案，在 Sandbox 环境中安全执行
- Skills 定义了 UI 组件的生成模板，Agent 负责组合和填充
- 是 Creative CoWork 区别于传统 AI 工具的核心差异化特性

## 详细说明

GENUI（Generated UI）的核心思想是打破传统软件的固定界面范式。在传统工具中，用户面对的是开发者预设的固定界面——菜单、按钮、面板的位置和功能在发布时就已确定。GENUI 则让 Agent 根据当前项目的上下文、已加载的 Skills、正在进行的任务，实时生成最适合当前工作流的界面。

01 DEMO 思路中首次提出了这一概念，作为 Creative CoWork 的核心创新点。02 产品深化将其细化为具体的技术方案和用户体验设计。一个关键的设计决策是选择 HTML 作为渲染方案（04 MVP 需求），原因是 HTML 的表达力足够强、LLM 对 HTML 的生成能力已经成熟、且可以在 Sandbox 中安全执行。这意味着 Agent 生成的不是抽象的 UI 描述，而是可以直接渲染的 HTML + CSS + JavaScript。

06 侧边栏设计进一步定义了 GENUI 的运行架构：Sandbox + GENUI 的组合。Sandbox 提供安全的执行环境，GENUI 在其中渲染。这种架构确保了生成的界面不会影响主应用的稳定性，同时允许丰富的交互能力。用户看到的不是一个聊天界面旁边的固定工具栏，而是一个会随着对话和任务演变而变化的动态工作空间。

GENUI 的价值在于它将 Agent 的能力从"文本对话"扩展到了"可视化协作"，让超创用户能够在一个动态界面中完成从构思到交付的全流程。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/01-DEMO思路]] | 首次提出 GENUI 概念，定义为核心创新点 |
| [[raw/02-产品深化]] | 细化 GENUI 的技术方案和用户体验设计 |
| [[raw/04-MVP需求]] | 确定 HTML 渲染方案，定义 GENUI 的技术实现路径 |
| [[raw/06-侧边栏设计]] | 定义 Sandbox + GENUI 的运行架构和安全模型 |

## 关联概念

- [[concepts/context-container]] — GENUI 根据 Context Container 中的状态动态渲染
- [[concepts/skills-system]] — Skills 定义了 GENUI 可用的 UI 组件模板
- [[concepts/creative-agent-design]] — GENUI 是创意 Agent 面向用户的可视化层

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
