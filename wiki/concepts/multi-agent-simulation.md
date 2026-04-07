# Multi-Agent Simulation

> 用大量 LLM agent 模拟人类社会动力学的范式。OASIS (CAMEL-AI, 1M agents + RecSys) 是底座，MiroFish (20 岁本科生 vibe code 10 天 + $4M 融资) 是产品化。

## 核心要点

- **范式区别**：
  | 维度 | 主流 Multi-Agent | Multi-Agent Simulation |
  |---|---|---|
  | 模式 | Agent-as-Worker（被指挥完成任务） | **Agent-as-Simulation**（自主行为，看涌现）|
  | 目的 | 完成任务 | **观察集体行为** |
  | 输出 | 任务结果 | 群体预测报告 |
  | 数量 | < 10 | **数千～百万** |
- **OASIS 论文** ([arxiv 2411.11581](https://arxiv.org/abs/2411.11581))：CAMEL-AI 主导，22+ 作者（上海 AI Lab、大连理工、牛津、KAUST、复旦）。**82 引用 / 13 influential**。1M agents 一个 timestep ≈ 27×A100, 18 小时
- **OASIS 不可替代的组合**：百万级 + LLM-driven + **内嵌 RecSys** + 动态网络 + 多平台（X + Reddit）。**RecSys 是它区别于 Smallville/AgentVerse/SOTOPIA 的关键**（X 路径用 TwHIN-BERT embedding，Reddit 路径用 hot-score）
- **三个验证实验**：(1) **Information Propagation** 复刻 Vosoughi 2018 Science 198 条 rumor cascade（RMSE ≈ 30%，重现"假新闻比真新闻传得远"）；(2) **Group Polarization**（uncensored 模型比 RLHF 模型更易极化）；(3) **Herd Effect** 复刻 Muchnik 2013 Science（LLM agent 比真人更顺从，"agents lack critical mind"）
- **MiroFish 数据**：作者郭航江 (BUPT 大四，20 岁)，**vibe coding 10 天**，陈天桥孵化，**24 小时内完成 ¥3000 万 ($4.1M) 融资**，GitHub **50.9k stars**
- **最大缺口**：21+ action 全是社交动作（post/repost/like/comment/follow/...），**没有支付/定价/订单/合约**——这是 [[concepts/knowledge-agent-network|Agent Economy]] 关心的核心，做 economy 必须自己扩 action 空间

## 详细说明

**为什么需要百万级**：OASIS 论文最重要的论据之一——herd effect **只在大尺度才出现**。agent 数量从 196 → 100K，回答 diversity 与 helpfulness 单调上升。这意味着小规模 simulation（Smallville 的 25 个 agent）拍不出真实社会的涌现行为。

**Time Engine 是工程关键**：24 维"逐小时活跃度概率向量"从真实历史数据拟合，离散 timestep（默认 3 分钟）。这是把 LLM 调用稀疏化、避免 1M agent 同时推理爆炸的核心设计——没有它，1M × 1 timestep 的成本会失控。

**对 Agent Economy 的两条启示**：(1) **不卖能力，卖"群体仿真结果"** 是 Agent Economy 中目前没人做的品类——MiroFish 接 Polymarket 的案例（每笔交易前模拟 2,847 个数字人，338 笔报告盈利 $4,266）就是 Agent-to-Agent 经济回路的雏形；(2) OASIS 不直接是 economy 底座，但应当作为**需求侧的 testbed**——让卖 agent service 的人在上线前用 1k-10k 合成消费者压测产品/定价/留存。

**与 [[concepts/world-model|World Model]] 的关系**：OASIS / MiroFish 模拟的不是物理世界，而是**社会世界 / 群体心理**。这是 World Model 三种定义中第 2 种"理解世界"的另一种路径——不通过物理规律，而是通过社会动力学。可以为 World Model 论文索引补一类 "Social Simulation as World Model"。

**主要批评**：(1) 没有 benchmark，没有任何已发布的"预测 vs 真实"前瞻验证；(2) 成本爆炸，只有 lab/大公司玩得起；(3) LLM bias 直接污染结果——aligned 模型会"压平"真实社会的极化现象；(4) Stochasticity 让 reproducibility 变难。

## 在知识库中的出现

| 来源 | 上下文 |
|------|------|
| `raw/articles/oasis-camel-ai-research.md` | OASIS 完整研究：5 大架构模块、action space、3 个验证实验、与 8 个竞品对比表、对 Agent Economy 的启示 |
| `raw/articles/mirofish-research.md` | MiroFish 完整研究：作者背景、5 阶段工作流、Polymarket 案例、与 Dongzhe 方向 4 点关联 |
| `raw/articles/oasis-followup-discussion.md` | OASIS 后续讨论：补充缺口分析与 Dongzhe-fit 角度 |

## 关联概念

- [[concepts/agent-communication]] — CAMEL (NeurIPS 2023) 是 OASIS 的母框架，已在 agent communication 索引中
- [[concepts/sub-agent-architecture]] — 1M agent 的分布式协调与子 agent 概念相通
- [[concepts/world-model]] — Social Simulation 是 World Model 的第二种定义路径
- [[concepts/knowledge-agent-network]] — KAN 节点之间的查询负载也可以用 OASIS-style 合成需求做压测
- [[connections/communication-to-economy]] — OASIS 缺的"经济原语"正是 Agent Economy 要补的层

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
