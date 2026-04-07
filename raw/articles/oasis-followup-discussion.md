# OASIS 后续讨论：还有什么值得研究

> 来源: 知识库 Query 讨论 (2026-04-07)
> 上下文: OASIS 深度研究后，决定下一步研究方向

---

## Dongzhe 的判断

CAMEL 母框架之前已经看过了，不再需要重新深入。

## 值得继续研究的方向（按价值排序）

### 🟢 高价值

#### 1. AgentTorch（OASIS 的最大对手）

- 唯一规模超过 OASIS 的（**8.4M agents**），但是 **rule-based 不是 LLM-driven**
- 这两条路线的优劣对比直接决定 "agent simulation 的未来是 LLM 还是规则" 这个根本问题
- **关键问题**: 1M LLM agent 是否已经够用？再大就该用 rule-based？这个边界在哪？

#### 2. Stanford Generative Agents (Smallville)

- multi-agent simulation 真正的开山之作，**数千引用**
- OASIS 论文里反复对比的就是它
- **核心三件套**: reflection + planning + memory
- **关键问题**: 25 个 agent 的小镇 → 1M agent 的社交网络，量变是怎么产生质变的？

### 🟡 中价值

#### 3. AgentScope (阿里)

- 另一个声称支持 1M agents 的框架，但**没有 RecSys**
- 中国生态的代表
- 看它怎么处理大规模和它的 niche

#### 4. SOTOPIA

- 和 OASIS 完全不同的方向 — **双人 social negotiation**
- 研究 agent 如何在社交目标驱动下交互
- **更接近 Agent Economy 中的 1v1 议价场景**

#### 5. Validation 批评论文

- 2025 PMC 论文 *"Validation is the central challenge for generative social simulation"*
- 学界对这整条路线的反思
- 如果要 build on OASIS，必须先理解最尖锐的批评

#### 6. Generative ABM 这个新子领域

- 学界正在为 "LLM 化的 Agent-Based Modeling" 建立学科边界
- **早期入场的研究者会成为这个方向的 anchor**

### 🔴 低价值（除非有特定用途）

#### 7. RecAgent / Agent4Rec
专注推荐系统场景的 multi-agent 模拟，已被 OASIS 包含。除非做"用 agent 模拟测试推荐算法"，否则不需要深看。

#### 8. AgentSims / S3 / HiSim
更早期、更小规模的工作。基本被 OASIS 全面超越，只有学术追源时需要。

---

## 推荐的研究节奏

- **立刻看（30 分钟）**: Validation 批评论文 — 让你立刻知道这条路线的死穴
- **这周看**: Stanford Generative Agents — 理解原始范式
- **下次研究**: AgentTorch vs OASIS — 决定 LLM-driven vs rule-based 边界
- **更后面**: SOTOPIA — 1v1 social agent，更接近经济场景

---

## 跳过 CAMEL 母框架的原因

虽然 CAMEL 是 NeurIPS 2023 论文（1,194 引用），是 multi-agent 通信奠基论文之一，但 Dongzhe 之前已经看过，所以不需要再深入。

如果未来要重新看 CAMEL，重点是：
- inception prompting / role-playing 机制能否用在 Knowledge Agent Network 的"节点之间互查"上
- CAMEL 的 agent / memory / tool 抽象是否值得 Knowledge Agent Network 复用
