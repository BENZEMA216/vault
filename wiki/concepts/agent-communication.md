# Agent Communication（Agent 通信）

> Agent 之间交换信息、协调行为、协作完成任务的机制和协议

## 核心要点

- Agent 通信是 Multi-Agent 系统的基础能力，决定了协作质量、效率和可扩展性
- 2026 年已形成 **MCP + A2A 双协议栈**事实标准：MCP 解决纵向（Agent → Tool），A2A 解决横向（Agent ↔ Agent）
- 学术研究识别出 7 种核心通信模式，从经典 Debate 到前沿 Latent Space Communication
- **最大空白**：Agent 间如何定价、结算、分润几乎没有标准化产品 — Agent Economy 的核心机会

## 协议分层

### Agent-to-Tool 层
- **MCP** (Anthropic) — 97M 月下载，事实标准。JSON-RPC 2.0，四大原语（Resources / Tools / Prompts / Sampling）。2025.12 捐赠 Linux Foundation AAIF，OpenAI/Google/MS/Amazon 全采用

### Agent-to-Agent 层
- **A2A** (Google) — Agent Card + Task lifecycle（submitted → working → completed），100+ partner。v0.3 支持 gRPC + Agent Card 数字签名。2025.06 捐赠 Linux Foundation
- **ACP** (IBM → 并入 A2A) — Brokered client-server 架构，引入中央注册中心概念，2025 末并入 A2A

### Agent Network 层
- **ANP** — W3C DID-based 去中心化身份 + 无需中央注册中心的 Agent 发现。三层架构：Identity & Encryption → Meta-Protocol Negotiation → Application Protocol。W3C 草案规范 (2026.01)
- **AGNTCY** — 6 层架构（OASF → Agent Directory → SLIM → Identity → Observability → Security），Cisco/Dell/Google Cloud/Oracle 创始成员，Linux Foundation 项目

### Agent Commerce 层
- **UCP** (Google + Shopify + Visa) — Agentic Commerce 开放标准，支持 Checkout/Identity/Order/Cart/Catalog
- **AITP** (NEAR Foundation) — Web3 导向的 Agent 交互 + 支付协议
- **AP2** — Agent Payment Protocol，角色分离 + 加密签名数字合约

## 七种通信模式

1. **Debate/Discussion** — 多 Agent 辩论提升推理质量。奠基论文: Du et al. (ICML 2024)。2025 年发现 debate 效果存在天花板，需要异构 Agent + 动态辩论突破
2. **Role-Playing** — 角色扮演 + inception prompting。CAMEL (NeurIPS 2023) 开创，ChatDev (ACL 2024) 引入 Chat Chain，MetaGPT (ICLR 2024) 用 SOP 编码替代无结构对话
3. **Hierarchical Delegation** — 管理者-工作者委托。DyLAN 证明动态 Agent 选择优于静态分配（+13% MATH），AutoGen 提供最通用的 conversation framework
4. **Blackboard** — 共享工作空间 + 自愿响应（非指派式）。端到端任务成功率相对提升 +57%，数据发现准确度 +9%
5. **Market-Based** — 经济机制驱动的协调，Agent 交换概率信念通过"交易"趋向共识。准确率比 single-shot baseline 高 +10%，保持推理可解释性
6. **Emergent** — Agent 自发发展共享语言。仅需 4 轮通信即可发展出具有组合性、泛化性、多义性的共享语言
7. **Latent Space** — 隐空间直接传输，绕过自然语言瓶颈。C2C: 延迟降低 2.5x，准确率 +6.4-14.2%；LatentMAS: 比 text-based 高效 235-471x

## 框架层

- **OpenAI Agents SDK** — Handoff 机制（LLM 自主路由）+ Agent-as-Tool（保持控制权委派）
- **AutoGen** — GroupChat + Pub-Sub 对话编程，支持 Round-robin / LLM-based Speaker Selection，已与 Semantic Kernel 合并为 Microsoft Agent Framework
- **LangGraph** — Shared State Graph，Supervisor / Hierarchical / Scatter-Gather 模式，月搜索量 27,100（框架类最高）
- **CrewAI** — 角色编排 + Delegation Tool + Question Tool，"Crew" 隐喻最直觉的多 Agent 模型

## 产品格局

### Enterprise 四巨头
- **Salesforce Agentforce** — $8 亿 ARR，29,000 deals，AgentExchange Marketplace
- **IBM watsonx Orchestrate** — 80+ 企业应用集成，150+ Agent + 工具 catalog
- **Microsoft Copilot Studio** — A2A + MCP 双协议原生支持，跨 first/second/third-party Agent 通信
- **Amazon Bedrock Agents** — Multi-Agent Collaboration GA，Supervisor + Specialist 模式，I/O wait 免费省 30-70%

### 中国生态
- **Coze 2.0** (字节跳动) — 开源 CozeStudio + CozeLoop (Apache 2.0)，Agent Skills Store，入门门槛最低
- **Dify** — 60K+ GitHub stars，开源生态最活跃，全球化定位
- **蚂蚁 Agentar** — 金融级 Agent 平台，中国信通院最高 5 级认证，按效果付费

### 市场数据
- Agentic AI 市场 (2025): $70.6 亿 → 2032: $932 亿 (CAGR 44.6%)
- 中国企业级 Agent 市场 (2025): ~1900 亿 RMB (CAGR 110%+)
- 79% 企业已采用 Agent，仅 11% 进入生产 — 最大 deployment gap

## 最大空白

**Agent 间如何定价、结算、分润几乎没有标准化产品** — Agent Economy 的核心机会。具体空白：

1. Agent 经济层缺失 — 谁来做跨平台的 Agent pricing/billing/settlement？
2. Agent Discovery 仍原始 — ANS/Agent-Reg 仅在 proposal 阶段
3. 跨平台 Agent 互操作 — A2A 有 50+ partner 但实际生产部署极少
4. 中国生态与全球协议脱节 — MCP 有一定采用但 A2A/AGNTCY 参与度极低
5. AGNTCY 的 6 层架构是最好的参考框架 — 但缺少经济层

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/articles/agent-communication/protocols-and-standards]] | 协议分层、MCP/A2A/ANP/AGNTCY/UCP 详细分析、FIPA ACL 历史 |
| [[raw/articles/agent-communication/academic-research]] | 7 种通信模式、25+ 篇论文分析、Benchmark 评估体系 |
| [[raw/articles/agent-communication/product-landscape]] | 40+ 产品/平台格局分析、商业模式分类、中国生态 |
| [[raw/articles/learning-notes/0410-agent2agent]] | A2A 协议学习笔记 |

## 关联概念

- [[concepts/agent-loop]] — 通信是 Agent Loop 的外部接口
- [[concepts/sub-agent-architecture]] — 子 Agent 通信是最基础的 agent-to-agent 场景
- [[concepts/tool-routing]] — MCP 作为 Agent-to-Tool 通信层
- [[concepts/harness-engineering]] — 通信协议是 Harness 的编排层
- [[concepts/context-engineering]] — 通信效率直接影响 context budget

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
