# World Model 的定义问题：三种定义，三套评估，互不兼容

> 来源: 知识库 Query 讨论 (2026-04-06)
> 核心判断: 不是"没有好的 benchmark"，而是"大家在评估不同的东西，却都叫它 World Model"

---

## 三种定义

### 1. Agent 工具论："World Model 是为 Agent 服务的"

> 好的 World Model = Agent 用它做决策后，任务成功率更高

- **代表**: Dreamer 系列、V-JEPA 2-AC、LATS、RAP
- **Benchmark**: Atari 100K、DMControl、VP2
- **评估方式**: 不看模拟得像不像，只看 Agent 表现好不好
- **关键证据**: VP2 发现视觉相似度和控制性能**不相关**
- **实践验证**: Waymo 用 World Model 做仿真——物理不完全准确也没关系，能发现 failure case 就够

这是目前最"落地"的定义。

### 2. 认知科学论："World Model 是对物理世界的理解"

> 好的 World Model = 真正"懂"物理规律，能区分可能和不可能

- **代表**: LeCun JEPA、IntPhys 2、Othello-GPT
- **Benchmark**: IntPhys 2、PhyGenBench、PhyWorldBench
- **评估方式**: 给模型看"违反物理"的视频，它能不能发现不对
- **当前状态**: SOTA ≈ 随机 50%，人类 ≈ 完美
- **核心困境**: 怎么测"真正理解"？能区分违反物理的视频 = 理解物理？还是另一种 pattern matching？

这是 LeCun 的路线。他认为 1 和 3 都是假的 World Model。

### 3. 模拟器论："World Model 是世界的高保真复制品"

> 好的 World Model = 模拟出的世界越逼真、越一致、越可交互

- **代表**: Sora、Genie 3、GameGen-X
- **Benchmark**: VBench、WorldScore、WCS、FVD
- **评估方式**: 视觉保真度 + 时间一致性 + 可控性
- **已被部分证伪**: Sora 视觉逼真但不理解物理，也不能用于 Agent 决策。Sora 关闭是标志性事件

---

## 三者之间的根本张力

```
              理解物理 (2)
               /      \
              /   ？    \
             /          \
    帮 Agent 决策 (1) ——— 模拟逼真 (3)
```

**关键矛盾**:

| 关系 | 结论 | 证据 |
|------|------|------|
| 1 不需要 3 | Agent 在低保真 latent space 规划就够了 | LeWM 15M 参数 = 匹配 foundation model planning |
| 3 不保证 2 | 模拟逼真 ≠ 理解物理 | Sora 之死 |
| 2 不需要 3 | 理解物理不需要生成像素 | JEPA 在 embedding space 预测 |
| 1 可能不需要 2 | 靠统计 pattern 就能做好决策 | Dreamer 系列不"理解"物理但 Atari 超人 |

**所以**: 这三个定义描述的是**三个不同的问题**，各自有独立的 benchmark 体系，它们之间的关系尚未被严格证明。

---

## 当前 Benchmark 的归属

| Benchmark | 测的是哪个定义 | 局限 |
|-----------|--------------|------|
| Atari 100K / DMControl | 1 (Agent 工具) | 可被 pattern matching 破解 |
| VBench / FVD / WorldScore | 3 (模拟逼真) | 视觉好 ≠ 有用 |
| IntPhys 2 / PhyGenBench | 2 (物理理解) | 被动判断，不测决策能力 |
| WorldSimBench | **1+3 交叉** | 最接近综合评估，但太早期 |
| VP2 | **1 的纯化版** | 直接测控制性能，不测视觉 |
| GameGen-X SR-C/SR-E | 3+可控性 | 自创指标，待验证 |

---

## 缺失的 Benchmark：1 + 2 的交叉

最有价值但目前不存在的 benchmark 方向：

> **给 Agent 一个从未见过的物理场景，它必须基于物理推理做出正确决策**

设计思路:
- 不只是区分合理/不合理视频（IntPhys 太被动）
- 不只是跑 Atari 得分（可以靠 pattern matching）
- 而是: Agent 被放到**有新物理规则的世界里**（比如重力方向变了），看它**多快能适应**

这能区分:
- "真正理解物理" = 快速适应新规则（因为理解了底层原理）
- "只是记住 pattern" = 在新规则下完全失败

**最接近的现有工作**: WorldSimBench (ICML 2025) 的 Implicit Manipulative Evaluation，但还远不够。

---

## 对 World Model 研究的启示

1. **选赛道时必须明确**: 你在解 1/2/3 哪个问题？混在一起会导致 benchmark 不匹配
2. **车昊轩 (GameGen-X) 在 3**: 模拟逼真 + 可交互，但需要回答 "这对 Agent/物理理解有什么用"
3. **LeCun (JEPA) 在 2**: 认知正确性，但需要回答 "怎么验证真正理解 vs 另一种 pattern matching"
4. **Dreamer/Waymo 在 1**: 最务实，但可能在 OOD (out-of-distribution) 场景下脆弱
5. **真正的突破可能来自 1+2 交叉**: 一个因为理解物理而能 zero-shot 适应新环境的 Agent
