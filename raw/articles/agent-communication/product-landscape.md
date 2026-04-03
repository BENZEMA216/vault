# Agent Communication 商业产品全景报告

> 调研时间: 2026-04-03 | 覆盖范围: 全球 + 中国生态

---

## 一、市场概览

### 市场规模

| 指标 | 数据 |
|------|------|
| Agentic AI 市场 (2025) | $70.6 亿 |
| Agentic AI 市场 (2032 预测) | $932 亿, CAGR 44.6% |
| 中国企业级 Agent 市场 (2025) | ~1900 亿 RMB, CAGR 110%+ |
| 自主 AI Agent 市场 (2026 预测) | $85 亿 |
| AI Agent crypto 项目 (2025 末) | 550+, 市值 $43.4 亿 |

### 关键趋势

1. **协议标准化**: MCP (Anthropic, 2024.11) + A2A (Google, 2025.04) + ACP (IBM) 三大协议并行，已统一纳入 Linux Foundation AAIF
2. **Multi-Agent 爆发**: 企业平均使用 12 个 AI Agent, 预计 2 年内增长 67%
3. **Agent Marketplace 兴起**: AWS 2700+ Agent listings, Google/Salesforce/Kore.ai 各有 marketplace
4. **79% 企业已采用 Agent, 仅 11% 进入生产**——最大的 deployment gap

---

## 二、Agent Orchestration Platforms (编排平台)

### 2.1 CrewAI

| 维度 | 详情 |
|------|------|
| **做什么** | 基于角色的 Multi-Agent 编排框架, Agent 以 "Crew" 形式协作 |
| **通信模型** | Role-based agent design, 定义 Agent 角色 + 任务 + 流程, Agent 间通过 Crew 内部消息传递 |
| **定价** | Free: 50 exec/月; Pro: $6K/年 (1000 exec/月); Enterprise: $60K/年; Ultra: $120K/年 |
| **阶段** | GA, 开源核心 + 商业 Cloud |
| **客户** | IBM (WatsonX.AI), PwC, Piracanjuba |
| **差异化** | Python-first, 最直觉的 multi-agent 抽象, 开源 26K+ GitHub stars |

### 2.2 Microsoft Agent Framework (Semantic Kernel + AutoGen)

| 维度 | 详情 |
|------|------|
| **做什么** | 合并 AutoGen 动态多智能体编排 + Semantic Kernel 企业级生产能力 |
| **通信模型** | 异步消息驱动, event-driven + request/response; 支持 Sequential/Concurrent/GroupChat/Handoff/Magentic 编排模式 |
| **定价** | 开源免费 (MIT), Copilot Studio 商业版按 Microsoft 365 许可 |
| **阶段** | Public Preview (2025.10), GA 目标 Q1 2026 |
| **客户** | Microsoft 365 生态全量企业 |
| **差异化** | C#/Python/Java 多语言; 原生 Azure 集成; SOC 2/HIPAA 合规; Graph-based workflow |

### 2.3 Kore.ai

| 维度 | 详情 |
|------|------|
| **做什么** | 企业级 AI Agent 平台, 多 Agent 编排 + 对话式 AI |
| **通信模型** | 多种编排模式: 层级式/自主式/混合式, Agent 通过平台内部 bus 通信 |
| **定价** | Enterprise 定制, 起步 ~$300K/年, 按 "billing session" (15 分钟块) 计费 |
| **阶段** | GA |
| **客户** | Morgan Stanley, Pfizer, Coca-Cola |
| **差异化** | 垂直金融/医疗合规能力, Gartner 认可 |

### 2.4 Letta (formerly MemGPT)

| 维度 | 详情 |
|------|------|
| **做什么** | Stateful Agent 平台, Agent 具有持久记忆和自我改进能力 |
| **通信模型** | LLM-as-OS 范式, Agent 自主管理 memory/context/reasoning loop; 支持 multi-agent orchestration |
| **定价** | 开源免费, Letta Cloud beta 免费试用 |
| **阶段** | Beta / Early Stage, Seed $10M (Felicis 领投, 2024.09) |
| **客户** | 开发者社区为主 |
| **差异化** | Memory-first 架构, Agent 跨会话记忆, Terminal-Bench #1 model-agnostic coding agent |

### 2.5 Julep

| 维度 | 详情 |
|------|------|
| **做什么** | Serverless AI Workflow 平台, "Firebase for AI Agents" |
| **通信模型** | Multi-step task workflow, GitHub Actions 风格定义, 支持分支/循环/并行, 基于 Temporal |
| **定价** | 开源自托管免费; 托管后端 **2025.12.31 关闭** |
| **阶段** | ⚠️ 托管服务已关闭, 仅剩开源自托管 |
| **差异化** | 长期记忆 + 复杂工作流编排, 但商业化失败 |

### 2.6 Fixie.ai → Ultravox

| 维度 | 详情 |
|------|------|
| **做什么** | 原为 Agent 开发平台, **已 pivot 为 Voice AI 平台 (Ultravox)** |
| **通信模型** | 已不再做 Agent 通信, 转为实时语音处理 |
| **定价** | $29/月 (原 Agent 平台) |
| **阶段** | Pivot 完成, 品牌转为 Ultravox |
| **融资** | $17M (Redpoint Ventures) |

### 2.7 Relevance AI

| 维度 | 详情 |
|------|------|
| **做什么** | No-code Agent-as-a-Service 平台, 预构建 Agent 模板 + 工作流自动化 |
| **通信模型** | Workflow editor 中 Agent 按步骤串联, 支持 embedding/memory/tool integration |
| **定价** | Free: 200 Actions/月; Pro: $19/月 (2500 agent runs); 2025.09 改为 Actions + Vendor Credits 分离计费 |
| **阶段** | GA, Series B $24M (Bessemer 领投) |
| **差异化** | No-code 最友好, 预构建模板丰富, 可自带 API key |

---

## 三、Agent Marketplaces / Networks (市场与网络)

### 3.1 Salesforce AgentExchange

| 维度 | 详情 |
|------|------|
| **做什么** | Agentforce 的 Agent Marketplace, 发现/试用/购买 partner 预构建 Agent |
| **通信模型** | Agent 通过 Agentforce 平台编排, Slack 作为对话层, Data 360 统一搜索跨 200+ 数据源 |
| **定价** | Agentforce ARR $8 亿, 169% YoY; 29,000 deals closed |
| **阶段** | GA (Spring '26) |
| **客户** | Salesforce 全量 CRM 客户 |
| **差异化** | CRM 生态绑定, Agentic Enterprise Search, Nvidia Agent Toolkit 集成 |

### 3.2 AWS Marketplace (Agent Category)

| 维度 | 详情 |
|------|------|
| **做什么** | AWS 云上 Agent 分发, 2700+ listings |
| **通信模型** | Bedrock Agents 编排, Lambda 集成, OpenAPI schema Action Groups |
| **定价** | Usage-based, I/O wait 免费 (省 30-70% 成本) |
| **阶段** | GA |
| **差异化** | AWS 生态锁定, consumption-based 弹性 |

### 3.3 Google Cloud AI Agent Marketplace

| 维度 | 详情 |
|------|------|
| **做什么** | Gemini Enterprise 集成的 partner Agent 生态 |
| **通信模型** | Vertex AI Agent Builder + ADK, A2A protocol 原生支持 |
| **定价** | $0.00994/vCPU-hour; Search $1.50-$6/1K queries; Memory $0.25/1K events |
| **阶段** | GA |
| **差异化** | A2A 协议发起者, ADK <100 行 Python 建 multi-agent |

### 3.4 Kore.ai AI Marketplace

| 维度 | 详情 |
|------|------|
| **做什么** | 预构建 Agent + 工具市场 |
| **阶段** | GA |

---

## 四、Blockchain-Based Agent Networks (去中心化 Agent 网络)

### 4.1 Artificial Superintelligence Alliance (ASI) = Fetch.ai + SingularityNET + Ocean Protocol

| 维度 | 详情 |
|------|------|
| **做什么** | 去中心化 Autonomous Economic Agents (AEAs) 基础设施 |
| **通信模型** | 多层网络, AEA 自主协商交易; 跨 Ethereum/Cosmos/Cardano/Polkadot/Solana 五链 |
| **代币** | ASI (三方合并, 2024.07), 市值曾达 $92 亿 |
| **用例** | 智能交通路线协商, DeFi 自动化, 智能家居能源协调 |
| **阶段** | GA (基础设施), 生态建设中 |
| **差异化** | 最大独立开源 AI 联盟, AGI 愿景, 跨链互操作 |

### 4.2 Naptha.ai

| 维度 | 详情 |
|------|------|
| **做什么** | 去中心化 multi-agent 开发框架, 100 万 Agent 同时编排 |
| **通信模型** | 异构 Agent 跨框架/跨语言协作, 16+ 开源模型 (DeepSeek R1, Qwen 2.5, Nous Hermes 3), 去中心化知识存储 |
| **阶段** | V1 Beta |
| **差异化** | Multi-Agent Universal Intelligence (MAUI) 愿景, 去中心化替代方案 |

---

## 五、Agent Communication Infrastructure (通信基础设施)

### 5.1 协议层: MCP vs A2A vs ACP

| 协议 | 发起方 | 发布时间 | 功能定位 | 采用率 |
|------|--------|----------|----------|--------|
| **MCP** (Model Context Protocol) | Anthropic | 2024.11 | Agent ↔ Tool/Context (垂直连接) | 9700 万月下载 (Python+TS), OpenAI/Google/MS/Amazon 全采用 |
| **A2A** (Agent-to-Agent) | Google | 2025.04 | Agent ↔ Agent (水平通信) | 50+ 合作伙伴, v0.3 发布, Linux Foundation 项目 |
| **ACP** (Agent Communication Protocol) | IBM | 2025 | Agent 间通信 | 企业级采用 |

**关系**: MCP 和 A2A **互补而非竞争** — MCP 解决单 Agent 的工具接入, A2A 解决多 Agent 间发现与协作。两者已统一归入 Linux Foundation AAIF (2025.12), 由 OpenAI/Anthropic/Google/Microsoft/AWS/Block 六家共建。

### 5.2 AGNTCY (Internet of Agents)

| 维度 | 详情 |
|------|------|
| **做什么** | Agent 互联网基础设施: 发现/身份/消息/可观测性 |
| **架构** | 6 层: OASF (schema) → Agent Directory → SLIM (gRPC 消息) → Identity → Observability → Security |
| **关键技术** | 量子安全加密, MLS 安全协议, 去中心化身份 |
| **成员** | Linux Foundation 项目, Cisco/Dell/Google Cloud/Oracle/Red Hat 创始成员, 75+ 公司, LangChain/Galileo 核心维护 |
| **阶段** | Early stage, 组件持续演进 |
| **差异化** | 最完整的 Agent 互联网架构设计, 工业级治理 |

### 5.3 Agent Name Service (ANS)

| 维度 | 详情 |
|------|------|
| **做什么** | DNS-for-Agents, PKI 身份验证 + 通用注册机制 |
| **差异化** | 补充 MCP/A2A 的 discovery gap, 增强跨协议信任 |
| **阶段** | Proposal / Early implementation |

### 5.4 Agent Registry (Agent-Reg for A2A)

| 维度 | 详情 |
|------|------|
| **做什么** | 开放 Agent 注册表, 基于 A2A 协议的 Agent Card 发布与发现 |
| **阶段** | 开源项目 |

---

## 六、Enterprise Agent Platforms (企业级平台)

### 6.1 Salesforce Agentforce

| 维度 | 详情 |
|------|------|
| **做什么** | CRM 原生 Agent 平台, 从构建到部署到治理 |
| **通信模型** | Agent Builder (对话式 + 低代码 + pro-code); Slack 作为编排层; Data 360 跨源搜索 |
| **定价** | ARR $8 亿, 按 "agentic work units" 计费 |
| **阶段** | GA (Spring '26 大更新) |
| **客户** | 29,000 deals, 2.4B agentic work units 交付 |
| **差异化** | Specialist 认证引入 Multi-Agent Interoperability; A2A 支持 |

### 6.2 IBM watsonx Orchestrate

| 维度 | 详情 |
|------|------|
| **做什么** | 企业 Agent 编排中枢, 80+ 企业应用集成 |
| **通信模型** | No-code to pro-code Agent 构建; 150+ Agent + 工具 catalog; 跨供应商 Agent 共享 context + 任务交接 |
| **定价** | 企业定制, 按使用量 |
| **阶段** | GA |
| **集成** | Adobe, AWS, Microsoft, Oracle, Salesforce Agentforce, SAP, ServiceNow, Workday |
| **客户** | Box, MasterCard, Oracle, Salesforce, ServiceNow, 11x |
| **差异化** | 最广泛的企业应用集成 (80+), 预构建 domain agent (HR/Sales/Procurement), Agent Catalog |

### 6.3 Microsoft Copilot Studio

| 维度 | 详情 |
|------|------|
| **做什么** | Microsoft 365 生态的 Agent 构建与编排 |
| **通信模型** | A2A 协议 GA; M365 Agents SDK 编排; Fabric 集成; MCP 原生支持 |
| **定价** | Microsoft 365 许可绑定 |
| **阶段** | GA (Multi-Agent 能力 2026 初 GA) |
| **差异化** | A2A + MCP 双协议原生支持; 跨 first/second/third-party Agent 通信 |

### 6.4 Amazon Bedrock Agents

| 维度 | 详情 |
|------|------|
| **做什么** | AWS 上的 Agent 构建/部署/管理, Multi-Agent Collaboration |
| **通信模型** | Supervisor Agent 协调多个 Specialist Agent; OpenAPI schema Action Groups; Lambda 原生集成 |
| **定价** | Consumption-based, I/O wait 免费 (典型省 30-70%), AgentCore 弹性计费 |
| **阶段** | GA (Multi-Agent Collaboration GA 2025) |
| **差异化** | AWS 生态深度集成, 1 小时 prompt caching, server-side tool use |

### 6.5 Google Vertex AI Agent Builder + ADK

| 维度 | 详情 |
|------|------|
| **做什么** | Google Cloud 上构建 multi-system Agent |
| **通信模型** | Agent Development Kit (ADK) <100 行 Python; A2A 协议原生; Agent Engine 托管 |
| **定价** | vCPU $0.00994/hr; Memory $0.009/GB-hr; Session/Memory $0.25/1K events |
| **阶段** | GA |
| **差异化** | A2A 协议发起者, Gemini 原生, ADK 极简开发体验 |

---

## 七、Developer Tools for Multi-Agent (开发者工具)

### 7.1 Smolagents (HuggingFace)

| 维度 | 详情 |
|------|------|
| **做什么** | 极简 code-centric Agent 框架, ~1000 行核心代码 |
| **通信模型** | CodeAgent 用代码执行 actions (非 JSON), 原生函数嵌套/循环/条件; MCP server 集成 |
| **定价** | 开源免费 |
| **阶段** | GA, 26K+ GitHub stars |
| **差异化** | 最小化设计哲学, Agent 用代码思考而非 JSON, HuggingFace Hub 工具共享 |

### 7.2 LangChain / LangGraph

| 维度 | 详情 |
|------|------|
| **做什么** | LLM 应用开发框架 + Graph-based Agent 编排 |
| **通信模型** | Modular chain 架构, 可换 LLM/向量库; LangGraph 支持 stateful multi-agent workflow |
| **定价** | 开源免费, LangSmith 商业版 |
| **阶段** | GA, de facto 标准 |
| **差异化** | 最大社区, 最多集成; 但 API 频繁 breaking change |

### 7.3 OpenAI Agents SDK / Swarm

| 维度 | 详情 |
|------|------|
| **做什么** | OpenAI 原生 Agent 开发工具 |
| **通信模型** | Handoff-based agent routing, tool use |
| **定价** | 开源免费 + OpenAI API 用量 |
| **阶段** | GA |
| **差异化** | OpenAI 生态绑定 |

---

## 八、中国生态 (Chinese Ecosystem)

### 8.1 字节跳动 Coze / 扣子

| 维度 | 详情 |
|------|------|
| **做什么** | No-code/Low-code Agent 开发平台, 2026.01 发布 2.0 |
| **通信模型** | Multi-Agent 模式: 多个 Agent 按角色分工 (搜集员/分析师/撰写员); 拖拽式工作流; Agent Skills Store |
| **定价** | 按消息计费, 基础功能免费 |
| **阶段** | GA, 开源 CozeStudio + CozeLoop (Apache 2.0) |
| **关键功能** | Agent Plan (跨天/月任务自主分解); Agent Skills Store; Vibe Coding 环境; 多平台部署 (Discord/WhatsApp/飞书) |
| **差异化** | 入门门槛最低, 字节流量生态, 2.0 从聊天工具升级为"智能工作伙伴" |

### 8.2 Dify

| 维度 | 详情 |
|------|------|
| **做什么** | 开源 LLM 应用开发平台, Agentic Workflow Builder |
| **通信模型** | 可视化 workflow + RAG pipeline + Agent Node (Function Calling/ReAct); 自主决定调用工具/检索/响应 |
| **定价** | 自托管免费; Cloud: Sandbox 免费, Pro $59/月, Team $159/月, Enterprise 定制 |
| **阶段** | GA, 60K+ GitHub stars |
| **差异化** | 开源生态最活跃, 全球化定位, 从 prototype 到 production 一站式 |

### 8.3 百度文心智能体平台

| 维度 | 详情 |
|------|------|
| **做什么** | 四层 Agentic AI 体系: 千帆(基础设施) → 智能体平台(开发) → ERNIE Bot Agent(框架) → 文心大模型(核心) |
| **规模** | 50,000+ 开发者, 10,000+ 企业, 30,000+ 智能体 |
| **差异化** | 中文语义理解最强, 结构化数据处理优势 |

### 8.4 腾讯元器

| 维度 | 详情 |
|------|------|
| **做什么** | 零代码 Agent 平台, 微信生态深度集成 |
| **定价** | 完全免费 |
| **差异化** | 微信生态原生, 零代码最友好 |

### 8.5 阿里通义 (Tongyi Qianwen)

| 维度 | 详情 |
|------|------|
| **做什么** | 三层 Agentic AI 框架: 框架(灵活开发) → 平台(简化部署) → 应用(场景落地) |
| **差异化** | 全开发生命周期覆盖, 阿里云生态 |

### 8.6 蚂蚁数科 Agentar

| 维度 | 详情 |
|------|------|
| **做什么** | 金融级 Agent 平台, 全链路可信 |
| **定价** | 按效果付费 |
| **差异化** | 中国信通院最高 5 级认证, 亿级金融数据, 百余个核心金融 MCP 服务 |

### 8.7 BetterYeah AI

| 维度 | 详情 |
|------|------|
| **做什么** | NeuroFlow 框架, 企业级 Agent 全生命周期管理 |
| **通信模型** | 可视化 Flow GUI 编排, 800+ 业务子节点, 多 Agent 协同 |
| **差异化** | 双重开发模式 (低代码+专业代码), 多模态 RAG 融合 |

### 8.8 MasterAgent

| 维度 | 详情 |
|------|------|
| **做什么** | 多 Agent 生成与协作平台, 解决方案模板一键共享 |
| **定位** | 自主可控的"中国方案" |

### 8.9 中国电信星辰

| 维度 | 详情 |
|------|------|
| **做什么** | 运营商级 Agent 平台, 自主规划 + 工作流模式 |

### 8.10 中国移动通信 AI Agent

| 维度 | 详情 |
|------|------|
| **产品线** | 消息 Agent, 通话 Agent, 云 OS Agent |

---

## 九、核心发现与洞察

### 9.1 Agent 通信模型演进

```
2024: 单 Agent + Tool Use (MCP 范式)
  ↓
2025: Multi-Agent Orchestration (内部编排, CrewAI/AutoGen 范式)
  ↓
2025-2026: Agent-to-Agent (A2A 范式, 跨平台/跨供应商)
  ↓
2026+: Internet of Agents (AGNTCY 范式, 去中心化发现/身份/消息)
```

### 9.2 竞争格局分层

| 层级 | 玩家 | 模式 |
|------|------|------|
| **协议层** | Google (A2A), Anthropic (MCP), IBM (ACP), AGNTCY | 开源标准, Linux Foundation |
| **平台层 (Enterprise)** | Salesforce, IBM, Microsoft, AWS, Google | CRM/ERP/Cloud 绑定 |
| **平台层 (Developer)** | CrewAI, LangChain, Dify, Coze, Smolagents | 开源 + 商业 Cloud |
| **基础设施层** | AGNTCY, Naptha.ai, ASI Alliance | 去中心化, Agent 互联网 |
| **Marketplace 层** | Salesforce AgentExchange, AWS, Google Cloud, Kore.ai | Agent 分发与变现 |

### 9.3 商业模式分类

| 模式 | 代表 | 特点 |
|------|------|------|
| **Execution-based** | CrewAI, Bedrock | 按执行次数/vCPU 时间计费 |
| **Session-based** | Kore.ai | 按对话 session (15 分钟块) 计费 |
| **Action + Credit** | Relevance AI | Actions + Vendor Credits 分离 |
| **Seat + Usage** | Dify, Coze | 按席位 + 消息量 |
| **Enterprise Custom** | IBM, Salesforce, Kore.ai | $300K+/年起步 |
| **Open Source + Cloud** | CrewAI, Dify, Letta | 核心开源, 托管增值 |
| **Token Economy** | ASI Alliance, Naptha | Crypto token + 去中心化 |
| **按效果付费** | 蚂蚁 Agentar | 成果导向 |

### 9.4 关键空白与机会

1. **Agent 经济层缺失**: 目前平台解决的是"Agent 怎么通信", 但 **Agent 间如何定价、结算、分润** 几乎没有标准化产品
2. **Agent Discovery 仍原始**: ANS/Agent-Reg 仅在 proposal 阶段, 实际的 Agent 发现仍依赖中心化 marketplace
3. **跨平台 Agent 互操作**: A2A 虽有 50+ partner, 但实际生产部署极少, 86% IT 领导担心 Agent 引入更多复杂度
4. **中国生态与全球协议脱节**: 国内平台几乎都在做自己的闭环, MCP 有一定采用但 A2A/AGNTCY 参与度极低
5. **Agent 安全与信任**: 身份验证、权限控制、quantum-safe 通信仍在早期 (AGNTCY SLIM 是最前沿)
6. **Agent 可观测性**: 多 Agent 系统的 debug/trace/monitor 工具严重不足

---

## 十、与 Agent Economy 研究的关联

对于 BENZEMA216 的 Agent Economy 方向, 以下信号最值得关注:

1. **Agent Marketplace 是确定趋势** — Salesforce/AWS/Google 三巨头已入场, 但都是 closed ecosystem
2. **开放的 Agent 经济基础设施 = 巨大空白** — 谁来做跨平台的 Agent pricing/billing/settlement?
3. **中国市场 1900 亿 RMB + 110% CAGR** — 但严重缺乏 Agent 间通信标准和经济层
4. **YC W26 batch 41.5% 做 Agent infra** — 赛道验证, 但大多是垂直应用而非基础设施
5. **AGNTCY 的 6 层架构是最好的参考框架** — 但缺少经济层 (pricing/billing/marketplace)
6. **Coze 开源 (Apache 2.0)** — 可能成为中国 Agent 开发的事实标准, 值得密切跟踪

---

*Sources: Gartner, Deloitte, IDC, PitchBook, YC, 中国信通院, 各公司官网及文档*
