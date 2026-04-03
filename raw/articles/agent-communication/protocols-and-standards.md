# Agent Communication Protocols & Standards 深度研究报告

> 研究日期: 2026-04-03
> 研究者: BENZEMA216 + Claude Opus 4.6

---

## 目录

1. [研究概要与核心结论](#研究概要与核心结论)
2. [协议全景图](#协议全景图)
3. [Tier 1: 基础设施级协议](#tier-1-基础设施级协议)
   - Google A2A
   - Anthropic MCP
   - ANP (Agent Network Protocol)
4. [Tier 2: 框架级多 Agent 通信](#tier-2-框架级多-agent-通信)
   - OpenAI Agents SDK
   - Microsoft AutoGen / Agent Framework
   - LangGraph
   - CrewAI
5. [Tier 3: 新兴 & 垂直协议](#tier-3-新兴--垂直协议)
   - IBM ACP → 已并入 A2A
   - AGP (Agent Gateway Protocol)
   - AGNTCY
   - LMOS
   - Agora
   - AITP
   - UCP (Universal Commerce Protocol)
   - agents.json
   - AP2 (Agent Payment Protocol)
6. [Tier 0: 历史标准 — FIPA ACL](#tier-0-历史标准--fipa-acl)
7. [协议对比矩阵](#协议对比矩阵)
8. [2026 协议格局分析](#2026-协议格局分析)
9. [对 Agent Economy 基础设施的启示](#对-agent-economy-基础设施的启示)

---

## 研究概要与核心结论

### 核心发现

1. **MCP + A2A 双协议栈已成为事实标准**：MCP 解决纵向问题（Agent → Tools/Data），A2A 解决横向问题（Agent ↔ Agent）。两者互补而非竞争。

2. **Linux Foundation 成为协议治理中心**：A2A（2025.06 捐赠）、MCP（2025.12 通过 Agentic AI Foundation 捐赠）均在 Linux Foundation 下治理，IBM ACP 已并入 A2A。

3. **框架级方案 vs 协议级方案分化明显**：OpenAI Agents SDK、AutoGen、LangGraph、CrewAI 是框架（如何编排 Agent），而 MCP、A2A、ANP 是协议（如何让不同框架的 Agent 互通）。

4. **去中心化方向值得关注**：ANP 使用 W3C DID 实现去中心化身份 + 无需中央注册中心的 Agent 发现，已进入 W3C 工作组阶段。

5. **商业交易协议兴起**：Google UCP（电商）、AITP（NEAR/Web3 支付）、AP2（Agent 支付）代表 Agent Economy 的商业层。

---

## 协议全景图

```
┌─────────────────────────────────────────────────────────────┐
│                    Agent Protocol Stack 2026                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Commerce Layer    UCP (Google)  │  AITP (NEAR)  │  AP2     │
│                                                             │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Agent↔Agent       A2A (Google/LF)  │  ANP (去中心化)       │
│                                                             │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Agent↔Tools       MCP (Anthropic/LF)                       │
│                                                             │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Gateway Layer     Agent Gateway (AGNTCY/Cisco)             │
│                                                             │
│  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─  │
│                                                             │
│  Orchestration     OpenAI SDK │ AutoGen │ LangGraph │CrewAI │
│  Frameworks                                                 │
└─────────────────────────────────────────────────────────────┘
```

---

## Tier 1: 基础设施级协议

### 1. Google A2A (Agent-to-Agent Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 开放协议 (Protocol) |
| **发布** | 2025.04 (Google), 2025.06 捐赠 Linux Foundation |
| **当前版本** | v0.3 (2025.07) |
| **定位** | Agent 间横向通信标准 |

#### 架构

```
Client Agent ──HTTP/JSON-RPC──▶ Remote Agent (Server)
                                    │
                              Agent Card (发现)
                              Task (生命周期)
                              Message (通信)
                              Artifact (输出)
```

**Client-Remote 模型**：
- **Client Agent**: 发起任务请求
- **Remote Agent**: 执行任务、返回结果
- 不是严格的 peer-to-peer，而是请求方-执行方模型

#### Agent Card（核心发现机制）

```json
// 发布在 /.well-known/agent.json
{
  "name": "Travel Booking Agent",
  "description": "Handles flight and hotel bookings",
  "skills": [
    {
      "name": "book_flight",
      "inputModes": ["text"],
      "outputModes": ["text", "data"]
    }
  ],
  "authentication": {
    "schemes": ["bearer", "oauth2"]
  },
  "url": "https://agent.example.com/a2a"
}
```

- 每个 Agent 通过 HTTP 暴露 JSON 格式的 Agent Card
- 类似 OpenAPI spec，但描述的是 Agent 能力而非 API endpoint
- 支持数字签名（v0.3 新增）

#### Task 生命周期

```
submitted → working → [input-required] → completed / failed / cancelled
                ↓
          (streaming via SSE)
```

- Task 可立即完成或长时间运行
- 支持 Server-Sent Events (SSE) 流式更新
- Client 可以在 Task 运行中提供补充信息

#### 通信技术栈

- **传输**: HTTP/HTTPS
- **消息格式**: JSON-RPC 2.0
- **流式**: Server-Sent Events (SSE)
- **序列化**: JSON
- **v0.3 新增**: gRPC 支持

#### 安全模型

- 与 OpenAPI authentication schemes 对齐
- 支持 Bearer token、OAuth 2.0
- Agent Card 数字签名（v0.3）
- TLS 强制
- 企业级: 支持 air-gapped 环境

#### 采用状况

- **发起方**: Google Cloud
- **50+ 技术合作伙伴**: Atlassian, Box, Salesforce, SAP, ServiceNow, PayPal, MongoDB, LangChain, Workday, UKG
- **咨询公司**: Accenture, BCG, Deloitte, McKinsey, PwC, KPMG
- **100+ 组织** 参与 (截至 2026 初)
- **IBM ACP 已并入 A2A** (2025 末)
- **Linux Foundation 治理**
- **现实**: 虽然合作伙伴多，但 MCP 在开发者采用上更快。A2A 更偏企业 B2B 场景

#### 关键差异化

- 唯一真正聚焦 Agent-to-Agent 的开放协议
- Agent Card 提供标准化的能力发现
- Task 生命周期管理（不只是 RPC，而是有状态的任务追踪）
- 支持多模态（text, audio, video streaming）
- UX 协商（iframe, form, video 等 UI 能力的动态协商）

---

### 2. Anthropic MCP (Model Context Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 开放协议 (Protocol) |
| **发布** | 2024.11 (Anthropic), 2025.12 捐赠 Agentic AI Foundation (Linux Foundation) |
| **规范版本** | 2025-11-25 |
| **定位** | LLM/Agent 与外部工具、数据源的标准化连接 |

#### 架构

```
┌──────────┐     ┌──────────┐     ┌──────────────┐
│   Host   │────▶│  Client  │────▶│  MCP Server  │
│ (LLM App)│     │(Connector)│     │ (Tool/Data)  │
└──────────┘     └──────────┘     └──────────────┘
                                        │
                                  ┌─────┴─────┐
                                  │ Resources  │
                                  │ Tools      │
                                  │ Prompts    │
                                  │ Sampling   │
                                  └────────────┘
```

**三层角色**：
- **Host**: LLM 应用（如 Claude Desktop, Cursor, VS Code）
- **Client**: Host 内的连接器
- **Server**: 提供 Tools/Resources/Prompts 的服务

#### 四大原语 (Primitives)

| 原语 | 控制方 | 用途 |
|------|--------|------|
| **Resources** | Application-controlled | 上下文数据（文件、数据库记录等） |
| **Tools** | Model-controlled | AI 可调用的函数 |
| **Prompts** | User-controlled | 模板化的提示词和工作流 |
| **Sampling** | Server-controlled | Server 发起的 LLM 交互（实现 agentic 行为） |

#### 通信技术栈

- **消息格式**: JSON-RPC 2.0
- **传输**: HTTP, Stdio, Server-Sent Events (SSE), Streamable HTTP
- **连接**: 有状态（Stateful connections）
- **能力协商**: Client-Server 双向能力协商

#### 安全模型

- 用户知情同意原则
- 数据隐私（显式用户授权）
- Tool 安全（视为不受信任的代码执行）
- Sampling 控制（用户控制是否允许、可见性）
- **2026 路线图**: OAuth 2.1 + PKCE (Q2), SAML/OIDC 企业集成

#### 与 Agent-to-Agent 通信的关系

**当前定位**: MCP 是 Tool Layer，不是 Communication Layer。它解决 "Agent 如何访问外部工具和数据" 的问题。

**但有演进趋势**:
- **Q3 2026 路线图**: Agent-to-Agent Coordination — 一个 Agent 可以通过 MCP 调用另一个 Agent，就像调用 Tool Server 一样
- 这将创造层级化的 Agent 架构：Orchestrator Agent → Sub-Agent (通过 MCP)
- **Q4 2026**: MCP Registry（策展的 Server 目录 + 安全审计）

#### 采用状况 — 压倒性领先

- **10,000+ 活跃公共 MCP Server**
- **97M 月下载量** (截至 2026.03)
- **被所有主流 AI 产品采纳**: Claude, ChatGPT, Cursor, Gemini, Microsoft Copilot, VS Code
- **共同治理**: Anthropic, OpenAI, Block 共同创立 Agentic AI Foundation
- **事实标准地位已确立**

#### 关键差异化

- 开发者生态最大、采用最广
- 灵感来自 Language Server Protocol（LSP），把 LSP 对编程语言的标准化复制到 AI 工具生态
- 不尝试解决 Agent-to-Agent 问题（留给 A2A），但正在演进
- 简单、轻量的 JSON-RPC 设计降低了采用门槛

---

### 3. ANP (Agent Network Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 开放协议 (Protocol) |
| **定位** | 去中心化的 Agent 间通信 — "Agentic Web 时代的 HTTP" |
| **治理** | W3C AI Agent Protocol Working Group（Google, Huawei, Microsoft 参与） |
| **状态** | v1 (2025.05+), W3C 草案规范 (2026.01) |

#### 三层架构

```
┌─────────────────────────────────────────┐
│  Layer 3: Application Protocol Layer    │
│  语义化能力描述 + 高效协议管理           │
│  (JSON-LD + Schema.org)                 │
├─────────────────────────────────────────┤
│  Layer 2: Meta-Protocol Negotiation     │
│  动态协议协商 + Agent 网络自组织         │
├─────────────────────────────────────────┤
│  Layer 1: Identity & Encryption Layer   │
│  W3C DID (did:wba) + 端到端加密         │
│  去中心化身份认证                       │
└─────────────────────────────────────────┘
```

#### 通信模型

- **发现**: Agent 在标准路径（`/.well-known/agent-descriptions`）发布能力描述，搜索引擎可爬取
- **身份**: W3C Decentralized Identifiers (DID)，特别是 `did:wba` 方法
- **认证**: 加密签名验证，无需中央证书颁发机构
- **消息格式**: JSON-LD + Schema.org 语义词汇
- **传输**: HTTPS + TLS

#### 安全模型

- HTTPS-hosted DID Documents + 签名验证
- 端到端加密
- 去中心化——无需信任中央权威
- 接口加密签名防篡改
- 自动化描述爬取 + 已知良好哈希比对

#### 关键差异化

- **唯一真正去中心化的 Agent 协议**（不依赖中央注册中心）
- W3C 标准支持——有机构性的长期生命力
- Meta-Protocol 层允许 Agent 在运行时协商通信协议
- 适合跨组织、开放互联网场景
- 但: 协商开销大，生态还在早期

---

## Tier 2: 框架级多 Agent 通信

### 4. OpenAI Agents SDK

| 维度 | 详情 |
|------|------|
| **类型** | 框架 (Framework) |
| **前身** | Swarm (实验性) |
| **定位** | Python SDK，轻量级多 Agent 编排 |

#### 通信模型: Handoffs + Agent-as-Tool

**两种模式**:

1. **Handoffs（移交）**
   - Agent 将对话控制权完整移交给另一个 Agent
   - 对 LLM 而言，Handoff 表现为一个 tool call（如 `transfer_to_refund_agent`）
   - LLM 自主决定何时、移交给谁
   - 移交时，历史对话被打包为单条上下文消息
   - 适用于: 路由本身是工作流的一部分

2. **Agent-as-Tool（Agent 作为工具）**
   - 通过 `Agent.as_tool()` 将 Agent 注册为工具
   - Manager Agent 保持控制权，调用 Specialist Agent 完成子任务
   - Specialist 不接管对话
   - 适用于: 有界子任务的委派

#### 编排方式

- **LLM 驱动编排**: Agent 自主规划、选择工具/Handoff
- **代码驱动编排**: 确定性工作流（结构化输出、顺序链、并行执行）

#### 消息格式

- 内部 Python 对象，非标准化协议
- v0.6.0 (2025.11): Breaking change — handoff 时历史消息折叠为单条上下文

#### 关键差异化

- 极简设计，学习成本低
- Handoff 机制优雅（LLM 自主路由 vs 编程路由）
- 不是开放协议——绑定 OpenAI 生态

---

### 5. Microsoft AutoGen / Agent Framework

| 维度 | 详情 |
|------|------|
| **类型** | 框架 (Framework) |
| **演变** | AutoGen 0.2 → 0.4 (2025.01) → 与 Semantic Kernel 合并为 Microsoft Agent Framework (2025.10) |
| **定位** | 可定制的多 Agent 对话框架 |

#### GroupChat 架构

```
External Trigger
    ↓
GroupChatMessage → Manager receives
    ↓
Manager analyzes history → LLM selects next speaker
    ↓
RequestToSpeak → selected Agent's topic
    ↓
Agent publishes GroupChatMessage → group topic
    ↓
Cycle repeats until termination
```

**三种消息类型**:
1. `GroupChatMessage`: Agent 发布到公共 topic 的内容
2. `RequestToSpeak`: Manager 指定某 Agent 发言
3. Custom Messages: 自定义消息类型

#### 通信模型

- **Pub-Sub**: 所有参与者订阅和发布到同一 topic
- **Speaker Selection**: Round-robin / LLM-based / Custom rule-based
- **终止条件**: 关键词检测、最大轮次、自定义模式
- **嵌套对话**: 支持递归 GroupChat 层级

#### 关键差异化

- 最成熟的 GroupChat 抽象
- 已与 Semantic Kernel 合并——Microsoft 全栈支持
- 支持 Human-in-the-loop
- 框架级方案，不是跨平台协议

---

### 6. LangGraph / LangChain

| 维度 | 详情 |
|------|------|
| **类型** | 框架 (Framework) |
| **定位** | 基于有向图的 Agent 编排 |

#### 多 Agent 编排模式

1. **Supervisor Pattern（监督者模式）**
   - 中央 Supervisor Agent 控制所有通信流
   - 根据上下文决定调用哪个 Worker Agent
   - `langgraph-supervisor-py` 官方库

2. **Hierarchical Pattern（层级模式）**
   - Supervisor 嵌套 Supervisor
   - 多层委派

3. **Collaborative Pattern（协作模式）**
   - Scatter-Gather: 任务分发到多个 Agent，结果汇总
   - Pipeline: 不同 Agent 处理流水线的不同阶段

#### 核心架构

- **StateGraph**: 有向无环图 (DAG)
- **Nodes**: Agent、函数、决策点
- **Edges**: 数据流向
- **Checkpointing**: 内置检查点 + 时间旅行

#### 通信模型

- 基于共享状态 (Shared State)
- StateGraph 维护全局上下文
- 不是消息传递，而是状态传递
- 支持并行执行

#### 关键差异化

- 月搜索量 27,100（框架类最高）
- 基于图的编排——最灵活
- 内置 checkpoint/time-travel
- 与 LangChain 生态深度集成

---

### 7. CrewAI

| 维度 | 详情 |
|------|------|
| **类型** | 框架 (Framework) |
| **定位** | 角色驱动的多 Agent 协作 |

#### 角色体系

- **Manager**: 监督任务分配、监控进度
- **Worker**: 执行特定任务
- **Researcher**: 信息收集、数据分析

#### 通信机制

当 `allow_delegation=True` 时，Agent 自动获得两个协作工具:

1. **Delegation Tool**: `Delegate work to coworker(task, context, coworker)` — 将工作委派给队友
2. **Question Tool**: `Ask question to coworker(question, context, coworker)` — 向队友提问

#### 协作流程

| 模式 | 说明 |
|------|------|
| **Sequential** | 任务按序执行，通过 context 参数传递上游结果 |
| **Hierarchical** | Manager Agent 协调 Specialist Agent |
| **Consensus** | 基于共识的决策 |

#### 关键差异化

- "Crew" 隐喻——最直觉的多 Agent 模型
- 角色驱动设计，比 LangGraph 更高层抽象
- `memory=True` 允许 Agent 记住历史协作并改进策略
- 适合快速原型，但灵活性不如 LangGraph

---

## Tier 3: 新兴 & 垂直协议

### IBM ACP (Agent Communication Protocol) → 已并入 A2A

| 维度 | 详情 |
|------|------|
| **类型** | 协议 (Protocol) — 已停止独立开发 |
| **状态** | 2025 末并入 A2A |
| **原架构** | Brokered client-server，RESTful HTTP |

- 三组件: Agent Client + ACP Server (协议代理 + 注册中心) + ACP Agents
- 消息格式: MIME-typed multipart messages（支持文本、结构化数据、文件、图片）
- 发现: 中央注册中心 + Agent Detail Schemas
- 安全: Bearer tokens, mTLS, JWS 签名
- **历史意义**: 引入了 brokered registry 概念，现已贡献给 A2A

### AGP (Agent Gateway Protocol) / Agent Gateway

| 维度 | 详情 |
|------|------|
| **类型** | 网关/代理层 (Gateway) |
| **来源** | AGNTCY (Cisco 主导) |
| **实质** | 不是独立协议，而是 MCP + A2A 的统一网关 |

- Rust 编写，支持任意规模部署
- 支持 MCP (agent-to-tool) 和 A2A (agent-to-agent)
- 安全: JWT, API keys, RBAC + CEL policy engine, TLS
- 可观测性: OpenTelemetry metrics, logs, distributed tracing
- **价值**: 企业部署的统一入口，而非新协议

### AGNTCY

| 维度 | 详情 |
|------|------|
| **类型** | 组织/生态 (Initiative) |
| **来源** | Cisco 发起 |
| **治理** | 2025.07 加入 Linux Foundation |
| **定位** | "Internet of Agents 的协议栈" |

- 包含 Agent Gateway (AGP)、SLIM (Secure Low-Latency Interactive Messaging)
- 整合 ACP spec (来自 LangChain) 的发现、Group communication、Identity、Observability
- 不是单一协议，而是协议集合

### LMOS (Language Model Operating System)

| 维度 | 详情 |
|------|------|
| **类型** | 协议 (Protocol) |
| **来源** | Eclipse Foundation |
| **定位** | Internet of Agents (IoA) |

三层架构:
1. **Application Protocol Layer**: JSON-LD + 语义模型描述 Agent/Tool 能力
2. **Transport Protocol Layer**: 上下文感知的传输协议协商 (HTTP, MQTT, AMQP)
3. **Identity & Security Layer**: W3C DID + OAuth2

### Agora

| 维度 | 详情 |
|------|------|
| **类型** | Meta-Protocol |
| **定位** | LLM-powered Agent 的自适应通信 |

核心创新:
- **频繁通信**: 使用标准化 routines
- **罕见通信**: 使用自然语言
- **中间地带**: 使用 LLM 生成的 routines
- 代表了未来自适应通信的方向

### AITP (Agent Interaction & Transaction Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 协议 (Protocol) |
| **来源** | NEAR Foundation (Web3) |
| **状态** | RFC (2025.02), 尚未 v1.0 |
| **定位** | Agent 间安全交互 + 支付 |

- 聚焦跨信任边界的结构化交互
- 能力模块: 支付请求、决策协商、数据查询、链上身份/钱包验证
- 愿景: 大部分在线交互由 AI Agent 代理完成
- Web3 导向——与主流协议生态有距离

### UCP (Universal Commerce Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 协议 (Protocol) |
| **来源** | Google，2026.03 重大更新 |
| **定位** | Agentic Commerce 的开放标准 |

- 合作方: Shopify, Etsy, Wayfair, Target, Walmart, Visa, Mastercard, Stripe, Adyen
- 能力: Checkout, Identity linking, Order management, Cart, Product catalog
- 让 AI Agent 可以直接代理用户完成购物全流程
- **对 Agent Economy 最直接相关的协议**

### agents.json

| 维度 | 详情 |
|------|------|
| **类型** | 规范 (Specification) |
| **状态** | 疑似废弃 (最后更新 2025.02, Demo 失效, Discord 过期) |

- 标准化的机器可读格式，声明 AI-compatible 接口和工作流
- 类似 robots.txt 但面向 AI Agent
- 想法好但执行不力，已被 MCP + A2A 的 Agent Card 取代

### AP2 (Agent Payment Protocol)

| 维度 | 详情 |
|------|------|
| **类型** | 协议 (Protocol) |
| **定位** | Agent 间金融交易 |

- 角色分离: Shopping Agent, Merchant, Credentials Provider, Processor
- 三种 Mandate: Cart Mandate, Intent Mandate, Payment Mandate
- 加密签名的数字合约
- 限制 Agent 消费权限

---

## Tier 0: 历史标准 — FIPA ACL

| 维度 | 详情 |
|------|------|
| **类型** | 标准 (Standard) |
| **组织** | IEEE FIPA (Foundation for Intelligent Physical Agents) |
| **时代** | 1990s 末 |
| **定位** | 最早的 Agent 通信语言标准 |

#### 核心设计

- 基于 **Speech Act Theory**（言语行为理论）
- **20+ performatives**: inform, request, propose, refuse, agree 等
- 消息字段: sender, receiver, content, ontology, language, protocol 等（12 个字段）
- 只有 performative 是必填字段

#### 对现代协议的影响

| FIPA ACL 概念 | 现代继承 |
|---------------|----------|
| Performatives (inform/request) | A2A Task lifecycle, MCP Tools |
| Agent Directory (DF) | A2A Agent Card, ANP agent-descriptions |
| Ontology 字段 | JSON-LD in ANP/LMOS |
| Content Language | 自然语言 + JSON 混合 |
| IIOP/HTTP 传输 | HTTP + SSE (全面 web-native) |

#### 历史意义

- 确立了 "Agent 通信需要结构化意图表达" 的范式
- Performatives → 现代 Task 状态机的前身
- Agent Directory → Agent Card / Registry 的前身
- 但: 过于学术化，实际工业采用率低

---

## 协议对比矩阵

| 维度 | MCP | A2A | ANP | ACP (已并入A2A) |
|------|-----|-----|-----|-----------------|
| **类型** | Protocol | Protocol | Protocol | Protocol |
| **方向** | Agent↔Tool (纵向) | Agent↔Agent (横向) | Agent↔Agent (去中心化) | Agent↔Agent (Brokered) |
| **消息格式** | JSON-RPC 2.0 | JSON-RPC over HTTP | JSON-LD + Schema.org | MIME multipart |
| **传输** | HTTP, Stdio, SSE | HTTP, SSE, gRPC | HTTPS + TLS | HTTP |
| **发现** | 手动配置/静态 URL | Agent Card (`/.well-known/agent.json`) | DID + 搜索引擎爬取 | 中央注册中心 |
| **身份认证** | Token-based, OAuth 2.1 (计划) | DID / Bearer / OAuth 2.0 | W3C DID (did:wba) | Bearer, mTLS, JWS |
| **状态管理** | 有状态连接 | Task 生命周期 | 无状态 (DID token) | Session-aware |
| **流式** | SSE | SSE + Push Notification | N/A | Incremental streams |
| **治理** | Linux Foundation (AAIF) | Linux Foundation | W3C Working Group | → A2A |
| **采用度** | ★★★★★ | ★★★☆☆ | ★★☆☆☆ | ★☆☆☆☆ (已停) |

| 维度 | OpenAI Agents SDK | AutoGen | LangGraph | CrewAI |
|------|-------------------|---------|-----------|--------|
| **类型** | Framework | Framework | Framework | Framework |
| **通信模型** | Handoff + Agent-as-Tool | Pub-Sub GroupChat | Shared State Graph | Delegation + Question |
| **编排模式** | LLM-driven / Code-driven | Manager-based Speaker Selection | Supervisor / Hierarchical / Scatter-Gather | Sequential / Hierarchical / Consensus |
| **消息格式** | 内部 Python 对象 | GroupChatMessage / RequestToSpeak | State Dict | Task context |
| **跨框架互通** | ❌ | ❌ | ❌ | ❌ |
| **开源** | ✅ | ✅ | ✅ | ✅ |
| **采用度** | ★★★★☆ | ★★★☆☆ | ★★★★★ | ★★★☆☆ |

---

## 2026 协议格局分析

### 已确立的分层共识

```
"Start with MCP → Add A2A when multi-agent coordination is needed
 → Evaluate commerce protocols (UCP/AITP) when autonomous transacting required"
```

### 关键趋势

1. **协议整合加速**
   - IBM ACP → 并入 A2A
   - agents.json → 实质废弃
   - AGNTCY → 整合层而非竞争协议
   - MCP + A2A 双寡头格局

2. **治理机构化**
   - Linux Foundation 成为 Agent 协议的 "联合国"
   - W3C 介入 ANP（去中心化方向）
   - NIST 启动 AI Agent Standards Initiative (2026.02)
   - Agentic AI Foundation (AAIF): Anthropic + OpenAI + Block 共治 MCP

3. **Commerce Layer 崛起**
   - Google UCP: 与 Shopify, Walmart, Visa 等合作
   - AITP: Web3/NEAR 生态
   - AP2: Agent 支付
   - 这是 Agent Economy 的直接基础设施

4. **安全成为一等公民**
   - Microsoft 发布 Agent Governance Toolkit (2026.04)
   - A2A v0.3: Agent Card 数字签名
   - MCP 路线图: OAuth 2.1 + PKCE (Q2 2026)
   - NIST Federal AI Agent 安全标准

### 推荐采用路径 (2026)

| 阶段 | 协议 | 用途 |
|------|------|------|
| **Stage 1** | MCP | Agent → Tool/Data 连接 |
| **Stage 2** | A2A | Agent ↔ Agent 协作 |
| **Stage 3** | UCP / AITP | Agent Commerce 交易 |
| **Stage 4** | ANP (观望) | 开放互联网去中心化 Agent 网络 |

---

## 对 Agent Economy 基础设施的启示

### 直接相关的发现

1. **Agent 服务化 = A2A Agent Card**
   - 每个 Agent Service 需要一个标准化的 Agent Card
   - 这就是 Agent 的 "简历" + "API Spec"
   - 发布在 `/.well-known/agent.json`
   - 发现问题的标准答案

2. **Agent Marketplace = MCP Registry + A2A Agent Cards**
   - MCP Registry (Q4 2026): 策展的 Server 目录 + 安全审计 + SLA
   - A2A Agent Card: Agent 能力广告
   - 结合两者 = Agent 市场的技术底座

3. **Agent 计费 = Task 生命周期**
   - A2A 的 Task 有状态: submitted → working → completed
   - 天然适合按 Task 计费
   - Task Artifact = 可计量的输出

4. **Agent 间支付 = UCP / AITP / AP2**
   - UCP 最有可能成为主流（Google + Shopify + Visa 背书）
   - AITP 是 Web3 替代方案
   - AP2 专注支付安全

5. **Agent 信任 = DID + Agent Card Signature**
   - ANP 的 DID 方案最优雅但采用率低
   - A2A v0.3 的 Agent Card 签名是务实选择
   - 信任链: 签名 → 验证 → 评级 → Marketplace

6. **框架无关性是关键**
   - OpenAI SDK / AutoGen / LangGraph / CrewAI 各自为政
   - 但通过 MCP + A2A 可以实现跨框架互通
   - Agent Economy 基础设施必须是协议级的，不能绑定框架

### 值得持续跟踪的信号

- [ ] MCP Agent-to-Agent Coordination (Q3 2026) — 会不会侵蚀 A2A 的地盘？
- [ ] MCP Registry (Q4 2026) — 会不会成为事实上的 Agent Marketplace？
- [ ] ANP W3C 标准进展 — 去中心化 Agent 身份的未来
- [ ] UCP 第二次更新 — Google 在 Agent Commerce 的野心
- [ ] NIST AI Agent Standards — 美国政府监管信号
- [ ] Microsoft Agent Governance Toolkit 的实际采用

---

## Sources

### Google A2A
- [Agent2Agent protocol is getting an upgrade | Google Cloud Blog](https://cloud.google.com/blog/products/ai-machine-learning/agent2agent-protocol-is-getting-an-upgrade)
- [Announcing the Agent2Agent Protocol (A2A) | Google Developers Blog](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/)
- [Linux Foundation launches A2A Protocol Project](https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents)
- [What Is Agent2Agent (A2A) Protocol? | IBM](https://www.ibm.com/think/topics/agent2agent-protocol)
- [What happened to Google's A2A? | fka.dev](https://blog.fka.dev/blog/2025-09-11-what-happened-to-googles-a2a/)

### Anthropic MCP
- [Model Context Protocol | Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)
- [Donating MCP and establishing AAIF | Anthropic](https://www.anthropic.com/news/donating-the-model-context-protocol-and-establishing-of-the-agentic-ai-foundation)
- [MCP Specification 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25)
- [A Year of MCP | Pento](https://www.pento.ai/blog/a-year-of-mcp-2025-review)
- [Anthropic and OpenAI Join Forces on MCP Apps Extension](https://inkeep.com/blog/anthropic-openai-mcp-apps-extension)

### OpenAI Agents SDK
- [Handoffs - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/handoffs/)
- [Agent orchestration - OpenAI Agents SDK](https://openai.github.io/openai-agents-python/multi_agent/)
- [OpenAI Agents SDK Review | Mem0](https://mem0.ai/blog/openai-agents-sdk-review)

### Microsoft AutoGen
- [Multi-agent Conversation Framework | AutoGen 0.2](https://microsoft.github.io/autogen/0.2/docs/Use-Cases/agent_chat/)
- [Group Chat — AutoGen](https://microsoft.github.io/autogen/stable//user-guide/core-user-guide/design-patterns/group-chat.html)
- [AutoGen to Microsoft Agent Framework Migration Guide](https://learn.microsoft.com/en-us/agent-framework/migration-guide/from-autogen/)

### LangGraph
- [LangGraph: Agent Orchestration Framework](https://www.langchain.com/langgraph)
- [LangGraph Multi-Agent Orchestration Guide | Latenode](https://latenode.com/blog/ai-frameworks-technical-infrastructure/langgraph-multi-agent-orchestration/langgraph-multi-agent-orchestration-complete-framework-guide-architecture-analysis-2025)
- [langgraph-supervisor-py | GitHub](https://github.com/langchain-ai/langgraph-supervisor-py)

### CrewAI
- [CrewAI Collaboration Docs](https://docs.crewai.com/en/concepts/collaboration)
- [CrewAI Guide | Mem0](https://mem0.ai/blog/crewai-guide-multi-agent-ai-teams)
- [CrewAI | GitHub](https://github.com/crewaiinc/crewai)

### ANP
- [Agent Network Protocol](https://agent-network-protocol.com/)
- [ANP Technical White Paper | arXiv](https://arxiv.org/abs/2508.00007)
- [ANP | GitHub](https://github.com/agent-network-protocol/AgentNetworkProtocol)

### Comparative / Survey
- [A Survey of Agent Interoperability Protocols | arXiv](https://arxiv.org/html/2505.02279v1)
- [Top AI Agent Protocols in 2026 | GetStream](https://getstream.io/blog/ai-agent-protocols/)
- [Best Multi-Agent Frameworks in 2026 | GuruSup](https://gurusup.com/blog/best-multi-agent-frameworks-2026)
- [Comparative Analysis: MCP, ANP, Agora, agents.json, LMOS, AITP](https://agent-network-protocol.com/blogs/posts/agent-communication-protocols-comparison.html)
- [A2A, ACP, and agents.json: What's Next | Dawn Liphardt](https://www.dawnliphardt.com/a2a-acp-and-agents-json-whats-next-for-these-agent-based-protocols/)

### Commerce & Emerging
- [Universal Commerce Protocol | Google Developers](https://developers.google.com/merchant/ucp)
- [AITP: Agent Interaction & Transaction Protocol](https://aitp.dev/)
- [AGNTCY ACP Spec | GitHub](https://github.com/agntcy/acp-spec)
- [Agent Gateway](https://agentgateway.dev/)
- [NIST AI Agent Standards Initiative](https://labs.cloudsecurityalliance.org/research/csa-research-note-nist-ai-agent-standards-federal-framework/)
- [MCP vs A2A Protocols 2026 | OneReach](https://onereach.ai/blog/guide-choosing-mcp-vs-a2a-protocols/)
- [AI Agent Protocol Ecosystem Map 2026](https://www.digitalapplied.com/blog/ai-agent-protocol-ecosystem-map-2026-mcp-a2a-acp-ucp)

### FIPA ACL
- [FIPA Agent Communication Language | SmythOS](https://smythos.com/developers/agent-development/fipa-agent-communication-language/)
- [Agent Communications Language | Wikipedia](https://en.wikipedia.org/wiki/Agent_Communications_Language)
- [Agent Communication Protocols Explained | DigitalOcean](https://www.digitalocean.com/community/tutorials/agent-communication-protocols-explained)
