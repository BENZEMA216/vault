# 上下文工程（Context Engineering）

> 超越 Prompt Engineering 的新范式：系统性地管理 LLM 可用的所有上下文，包括上下文压缩、动态裁剪、摘要维护、RAG 等技术的综合工程实践。

## 核心要点

- 管理对象不只是 prompt，而是 LLM 可见的所有信息：系统指令、工具定义、对话历史、检索结果、文件内容
- 核心技术栈：上下文压缩（compression）、动态裁剪（pruning）、摘要维护（summary maintenance）、RAG
- Anthropic 和 Manus 都在推动这一概念从实践经验升级为工程学科
- 与 Prompt Engineering 的关系：Prompt Engineering 是子集，Context Engineering 是超集

## 详细说明

Context Engineering 是 2025-2026 年 AI 应用开发领域最重要的范式转变之一。当 LLM 的能力不再是主要瓶颈时，"给模型看什么"比"怎么跟模型说话"更关键。Prompt Engineering 关注单条提示词的措辞和结构，而 Context Engineering 关注整个上下文窗口的信息编排——什么该放进去、什么该省略、什么时候加载、以什么粒度呈现。

Context Management 技术方案汇总提供了这一领域的技术综述，梳理了主流的上下文管理技术。SWE-Pruner 的研究证明了动态裁剪的有效性：通过智能移除与当前任务无关的上下文，不仅节省 token，还能提升任务完成率——因为减少了无关信息对模型注意力的干扰。Claude Context Awareness 进一步探索了 token 预算感知机制，让 Agent 能够根据剩余 token 预算动态调整上下文策略。

在 Creative CoWork 的技术栈中，Context Engineering 是底层基础设施。0201 技术研究的第 6 节定义了上下文管理策略，将渐进式披露、动态裁剪、摘要维护整合为统一的框架。Blog 阅读清单中追踪的行业趋势表明，Anthropic、OpenAI、Manus 等团队都在将 Context Engineering 从零散的技巧提升为系统性的工程实践。

这一概念对 Agent 开发者的启示是：构建优秀 Agent 的核心工作不是调 prompt，而是设计上下文管道（context pipeline）——从信息源到模型输入之间的整个数据流。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/Context-Management技术方案汇总]] | 系统性技术综述，梳理主流上下文管理方案 |
| [[raw/SWE-Pruner]] | 动态裁剪研究，证明精简上下文可提升效果 |
| [[raw/Claude-Context-Awareness]] | Token 预算感知机制，Agent 自适应上下文策略 |
| [[raw/Blog阅读清单]] | 行业趋势追踪，Context Engineering 成为主流话题 |
| [[raw/0201-技术研究]] | 第 6 节定义 Creative CoWork 的上下文管理策略框架 |
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | Context Budget 管理、KV-Cache 效率、Compaction 策略、Just-in-Time Retrieval |
| [[raw/articles/harness-engineering/awesome-harness-engineering]] | Context, Memory & Working State 完整资源列表 |

## 关联概念

- [[concepts/progressive-disclosure]] — 渐进式披露是 Context Engineering 的核心实施策略
- [[concepts/agent-memory]] — 跨会话记忆是上下文在时间维度上的延伸
- [[concepts/tool-routing]] — 工具路由中的上下文感知和动态加载
- [[concepts/harness-engineering]] — Context engineering 是 harness 的核心支柱之一
- [[concepts/self-verification]] — 验证结果需要高效利用 context
- [[concepts/agent-runtime]] — Runtime 的 compaction/reset 策略直接影响 context 管理

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
