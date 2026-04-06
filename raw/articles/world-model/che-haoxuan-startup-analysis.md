# 车昊轩 World Model 创业构想分析

> 来源: 知识库 Query 讨论 (2026-04-06)
> 背景: 车昊轩（HKUST PhD 2025, 华为香港研究中心, GameGen-X 一作 ICLR 2025）拉 Dongzhe 创业

---

## 三层架构

```
Layer 3: World Engine（世界引擎）
         每个人创建自己的世界，Agent 在里面自主生活
              ↑ 驱动
Layer 2: Agent Harness（Agent 驾驭层）
         Agent 的可靠运行环境、约束、验证
              ↑ 推理内核
Layer 1: World Model（世界基模）
         理解物理/因果/交互的基座模型
```

**愿景**: 用户说"创建一个中世纪城镇" → World Engine 生成可交互世界 → Agent 遵循物理规律自主生活 → 用户观察、干预、设定规则

**品类**: 不是游戏（不需要写代码），不是视频（可交互），不是 Sim（Agent 有自主性）。**目前不存在的产品品类。**

---

## 定义归属

对照 World Model 三种定义分析，他做的是 **1+2+3 全栈**：

- Layer 1 → 定义 2（认知科学论，真正理解物理）
- Layer 2 → 定义 1（Agent 工具论，让 Agent 可靠运行）
- Layer 3 → 定义 3（模拟器论，生成可交互的世界）

---

## 竞品对标

| 团队 | 做了哪层 | 缺什么 |
|------|----------|--------|
| LeCun / AMI Labs ($1.03B) | Layer 1 | 不做产品 |
| Harness Engineering 社区 | Layer 2 | 不做 World Model |
| Genie 3 / GameGen-X | Layer 3 | 没有真正的 WM 推理 |
| Dreamer / V-JEPA 2 | L1 + 部分 L2 | 不做渲染/世界 |
| World Labs (Fei-Fei Li, $1.23B) | Layer 3 | 不做 Agent |
| NVIDIA Cosmos | L1+L2+L3 平台 | 平台/工具，非消费级 |
| **Minecraft 生态** | **最接近全栈** | 传统引擎，非神经网络 |

**关键**: 目前没有团队做全栈。大多数只选了一层。

---

## 车昊轩的优势

- **Layer 3 学术基础**: GameGen-X (ICLR 2025) = 第一个 DiT 开放世界游戏视频生成
- **工业经验**: ByteDance (Kling) + 快手 + 腾讯
- **数据种子**: OGameData — 150+ AAA 游戏, 100 万+ 视频片段
- **团队**: 正在指导 11 位 PhD/MSc 学生，有学术产出能力
- **CAPYBARA (2026)**: 统一视觉创作模型，往 Layer 1 方向走
- **VisionDirector (CVPR 2026)**: Closed-loop refinement，和 Agent Harness 的 self-verification 同一思路

---

## 关键风险

### 1. Layer 1 是生死问题

他站在 LeCun ($1.03B)、DeepMind (Genie 3)、Meta (V-JEPA 2) 的对面。

**技术路线选择困境**:
- 走 autoregressive（和 GameGen-X 一致）→ LeWM 证明 JEPA 在 planning 快 48x
- 走 JEPA → 不能直接渲染，需要额外生成器
- 最可能: JEPA + AR 混合架构（2026 共识方向）

### 2. 数据护城河

- NVIDIA Cosmos 用了 2000 万小时视频
- OGameData (150+ 游戏) 是种子，但够不够做通用 World Model？

### 3. 算力与成本

- World Model 推理 8-32 GPU/request（LLM 的 4-8 倍）
- "让每个人创建世界" 的 unit economics？
- **Sora 教训**: OpenAI 因运营成本 (~$100 万/天) 关闭 Sora

### 4. 物理准确性

- Genie 3 的 Reliability Paradox: 物理不准确的 world model 能训练可靠 Agent 吗？
- 如果 Layer 1 只做到 Genie 3 水平（视觉逼真但物理不准），整个大厦地基不稳

---

## Dongzhe 的交叉点（如果加入）

Dongzhe 的 Agent 基础设施研究直接填 Layer 2：

| 已有研究 | 对应需求 |
|----------|----------|
| Harness Engineering 深度研究 | Agent Harness 设计 |
| Agent Communication（MCP/A2A/7 种通信模式） | 世界里多 Agent 通信协议 |
| Agent Economy（定价、交易、分润） | Agent 在世界里自主生活的经济系统 |
| Context Engineering | Agent 的上下文管理 |
| Creative CoWork 产品经验 | 面向创作者的产品设计 |

**Agent Economy 是最独特的价值点**: "Agent 在世界里自主生活" = 需要经济机制（AGNTCY 缺少的 economic layer），这是目前整个 World Model 赛道都没有人碰的方向。

---

## 总体评价

**技术上极其 ambitious、产品愿景非常清晰、执行难度接近 moonshot。**

成败取决于:
1. Layer 1 能否超越 "视觉逼真但物理不准" 的当前天花板
2. 三层垂直整合的复杂度能否被一个 startup 驾驭
3. Unit economics 能否 work（Sora 的前车之鉴）

**最大机会**: 如果 Layer 1 做对了，这是一个目前不存在的产品品类
**最大风险**: 如果 Layer 1 只到 Genie 3 水平，就是一个更贵的游戏引擎
