# MiroFish 深度研究

> 来源: 知识库 Query 讨论 (2026-04-07)
> 关键词: multi-agent simulation, swarm intelligence, super-individual, OASIS, 陈天桥

---

## 一句话定义

把任意现实素材（新闻、政策、财报、小说）喂进去 → 系统自动孵化数千个有独立人格、长期记忆和社交行为的 AI Agent → 丢进模拟的"平行数字世界"（类 Twitter / Reddit）→ 观察涌现行为 → 输出预测报告。

**不是**统计外推，**而是**模拟集体人类行为的涌现。

## 谁做的

- **作者**: 郭航江 / GitHub `666ghj` (BaiFu / 白浮)
- **年龄**: **20 岁**
- **学校**: 北京邮电大学 (BUPT) 大四
- **开发周期**: vibe coding **10 天** 完成
- **前作**: BettaFish — 一周内 GitHub Trending #1, 20k stars
- **背书**: **陈天桥**（盛大集团创始人，前中国首富）孵化

## 融资 & 热度

| 指标 | 数据 |
|------|------|
| 融资金额 | **3000 万 RMB (~$4.1M)** |
| 融资速度 | demo 发布后 **24 小时内** |
| GitHub Stars | **~50.9k** |
| Forks | 7.5k |
| 排名 | 曾压过 OpenAI / Google / Microsoft 登顶 GitHub Global Trending #1 |

陈天桥的投资逻辑：**"super-individual" 理论** —— AI 时代单个开发者就能创造公司级别价值。

## 技术架构

5 阶段工作流：

1. **Graph Building** — 从 seed 文档抽实体关系，构建 GraphRAG
2. **Environment Setup** — persona generation，生成上千 Agent
3. **Dual-platform Simulation** — 类 Twitter + 类 Reddit 双平台并行模拟
4. **Report Generation** — ReportAgent + toolset 生成分析报告
5. **Deep Interaction** — "God's-eye view"，用户随时注入新变量重跑

技术栈：
- **Backend**: Python 3.11–3.12
- **Frontend**: Vue.js
- **底层框架**: **CAMEL-AI 的 OASIS** (Open Agent Social Interaction Simulations)，支持 **1M agents** 和 23 种社交动作
- **Memory**: Zep Cloud
- **LLM**: OpenAI SDK 兼容（推荐阿里 Qwen-plus 跑成本）
- **License**: AGPL-3.0
- **版本**: v0.1.2（早期原型）

## 应用场景

- 金融决策 / 交易信号
- 政策影响测试
- PR 危机模拟
- 营销策略评估
- 创意实验

**实际案例**: 有开发者把 MiroFish 接到 **Polymarket 交易 bot**，每笔交易前模拟 2,847 个数字人，跑 338 笔报告盈利 **$4,266**。

## 商业化

**目前没有定价** — 纯开源 + 社区。痛点：LLM token 消耗极大，每个 Agent 每轮交互都要调 LLM，官方建议单次模拟控制在 40 轮内。

## 差异化

| 维度 | 主流 Multi-Agent | MiroFish |
|------|-----------------|----------|
| 范式 | Agent-as-Worker（被指挥完成任务） | **Agent-as-Simulation**（自主行为，看涌现）|
| 目的 | 完成任务 | **观察集体行为** |
| 输出 | 任务结果 | 群体预测报告 |
| Agent 数量 | 通常 < 10 | **数千～百万** |

## 主要批评

1. **没有 benchmark** — 没有任何已发布的"预测 vs 真实"验证
2. **成本高** — LLM API 费用对个人不友好
3. **LLM bias 放大** — Agent 群体可能比现实更极化
4. **早期阶段** — v0.1.2，团队还在招人

## 生态分叉

- `nikmcfly/MiroFish-Offline` — 全本地化 fork（Neo4j + Ollama）
- `amadad/mirofish` — 英文社区 fork

## 与 Dongzhe 研究方向的关联

### 1. Agent Economy 的全新品类

- **不卖能力，卖"群体仿真结果"** — 这是 Agent Economy 中目前没人做的品类
- **Polymarket 案例** = Agent-to-Agent 经济回路：模拟 Agent 群体的预测 → 喂给交易 Agent → 输出可货币化的 PnL
- 直接对应 [[concepts/agent-communication]] 的 Market-Based Coordination 模式

### 2. Super-Individual 叙事

- 陈天桥下注的不是公司，是 **单个 20 岁开发者 + AI coding**
- 直接对应 **Agent-friendly / vibe coding** 趋势
- 与你正在做的 Knowledge Agent Network 同构 — **个人成为节点**，靠工具撬动公司级影响力

### 3. World Model 的另一种实现

- 与 [[concepts/world-model]] 索引中的 Genie / Sora 不同：MiroFish 模拟的不是物理世界，而是 **社会世界 / 群体心理**
- 这是 World Model 三种定义中第 2 种"理解世界"的另一种路径 —— 不通过物理规律，而是通过社会动力学
- 论文索引可考虑增加这一类: "Social Simulation as World Model"

### 4. CAMEL-AI / OASIS 是技术底座

- OASIS 已经在我们 [[wiki/concepts/agent-communication]] 的研究范围里（CAMEL 是 NeurIPS 2023 论文）
- MiroFish 是 OASIS 的产品化案例，验证了 1M Agent 群体仿真的工程可行性

## 关键 URL

- **GitHub**: https://github.com/666ghj/MiroFish
- **作者**: https://github.com/666ghj
- **Demo**: https://666ghj.github.io/mirofish-demo/
- **第三方 Demo**: https://mirofish.homes/
- **离线 fork**: https://github.com/nikmcfly/MiroFish-Offline
- **OASIS (底座)**: CAMEL-AI 框架

## 信号意义

1. **vibe coding + super-individual = 新创业范式** — 10 天 + 1 个 20 岁开发者 = $4.1M + 50k stars
2. **群体仿真是被低估的赛道** — 比单 Agent / 任务 Agent 更接近真实社会
3. **陈天桥的下注方向值得关注** — 他在押"个体即公司"的叙事
4. **Polymarket 案例验证了变现路径** — Agent 仿真 → 预测 → 交易 → 盈利
