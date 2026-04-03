# Agent Communication 学术研究深度报告

> 研究时间: 2026-04-03
> 覆盖范围: 2023-2026 年 Agent 通信相关学术论文与工业实践

---

## 目录

1. [综述论文全景](#1-综述论文全景)
2. [Communication Pattern 分类体系](#2-communication-pattern-分类体系)
3. [Pattern 1: Debate/Discussion（辩论/讨论式）](#3-pattern-1-debatediscussion)
4. [Pattern 2: Role-Playing（角色扮演式）](#4-pattern-2-role-playing)
5. [Pattern 3: Hierarchical Delegation（层级委派式）](#5-pattern-3-hierarchical-delegation)
6. [Pattern 4: Blackboard Systems（黑板系统/共享工作区）](#6-pattern-4-blackboard-systems)
7. [Pattern 5: Market-Based Coordination（市场机制式）](#7-pattern-5-market-based-coordination)
8. [Pattern 6: Emergent Communication（涌现式通信）](#8-pattern-6-emergent-communication)
9. [前沿: Latent Space Communication（隐空间通信）](#9-前沿-latent-space-communication)
10. [Agent 互操作协议（Production 层）](#10-agent-互操作协议)
11. [Benchmark 与评估体系](#11-benchmark-与评估体系)
12. [Production 框架对比](#12-production-框架对比)
13. [关键洞察与研究空白](#13-关键洞察与研究空白)
14. [参考文献](#14-参考文献)

---

## 1. 综述论文全景

### 1.1 核心综述

| 论文 | 作者 | 年份/会议 | 核心贡献 |
|------|------|-----------|----------|
| **Large Language Model based Multi-Agents: A Survey of Progress and Challenges** | Taicheng Guo et al. | 2024 / IJCAI 2024 | 首个系统性 LLM Multi-Agent 综述，覆盖 profiling、communication、capability enhancement |
| **Beyond Self-Talk: A Communication-Centric Survey of LLM-Based Multi-Agent Systems** | Bingyu Yan et al. | 2025 / arXiv | **唯一以 Communication 为中心**的综述，提出 system-level + internal communication 双层框架 |
| **Multi-Agent Collaboration Mechanisms: A Survey of LLMs** | Khanh-Tung Tran et al. | 2025 / arXiv | 五维分类法: actors, types, structures, strategies, coordination protocols |
| **A Survey on LLM-based Multi-Agent System: Recent Advances and New Frontiers** | (多作者) | 2025 / arXiv | 侧重应用场景分类：复杂任务求解、场景模拟、Agent 评估 |

### 1.2 Communication-Centric Survey 的核心框架

**"Beyond Self-Talk" (Yan et al., 2025)** 提出的双层分类最为系统：

**System-Level Communication（系统层通信）:**
- **Architecture**: 通信拓扑结构设计
- **Goals**: 驱动协作的目标类型
- **Protocols**: 治理通信的规则

**System Internal Communication（系统内部通信）:**
- **Strategies**: Agent 采用的通信策略
- **Paradigms**: 通信范式与模式
- **Objects**: Agent 间交换的对象
- **Content**: 传输的实质信息

**识别的四大挑战**: Communication Efficiency、Security Vulnerabilities、Inadequate Benchmarking、Scalability

### 1.3 五维协作分类法

**Tran et al. (2025)** 的分类法对 agent communication 设计决策最具操作性：

| 维度 | 选项 | 适用场景 |
|------|------|----------|
| **Types（协作类型）** | Cooperation / Competition / Coopetition | Cooperation 适合代码生成；Competition 适合 debate；Coopetition 适合谈判 |
| **Structures（通信结构）** | Centralized / Decentralized / Hierarchical | Centralized 适合 QA；Decentralized 适合创意内容；Hierarchical 适合代码生成 |
| **Strategies（协作策略）** | Rule-based / Role-based / Model-based | Rule-based 可预测；Role-based 可模块化；Model-based 可自适应 |

**关键发现**: 最优通信结构因任务和 Agent 组成而异，不存在 one-size-fits-all 的方案。

---

## 2. Communication Pattern 分类体系

基于全部文献梳理，Agent Communication 可归纳为以下 6+1 种核心 pattern：

```
Agent Communication Patterns
├── 1. Debate/Discussion — 多 Agent 辩论以提升推理质量
├── 2. Role-Playing — Agent 承担角色并按角色规则交互
├── 3. Hierarchical Delegation — Manager-Worker 层级委派
├── 4. Blackboard Systems — 共享黑板/工作区间接通信
├── 5. Market-Based Coordination — 经济机制驱动的协调
├── 6. Emergent Communication — Agent 自发形成通信协议
└── 7. Latent Space Communication — 隐空间直接语义传输（前沿）
```

每种 pattern 下面详细分析。

---

## 3. Pattern 1: Debate/Discussion

### 3.1 奠基论文

**Improving Factuality and Reasoning in Language Models through Multiagent Debate**
- **作者**: Yilun Du, Shuang Li, Antonio Torralba, Joshua B. Tenenbaum, Igor Mordatch (MIT & Google Brain)
- **年份/会议**: 2023 → ICML 2024
- **Communication Model**: 多个 LLM 实例独立生成回答 → 互相看到对方答案 → 多轮辩论 → 趋向共识
- **核心机制**: 3 个 Agent，2 轮辩论，每轮 Agent 能看到其他 Agent 的回答并修正自己的答案
- **关键贡献**: 证明多 Agent 辩论能显著提升数学推理和事实准确性，减少幻觉
- **局限**: 计算成本随 Agent 数和轮数线性增长；仅用 3 Agent + 2 轮

### 3.2 社会心理学视角

**Exploring Collaboration Mechanisms for LLM Agents: A Social Psychology View**
- **作者**: Jintian Zhang, Xin Xu, Ningyu Zhang, Ruibo Liu, Bryan Hooi, Shumin Deng
- **年份/会议**: 2023 → ACL 2024
- **Communication Model**: 构建 4 种 "社会"：Agent 具有不同 trait (easy-going / overconfident) + 不同 thinking pattern (debate / reflection)
- **关键发现**:
  - LLM Agent 表现出类人社会行为（从众效应、共识达成）
  - 某些协作策略超越 SOTA，同时使用更少 API tokens
  - **overconfident Agent + debate 模式**在某些任务上效果最好
- **对 Communication 的启示**: Agent 的 "性格" 设计影响通信质量

### 3.3 Debate 的局限性（2025 最新发现）

**Multi-LLM-Agents Debate: Performance, Efficiency, and Scaling Challenges** (ICLR Blogposts 2025)

**关键结论**: 当前 Multi-Agent Debate 方法**无法一致性地超越更简单的 single-agent 策略**，即使增加计算资源。这挑战了 "更多 Agent = 更好" 的假设。

**Adaptive Heterogeneous Multi-Agent Debate (A-HMAD)** (2025):
- 现有 debate 用同质 Agent + 简单多数投票，限制了效果
- A-HMAD 引入异构专家 Agent + 动态辩论特征，提升在教育和事实推理上的表现

---

## 4. Pattern 2: Role-Playing

### 4.1 CAMEL: 角色扮演的开创者

**CAMEL: Communicative Agents for "Mind" Exploration of Large Language Model Society**
- **作者**: Guohao Li, Hasan Abed Al Kader Hammoud, Hani Itani, Dmitrii Khizbullin, Bernard Ghanem
- **年份/会议**: 2023 / NeurIPS 2023
- **Communication Model**: **Inception Prompting** — 通过三层 prompt 引导 Agent 角色扮演
  - Task Specifier Prompt: 定义任务
  - Assistant System Prompt: 定义助手角色、通信格式、约束
  - User System Prompt: 定义用户角色、交互模式、终止条件
- **核心机制**: AI User 持续向 AI Assistant 发指令，形成 instruction-solution pairs 的流式数据
- **关键贡献**: 证明 role-playing 可规模化生成多样化对话数据；产出 CAMEL AI Society 和 CAMEL Code 数据集
- **局限**: 角色固定，缺乏动态角色切换能力

### 4.2 ChatDev: 角色扮演 + Chat Chain

**ChatDev: Communicative Agents for Software Development**
- **作者**: Chen Qian, Wei Liu et al.
- **年份/会议**: 2023 → ACL 2024
- **Communication Model**:
  - **Chat Chain**: 将开发过程分为 Design → Coding → Testing 三阶段，每阶段包含多个 subtask
  - **双 Agent 设计**: Instructor(ℐ) + Assistant(𝒜) 配对，简化多 Agent 拓扑
  - **消息传递**: `⟨ℐ→𝒜, 𝒜↝ℐ⟩↺` 循环直到任务完成或达到 10 轮上限
  - **Communicative Dehallucination**: 角色反转，Assistant 先主动请求细节再回答
- **Agent 角色**: CEO, CTO, Programmer, Reviewer, Tester（5 个角色）
- **实验结果**:
  - Completeness: 0.56 (vs MetaGPT 0.48)
  - Executability: 0.88 (vs MetaGPT 0.41)
  - Quality: 0.40 (vs MetaGPT 0.15)
- **关键洞察**: 角色分配对质量至关重要——移除 system prompt 中的角色定义会显著降低质量
- **局限**: 只适合简单原型，非 production 级别；token 消耗大

### 4.3 MetaGPT: SOP 编码的角色协作

**MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework**
- **作者**: Sirui Hong, Mingchen Zhuge, Jonathan Chen et al.
- **年份/会议**: 2023 → ICLR 2024
- **Communication Model**:
  - **SOP（标准化操作流程）**: 将人类工作流编码为 Agent 操作序列，替代无结构对话
  - **Shared Message Pool**: 中心化存储所有 Agent 输出，解耦通信时序
  - **Publish-Subscribe 机制**: Agent 按角色订阅相关消息，过滤信息过载
- **Agent 角色**: Product Manager → Architect → Project Manager → Engineer → QA Engineer
- **关键创新**: 用**结构化文档**（PRD、系统设计图、接口定义）替代自然语言交流，防止 "telephone game" 式的幻觉级联
- **实验结果**:
  - HumanEval Pass@1: 85.9% (GPT-4)
  - SoftwareDev executability: 3.75/4.0 (vs ChatDev 2.25)
  - 人工修订次数: 0.83 (vs 竞品 2.5)
- **局限**: SOP 相对固定，不能动态自组织；跨项目不能复用经验；bug 修复重试上限 3 次

---

## 5. Pattern 3: Hierarchical Delegation

### 5.1 DyLAN: 动态 Agent 网络

**Dynamic LLM-Agent Network: An LLM-agent Collaboration Framework with Agent Team Optimization**
- **作者**: Zijun Liu, Yanzhe Zhang, Peng Li, Yang Liu, Diyi Yang
- **年份**: 2023 (arXiv 2310.02170)
- **Communication Model**:
  - **动态交互架构**: 推理时自动选择参与 Agent，非静态固定
  - **Early-stopping 机制**: 自动判断何时停止多轮交互
  - **Agent Importance Score**: 无监督指标，度量每个 Agent 的贡献度
- **实验结果**:
  - MATH: +13.0% (vs GPT-3.5-turbo 单次执行)
  - HumanEval: +13.3%
  - MMLU 特定学科: +25.0%
- **关键贡献**: 证明动态 Agent 选择优于静态分配，Agent 团队组成可优化

### 5.2 AutoGen: 可编程对话模式

**AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation**
- **作者**: Qingyun Wu, Gagan Bansal, Jieyu Zhang et al. (Microsoft Research)
- **年份**: 2023 (arXiv 2308.08155)
- **Communication Model**:
  - **Conversable Agent 抽象**: 每个 Agent 可定制、可对话、可组合 LLM + Human + Tools
  - **多种对话模式**: Two-agent chat, Sequential chat, Group chat, Nested chat
  - **Human-in-the-loop**: 人类可以随时介入任何 Agent 的对话流
  - **灵活编程**: 自然语言 + 代码均可定义对话模式
- **应用领域**: 数学、编程、QA、运筹优化、在线决策、娱乐
- **关键贡献**: 提供最通用的 multi-agent conversation framework，支持几乎任意拓扑
- **后续发展**: 2025 年 Microsoft 发布 Microsoft Agent Framework，合并 AutoGen 的动态编排与 Semantic Kernel 的生产基础

### 5.3 AgentVerse: 动态群组协作

**AgentVerse: Facilitating Multi-Agent Collaboration and Exploring Emergent Behaviors**
- **作者**: Weize Chen, Yusheng Su et al.
- **年份**: 2023 → ICLR 2024
- **Communication Model**: 动态调整 Agent 群组组成，包括 Expert Recruitment → Collaborative Decision Making → Action Execution → Evaluation 四阶段
- **涌现行为观察**:
  - **Volunteer behavior**: Agent 主动帮助同伴
  - **Conformity behavior**: Agent 调整自身以对齐共同目标
  - **Destructive behavior**: 对群体效率有负面影响的行为
- **关键贡献**: 首次系统观察到 LLM Agent 群体中的涌现社会行为

---

## 6. Pattern 4: Blackboard Systems

### 6.1 LLM-Based Multi-Agent Blackboard System

**LLM-Based Multi-Agent Blackboard System for Information Discovery in Data Science**
- **年份**: 2025 (arXiv 2510.01285)
- **Communication Model**:
  - 中央 Agent 在共享黑板上发布请求
  - 自治的下属 Agent 自主决定是否有能力响应
  - 非分配式——Agent **自愿竞标**而非被指派
- **实验结果**: 端到端任务成功率相对提升 57%，数据发现准确度提升 9%
- **关键洞察**: 自愿响应机制比指派式分配更高效

### 6.2 LbMAS: 通用黑板架构

**Exploring Advanced LLM Multi-Agent Systems Based on Blackboard Architecture**
- **年份**: 2025 (arXiv 2507.01701)
- **Communication Model**: Agent 通过黑板共享所有信息 → 根据黑板内容选择动作 → 重复选择-执行直到达成共识
- **关键创新**: 可根据黑板内容动态调整协作机制

### 6.3 Terrarium

**Terrarium: Revisiting the Blackboard for Multi-Agent Safety**
- **年份**: 2025 (arXiv 2510.14312)
- **Communication Model**: 黑板作为结构化共享工作区，异构 Agent 发布 partial results、hypotheses、constraints、goals
- **关键贡献**: 将黑板架构应用于 multi-agent safety 场景

**Pattern 4 总结**: Blackboard 系统的核心优势是**解耦通信**——Agent 无需知道谁会处理请求，实现了类似 event-driven 的异步协作。

---

## 7. Pattern 5: Market-Based Coordination

### 7.1 Market Making as Coordination Framework

**From Competition to Coordination: Market Making as a Scalable Framework for Safe and Aligned Multi-Agent LLM Systems**
- **作者**: Brendan Gho et al.
- **年份**: 2025-2026 (arXiv 2511.17621)
- **Communication Model**:
  - 每个 Agent 作为市场参与者，交换概率信念
  - 通过 "交易" 概率估计趋向共识
  - 个体激励与集体真相追求对齐
- **实验结果**: 准确率比 single-shot baseline 高出 10%，同时保持推理可解释性
- **关键贡献**: 将经济学市场机制引入 Agent 协调，实现自组织、可验证的推理

### 7.2 Magentic Marketplace (Microsoft Research)

- 设计和实现了用于研究 LLM Agent 在双边经济市场全生命周期的环境
- 覆盖: 搜索、询价、谈判、交易

### 7.3 AgenticPay: 买卖谈判系统

**AgenticPay: A Multi-Agent LLM Negotiation System for Buyer-Seller Transactions**
- **年份**: 2026 (arXiv 2602.06008)
- 将谈判形式化为具有私有估值的 stochastic language game
- 支持 110+ 任务，从双边议价到多对多市场

### 7.4 LLM 共谋研究

**Evaluating LLM Agent Collusion in Double Auctions** (2025)
- 研究 LLM Agent 在连续双向拍卖中的卖方共谋行为
- **关键发现**: Agent 通信能力、模型选择、环境压力都影响共谋的涌现

**Institutional AI: Governing LLM Collusion in Multi-Agent Cournot Markets** (2026)
- 将 alignment 从 "Agent 偏好工程" 重构为 "制度空间的机制设计"

---

## 8. Pattern 6: Emergent Communication

### 8.1 机器语言的涌现

**Emergence of Machine Language in LLM-based Agent Communication**
- **年份**: 2025 → submitted to ICLR 2026
- **实验设置**: Referential game — speaker 用预定义字母表描述目标对象，listener 需从干扰项中识别目标
- **关键发现**:
  - 541 个对象，两个 Agent 仅用 **4 轮通信**（每轮最多 3 次尝试）就发展出共享语言
  - 涌现的语言展现 **compositionality（组合性）、generalizability（泛化性）、morphemes（词素）、polysemy（多义性）** — 这些都是人类语言的定义特征
- **启示**: LLM Agent 有能力自发创造高效的非自然语言通信协议

### 8.2 Multi-Agent 涌现协调

**Emergent Coordination in Multi-Agent Language Models**
- **作者**: Christoph Riedl
- **年份**: 2025 (arXiv 2510.05174)
- **方法**: 用 partial information decomposition of time-delayed mutual information (TDMI) 量化涌现
- **关键实验**:
  - Control: 强时序协同但有限协调
  - + Personas: 产生稳定的身份关联分化
  - + Personas + Reflection: 产生身份分化 + 目标导向互补
- **核心发现**: 可以通过 prompt 设计**引导** multi-agent 系统从 "简单聚合" 演变为 "高阶集体"

### 8.3 通信协议标准化的紧迫性

**LLM Agent Communication Protocol (LACP) Requires Urgent Standardization** (2025)
- 当前生态存在: OpenAI Function Calling, LangChain Agent Protocol, Anthropic MCP, ACP, ANP, Agora, Google A2A 等多种协议
- **碎片化导致互操作性困境**，需要 telecom-inspired 的统一协议

---

## 9. 前沿: Latent Space Communication

这是 2025-2026 年最前沿的研究方向，超越自然语言文本，在隐空间层面实现 Agent 间通信。

### 9.1 Cache-to-Cache (C2C)

**Cache-to-Cache: Direct Semantic Communication Between Large Language Models**
- **年份**: 2025 (arXiv 2510.03215)
- **核心机制**: 用神经网络将源模型的 KV-cache 投影并融合到目标模型的 KV-cache，绕过低效的 text generation
- **性能**:
  - 准确率比单模型高 6.4-14.2%
  - 比文本通信范式高 3.1-5.4%
  - 延迟降低 2.5x
- **关键创新**: 可学习的 gating mechanism 选择受益于 cache 通信的目标层

### 9.2 LatentMAS

- 与 C2C 不同，LatentMAS 的 layer-wise caches 同时封装初始输入上下文和新生成的 latent thoughts
- 在 Qwen3 模型上，latent thoughts generation 比 text-based reasoning 高效 **235.7 / 377.1 / 471.4 倍**

### 9.3 ThoughtComm

- 学习共享隐空间，使用可训练 encoder-decoder 和 prefix modules，冻结底层 LLM
- Agent 间交换信息无需显式文本

**Latent Communication 的意义**: 这一方向从根本上改变了 "Agent 通信 = 自然语言对话" 的假设，可能是未来高效 multi-agent 系统的关键。

---

## 10. Agent 互操作协议

### 10.1 四大协议对比

基于 **"A Survey of Agent Interoperability Protocols" (2025, arXiv 2505.02279)**:

| 协议 | 创建者 | 时间 | 架构 | 核心用途 | 当前状态 |
|------|--------|------|------|----------|----------|
| **MCP** | Anthropic | 2024.11 | Client-Server, JSON-RPC 2.0 | LLM Tool 集成 | **事实标准**，OpenAI/Google 均采纳 |
| **ACP** | IBM | 2025.03 | REST, Brokered | Agent 间结构化消息 | 稳步发展 |
| **A2A** | Google | 2025.04 | Peer-to-Peer, Agent Card | 企业级 Agent 协作 | 已入 Linux Foundation，但发展放缓 |
| **ANP** | 开源社区 | 2024 | Decentralized, DID | 开放互联网 Agent 市场 | 仍在成熟中 |

### 10.2 分阶段采纳路线图

论文提出的**互补而非竞争**的路线图:

```
Stage 1: MCP     → Tool 调用基础层
Stage 2: ACP     → 跨框架 Agent 消息传递
Stage 3: A2A     → 企业多 Agent 协作
Stage 4: ANP     → 开放互联网去中心化 Agent 市场
```

**核心洞察**: "No single protocol suffices across all contexts" — 没有单一协议能满足所有场景。

### 10.3 MCP 的生态地位

- 2024.11 发布以来，社区已构建数千个 MCP Server
- SDK 覆盖所有主要编程语言
- 使用 JSON-RPC 2.0，支持 HTTP, Stdio, SSE
- 四个核心 primitive: Tools, Resources, Prompts, Sampling
- **局限**: 中心化服务器假设、无动态 peer discovery、易受 prompt injection 攻击

---

## 11. Benchmark 与评估体系

### 11.1 MultiAgentBench (MARBLE)

**MultiAgentBench: Evaluating the Collaboration and Competition of LLM agents**
- **作者**: Kunlun Zhu et al.
- **年份**: 2025 / ACL 2025
- **评估维度**:
  - 4 种 coordination protocols: Star, Chain, Tree, **Graph**（表现最佳）
  - Milestone-based KPI: 不仅测任务完成，还测协作质量
  - **Communication Score**: LLM judge 对每条 inter-agent utterance 打 1-5 分（clarity + relevance），取平均
- **关键发现**: gpt-4o-mini 达到最高平均任务分数；cognitive planning 提升 milestone 达成率 3%

### 11.2 COMMA

**COMMA: A Communicative Multimodal Multi-Agent Benchmark**
- 评估多模态 multi-agent 系统的协作性能
- 覆盖 4 类 agentic capability 在 communicative collaboration 中的表现

### 11.3 TheAgentCompany

- 通过模拟同事通信测试 Agent 通信能力
- Agent 使用 RocketChat 与 "公司成员" 交流获取任务信息

### 11.4 通信质量度量方法总结

| 指标 | 描述 | 来源 |
|------|------|------|
| **Communication Score** | LLM judge 评估每条消息的 clarity + relevance (1-5) | MARBLE |
| **Task Completion Rate** | 最终任务完成率 | 通用 |
| **Milestone Achievement** | 中间里程碑达成率 | MARBLE |
| **Token Efficiency** | 达成同等质量所需 token 数 | MachineSoM |
| **Convergence Speed** | 达成共识所需轮数 | Debate papers |
| **Emergent Behavior Score** | 涌现行为正面/负面比率 | AgentVerse |

---

## 12. Production 框架对比

### 12.1 主要框架

| 框架 | 核心架构 | 通信模式 | 生产就绪度 | 代表用户 |
|------|----------|----------|------------|----------|
| **LangGraph** | Directed Graph + Conditional Edges | 图节点间状态传递 | 高 | LinkedIn, Uber, 400+ 企业 |
| **CrewAI** | Role-based Crews + Process Types | 角色间任务委派 | 高 | 60% Fortune 500 |
| **AutoGen** (→ Microsoft Agent Framework) | Conversable Agents + Flexible Topology | 任意对话模式 | 高（2025.10 public preview） | Microsoft 生态 |
| **OpenAI Agents SDK** | Agents + Handoffs + Tools + Guardrails | Agent 间 Handoff | 中-高 | OpenAI 生态 |
| **Google ADK** | Deep Google Cloud Integration | A2A Protocol | 中 | Google Cloud 生态 |

### 12.2 市场数据

- 2025 年全球 Agentic AI 市场: **$7.6B**（2024 年 $5.4B）
- 2034 年预期: **$196.6B**
- 仅 **2%** 组织实现规模化部署，**61%** 仍在探索阶段

### 12.3 生产部署的 Communication 模式

**Multi-Agent LLM Orchestration for Incident Response** (2025):
- Multi-agent 编排实现 **100% 可操作建议率**（vs single-agent 的 1.7%）
- Action specificity 提升 **80x**
- Solution correctness 提升 **140x**

---

## 13. 关键洞察与研究空白

### 13.1 核心洞察

1. **结构化 > 自然语言**: MetaGPT 用结构化文档替代自由对话，显著减少幻觉级联。Structured output 是 multi-agent communication 的关键设计原则。

2. **动态 > 静态**: DyLAN 证明动态 Agent 选择优于静态分配，AutoGen 的灵活对话模式优于固定拓扑。

3. **Debate 的效果存在天花板**: 2025 年研究表明 multi-agent debate 无法一致性超越 single-agent，需要异构 Agent + 动态辩论才能突破。

4. **Blackboard 系统的复兴**: 传统 AI 的 blackboard architecture 在 LLM 时代复兴，自愿响应机制（vs 指派式）提升 57% 任务成功率。

5. **Latent Communication 是未来方向**: C2C 和 LatentMAS 证明 Agent 间通信不必经过自然语言，latent space 直接传输可提速 2.5-471x。

6. **LLM Agent 能自发创造语言**: 仅需 4 轮通信，Agent 就能发展出具有组合性和泛化性的共享语言。

7. **协议碎片化是当前最大痛点**: MCP/A2A/ACP/ANP 各有定位但互操作性差，需要分阶段互补采纳。

### 13.2 研究空白

| 空白领域 | 现状 | 机会 |
|----------|------|------|
| **Communication Efficiency** | 多数框架用全文本通信，token 消耗大 | Latent communication / compressed messaging |
| **Communication Security** | 几乎没有研究 Agent 间通信的安全性 | 防篡改、防注入的 Agent 通信协议 |
| **跨框架互操作** | 各框架自成体系 | 统一的 Agent communication middleware |
| **Coopetition** | 学术上严重不足 | 混合竞争-合作场景的通信机制 |
| **Communication 成本优化** | 多数方案 token 消耗随 Agent 数指数增长 | 基于重要性的选择性通信 |
| **长时间 Multi-Agent 协作** | 研究集中在单次任务 | 跨会话、持久化的 Agent 关系和通信记忆 |
| **Real-world Deployment 研究** | 仅 2% 企业规模化部署 | 生产环境下 multi-agent communication 的实证研究 |

### 13.3 对 Agent Economy 的启示

对于构建 Agent 经济基础设施，以下通信模式最值得关注:

1. **Market-Based Coordination**: 直接映射到 Agent 服务交易场景
2. **Blackboard / Shared Workspace**: 适合构建 Agent marketplace 的任务发布-竞标机制
3. **MCP + A2A 协议栈**: 工具集成 (MCP) + Agent 间协作 (A2A) 的分层方案
4. **Latent Communication**: 未来高频 Agent 交互的效率基础

---

## 14. 参考文献

### 核心论文

1. Du, Y. et al. (2024). "Improving Factuality and Reasoning in Language Models through Multiagent Debate." ICML 2024. [arXiv:2305.14325](https://arxiv.org/abs/2305.14325)

2. Li, G. et al. (2023). "CAMEL: Communicative Agents for 'Mind' Exploration of Large Language Model Society." NeurIPS 2023. [arXiv:2303.17760](https://arxiv.org/abs/2303.17760)

3. Qian, C. et al. (2024). "ChatDev: Communicative Agents for Software Development." ACL 2024. [arXiv:2307.07924](https://arxiv.org/abs/2307.07924)

4. Hong, S. et al. (2024). "MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework." ICLR 2024. [arXiv:2308.00352](https://arxiv.org/abs/2308.00352)

5. Wu, Q. et al. (2023). "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation." [arXiv:2308.08155](https://arxiv.org/abs/2308.08155)

6. Chen, W. et al. (2024). "AgentVerse: Facilitating Multi-Agent Collaboration and Exploring Emergent Behaviors." ICLR 2024. [arXiv:2308.10848](https://arxiv.org/abs/2308.10848)

7. Liu, Z. et al. (2023). "Dynamic LLM-Agent Network: An LLM-agent Collaboration Framework with Agent Team Optimization." [arXiv:2310.02170](https://arxiv.org/abs/2310.02170)

8. Zhang, J. et al. (2024). "Exploring Collaboration Mechanisms for LLM Agents: A Social Psychology View." ACL 2024. [arXiv:2310.02124](https://arxiv.org/abs/2310.02124)

### 综述论文

9. Guo, T. et al. (2024). "Large Language Model based Multi-Agents: A Survey of Progress and Challenges." IJCAI 2024. [arXiv:2402.01680](https://arxiv.org/abs/2402.01680)

10. Yan, B. et al. (2025). "Beyond Self-Talk: A Communication-Centric Survey of LLM-Based Multi-Agent Systems." [arXiv:2502.14321](https://arxiv.org/abs/2502.14321)

11. Tran, K-T. et al. (2025). "Multi-Agent Collaboration Mechanisms: A Survey of LLMs." [arXiv:2501.06322](https://arxiv.org/abs/2501.06322)

### 前沿论文 (2025-2026)

12. (2025). "Cache-to-Cache: Direct Semantic Communication Between Large Language Models." [arXiv:2510.03215](https://arxiv.org/abs/2510.03215)

13. Riedl, C. (2025). "Emergent Coordination in Multi-Agent Language Models." [arXiv:2510.05174](https://arxiv.org/abs/2510.05174)

14. (2025). "Emergence of Machine Language in LLM-based Agent Communication." [OpenReview](https://openreview.net/forum?id=zy06mHNoO2)

15. Gho, B. et al. (2025-2026). "From Competition to Coordination: Market Making as a Scalable Framework for Safe and Aligned Multi-Agent LLM Systems." [arXiv:2511.17621](https://arxiv.org/abs/2511.17621)

16. Zhu, K. et al. (2025). "MultiAgentBench: Evaluating the Collaboration and Competition of LLM agents." ACL 2025. [arXiv:2503.01935](https://arxiv.org/abs/2503.01935)

### 互操作协议

17. (2025). "A Survey of Agent Interoperability Protocols: MCP, ACP, A2A, and ANP." [arXiv:2505.02279](https://arxiv.org/html/2505.02279v2)

18. (2025). "LLM Agent Communication Protocol (LACP) Requires Urgent Standardization." [arXiv:2510.13821](https://arxiv.org/html/2510.13821v1)

### Blackboard 系统

19. (2025). "LLM-Based Multi-Agent Blackboard System for Information Discovery in Data Science." [arXiv:2510.01285](https://arxiv.org/abs/2510.01285)

20. (2025). "Exploring Advanced LLM Multi-Agent Systems Based on Blackboard Architecture." [arXiv:2507.01701](https://arxiv.org/abs/2507.01701)

### Market-Based

21. (2026). "AgenticPay: A Multi-Agent LLM Negotiation System for Buyer-Seller Transactions." [arXiv:2602.06008](https://arxiv.org/html/2602.06008)

22. Microsoft Research. "MAGENTIC MARKETPLACE: An Open-Source Environment for Studying Agentic Markets." [PDF](https://www.microsoft.com/en-us/research/wp-content/uploads/2025/10/multi-agent-marketplace.pdf)

### Production 框架

23. Google. "Agent2Agent Protocol (A2A)." [Blog](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/)

24. Anthropic. "Model Context Protocol (MCP)." [Spec](https://modelcontextprotocol.io/specification/2025-11-25)

25. (2025). "The Orchestration of Multi-Agent Systems: Architectures, Protocols, and Enterprise Adoption." [arXiv:2601.13671](https://arxiv.org/html/2601.13671v1)
