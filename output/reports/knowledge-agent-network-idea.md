# Knowledge Agent Network：从个人知识库到知识经济

> 来源: 知识库 Query 讨论 (2026-04-07)
> 状态: 🔍 值得深入
> 核心洞察: 每个人的 LLM Wiki 是一个 Agent 节点，互联成知识网络

---

## 一句话描述

> 给知识工作者，解决"知识孤岛 + 协作低效"的问题，通过将每个人的 LLM-compiled 知识库变成可互联的 Agent 节点

## 从 AK Wiki 到 Knowledge Agent Network 的演进

```
AK Wiki (现在):
你的 raw/ → LLM 编译 → 你的 wiki → 你自己查询

Knowledge Agent Network (未来):
你的 Wiki Agent ←→ 他的 Wiki Agent ←→ 她的 Wiki Agent
      ↑                    ↑                    ↑
   你的知识              他的知识              她的知识
```

**本质**: 每个人的知识库是一个可被其他 Agent 查询的节点。个人化的 Wikipedia + Agent 互联 + 知识经济。

---

## 场景

### 场景 1: 研究协作
- A 深度研究了 Agent Communication（30 篇论文 compiled）
- B 深度研究了 CI 竞品市场
- C 深度研究了 World Model
- A 的 agent 问 C 的 agent "JEPA 最新进展" → 基于 compiled knowledge 回答，比 ChatGPT 准（因为是深度研究的积累，不是通用训练数据）

### 场景 2: 投资人网络
- 每个投资人有自己的 deal flow wiki
- GP 的 agent 查询多个 LP 的 agent："谁在看 World Model 赛道？"
- 知识分层权限：公开层 / 朋友层 / 私密层

### 场景 3: 团队知识网络
- 产品 wiki + 工程 wiki + 设计 wiki
- PM 的 agent 问工程的 agent："上次那个 API 限制是怎么回事？"
- 不需要每个人读所有文档——agent 之间自动路由

### 场景 4: 知识交易
- PhD 的论文综述 wiki agent 对外提供付费查询
- 行业分析师的市场 wiki agent 按次收费
- 形成知识的 marketplace

---

## 与现有方案的根本区别

| 方案 | 模式 | 局限 |
|------|------|------|
| Google Scholar | 搜索论文 | 原始文档，没有编译 |
| Perplexity | 搜索 + 总结 | 无状态，不积累 |
| NotebookLM | 个人 RAG | 不互联，孤岛 |
| Wikipedia | 人工编译公共 wiki | 不个人化，维护成本巨大 |
| **Knowledge Agent Network** | **LLM-compiled wiki 互联成网络** | **新品类** |

---

## 与 Dongzhe 已有研究的完美对齐

| 已有研究 | 对应产品需求 |
|----------|------------|
| Agent Communication（MCP/A2A/7 种通信模式） | Wiki Agent 之间的通信协议 |
| Agent Economy（定价/交易/分润） | 知识查询的计价和结算 |
| AGNTCY 缺 economic layer | **这个产品补这一层** |
| A2A Agent Card | 每个 Wiki Agent 的身份卡（我的知识领域/价格/权限） |
| MCP | Wiki Agent 暴露为 MCP Server，可被任何 LLM 调用 |
| Harness Engineering | Agent 查询的可靠性、验证、安全 |
| Context Engineering | 跨 Agent 查询的 context 管理 |

---

## 产品演进路径

```
Phase 1: Obsidian 插件
         个人 LLM Wiki（ingest/query/lint）
         验证: 知识编译有价值吗？
         切入: Obsidian 600万用户
         成本: $0，2-4 周

Phase 2: Wiki Agent
         你的 wiki 可被 API/MCP 查询
         验证: 别人想查你的知识吗？
         技术: wiki 暴露为 MCP Server

Phase 3: Agent 网络
         多个 Wiki Agent 互相发现、查询
         验证: 知识共享有网络效应吗？
         技术: A2A Agent Card 做发现，MCP 做查询

Phase 4: Knowledge Economy
         知识的定价、分润、marketplace
         验证: 知识交易有市场吗？
         技术: 经济层（AGNTCY 缺的那一层）
```

**Phase 1 成本低可验证，Phase 4 才是大故事。**

---

## 技术架构（概念）

```
┌─────────────────────────────────────────────┐
│              Knowledge Economy               │
│         (定价 / 交易 / 分润 / 声誉)            │
├─────────────────────────────────────────────┤
│            Agent Network Layer               │
│      (发现: A2A Agent Card / 路由 / 权限)       │
├─────────────────────────────────────────────┤
│          Communication Layer                 │
│            (MCP / A2A Protocol)              │
├─────────────────────────────────────────────┤
│            Wiki Agent (每个用户)               │
│   ┌──────────────────────────────────────┐  │
│   │  raw/ → LLM compile → wiki/          │  │
│   │  _index.md / concepts/ / log.md      │  │
│   │  ingest / query / lint               │  │
│   └──────────────────────────────────────┘  │
└─────────────────────────────────────────────┘
```

---

## 关键问题

### 1. 隐私与权限
- 我的知识哪些可以共享？
- 需要: 公开 / 朋友 / 私密 三层权限
- 可参考: Git 的 public/private repo 模型

### 2. 质量与信任
- LLM 编译的知识如何保证准确？
- 错误知识在网络里传播怎么办？
- 可参考: Wikipedia 的引用验证 + 声誉系统

### 3. 激励机制
- 为什么我要让别人查我的 wiki？
- 需要经济激励: token / 积分 / 付费查询
- 可参考: Stack Overflow 的声誉体系 + API marketplace 的按次计费

### 4. 冷启动
- 网络效应型产品经典难题
- 解法: Phase 1 先做个人工具（不需要网络），积累用户后再开互联

---

## 评分卡

| 维度 | Phase 1 (插件) | Phase 4 (网络) |
|------|---------------|---------------|
| 问题频率 | 5 | 5 |
| 问题强度 | 4 | 5 (知识协作是组织级痛点) |
| 付费意愿 | 3 | 4 (B2B 知识网络付费意愿强) |
| 市场规模 | 4 | 5 (知识经济 = 巨大) |
| 独特优势 | 3 | 4 (Agent Economy 研究独一份) |
| 现有方案质量 | 4 | 5 (完全没有) |
| **总分** | **23/30** | **28/30** |

Phase 4 的 28/30 意味着: **如果能走到那一步，这是一个值得 all-in 的方向。**

---

## 和车昊轩 World Model 创业的对比

| 维度 | Knowledge Agent Network | 车昊轩 World Model |
|------|------------------------|-------------------|
| 技术风险 | 中（LLM API + 协议已有） | 极高（基模研发） |
| 资金需求 | 低（插件起步） | 极高（对标 $1B AMI Labs） |
| 时间到 MVP | 2-4 周 | 6-12 个月 |
| 市场验证 | 可快速验证 | 需要长期研发 |
| 天花板 | 知识经济（巨大） | 世界模拟（巨大） |
| Dongzhe 角色 | 核心（Agent Economy 是关键层） | 辅助（填 Layer 2） |

---

## 下一步

1. 存入知识库 ✅
2. 发给后端兄弟（xz1220）讨论
3. 录 vault demo 视频，测社区反应
4. 如果信号成立，Phase 1 插件开发（2-4 周）
