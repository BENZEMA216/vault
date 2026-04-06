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
