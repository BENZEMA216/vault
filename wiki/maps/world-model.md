# World Model 全景地图

> 覆盖技术路线、学术演进、产品格局、论文索引、投资数据、与 Agent Economy 交叉的完整 World Model 研究地图

---

## 一、技术路线全景图

```
┌─────────────────────────────────────────────────────────────────┐
│                  World Model 技术路线 2026                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Autoregressive        Diffusion           JEPA                 │
│  ├ Dreamer V1-V4       ├ Sora / Sora 2     ├ I-JEPA (图像)      │
│  ├ MuZero              ├ DIAMOND           ├ V-JEPA / V-JEPA 2  │
│  ├ IRIS / STORM        ├ GameNGen          ├ LeWM (2026)        │
│  ├ EfficientZero       ├ UniSim            └ V-JEPA 2-AC (机器人)│
│  ├ Genie 1/2/3         └ DriveDreamer                           │
│  └ TD-MPC / TD-MPC2                                            │
│                                                                 │
│  SSM / Mamba           3D Spatial                               │
│  └ 探索阶段            ├ World Labs (Marble)                     │
│                        ├ OccWorld                                │
│                        └ NVIDIA Cosmos 3D                       │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│  融合趋势:                                                       │
│  • Autoregressive + JEPA (Dreamer + 表征预测)                    │
│  • Diffusion + 3D (Sora → World Labs 路线)                      │
│  • JEPA + Robotics (V-JEPA 2-AC)                                │
│  • 所有路线 + Agent (world model 作为 Agent 内核)                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 二、学术演进脉络

```
2018    Ha & Schmidhuber — "World Models" (NeurIPS)
        VAE + MDN-RNN, Agent 在"梦境"中学习
        ↓

2020    DreamerV1 (ICLR) — 潜在空间想象训练
        MuZero (Nature) — 学习规则 + 搜索规划, 超人表现
        ↓

2021    DreamerV2 (ICLR) — 离散 world model
        EfficientZero (NeurIPS) — 100K 步超越人类
        ↓

2022    LeCun JEPA Vision (OpenReview) — 六模块认知架构
        TD-MPC (ICML) — 时间差分 + MPC
        Inner Monologue (CoRL) — 语言模型具身推理
        ↓

2023    I-JEPA (CVPR) — 图像自监督 JEPA
        DreamerV3 (→ Nature 2025) — 通用 world model, 150+ 任务
        Othello-GPT (ICLR) — LLM 涌现棋盘表征
        Tree of Thoughts (NeurIPS) — LLM 搜索规划
        RAP (EMNLP) / LATS (→ ICML 2024) — LLM as World Model + Agent
        GAIA-1 (Wayve) — 自动驾驶 world model
        IRIS (ICLR) — Transformer world model
        STORM (NeurIPS) — 高效随机 Transformer
        UniSim (→ ICLR 2024) — 交互式世界模拟器
        RT-2 (CoRL) — Vision-Language-Action
        ↓

2024    Sora (OpenAI) — "视频生成 = 世界模拟器"
        Genie (ICML) — 从视频学习交互式环境
        Genie 2 (DeepMind) — 大规模基础 world model
        V-JEPA (Meta) — 视频 JEPA
        DIAMOND (NeurIPS Spotlight) — Diffusion world model
        GameNGen (Google) — Diffusion 实时游戏引擎
        NVIDIA Cosmos — 开源 World Foundation Models
        GameGen-X (→ ICLR 2025) — 开放世界游戏
        DriveDreamer / GenAD / OccWorld (ECCV) — 驾驶 world model
        ↓

2025    V-JEPA 2 — 100 万小时视频, zero-shot robot planning
        Genie 3 — 24fps/720p 实时交互
        Sora 2 (OpenAI)
        GAIA-2 / GAIA-3 (Wayve)
        Dreamer4
        DreamerV3 → Nature 正式发表
        World Model Scaling Laws (ICML)
        ↓

2026    LeWM (LeCun team) — JEPA world model, 解决 collapse, 48x 更快
        World Model as Service 空白待填补
        JEPA + Agent 融合加速
```

---

## 三、产品三层

### Layer 1: Research（基础研究）

| 项目 | 组织 | 路线 | 关键贡献 |
|------|------|------|----------|
| Dreamer V1-V4 | Danijar Hafner | Autoregressive | RL world model 最成熟的研究线 |
| MuZero / EfficientZero | DeepMind / 清华 | Autoregressive | 学习规则 + 搜索，超人表现 |
| JEPA 系列 | Meta / LeCun | JEPA | 表征空间预测路线 |
| Othello-GPT | Harvard | 理论 | LLM 涌现 world model 的证据 |
| DIAMOND | 学术 | Diffusion | Diffusion world model |
| STORM | 学术 | Autoregressive | 高效 Transformer world model |

### Layer 2: Platform（平台）

| 平台 | 组织 | 定位 | 开源 |
|------|------|------|------|
| **NVIDIA Cosmos** | NVIDIA | World Foundation Models + 仿真 | Apache 2.0 |
| **NVIDIA Omniverse** | NVIDIA | 工业数字孪生 + 仿真 | 商业 |
| **UniSim** | Google | 交互式世界模拟器 | 研究 |
| **Genie 3** | DeepMind | 通用交互式世界生成 | 研究 |

### Layer 3: Application（应用产品）

| 产品 | 公司 | 赛道 | 融资 | 状态 |
|------|------|------|------|------|
| **Marble** | World Labs | 3D 创意 | $1.23B | Beta |
| **GAIA-3** | Wayve | 自动驾驶 | $1.05B | Production |
| **AMI** | AMI Labs | 通用 WM | $1.03B | Pre-product |
| **Runway Worlds** | Runway | 视频/3D | 已上市 | Production |
| **GWM-1** | Runway | 视频 | 已上市 | Production |
| **Sora 2** | OpenAI | 视频 | — | Production |
| **绝影** | 商汤 | 自动驾驶 | — | Production |
| **Matrix-Zero** | 昆仑万维 | 游戏 | — | Research |
| **Seedance** | 字节 | 视频 | — | Production |

---

## 四、完整论文索引（按领域）

### Games / RL

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| World Models (Ha & Schmidhuber) | 2018 | 1,559 | S |
| MuZero | 2020 | 3,000+ | S |
| DreamerV1 | 2020 | 1,787 | S |
| DreamerV2 | 2021 | ~484 | A |
| EfficientZero | 2021 | 318 | A |
| TD-MPC | 2022 | 376 | A |
| IRIS | 2023 | 286 | B |
| DreamerV3 | 2023 | 983 | A |
| STORM | 2023 | 111 | B |
| TD-MPC2 | 2024 | ~150 | B |
| DIAMOND | 2024 | ~150 | B |
| GameNGen | 2024 | 191 | B |
| Genie | 2024 | ~250 | B |
| GameGen-X | 2024 | 新 | C |
| Genie 2 | 2024 | 新 | C |
| Genie 3 | 2025 | 新 | C |
| Dreamer4 | 2025 | 新 | C |

### Video Generation

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| Sora Technical Report | 2024 | ~800 | A |
| UniSim | 2023 | 378 | A |
| Sora 2 | 2025 | 新 | C |

### Autonomous Driving

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| GAIA-1 | 2023 | 487 | A |
| DriveDreamer | 2023 | 252 | B |
| GenAD | 2024 | 189 | B |
| OccWorld | 2023 | ~150 | B |
| GAIA-2 | 2025 | 新 | C |
| GAIA-3 | 2025 | 新 | C |
| NVIDIA Cosmos | 2025 | ~200 | B |

### Robotics / Embodied AI

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| RT-2 | 2023 | ~800 | A |
| Inner Monologue | 2022 | ~400 | A |
| V-JEPA | 2024 | ~200 | B |
| V-JEPA 2 | 2025 | 新 | C |

### Planning / Reasoning (LLM as World Model)

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| Tree of Thoughts | 2023 | 3,634 | S |
| RAP | 2023 | ~500 | A |
| LATS | 2023 | 409 | A |
| Othello-GPT | 2023 | 新 | C |

### JEPA 系列

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| LeCun JEPA Vision | 2022 | 3,000+ | S |
| I-JEPA | 2023 | 718 | A |
| V-JEPA | 2024 | ~200 | B |
| V-JEPA 2 | 2025 | 新 | C |
| LeWM | 2026 | 新 | C |

### 综述

| 论文 | 年份 | 引用 | Tier |
|------|------|------|------|
| Understanding World or Predicting Future? | 2024 | ~120 | B |

> 详细论文信息（作者、会议、链接）见 [[output/reports/world-model/paper-index]]

---

## 五、投资格局

```
┌────────────────────────────────────────────────┐
│           World Model 投资 (截至 2026 Q1)        │
│           累计融资: $80亿+                        │
├────────────────────────────────────────────────┤
│                                                │
│  $1B+ Club                                     │
│  ├ World Labs   $1.23B  (Fei-Fei Li, 3D 空间)  │
│  ├ Wayve        $1.05B  (自动驾驶)               │
│  └ AMI Labs     $1.03B  (通用 WM, Fei-Fei Li)  │
│                                                │
│  大公司内部投入                                   │
│  ├ NVIDIA       Cosmos + Omniverse              │
│  ├ Google       Genie 3 + UniSim                │
│  ├ OpenAI       Sora 2                          │
│  ├ Meta         V-JEPA 2 + LeWM                 │
│  └ 中国公司      商汤/腾讯/昆仑万维/字节            │
│                                                │
│  投资热点转移                                     │
│  2023: 自动驾驶 world model                      │
│  2024: 视频生成 (Sora 效应)                       │
│  2025: 3D 空间 + 通用 world model                │
│  2026: JEPA + Agent 融合                         │
│                                                │
└────────────────────────────────────────────────┘
```

---

## 六、与 Agent Economy 的交叉

### World Model 在 Agent 架构中的位置

```
┌────────────────────────────────────────┐
│           Agent Architecture           │
├────────────────────────────────────────┤
│                                        │
│  Perception → World Model → Planning   │
│                  │                      │
│              预测行动后果                 │
│              模拟多个方案                 │
│              评估风险/收益               │
│                  │                      │
│              Action → Environment       │
│                                        │
└────────────────────────────────────────┘
```

### 交叉机会

1. **World Model as Agent Service** — 将 world model 包装为 Agent 可调用的预测 API（当前空白）
2. **Agent 训练环境** — Genie 3 / Cosmos 生成的虚拟环境用于 Agent 训练
3. **Creative CoWork 连接** — 视频 Agent 需要 world model 预测生成质量、判断物理一致性
4. **安全测试** — World model 作为 Agent 行为的安全模拟器
5. **数字孪生 + Agent** — 工业 Agent 在数字孪生（world model）中规划和验证

### 相关 Agent Economy 研究

- [[connections/world-model-to-agent]] — 详细交叉分析
- [[connections/communication-to-economy]] — Agent Communication 经济层空白

---

## 关联概念

- [[concepts/world-model]] — World Model 概念文章
- [[concepts/agent-loop]] — Agent 循环架构
- [[concepts/context-engineering]] — 上下文工程
- [[concepts/harness-engineering]] — Agent 运行环境工程
- [[concepts/agent-runtime]] — Agent 运行时基础设施
- [[concepts/video-agent-workflow]] — 视频 Agent 工作流

## 关联地图

- [[maps/agent-infrastructure]] — Agent 基础设施全景
- [[maps/agent-communication]] — Agent 通信全景

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
