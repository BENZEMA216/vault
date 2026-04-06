# Agent Communication 全景地图

> 覆盖协议栈、学术脉络、产品格局、论文索引的完整 Agent 通信研究地图

---

## 一、协议栈全景

```
┌─────────────────────────────────────────────────────────────┐
│                  Agent Protocol Stack 2026                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Commerce Layer    UCP (Google)  │  AITP (NEAR)  │  AP2     │
│                   Shopify/Visa     Web3 支付       Agent 支付 │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Agent↔Agent      A2A (Google/LF)  │  ANP (去中心化/W3C)    │
│                   Agent Card+Task    DID+Meta-Protocol      │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Agent↔Tools      MCP (Anthropic/LF)                       │
│                   97M 月下载, 10K+ MCP Server               │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Gateway Layer    AGNTCY (Cisco/LF) — 6 层架构              │
│                   OASF→Directory→SLIM→Identity→Obs→Security │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Orchestration    OpenAI SDK │ AutoGen │ LangGraph │ CrewAI │
│  Frameworks       Handoff      Pub-Sub   StateGraph  Role   │
└─────────────────────────────────────────────────────────────┘
```

### 协议对比速查

| 协议 | 方向 | 消息格式 | 发现机制 | 治理 | 采用度 |
|------|------|----------|----------|------|--------|
| **MCP** | Agent↔Tool (纵向) | JSON-RPC 2.0 | 手动配置/静态 URL | Linux Foundation (AAIF) | ★★★★★ |
| **A2A** | Agent↔Agent (横向) | JSON-RPC over HTTP | Agent Card (`/.well-known/agent.json`) | Linux Foundation | ★★★☆☆ |
| **ANP** | Agent↔Agent (去中心化) | JSON-LD + Schema.org | W3C DID + 搜索引擎爬取 | W3C Working Group | ★★☆☆☆ |
| **AGNTCY** | Agent 互联网基础设施 | gRPC (SLIM) | Agent Directory | Linux Foundation | ★★☆☆☆ |
| **UCP** | Agent Commerce | — | — | Google 主导 | ★★☆☆☆ |

### 推荐采纳路径

```
Stage 1: MCP     → Agent → Tool/Data 连接（已成事实标准）
Stage 2: A2A     → Agent ↔ Agent 跨平台协作
Stage 3: UCP/AITP → Agent Commerce 交易层
Stage 4: ANP     → 开放互联网去中心化 Agent 网络（观望）
```

---

## 二、学术研究脉络

### 时间线

```
1990s   FIPA ACL — Speech Act Theory, 20+ performatives
        ↓ (奠定 "Agent 通信需要结构化意图表达" 范式)

2023    CAMEL (NeurIPS) — Role-Playing + Inception Prompting 开创
        ChatDev (ACL 2024) — Chat Chain + Communicative Dehallucination
        Du et al. — Multi-Agent Debate 奠基 (→ ICML 2024)
        AutoGen — 可编程对话模式框架
        AgentVerse — 首次观察 LLM Agent 涌现社会行为
        DyLAN — 动态 Agent 选择优于静态分配
        ↓
2024    MetaGPT (ICLR) — SOP 编码 + Shared Message Pool, 结构化文档替代自然语言
        Guo et al. (IJCAI) — 首个系统性 LLM Multi-Agent 综述
        MCP 发布 (Anthropic, 2024.11)
        ↓
2025    Yan et al. — 唯一以 Communication 为中心的综述 ("Beyond Self-Talk")
        Tran et al. — 五维协作分类法
        Blackboard 系统复兴 — 自愿响应机制 +57% 成功率
        Emergent Communication — 4 轮即可发展共享语言
        C2C / LatentMAS — Latent Space Communication (2.5-471x 效率提升)
        A2A 发布 (Google, 2025.04)
        MultiAgentBench (MARBLE) — Graph 拓扑表现最佳
        ↓
2025-   Market-Based Coordination — 经济机制驱动 Agent 协调 (+10% accuracy)
2026    AgenticPay — 多 Agent 买卖谈判系统 (110+ 任务)
        A-HMAD — 异构专家 + 动态辩论突破 debate 天花板
        LLM 共谋研究 — 制度空间的机制设计
```

### 七种通信模式总览

| #   | Pattern                 | 代表论文                        | 核心机制                      | 关键数据                               |
| --- | ----------------------- | --------------------------- | ------------------------- | ---------------------------------- |
| 1   | Debate/Discussion       | Du et al. (ICML 2024)       | 多 LLM 互看回答 + 多轮辩论         | 提升推理，但无法一致性超越 single-agent         |
| 2   | Role-Playing            | CAMEL, ChatDev, MetaGPT     | 角色分配 + SOP + Chat Chain   | MetaGPT HumanEval 85.9%            |
| 3   | Hierarchical Delegation | DyLAN, AutoGen              | 动态 Agent 选择 + 可编程对话       | DyLAN: MATH +13%, HumanEval +13.3% |
| 4   | Blackboard              | LLM-Based Blackboard (2025) | 共享工作区 + 自愿竞标              | +57% 任务成功率                         |
| 5   | Market-Based            | Gho et al. (2025-2026)      | 概率信念交换 + "交易" 趋向共识        | +10% accuracy, 可解释                 |
| 6   | Emergent                | Machine Language (2025)     | Referential game → 共享语言涌现 | 4 轮通信即可                            |
| 7   | Latent Space            | C2C, LatentMAS, ThoughtComm | KV-cache 投影 / 隐空间直接传输     | 2.5-471x 效率提升                      |

---

## 三、产品三层格局

### Layer 1: Infrastructure（协议 + 注册）

| 产品/项目 | 类型 | 关键特征 |
|-----------|------|----------|
| MCP (Anthropic/LF) | 协议 | 97M 月下载，事实标准 |
| A2A (Google/LF) | 协议 | Agent Card + Task lifecycle |
| ANP (W3C) | 协议 | DID 去中心化身份 |
| AGNTCY (Cisco/LF) | 基础设施 | 6 层 Agent 互联网架构 |
| UCP (Google) | 协议 | Agentic Commerce |
| Agent-Reg | 注册表 | 基于 A2A Agent Card 的开放注册 |
| ANS | 命名服务 | DNS-for-Agents (Proposal 阶段) |

### Layer 2: Platform（编排 + 部署）

| 平台 | 定位 | 通信模型 | 采用 |
|------|------|----------|------|
| CrewAI | 角色编排框架 | Delegation + Question Tool | 60% Fortune 500 |
| LangGraph | 图编排框架 | Shared State Graph | 400+ 企业 |
| AutoGen / MS Agent Framework | 对话编排框架 | Pub-Sub GroupChat | Microsoft 生态 |
| OpenAI Agents SDK | 轻量编排 | Handoff + Agent-as-Tool | OpenAI 生态 |
| Google ADK | Cloud 编排 | A2A 原生 | Google Cloud |
| Coze 2.0 | No-code 平台 | Multi-Agent + Skills Store | 字节生态 |
| Dify | 开源平台 | Workflow + RAG + Agent Node | 60K stars |

### Layer 3: Application（垂直 Agent）

| 平台 | 领域 | 规模 |
|------|------|------|
| Salesforce Agentforce | CRM | $8 亿 ARR, 29K deals |
| IBM watsonx Orchestrate | Enterprise 集成 | 80+ 应用, 150+ Agent |
| Microsoft Copilot Studio | M365 生态 | A2A + MCP 双协议 |
| Amazon Bedrock Agents | Cloud | 2700+ listings |
| 蚂蚁 Agentar | 金融 | 信通院 5 级认证 |
| 百度文心智能体平台 | 通用 | 50K+ 开发者, 30K+ Agent |
| ASI Alliance | 去中心化 | 跨 5 链, 市值曾达 $92 亿 |

### 商业模式分类

| 模式 | 代表 |
|------|------|
| Execution-based (按执行) | CrewAI, Bedrock |
| Session-based (按会话) | Kore.ai |
| Action + Credit (分离计费) | Relevance AI |
| Seat + Usage (席位+用量) | Dify, Coze |
| Enterprise Custom ($300K+/年) | IBM, Salesforce |
| Open Source + Cloud | CrewAI, Dify, Letta |
| Token Economy | ASI Alliance, Naptha |
| 按效果付费 | 蚂蚁 Agentar |

---

## 四、关键论文索引

### 综述论文

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2024 | Guo et al. "LLM-based Multi-Agents: Survey" (IJCAI) | 首个系统性 LLM Multi-Agent 综述 |
| 2025 | Yan et al. "Beyond Self-Talk" | 唯一以 Communication 为中心的综述 |
| 2025 | Tran et al. "Multi-Agent Collaboration Mechanisms" | 五维分类法 (actors/types/structures/strategies/protocols) |
| 2025 | "A Survey of Agent Interoperability Protocols" | MCP/ACP/A2A/ANP 四协议对比 |

### Debate/Discussion

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2023→ICML 2024 | Du et al. "Multiagent Debate" | 多 Agent 辩论提升推理，奠基论文 |
| 2023→ACL 2024 | Zhang et al. "Social Psychology View" | LLM Agent 表现类人社会行为 |
| 2025 | "Multi-LLM-Agents Debate: Scaling Challenges" | Debate 无法一致性超越 single-agent |
| 2025 | A-HMAD | 异构专家 + 动态辩论突破天花板 |

### Role-Playing

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2023 (NeurIPS) | CAMEL | Inception Prompting 开创角色扮演范式 |
| 2023→ACL 2024 | ChatDev | Chat Chain + Communicative Dehallucination |
| 2023→ICLR 2024 | MetaGPT | SOP 编码 + Shared Message Pool + 结构化文档 |

### Hierarchical Delegation

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2023 | DyLAN | 动态 Agent 选择 + Agent Importance Score |
| 2023 | AutoGen (Microsoft) | 可定制多 Agent 对话框架 |
| 2023→ICLR 2024 | AgentVerse | 动态群组 + 涌现社会行为 |

### Blackboard Systems

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2025 | LLM-Based Blackboard System | 自愿竞标式协作 +57% 成功率 |
| 2025 | LbMAS | 通用黑板架构 + 动态协作机制 |
| 2025 | Terrarium | 黑板用于 multi-agent safety |

### Market-Based

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2025-2026 | Gho et al. "Market Making as Coordination" | 经济学市场机制引入 Agent 协调 |
| 2026 | AgenticPay | 多 Agent 买卖谈判 (110+ 任务) |
| 2025 | LLM Agent Collusion in Double Auctions | Agent 共谋行为涌现研究 |

### Emergent Communication

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2025 | "Emergence of Machine Language" | 4 轮通信发展共享语言 |
| 2025 | Riedl "Emergent Coordination" | TDMI 量化涌现 + prompt 引导集体智能 |

### Latent Space Communication

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2025 | C2C (Cache-to-Cache) | KV-cache 投影融合，延迟降低 2.5x |
| 2025 | LatentMAS | Layer-wise caches，比 text-based 高效 235-471x |
| 2025 | ThoughtComm | 共享隐空间 encoder-decoder |

### Benchmark

| 年份 | 论文 | 核心贡献 |
|------|------|----------|
| 2025 (ACL) | MultiAgentBench (MARBLE) | Graph 拓扑最佳 + Communication Score |
| 2025 | COMMA | 多模态 multi-agent 协作评估 |

---

## 五、数据来源

| 文件 | 覆盖范围 |
|------|----------|
| [[raw/articles/agent-communication/protocols-and-standards]] | 15+ 协议/框架深度分析，FIPA ACL 到 UCP |
| [[raw/articles/agent-communication/academic-research]] | 25+ 篇论文，7 种通信模式，Benchmark 体系 |
| [[raw/articles/agent-communication/product-landscape]] | 40+ 产品/平台，市场数据，商业模式分类 |
| [[raw/articles/learning-notes/0410-agent2agent]] | A2A 协议学习笔记 |
| [[raw/articles/learning-notes/0310-agent-knowledge]] | MCP 协议深度学习 |
| [[raw/articles/learning-notes/0615-agent-architectures]] | 多种 Agent 架构对比 |
| [[raw/articles/learning-notes/0702-scaling-test-time-compute]] | 多 Agent 文明的 Scaling |

---

## 六、对 Agent Economy 的启示

### 直接映射

| 通信研究发现 | Agent Economy 应用 |
|-------------|-------------------|
| **Market-Based Coordination** | Agent 服务交易 — 概率信念交换 → 服务报价/竞标 |
| **A2A Agent Card** | Agent 服务化标准身份证 — `/.well-known/agent.json` |
| **MCP Registry** (Q4 2026) | Agent Marketplace 技术底座 — 策展目录 + 安全审计 + SLA |
| **Blackboard 自愿竞标** | 任务发布 → Agent 自愿响应 → 最优匹配 |
| **UCP** | Agent Commerce 交易层 — 购物全流程代理 |
| **A2A Task lifecycle** | 按 Task 计费的天然基础 (submitted → working → completed) |

### 最大机会

1. **AGNTCY 缺少 economic layer** — 6 层架构中没有 pricing/billing/marketplace，这是最大的结构性空白
2. **跨平台 Agent 经济基础设施** — Salesforce/AWS/Google Marketplace 都是 closed ecosystem，缺少开放的跨平台方案
3. **中国市场 1900 亿 RMB + 110% CAGR** — 严重缺乏 Agent 间通信标准和经济层，中美数据打通有窗口期
4. **DID + Agent Card → 信任链** — 签名 → 验证 → 评级 → Marketplace，信任基础设施是经济层的前提

### 需持续跟踪的信号

- MCP Agent-to-Agent Coordination (Q3 2026) — 是否侵蚀 A2A 地盘
- MCP Registry (Q4 2026) — 是否成为事实上的 Agent Marketplace
- UCP 第二次更新 — Google 在 Agent Commerce 的野心
- ANP W3C 标准进展 — 去中心化 Agent 身份的未来
- NIST AI Agent Standards — 美国政府监管信号

---

## 关联地图

- [[maps/agent-infrastructure]] — Agent 基础设施全景（循环、记忆、上下文、工具）
- [[maps/harness-engineering]] — Harness Engineering 六大支柱

## 关联概念

- [[concepts/agent-communication]] — 概念文章
- [[concepts/agent-loop]] — Agent 循环架构
- [[concepts/tool-routing]] — MCP 作为 Agent-to-Tool 层
- [[concepts/sub-agent-architecture]] — 子 Agent 通信
- [[concepts/context-engineering]] — 通信效率影响 context budget

## 关联发现

- [[connections/communication-to-economy]] — Agent Communication → Agent Economy 关联分析

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
