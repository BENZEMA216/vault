# Interactive Video Generation / Video World Model — Benchmark 全景

> 研究时间: 2026-04-06
> 覆盖: 24 个 benchmark，9 类 metrics

---

## 一、通用 Video Generation Benchmarks

### VBench / VBench++ / VBench-2.0（最广泛使用）
- **来源**: Vchitect (CVPR 2024 Highlight → 持续迭代)
- **维度**: VBench 1.0 = 16 维; VBench-2.0 = 5 大类 18 维, 从"表面忠实度"扩展到"内在忠实度"（物理定律、常识推理、组合性、人体结构）
- **使用者**: 几乎所有主流 T2V 模型（Sora, Kling, CogVideo, OpenSora 等）
- **URL**: [vchitect.github.io/VBench-project](https://vchitect.github.io/VBench-project/) | [Leaderboard](https://huggingface.co/spaces/Vchitect/VBench_Leaderboard)

### EvalCrafter（CVPR 2024）
- 17 个客观指标, 700 条 prompt, 覆盖 Visual/Content/Motion/Text-Video Alignment
- **URL**: [evalcrafter.github.io](https://evalcrafter.github.io/)

### T2V-CompBench（CVPR 2025）
- 组合式 T2V 生成, 7 类别 1400 条 prompt
- **URL**: CVPR 2025 paper

---

## 二、Interactive / Game Video Generation（车昊轩的赛道）

### GameGen-X Evaluation（ICLR 2025）— 当前最具参考价值
- **Generation**: FID (252.1), FVD (759.8), TVA (0.87), User Preference (0.82)
- **Control**（自创指标）:
  - **SR-C** (Character Action Success Rate): 63.0%
  - **SR-E** (Environment Event Success Rate): 56.8%
- **Baselines**: OpenSora, CogVideoX-5B, Kling, Pika, Runway, Luma
- **数据集**: OGameData — 150+ AAA 游戏, 100 万+ 片段
- **URL**: [gamegen-x.github.io](https://gamegen-x.github.io/)

### GameNGen Evaluation（Google, 2024）
- DOOM 实时模拟: PSNR 29.43, LPIPS 0.249, 20 FPS
- Human eval: AI 生成在 58-60% 情况下被认为真实（≈ 随机猜测）
- **URL**: [gamengen.github.io](https://gamengen.github.io/)

### Genie 系列评估方法演进
- **Genie 1** (ICML 2024): 定性为主, 11B 参数, 8 个 latent actions
- **Genie 2** (2024 Q4): ~10 秒一致性
- **Genie 3** (2025): **从视觉指标转向 agent task success rate** — 与 SIMA agent 配合评估

> **关键趋势**: DeepMind 将评估重心从视觉质量转向 task success 和 agent generalization

---

## 三、Video World Model 专属 Benchmarks

### WorldScore（Stanford, ICCV 2025）— 世界生成统一评估
- Controllability / Quality / Dynamics 三维度
- 3,000 测试样例, 20 个模型
- 来自 Fei-Fei Li 团队
- **URL**: [haoyi-duan.github.io/WorldScore](https://haoyi-duan.github.io/WorldScore/)

### WorldSimBench（ICML 2025）— World Simulator 能力评估
- 双重评估: Explicit Perceptual (35,000+ 样本) + Implicit Manipulative（生成视频能否转化为控制信号）
- 覆盖: Embodied / Driving / Robot Manipulation
- **URL**: [WorldSimBench](https://iranqin.github.io/WorldSimBench.github.io/)

### World Consistency Score (WCS, 2025)
- 4 子指标: Object Permanence / Relation Stability / Causal Compliance / Flicker Penalty
- **URL**: [arxiv.org/abs/2508.00144](https://arxiv.org/abs/2508.00144)

---

## 四、Physics Reasoning Benchmarks

### PhyGenBench（ICML 2025, Shanghai AI Lab）
- 160 prompt, 27 物理定律, 4 物理域
- 分层评估: Key Phenomena Detection → Physics Order → Naturalness
- **关键发现: 当前模型严重缺乏物理常识**
- **URL**: [phygenbench123.github.io](https://phygenbench123.github.io/)

### IntPhys / IntPhys 2（Meta）
- 直觉物理: 模型区分"物理可能" vs "不可能"视频
- IntPhys 2: Permanence / Immutability / Continuity / Solidity
- **当前模型 ≈ 随机 (50%), 人类 ≈ 完美**
- **URL**: [intphys.cognitive-ml.fr](https://intphys.cognitive-ml.fr/)

### PhyWorldBench（2025）
- 10 物理类别 x 5 子类 + Anti-Physics 类
- Human annotation + MLLM 自动评分
- **URL**: [arxiv.org/abs/2507.13428](https://arxiv.org/abs/2507.13428)

---

## 五、Action-Conditioned / Controllability

### VP2（Video Prediction for Visual Planning）
- **关键发现: 视觉相似度 (FVD/PSNR) 和实际控制性能不一定相关**
- 11 任务类别, 310 实例, 直接评估 planning 成功率
- **URL**: [s-tian.github.io/projects/vp2](https://s-tian.github.io/projects/vp2/)

### ACT-Bench / DrivingGen（自动驾驶）
- DrivingGen: 14 模型, 400 样本, 极端场景
- **发现: 通用模型视觉好但物理差; 驾驶专用模型运动真实但视觉落后**

---

## 六、Game-Specific RL Benchmarks

| Benchmark | 测什么 | 规模 | World Model SOTA |
|-----------|--------|------|------------------|
| **Atari 100K** | 100K 步 sample efficiency | 26 游戏 | DIAMOND 1.46 HNS |
| **DMControl** | 连续控制 | 30+ 任务 | TD-MPC2 (104 tasks) |
| **Crafter** | 开放世界全能力 | 22 achievements | DreamerV3 |
| **MineDojo** | Minecraft 开放任务 | 1000+ tasks | STEVE-1 |

---

## 七、Metrics 速查

| Metric | 测什么 | 局限 |
|--------|--------|------|
| **FVD** | 视频分布距离 | 偏向图像质量, 忽略 motion collapse |
| **FID** | 单帧分布距离 | 无时间维度 |
| **LPIPS** | 感知相似度 | 逐帧, 不捕获时间一致性 |
| **PSNR/SSIM** | 信噪比/结构 | 与人类感知相关性差 |
| **CLIP Score** | 文本-视频对齐 | 粗粒度 |
| **SR-C/SR-E** | 控制成功率 | GameGen-X 自创, 待广泛采用 |
| **WCS** | 世界一致性 | 2025 新提出 |
| **Human Eval** | 人类偏好 | Gold standard 但成本高 |

---

## 八、趋势判断

1. **视觉质量 → 世界一致性**: VBench 2.0 + WorldScore + WCS 代表新方向
2. **被动评估 → 主动交互**: VP2 证明视觉指标 ≠ 控制性能, WorldSimBench 做 embodied evaluation
3. **Physics 成为独立赛道**: PhyGenBench / IntPhys 2 — 当前模型普遍接近随机
4. **Game/Interactive 缺统一标准**: GameGen-X 自创 SR-C/SR-E, Genie 用 task success — **这是一个做 benchmark 的机会**
5. **DeepMind 信号**: Genie 系列从视觉指标转向 agent task success, 预示未来评估方向
