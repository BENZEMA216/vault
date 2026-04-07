# Wiki Log

> Append-only chronological record of wiki operations.
> Prefixed for parseability: `grep "^## \[" log.md | tail -10`

## [2026-04-03] init | Knowledge Base Created
- Migrated from Projects/Study hierarchy to AK-style raw/wiki/output
- Physical restructure: 100+ files moved to raw/
- Initial compilation: 16 concepts, 3 maps, 3 connections

## [2026-04-03] ingest | Awesome Harness Engineering
- Source: https://github.com/walkinglabs/awesome-harness-engineering
- Deep research: 10 core articles from Anthropic/LangChain/Thoughtworks/HumanLayer/Inngest
- New concepts: harness-engineering, self-verification, safe-autonomy, agent-runtime, spec-driven-development
- New map: harness-engineering
- New connection: harness-to-creative
- Updated 5 existing concepts with harness references
- Wiki state: 21 concepts, 4 maps, 4 connections

## [2026-04-03] refactor | Merged Prompt Template Fragments
- Merged 34 fragment files in agent-prompt-design/prompts/ and skills/
- Consolidated into single modular-prompt-reference.md
- Cleaned Obsidian graph of orphan nodes

## [2026-04-03] ingest | Agent Communication Deep Research
- Three parallel research streams: protocols, papers, products
- Sources: 3 raw files (protocols-and-standards, academic-research, product-landscape)
- Paper index: 30 papers with citations and quality tiers
- New concept: agent-communication
- New map: agent-communication
- New connection: communication-to-economy
- Wiki state: 22 concepts, 5 maps, 5 connections

## [2026-04-06] ingest | World Model Deep Research
- Three parallel research streams: papers, products, tech trends
- Sources: 3 raw files (paper-index, deep-research, product-landscape)
- Paper index: 39 papers with citations and quality tiers
- New concept: world-model
- New map: world-model
- New connection: world-model-to-agent
- Wiki state: 23 concepts, 6 maps, 6 connections

## [2026-04-06] refactor | AK LLM Wiki Pattern Alignment
- Added log.md (this file)
- Restructured index.md by domain category
- Updated CLAUDE.md schema with full AK workflow spec
- Source: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## [2026-04-06] update | World Model 论文索引补充 + ToT 迁移
- Source: https://github.com/topics/world-models (210 repos)
- 移除 Tree of Thoughts（非 world model，迁至 reasoning 索引）
- 新增 11 个项目/论文：lingbot-world, HunyuanWorld, Helios, Cosmos-Predict2.5, Causal-Forcing, DayDreamer, TesserAct, CarDreamer, Epona, PointWorld, LS-Imagine
- 新增 3 个 awesome lists 引用
- 新增 3D/Spatial 分类
- 创建 raw/papers/reasoning/LLM-Reasoning-Paper-Index.md（含 ToT + CoT）
- 论文索引：39 → 49 篇

## [2026-04-06] ingest | World Model 最新进展 2025Q4-2026Q1
- Source: 多源 web research（34 articles/papers/blogs）
- 核心发现：Sora 关闭 + AMI Labs $1.03B + LeWM 15M 参数 + Densing Law
- 五个范式转变：Video Gen≠WM 定论、JEPA 实践化、WMaaS 机会、效率革命、自动驾驶 killer app
- Wiki state: 23 concepts, 6 maps, 6 connections

## [2026-04-06] ingest | Interactive Video Benchmark 全景
- 24 个 benchmark，9 类 metrics
- 关键发现：Game/Interactive 赛道缺统一标准

## [2026-04-06] query | World Model 定义问题分析
- 核心洞察：三种定义（Agent 工具 / 物理理解 / 模拟逼真）互不兼容
- 当前 benchmark 各测不同东西，都叫 "World Model"
- 缺失方向：1+2 交叉（因理解物理而能 zero-shot 适应新环境的 Agent）
- 产出归档至 raw/articles/world-model/world-model-definition-problem.md

## [2026-04-06] query | 车昊轩 World Model 创业构想分析
- 三层架构: World Model (基模) + Agent Harness + World Engine
- 对标: 目前无人做全栈，Minecraft 生态最接近
- Dongzhe 交叉点: Agent Harness + Communication + Economy 填 Layer 2
- 产出归档至 raw/articles/world-model/che-haoxuan-startup-analysis.md

## [2026-04-06] query | LLM Wiki 产品机会分析
- AK 说 "room for an incredible new product"
- 最小切入点: Obsidian 插件（不是独立产品）
- 评分 23/30（细化），核心差异: 搜索 vs 编译
- Obsidian AI 插件赛道无人做 LLM-compiled wiki
- 验证路径: vault demo → 社区反馈 → 最简插件 → 提交社区
- 产出归档至 raw/articles/llm-wiki-product-opportunity.md

## [2026-04-07] query | Knowledge Agent Network — 从个人知识库到知识经济
- 从 AK Wiki 插件发散到 Agent 互联：每个人的 wiki 是一个 Agent 节点
- 四阶段演进：Obsidian 插件 → Wiki Agent API → Agent 网络 → Knowledge Economy
- 评分：Phase 1 = 23/30, Phase 4 = 28/30
- 与 Dongzhe 的 Agent Economy 研究完美对齐（补 AGNTCY 缺的 economic layer）
- 产出归档至 raw/articles/knowledge-agent-network-idea.md

## [2026-04-07] query | Knowledge Agent Network 网络设计
- 冷启动方案: 从 3-5 人知识圈开始（非 C 端）
- 网络拓扑: 主题路由（C 方案）最 Agent-native
- 激励: 互惠 → 声誉 → 经济（分阶段）
- 最小有用单元: 深度研究变成可被全网 Agent 调用的知识服务
- 产出归档至 raw/articles/knowledge-agent-network-design.md

## [2026-04-07] build | Wiki MCP Server
- 将知识库暴露为 MCP Server（5 个 tools）
- GitHub: BENZEMA216/wiki-mcp-server
- 已注册到本地 ~/.mcp.json
- Knowledge Agent Network 的第一个节点

## [2026-04-07] deploy | Wiki MCP Server 上线 Railway
- Live endpoint: https://benzema-wiki-mcp-production.up.railway.app/mcp
- 改造为 HTTP/SSE 远程 MCP server (StreamableHTTP transport)
- vault 改为 public（让 Railway 容器可以 git clone）
- Tools: list_topics, read_page, search_knowledge, get_index, get_paper_index, get_log
- 自动 git pull vault 每 10 分钟
- 任何 MCP 客户端都可访问
- Knowledge Agent Network — Node #1 正式上线

## [2026-04-07] ingest | MiroFish 深度研究
- 20 岁开发者 vibe coding 10 天 → 24 小时融资 3000 万 RMB（陈天桥）
- GitHub 50.9k stars，曾压过 OpenAI/Google 登顶 Trending #1
- 范式: Multi-Agent Simulation（数千 Agent 群体仿真预测）
- 底座: CAMEL-AI 的 OASIS 框架，支持 1M agents
- 实际案例: Polymarket 交易 bot + 2,847 数字人模拟，338 笔盈利 $4,266
- 与 Dongzhe 研究关联: Agent Economy 新品类、super-individual 叙事、社会模拟型 World Model
- 产出归档至 raw/articles/mirofish-research.md

## [2026-04-07] ingest | OASIS (CAMEL-AI) 深度研究
- arXiv 2411.11581，82 引用 13 influential
- 唯一公开 1M LLM agent + RecSys + 动态网络 + 多平台的开源框架
- 5 大模块：Environment Server、RecSys (TwHIN-BERT)、Agent Module、Time Engine、Scalable Inferencer (vLLM)
- 三个验证实验：信息传播复刻 Vosoughi 2018、群体极化 (RLHF 压平极化)、Reddit 从众 (LLM 比真人更顺从)
- 关键发现：herd effect 只在大尺度涌现 — "为什么需要百万级"的硬证据
- ⭐ 关键缺口：21+ action 全是社交语义，没有支付/定价/合约 — Agent Economy 必须扩 action 空间
- 判断：OASIS 是 Social Sim as a Service 的最佳底座，但不是 Agent Economy 的底座
- 真正合适位置：Agent Economy 的"需求侧 testbed"
- 产出归档至 raw/articles/oasis-camel-ai-research.md

## [2026-04-07] query | OASIS 后续研究方向讨论
- 跳过 CAMEL 母框架（Dongzhe 之前已看过）
- 高价值: AgentTorch (8.4M agents 但 rule-based) + Stanford Smallville (奠基)
- 中价值: AgentScope (阿里, 1M 但无 RecSys), SOTOPIA (1v1 negotiation), Validation 批评论文
- 推荐节奏: Validation 批评(30 分钟) → Smallville(本周) → AgentTorch vs OASIS(下次)
- 产出归档至 raw/articles/oasis-followup-discussion.md

## [2026-04-07] ingest | slock.ai 深度研究
- Agent-native IM, slogan: "Where humans and AI agents collaborate"
- 作者: Fanqing Meng (SJTU 博士生, 多模态 LLM 研究者)
- 工程: vibe coding 7 天, 0 手写代码, 春节 side project
- ⚠️ 注意消歧: 不是 Slock.it (2015 以太坊), 不是 suckless slock, 不是 slock.date
- 阶段: 个人 demo, 无融资无团队无路线图
- 同赛道对照: HiClaw (阿里, Matrix 协议) + RockClaw (社区导向, 250 citizens)
- Super-individual 叙事第三个案例 (前两个: MiroFish, BENZEMA Wiki MCP)
- 在 Agent Economy 栈中位置: 协作介质层 (IM/UX 入口层)
- 产出归档至 raw/articles/slock-ai-research.md
