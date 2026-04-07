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
- 产出归档至 raw/articles/richard-chien/slock-ai-research.md

## [2026-04-07] correction | slock.ai 作者归属修正
- ❌ 之前错误: Fanqing Meng (SJTU 多模态 LLM 博士生)
- ✅ 正确: Richard Chien / 余超 (@stdrc), Moonshot AI 前 Kimi CLI 主作者
- 履历: NoneBot/OneBot 作者 (2016-2020) → SJTU IPADS 硕士 → RisingWave Labs → Moonshot Kimi CLI → slock.ai
- 误判源: 两人都跟 SJTU 有关
- 信号强度大幅上调: 不是学术 vibe code 玩具, 是十年 chatbot/agent 生态老兵的下一个项目
- 工程野心: slock.ai 同时占协作介质层 + 本地 daemon 执行层
- raw/articles/richard-chien/slock-ai-research.md 已全文重写

## [2026-04-07] ingest | Richard Chien 完整 agent stack
- 看了 stdrc GitHub 后发现：他不是在做单个产品，是在搭整个 agent stack
- ⚠️ 第二次修正：Botiverse 是 RC 的真实 GitHub org（2023-05 创建），不是无关
- 完整 stack：agent-vault (356★, 2 月) + kimi-cli + kosong + pykaos + slock + hakimi + kimchi
- ⭐ agent-vault 直接对应 KAN 的 secrets 层缺口（占位符模型可复用）
- "I build ecosystems, not just software" 是字面意义
- 产出归档至 raw/articles/richard-chien/stack.md

## [2026-04-07] ingest | Richard Chien 完整人物档案
- 双线深度研究: GitHub 38 repos + Twitter timeline 部分抓取
- 关键洞察: "协议人 + Empty Layer 设计美学 + 双线工程(Moonshot + botiverse)"
- 灵魂前史发现: qdp (2018, 把 QQ 消息当 transport protocol) = hakimi/slock 的远古祖先
- 中文 chatbot 生态他主导 ~13,000 stars (CQHTTP+NoneBot+NoneBot2+OneBot)
- RisingWave 时代: 375 commits, 写到 2025-07
- "95% by Cursor" reading-list README = vibe coding 公开宣言
- my-vibe-coding 元仓库 = AK LLM Wiki 的同构形态
- 网络: follow Xuanwo 等 agent infra 同温层, follower 包含 skyzh
- 6 个具体连接点到 Dongzhe 的 KAN/Agent Economy/AK Wiki/Agent Deck
- 产出归档至 raw/articles/richard-chien/deep-profile.md

## [2026-04-07] ingest | Richard Chien 完整生态深度代码评审
- 5 个并行 subagent 看了 7 个项目源码 (~19,000 LOC)
- kimi-cli (~25-35k LOC) + kosong (~4,200 LOC) + agent-vault (~1,100 LOC) + OneBot spec + kimchi + tldr-vscode + pink
- 最大发现: OneBot (2020) 和 Kimi Wire (2026) 几乎同一份设计的两次实现
- kimi-cli 7 个独有机制: D-Mail 物理时光机、Ralph loop、Steer、持久化 sub-agent、Wire external tool、dynamic injection、token 双轨计数
- kosong "Empty Layer" 美学: 12 件故意不做的事，4 个公开 API
- agent-vault 3 个最巧妙设计: bigram 词检测、split-join 替换、UNVAULTED SHA256 反向匹配
- kimchi 真相: 0 commits, 纯占位 fork (behind upstream 91)
- tldr-vscode 是 LLM Wiki Pattern 的现成蓝图: parser/renderer/diff 三层 + 双向行号映射
- pink 是 Python 版 Ink，预测将成为 Kimi CLI 下一代 UI
- 7 个具体可执行的 KAN 启示
- 一份 RFC 草案: "Wire Protocol Extension: Knowledge Node Discovery and Cross-Node Query"
- 产出归档至 raw/articles/richard-chien/code-review.md

## [2026-04-07] ingest | Claude Code 实际源码深度分析（自我修正）
- 基于本地 cli.js v2.1.87 (12.9MB, 16,750 行) 一手分析 + 2026-03-31 sourcemap 泄露后的多个 RE 仓库
- ⚠️ 修正了之前关于 "Claude Code 是 post-stream parallel" 的错误判断
- 真相: Claude Code 同时拥有两条 tool execution 路径，由 Statsig feature gate `tengu_streaming_tool_execution2` 控制
- Streaming 路径已在生产灰度，和 kimi-cli `_step()` 同构
- Claude Code 比 kimi-cli 更细: per-tool `isConcurrencySafe(input)` 谓词，能精确到 "3 read 并发 + 1 write 栅栏 + 后续 read 再并发"
- Compaction 三档: micro / auto / reactive
- Sub-agent: clean context recursion (vs kimi-cli 的持久化可 resume)
- 产出归档至 raw/articles/claude-code-source-analysis.md

## [2026-04-07] query | Agent Tool 并发模型完整讨论归档
- 起源: 阅读 RC 评审中"Sync handle, async future"段落引出的连环讨论
- 核心问题: agent 框架如何让 LLM streaming 和 tool execution 在时间上重叠
- 5 部分讨论: 革命性原理 + 主流框架现状 + Claude Code 真相 + 之前判断修正 + KAN 启示
- KAN 设计建议: per-node `isConcurrencySafe` 谓词 + 读并发 + 写栅栏 + Generator merge
- 产出归档至 raw/articles/agent-tool-concurrency-discussion.md

## [2026-04-03] ingest | Wiki 编译批次：LLM Wiki / KAN / Harness / Multi-Agent Sim / World Model
- 编译来源: raw/articles/llm-wiki-pattern.md, llm-wiki-product-opportunity.md, knowledge-agent-network-{idea,design}.md, richard-chien-{deep-profile,code-review}.md, claude-code-source-analysis.md, agent-tool-concurrency-discussion.md, slock-ai-research.md, mirofish-research.md, oasis-camel-ai-research.md, world-model/{world-model-definition-problem,che-haoxuan-startup-analysis,latest-developments-2025Q4-2026Q1}.md
- 新增 concepts (5): llm-wiki-pattern, knowledge-agent-network, agent-tool-concurrency, agent-native-im, multi-agent-simulation
- 新增 maps (1): agent-harness-implementations
- 新增 connections (2): harness-to-kan, llm-wiki-to-agent-network
- 更新 _index.md: 新增"LLM Wiki Pattern & 知识库基础设施"顶级 domain；Agent 基础设施 section 加 4 个新概念 + 1 个新 map + 2 个新 connection；数据源加 11 个新 raw 文件链接
- 更新 _summaries.md: 追加 15 个新 raw 文件的摘要条目
- Wiki 状态: 28 concepts, 7 maps, 8 connections

## [2026-04-07] refactor | RC 相关文件归入 raw/articles/richard-chien/
- 移动 4 个文件到专用文件夹：
  - richard-chien-stack.md → richard-chien/stack.md
  - richard-chien-deep-profile.md → richard-chien/deep-profile.md
  - richard-chien-code-review.md → richard-chien/code-review.md
  - slock-ai-research.md → richard-chien/slock-ai-research.md
- 更新 8 个 wiki 文件的路径引用 (_index, _summaries, log, 2 concepts, 1 map, 1 connection)
- 保留在外面的主题独立文件：
  - claude-code-source-analysis.md (Claude Code 主题)
  - agent-tool-concurrency-discussion.md (通用 agent 框架话题)
