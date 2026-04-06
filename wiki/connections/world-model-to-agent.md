# World Model → Agent 关联分析

> 将 World Model 研究发现映射到 Agent 系统设计和 Agent Economy 机会

---

## 关联总览

World Model 为 Agent 提供**"预测行动后果"**的核心能力。没有 world model 的 Agent 只能 trial-and-error；有 world model 的 Agent 可以在想象中预演、评估、规划，大幅提升效率和安全性。当前最大空白是**没有人将 world model 包装为 Agent 可调用的通用服务**。

---

## 一、World Model 作为 Agent 的内核

### Dreamer 系列 = Agent 在想象中训练

**核心机制**: Dreamer V1-V4 的 Agent 架构是 world model 与 Agent 最紧密的结合：

```
真实环境 → 收集数据 → 训练 World Model
                            ↓
                     World Model 生成想象轨迹
                            ↓
                     Agent 在想象中训练 (无需真实交互)
                            ↓
                     策略部署到真实环境
```

- DreamerV1: 在潜在空间想象，Actor-Critic 在想象中训练
- DreamerV2: 离散 world model，解决连续表征问题
- DreamerV3: 通用 world model，150+ 任务无需调参
- Dreamer4: 进一步扩展

**对 Agent 系统的启示**:
- Agent 不需要每次都与真实环境交互来学习
- 高质量 world model 可以 10-100x 加速 Agent 训练
- "想象中的错误" 没有真实代价 — 安全训练

### Genie 3 → SIMA 2: World Model 训练 Embodied Agent

**DeepMind 路径**:
1. Genie 3 生成 24fps/720p 的可交互世界
2. SIMA (Scalable Instructable Multiworld Agent) 在 Genie 生成的世界中训练
3. SIMA 2 预期利用 Genie 3 更高保真度的世界来训练更强的 embodied agent

**意义**: World model 不仅是 Agent 的组件，更是 Agent 的**训练场**。Genie 3 本质上是一个无限的、可控的、低成本的 Agent 训练环境生成器。

### V-JEPA 2-AC: Zero-Shot Robot Agent

**工作原理**:
1. V-JEPA 2 从 100 万小时视频学习物理世界表征（无标注）
2. Attach action-conditioned predictor
3. 给定当前观测 + 候选动作 → 在表征空间预测结果
4. 选择代价最低的动作执行

**对 Agent 系统的启示**:
- **Zero-shot transfer**: 无需机器人数据，从互联网视频即可学习 Agent 策略
- **无需 reward function**: 通过代价模块（而非 RL reward）驱动行为
- **通用性**: 同一个 world model 可适用于不同机器人和任务

---

## 二、LLM 同时作为 World Model 和 Agent

### RAP (Reasoning with LLM is Planning with World Model)

**核心发现**: LLM 可以同时扮演两个角色：
- **World Model 角色**: 给定状态和行动，LLM 预测下一个状态（状态转移函数）
- **Agent 角色**: LLM 选择最优行动（策略函数）

通过 MCTS（Monte Carlo Tree Search）连接两个角色：
1. LLM-as-World-Model 模拟行动结果
2. LLM-as-Agent 评估结果并选择下一步
3. MCTS 搜索最优行动序列

### LATS (Language Agent Tree Search)

**统一框架**: 将 LLM 推理、行动、规划统一在一个 tree search 框架中：
- **推理**: LLM 生成思考过程
- **行动**: LLM 选择工具调用 / 外部操作
- **规划**: Tree search 在 LLM-simulated 世界中探索

### Tree of Thoughts

**核心**: 让 LLM 在思维树中搜索，每个节点是一个"thought"，LLM 同时负责生成子节点（world model）和评估节点价值（agent）。

**对 Agent 系统的启示**:
- **当前 Agent 已在隐式使用 LLM 作为 world model** — 每次 LLM 预测工具调用的结果，都是在做 world modeling
- **显式化** world model 角色可以提升 Agent 的规划质量
- **搜索 + LLM** 可能是短期内最实用的 "world model + agent" 方案

---

## 三、产品空白: World Model as Agent Service

### 当前状态

**没有人做通用的 World Model API 服务。**

现有最接近的产品：
- NVIDIA Cosmos: 开源基础模型，但不是 API 服务
- OpenAI Sora: 视频生成 API，但不支持交互式 world simulation
- DeepMind Genie 3: 研究项目，未开放 API

### 需要的服务形态

```
World Model as Agent Service (WMaaS)
├── API: POST /predict
│   ├── input: { state, action, horizon }
│   └── output: { predicted_states[], confidence, physics_consistency_score }
│
├── API: POST /simulate
│   ├── input: { scenario, action_sequence }
│   └── output: { trajectory, outcome, risk_assessment }
│
├── API: POST /plan
│   ├── input: { current_state, goal_state }
│   └── output: { action_sequence, expected_cost, alternatives[] }
│
└── 定价: 按预测步数 / 模拟时长 / 复杂度
```

### 为什么这是空白

1. **技术门槛高** — 需要大规模 world model + 低延迟推理
2. **通用性难** — 不同领域（驾驶 vs 游戏 vs 机器人）的 world model 差异大
3. **评估困难** — "预测准确吗？" 很难量化衡量
4. **商业模式不清晰** — 按什么维度定价？

### 对 Agent Economy 的意义

如果 WMaaS 存在：
- Agent 可以**调用外部 world model** 而非自己内置 — 降低 Agent 开发门槛
- 不同 Agent 可以共享同一个高质量 world model — 规模效应
- World model 提供者获得 API 收入 — 新的 Agent Economy 角色

---

## 四、NVIDIA Cosmos → Agent Simulation Platform

### 当前定位

NVIDIA Cosmos 是目前最接近 "World Model as Platform" 的产品：
- 开源 World Foundation Models（Apache 2.0）
- 覆盖视频理解和生成
- 与 NVIDIA Omniverse 集成用于物理仿真
- 面向自动驾驶、机器人、工业数字孪生

### Agent Simulation 路径

```
NVIDIA Cosmos (World Model)
  + Omniverse (Physics Simulation)
  + Isaac (Robot Simulation)
  = Agent Training & Testing Platform
```

**已有用例**:
- 自动驾驶 Agent 在 Cosmos 生成的场景中训练
- 机器人 Agent 在 Isaac + Cosmos 中学习操作
- 工业 Agent 在 Omniverse 数字孪生中验证决策

**缺失**:
- 不是通用 Agent 可调用的 API
- 主要面向开发者而非 Agent
- 没有 Agent-to-WorldModel 的标准协议

---

## 五、Creative CoWork 连接

### 视频 Agent 需要 World Model 做质量预测

在 [[concepts/video-agent-workflow|视频 Agent 工作流]] 中，多个环节需要 world model 能力：

1. **分镜质量预测**: 生成视频前，预测当前 prompt 是否会产出物理一致的画面
2. **镜头衔接**: 预测两个镜头拼接后的视觉连贯性
3. **角色一致性**: 预测不同镜头中角色外观是否一致
4. **物理合理性**: 预测生成的动作是否符合物理规律

**当前问题**: 视频 Agent 没有 world model，只能生成后检查（post-hoc），浪费大量算力和时间。

**理想状态**: 视频 Agent 内置或调用 world model，在生成前预判质量，只生成高置信度的内容。

---

## 六、时间线与行动建议

### 近期可跟踪信号（2026 H2）

| 时间 | 事件 | 对 Agent 系统的影响 |
|------|------|-------------------|
| 2026 Q3 | V-JEPA 2 开源细节 | JEPA 路线是否可被广泛复用 |
| 2026 Q3 | NVIDIA Cosmos 2.0 | 是否增加 Agent API |
| 2026 Q4 | Genie 3 是否开放 API | World Model API 的先例 |
| 2026+ | LeWM 扩展实验 | JEPA world model 能否 scale |
| 2026+ | MCP Agent-to-Agent | World model 是否可通过 MCP 暴露 |

### 行动建议

1. **跟踪 Cosmos 2.0** — 如果 NVIDIA 增加 Agent API，WMaaS 空白可能被大厂填补
2. **实验 RAP/LATS** — 在 Creative CoWork 中让 LLM 显式充当 world model（预测视频质量）
3. **研究 V-JEPA 2-AC** — 零数据迁移的思路可能适用于创意 Agent
4. **关注 World Model as MCP Server** — 将 world model 暴露为 MCP Tool 是最自然的集成方式

---

## 来源文件

- [[raw/articles/world-model/paper-index]] — 论文索引
- [[raw/articles/world-model/deep-research]] — 深度研究报告

## 关联概念

- [[concepts/world-model]] — World Model 概念文章
- [[concepts/agent-loop]] — Agent 循环架构
- [[concepts/video-agent-workflow]] — 视频 Agent 工作流
- [[concepts/agent-communication]] — Agent 通信（MCP 集成路径）

## 关联地图

- [[maps/world-model]] — World Model 全景地图
- [[maps/agent-infrastructure]] — Agent 基础设施全景
- [[maps/agent-communication]] — Agent 通信全景

## 关联连接

- [[connections/communication-to-economy]] — Agent Economy 空白分析

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
