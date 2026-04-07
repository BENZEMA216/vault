# OASIS 深度研究：Open Agent Social Interaction Simulations

> 来源: 知识库 Query 讨论 (2026-04-07)
> 关键词: social simulation, multi-agent, CAMEL-AI, 1M agents, RecSys

---

## 一句话定义

**唯一一个公开把 LLM-driven social simulation 推到百万 agent 级别、且把 RecSys 内嵌进环境的开源框架。**

由 CAMEL-AI 主导，联合上海 AI Lab、大连理工、牛津、KAUST、复旦、西交、IC、马普所等 22+ 位作者完成。

## 论文与引用

| 项 | 值 |
|---|---|
| arXiv | [2411.11581](https://arxiv.org/abs/2411.11581) |
| 标题 | OASIS: Open Agent Social Interaction Simulations with One Million Agents |
| 一作 | Ziyi Yang, Zaibin Zhang, Zirui Zheng, Yuxian Jiang |
| 资深作者 | Guohao Li (CAMEL 创始人), Wanli Ouyang, Yu Qiao, Philip Torr |
| Year | 2024-11 (v5: 2025-03) |
| **Citation** | **82** (Influential: **13**) |

正在扩散，但还没成为社会模拟领域的事实标准（对比 Stanford Generative Agents 数千引）。

## 5 大架构模块

### 1. Environment Server
SQLite/Postgres 持有 users / posts / comments / follow graph / action trace / recommendations。所有 agent 行为写回这里 = 真正的"世界状态"。

### 2. Recommendation System (RecSys) ⭐ 最大差异化
- **X 路径**: in-network 帖按热度 + out-of-network 用 **TwHIN-BERT** embedding 兴趣匹配
- **Reddit 路径**: hot-score (upvote/downvote/recency)
- 2025-04 起支持 OpenAI Embedding 替代 TwHIN-BERT

**这是 OASIS 区别于所有前作（Smallville/AgentVerse/SOTOPIA）最关键的模块。**

### 3. Agent Module
基于 CAMEL agent，自带 memory（看过的帖、点赞、自己动作 + reasoning trace）+ Chain-of-Thought，可挂任意 LLM (OpenAI / Llama / vLLM)。

### 4. Time Engine
24 维"逐小时活跃度概率向量"从真实历史数据拟合。离散 timestep（默认 3 分钟）。

**这是把 LLM 调用稀疏化、避免 1M agent 同时推理爆炸的关键设计。**

### 5. Scalable Inferencer
基于 vLLM 的异步分布式 GPU 资源管理。论文给出的硬数据：
- **1M agent 一个 timestep ≈ 27×A100，耗时 18 小时**
- 单 timestep 产生 48.5K 推文 + 97.1K 评论

## Action Space (21+ → 23)

`sign_up, create_post, repost, like_post, dislike_post, create_comment, like/dislike_comment, follow, unfollow, mute, search_posts, search_users, trend, refresh, do_nothing`

2025 新增: `interview` (向 agent 提问获取观点)、`group chat` (建群/发消息/退群)、`report post`

## 与其他多 Agent 模拟框架对比

| 框架 | Max Agents | LLM-driven | RecSys | 动态网络 | 跨平台 |
|---|---|---|---|---|---|
| Generative Agents (Smallville) | 25 | ✓ | ✗ | partial | ✗ |
| AgentVerse | <100 | ✓ | ✗ | ✗ | ✗ |
| AgentSims | ~50 | ✓ | ✗ | ✗ | ✗ |
| S3 / HiSim | 1,000 | ✓ | ✗ | ✗ | X-only |
| RecAgent / Agent4Rec | 5–1,000 | ✓ | ✓ | ✗ | ✗ |
| AgentTorch | 8.4M | **rule-based** | ✗ | ✓ | n/a |
| AgentScope | 1M | ✓ | ✗ | ✗ | ✗ |
| **OASIS** | **1M** | **✓** | **✓** | **✓** | **X + Reddit** |

**不可替代的组合**: 百万级 + LLM-driven + 内嵌 RecSys + 动态网络 + 多平台

## 三个核心实验（论文的 validation 故事）

### 1. Information Propagation (X)
- 复刻 Vosoughi et al. 2018 Science 上的 198 条真实 rumor cascade
- 规模/广度/深度匹配良好，**RMSE ≈ 30%**
- propagation depth 系统性偏浅
- **重现"假新闻比真新闻传得远"**

### 2. Group Polarization (X)
- 群体讨论中观点变极端
- **uncensored model 比 RLHF model 更易极化**（aligned 模型反而压住了真实人群中的极化现象 — 对 alignment 研究的有趣发现）

### 3. Herd Effect (Reddit)
- 复刻 Muchnik 2013 Science 投票实验
- **LLM agent 比真人更顺从（herding）**，dislike 方向尤其明显
- "agents lack critical mind"

**附加发现**: agent 数量从 196 → 100K，回答 diversity 与 helpfulness 单调上升；herd effect **只在大尺度才出现** — 这是论文最重要的"为什么需要百万级"论据。

## CAMEL-AI 这个组织

- **CAMEL** (NeurIPS 2023) = Communicative Agents for Mind Exploration of LLM Society
  - 一作 Guohao Li (KAUST/Oxford)
  - 我们 [[concepts/agent-communication]] 索引中已有
- **公司主体**: **Eigent AI**
- **开源矩阵**:
  - `camel` — 多 agent 框架本体
  - `owl` — 通用任务自动化（GAIA #1, 58.18 分）
  - `oasis` — social simulation
  - `eigent` — 商业化产品线
- **资金**: GitHub SOS Fund，未见大额披露

## Adoption 信号

| | |
|---|---|
| GitHub | 4.1k★ / 444 fork |
| 维护 | 2025 几乎每月 release |
| Citations | 82 / 13 influential |
| **最大用户** | **MiroFish** (50.9k★, $4M 融资) |
| Forks | nikmcfly/MiroFish-Offline, amadad/mirofish |

## 局限与批评

### 1. 成本爆炸
1M agent × 1 timestep = 27 A100 × 18h，**只有 lab/大公司玩得起**

### 2. LLM bias 直接污染结果
- aligned model 会"压平"真实社会的极化现象
- LLM 倾向于过度礼貌/啰嗦，观点偏年轻/富裕/政治自由派
- zero-shot prompting 几乎无 fine-tuning，stereotype 风险高

### 3. Validation 缺口
- 除了三个 replicate 实验，**没有针对未来事件的"前瞻 ground truth"验证**
- "能重现历史" ≠ "能预测未来"

### 4. Stochasticity / 复现性差
LLM 输出抖动让 reproducibility 变难

### 5. 平台抽象有限
紧贴 Twitter/Reddit 数据模型，迁移到电商/直播/Discord 需要重写 Environment Server

### 6. ⭐ 关键缺口：Action space 没有经济原语
21–23 个 action 全是社交动作 — **没有"经济交易、支付、定价、合约"动作**。

**这恰恰是 Agent Economy 关心的核心。要做 economy 必须自己扩 action 空间。**

## 与其他研究脉络的连接

- **RL 多 agent**: 论文明说下一步是 RL，用"行为模仿真人"作为 reward 训 agent，可看作 social-MARL 的 sandbox 候选
- **Swarm intelligence**: MiroFish 把这个标签贴到 OASIS 上，但严谨意义上 OASIS 不是 swarm（无显式集体目标函数），更像 social ABM
- **Agent-Based Modeling (ABM)**: OASIS 是 ABM 经典范式（Schelling、Axelrod、Sugarscape）的 LLM 化升级，学界正在为它建立 "Generative ABM" 新子领域
- **CAMEL 母框架**: OASIS agent 可挂 CAMEL toolkit (Semantic Scholar、web search)，具备外部 tool use 能力

## 对 Dongzhe 方向的判断

### 作为 "Social Simulation as a Service" 基底：✅ 强烈合适

- 占据独一无二 niche（1M + LLM + RecSys + dynamic graph）
- 工程已被 CAMEL-AI 啃过最难部分（vLLM, time engine, recsys）
- PyPI 包化 + PettingZoo 接口 + 持续维护，二次开发友好
- MiroFish 已在公开市场验证需求（4M 融资、50k 星）
- **适合产品形态**: 营销 A/B 测试、政策/PR 沙盒、舆情预测、内容策略、信息战推演

### 作为 "Agent Economy" 基础设施：⚠️ 不直接合适

理由:
1. 21 个 action 全是社交语义，**没有支付/定价/订单/库存/合约**
2. Environment Server 是社交平台模型，不是 marketplace / settlement layer
3. 解决的是"agent 群体行为涌现"，不是"agent 之间价值交换"
4. cost model 是"研究级"，需要的是低延迟、可结算的实时 agent service

### ⭐ 真正合适的位置：需求侧的 testbed

把 OASIS 当成"市场需求侧的合成用户群"，让真实 agent service 在 OASIS 模拟出的"百万合成消费者/评论者"前先做：
- 压力测试
- 定价探索
- 内容策略验证
- 产品 vs 用户反馈循环

**这是 OASIS 在 agent economy stack 里的正确位置 — 需求侧的 testbed，而不是 economy 本身。**

## 给 Dongzhe 的两条具体启示

### 1. 如果做 "Social Simulation as a Service"

直接 fork OASIS，做四件事：
- 业务化的 seed 输入（像 MiroFish 的 GraphRAG）
- 行业垂直的 persona pack
- 报告/可视化层
- 成本优化（小模型 + cache + sub-sampling）

MiroFish 已经走通路径，但仍是工程 demo，**缺企业级 SLA + 行业 vertical**。

### 2. 如果做 "Agent Economy"

OASIS 不直接是底座，但应当作为 **"合成需求侧 sandbox"** 集成进来 — 让卖 agent service 的人在上线前能在 OASIS 模拟的 1k–10k 合成用户上压测产品/定价/留存。

**这反而可能是 OASIS 没人做的差异化角度。**

## 关键链接

- **论文**: https://arxiv.org/abs/2411.11581
- **GitHub**: https://github.com/camel-ai/oasis
- **文档**: https://docs.oasis.camel-ai.org
- **官方博客**: https://www.camel-ai.org/blogs/oasis
- **Vision blog**: https://www.camel-ai.org/blogs/project-oasis-automation-or-simulation
- **MiroFish (旗舰用户)**: https://github.com/666ghj/MiroFish
- **Validation 批评 (PMC)**: https://pmc.ncbi.nlm.nih.gov/articles/PMC12627210/
