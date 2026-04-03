# 上下文容器（Context Container）

> 项目级别的上下文聚合器，将素材、生成结果、参考资料、对话历史统一为 Agent 可访问的结构化上下文。

## 核心要点

- 以项目为单位聚合所有相关上下文，消除信息在多个工具/窗口间的分散问题
- 包含四大类内容：素材（inputs）、生成结果（outputs）、参考资料（references）、对话历史（conversation history）
- 是 Creative CoWork 架构中连接 Skills、GENUI、Agent Loop 的中枢节点
- 解决用户访谈中反复出现的核心痛点：上下文分散导致的效率损耗和创作断裂

## 详细说明

Context Container 是 Creative CoWork 产品的核心设计概念。在传统 AI 工具中，用户需要在不同应用之间反复复制粘贴素材、手动维护参考资料、重复描述项目背景，这种上下文分散是所有用户访谈中反复出现的第一痛点。Context Container 通过在项目层面建立统一的上下文聚合机制，让 Agent 和用户都能在同一个信息空间中工作。

从架构角度看，Context Container 不只是一个文件夹或数据库。在 01 DEMO 思路中它被定义为核心架构组件，在 02 产品深化的 9 个战略问题中被反复讨论其边界和实现方式。04 MVP 需求将其落地为文件管理系统，支持素材上传、分类、版本追踪。06 侧边栏设计中进一步通过 Sandbox 机制实现了上下文的隔离与复用。

Context Container 的设计哲学是：Agent 的能力上限取决于它能访问的上下文质量。与其让用户手动喂数据给 AI，不如让项目本身成为一个自维护的上下文容器，Agent 可以按需从中提取信息、写入结果，形成闭环。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/01-DEMO思路]] | 作为核心架构组件被定义，是产品的基础设施层 |
| [[raw/02-产品深化]] | 在 9 个战略问题中被深入讨论，明确其边界和扩展性 |
| [[raw/04-MVP需求]] | 落地为文件管理模块，定义了具体的上传、分类、版本追踪功能 |
| [[raw/06-侧边栏设计]] | 通过 Sandbox 实现上下文隔离与复用的具体架构 |
| [[raw/用户访谈总结]] | 多位用户反馈上下文分散为核心痛点，验证了此概念的必要性 |

## 关联概念

- [[concepts/skills-system]] — Skills 在 Context Container 内加载和执行，共享项目上下文
- [[concepts/genui]] — GENUI 根据 Context Container 中的当前状态动态渲染界面
- [[concepts/progressive-disclosure]] — Context Container 内的信息按需分层加载，避免上下文过载
- [[concepts/agent-loop]] — Agent Loop 在 Context Container 提供的上下文空间中运行循环

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
