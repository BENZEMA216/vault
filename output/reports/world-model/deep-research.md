# World Model 深度研究报告

> 更新时间: 2026-04-03
> 覆盖范围: 技术路线、LLM 辩论、JEPA Vision、产品格局、Scaling Laws、创意应用、开源生态、趋势判断

---

## 一、技术路线全景

World Model 的核心问题是：**如何让 AI 在内部建立一个可用于预测、模拟、规划的世界表征？** 2026 年已形成五大架构路线：

### 1. Autoregressive（自回归）

**代表作**: MuZero (Nature 2020), DreamerV1-V4, IRIS, STORM, Genie 系列

**原理**: 将世界状态序列化为 token 序列，通过 next-token prediction 逐步预测未来状态。Dreamer 系列在潜在空间中自回归，Genie/IRIS 使用 VQ-VAE 离散化后做 Transformer 预测。

**优势**:
- 与 LLM 技术栈天然兼容，可复用 Transformer 基础设施
- 序列建模能力强，长期依赖处理成熟
- MuZero/EfficientZero 证明可达到超人水平
- DreamerV3 通用性强，150+ 任务无需调参

**劣势**:
- 误差累积（error compounding）：预测越远越不准，长期预测退化严重
- Token 化过程损失信息，尤其是连续物理量
- 计算成本随序列长度线性增长
- 无法并行预测多个可能未来

### 2. Diffusion（扩散模型）

**代表作**: Sora/Sora 2 (OpenAI), DIAMOND (NeurIPS 2024), GameNGen, UniSim, DriveDreamer

**原理**: 将 world model 建模为去噪过程。给定当前状态和动作，通过迭代去噪生成未来帧。可在像素空间或潜在空间操作。

**优势**:
- 视觉质量极高，生成的未来帧逼真度远超自回归
- 天然支持多模态输出（可同时预测视频、深度、语义）
- 可生成多个合理未来（多样性采样）
- GameNGen 证明可实时运行（DOOM 20fps）

**劣势**:
- 推理慢：多步去噪计算量大
- 难以保证物理一致性（常见穿模、违反物理定律）
- 条件控制精度不足（动作→结果映射不够精确）
- 训练数据需求巨大

### 3. JEPA（Joint-Embedding Predictive Architecture）

**代表作**: LeCun JEPA Vision (2022), I-JEPA (CVPR 2023), V-JEPA (2024), V-JEPA 2 (2025), LeWM (2026)

**原理**: 在表征空间（而非像素空间）进行预测。编码器将观察映射到 embedding，predictor 在 embedding 空间预测未来表征。关键创新是**丢弃不可预测的信息**（噪声、无关细节），只预测高层语义。

**优势**:
- 避免像素级预测的计算浪费和误差累积
- 自监督学习，无需标注数据
- 样本效率极高（LeWM 1500 万参数，比 GPT-2 class 模型快 48x）
- V-JEPA 2 证明可 zero-shot 迁移到机器人控制
- LeCun 认为这是通往 AMI（Advanced Machine Intelligence）的路径

**劣势**:
- Representation collapse 是核心挑战（LeWM 2026 年才解决）
- 无法生成可视化的未来（不产出像素，难以 debug）
- 表征空间的可解释性差
- 生态和工具链不如 Autoregressive / Diffusion 成熟

### 4. SSM / Mamba

**代表作**: 部分 world model 研究开始探索 Mamba 替代 Transformer，但尚无里程碑式工作

**原理**: 用状态空间模型替代 Transformer 的 attention 机制，实现线性复杂度的序列建模。理论上适合长序列的 world simulation。

**优势**:
- 线性计算复杂度，可处理超长序列
- 推理效率高（无需 KV cache）
- 适合连续时间系统建模

**劣势**:
- 缺乏 world model 领域的标志性成果
- 对空间关系建模能力待验证
- 社区和工具链不如 Transformer 成熟
- 2026 年仍处于探索阶段

### 5. 3D Spatial（3D 空间模型）

**代表作**: World Labs (Fei-Fei Li), OccWorld, NVIDIA Cosmos 3D 模块

**原理**: 直接在 3D 空间中建模世界，使用 NeRF / 3D Gaussian Splatting / Occupancy Grid 等表征。从 2D 观测重建 3D 世界状态，在 3D 空间中进行预测和规划。

**优势**:
- 物理一致性最好（3D 几何约束天然存在）
- 可直接输出可操作的 3D 场景（导出到 Unreal/Unity）
- 对遮挡、视角变化的处理最自然
- World Labs Marble 已实现从单张图片生成可交互 3D 世界

**劣势**:
- 3D 重建本身是困难问题，引入额外误差
- 计算成本高（体素化/点云处理）
- 训练数据（带深度/3D 标注）获取困难
- 实时性挑战大

### 架构对比总览

| 维度 | Autoregressive | Diffusion | JEPA | SSM/Mamba | 3D Spatial |
|------|---------------|-----------|------|-----------|------------|
| 视觉质量 | 中 | ★★★★★ | N/A（表征空间） | 中 | 高 |
| 物理一致性 | 中 | 低-中 | 高（语义层） | 中 | ★★★★★ |
| 计算效率 | 中 | 低 | ★★★★★ | ★★★★★ | 低 |
| 样本效率 | 中 | 低 | ★★★★★ | 未知 | 低 |
| 可控性 | 高 | 中 | 中 | 高 | 高 |
| 生态成熟度 | ★★★★★ | ★★★★ | ★★★ | ★★ | ★★ |
| 代表公司 | DeepMind, Wayve | OpenAI, Google | Meta | — | World Labs, NVIDIA |

---

## 二、"LLM 有没有 World Model" 辩论

这是 2024-2026 年 AI 领域最核心的学术争论之一。

### 反方：LLM 没有真正的 World Model

**主要代表**: Yann LeCun

**核心论点**:
1. **LLM 是 next-token predictor，不是 world modeler** — 预测下一个 token ≠ 理解世界因果结构
2. **误差累积**（error compounding）— LLM 在 token 空间自回归，错误会滚雪球般放大，无法可靠地长期预测
3. **无法实时学习** — LLM 的知识固化在权重中，无法像人类那样在运行时持续更新世界模型
4. **缺乏感知接地**（perceptual grounding）— 纯文本训练无法获得真正的物理世界理解
5. **Hallucination 是结构性问题** — 不是 bug 而是自回归架构的必然结果

**LeCun 原话要旨**: "Autoregressive LLMs are doomed. They generate one token after another and have no way to plan. JEPA is the path to machines that can actually understand the world."

### 正方：LLM 涌现了 World Model

**核心证据**:
1. **Othello-GPT** (Li et al., ICLR 2023) — GPT 仅从棋步序列训练，却涌现出完整的 8x8 棋盘内部表征。用线性探针可以从隐藏层读出棋盘状态，证明模型学到了不只是统计相关性
2. **数学推理** — O3/O4 在 IMO 数学奥赛级别问题上接近满分，表明 LLM 在符号推理领域发展出了类似 world model 的能力
3. **代码生成** — LLM 写出正确代码需要理解变量状态、控制流、内存布局等"程序世界模型"
4. **MCTS + LLM** — RAP/LATS 证明 LLM 可以同时充当 world model（状态转移）和 agent（策略），Tree of Thoughts 也是类似思路

### 中间立场（2026 共识趋势）

**核心观点**: LLM 在特定领域（代码、数学、棋类）发展出了 **domain-specific world model**，但缺乏一致的、可迁移的、物理接地的世界理解。

具体而言：
- **有**: 符号域的因果推理能力（代码→执行结果，数学→证明）
- **有**: 统计规律的高效压缩（语言世界的 world model）
- **没有**: 直觉物理（物体碰撞、流体运动）
- **没有**: 持续在线学习的能力
- **没有**: 跨模态一致的世界表征

**最新研究** (2025-2026): 越来越多证据表明 LLM 的"world model"是 **Approximate World Model** — 在训练分布内高度准确，在分布外迅速崩溃。这与 LeCun 的批评和 Othello-GPT 的发现并不矛盾。

---

## 三、LeCun JEPA Vision 深度分析

### 六模块认知架构

LeCun 2022 年提出的 AMI（Advanced Machine Intelligence）蓝图包含六个模块：

```
┌─────────────────────────────────────────────────┐
│               LeCun 认知架构 (2022)               │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. Configurator (配置器)                        │
│     ├── 调节所有模块的参数                         │
│     └── 根据任务动态切换注意力                      │
│                                                 │
│  2. Perception Module (感知模块)                  │
│     ├── 将感官输入映射到表征空间                    │
│     └── 提取与当前任务相关的信息                    │
│                                                 │
│  3. World Model Module (世界模型模块)             │
│     ├── 预测世界在行动后的状态变化                  │
│     ├── 在表征空间而非像素空间预测                  │
│     └── 支持多步推演和想象                         │
│                                                 │
│  4. Cost Module (代价模块)                        │
│     ├── 评估预测状态的好坏                         │
│     └── 驱动行为选择                              │
│                                                 │
│  5. Actor Module (行动模块)                       │
│     ├── 根据代价模块优化行动序列                    │
│     └── 通过梯度下降在行动空间搜索                  │
│                                                 │
│  6. Short-Term Memory (短期记忆)                  │
│     ├── 维护世界状态的工作记忆                      │
│     └── 为 world model 提供上下文                  │
│                                                 │
└─────────────────────────────────────────────────┘
```

### JEPA vs Autoregressive vs Diffusion 对比

| 维度 | JEPA | Autoregressive | Diffusion |
|------|------|---------------|-----------|
| 预测空间 | 表征空间（丢弃不可预测信息） | Token 空间 | 像素/潜在空间 |
| 训练信号 | 自监督（表征对齐） | Next-token prediction | 去噪目标 |
| 信息损失 | 有意丢弃（feature design） | Token 化损失 | 噪声注入损失 |
| 多模态未来 | 天然支持（集合预测） | 需采样多条路径 | 天然支持（多样性采样） |
| 计算效率 | 极高（不需生成像素） | 中等 | 低（多步去噪） |
| 可解释性 | 低（表征空间难解读） | 中（可看 token） | 高（可看生成图像） |
| LeCun 评价 | "正确道路" | "注定失败" | "表面模仿" |

### LeWM (2026.3) — JEPA World Model 突破

**论文**: LeWorldModel (LeCun team, arXiv 2603.19312)

**核心突破**:
1. **解决 representation collapse** — JEPA 训练的经典难题是所有输入映射到相同表征（collapse）。LeWM 通过新的正则化策略（Variance-Invariance-Covariance Regularization 增强版）彻底解决
2. **1500 万参数** — 比 GPT-2 class 模型（~1.5 亿参数）小 10 倍
3. **48x 更快** — 因为在表征空间预测，无需生成 token/像素
4. **物理推理准确** — 在受控物理环境中，LeWM 正确预测碰撞、遮挡、重力效果
5. **可迁移** — 在未见过的环境中保持预测准确性

**意义**: 这是 LeCun JEPA Vision 从理论到实践的第一个有说服力的实例化。虽然规模还小，但证明了表征空间预测的路线可行。

### V-JEPA 2 (2025) — 视频理解到具身智能

**核心参数**:
- 训练数据: **100 万小时视频**
- 架构: ViT-based，视频 JEPA + Action-Conditioned 扩展（V-JEPA 2-AC）
- 关键能力: Zero-shot robot planning — 无需任何机器人数据，直接从视频学习物理世界规律并迁移到机器人控制

**V-JEPA 2-AC 工作原理**:
1. V-JEPA 2 从大规模视频学习物理世界表征
2. 在表征空间 attach 一个 action-conditioned predictor
3. 给定当前观测和候选动作，在表征空间预测结果
4. 选择代价最低的动作执行
5. 无需 reward function，无需 RL 训练

---

## 四、产品格局

### 融资总览

World Model 相关创业公司累计融资超过 **$80 亿**（截至 2026 Q1）。

### 五大赛道

#### 1. 视频 / 游戏 World Model

| 公司/产品 | 融资 | 核心能力 | 状态 |
|-----------|------|----------|------|
| **AMI Labs** (Fei-Fei Li spin-off) | $1.03B | 通用 world model，类 AGI 愿景 | Pre-product |
| **Runway GWM-1** | 已上市 | General World Model for video + 3D | Production |
| **Genie 3** (DeepMind) | 内部 | 24fps/720p 实时交互式世界生成 | Research → Product |
| **GameNGen** (Google) | 内部 | Diffusion 实时游戏引擎 | Research |
| **GameGen-X** | 学术 | 开放世界游戏视频生成 | ICLR 2025 |

#### 2. 自动驾驶 World Model

| 公司/产品 | 融资 | 核心能力 | 状态 |
|-----------|------|----------|------|
| **Wayve** | $1.05B | GAIA-1/2/3，端到端自动驾驶 world model | Production (UK) |
| **NVIDIA Cosmos** | 内部 | World Foundation Models，开源 | Platform |
| **商汤绝影** | — | 自动驾驶场景 world model | Production (China) |

#### 3. 3D 空间 / 创意

| 公司/产品 | 融资 | 核心能力 | 状态 |
|-----------|------|----------|------|
| **World Labs** (Fei-Fei Li) | $1.23B | 单图片 → 可交互 3D 世界（Marble 产品） | Beta |
| **Runway Worlds** | — | 可探索的 3D 世界生成 | Product |

#### 4. 机器人 World Model

| 公司/产品 | 融资 | 核心能力 | 状态 |
|-----------|------|----------|------|
| **V-JEPA 2-AC** (Meta) | 内部 | Zero-shot robot planning | Research |
| **RT-2** (Google) | 内部 | Vision-Language-Action 模型 | Research → Product |
| **NVIDIA Isaac + Cosmos** | 内部 | 机器人仿真 + world model | Platform |

#### 5. 工业数字孪生

| 公司/产品 | 核心能力 | 状态 |
|-----------|----------|------|
| **NVIDIA Omniverse + Cosmos** | 工业场景仿真 + world model 预测 | Production |
| **Siemens Xcelerator** | 数字孪生 + AI 预测 | Production |

### 关键玩家详解

**AMI Labs** ($1.03B, Fei-Fei Li spin-off):
- 2025 年从 World Labs 拆分（或重组），专注通用 world model
- 愿景：构建能理解物理世界因果结构的 AI
- 估值和期望极高，但目前无公开产品

**World Labs** ($1.23B, Fei-Fei Li):
- Marble 产品：从单张图片生成可交互 3D 世界
- 可导出到 Unreal Engine / Unity
- 面向影视预览、游戏原型、建筑可视化

**Wayve** ($1.05B):
- GAIA 系列是自动驾驶 world model 最成熟的研究线
- GAIA-3 实现高保真、长时间的驾驶场景生成
- 已在英国真实道路测试

### 中国玩家

| 公司 | 产品 | 方向 |
|------|------|------|
| **商汤绝影** | SenseAuto World Model | 自动驾驶场景生成 |
| **腾讯混元** | HunyuanVideo + World Model 探索 | 视频生成 |
| **昆仑万维** | Matrix-Zero | 游戏 world model（游戏场景生成） |
| **字节跳动** | Seedance + 内部 world model 研究 | 视频生成/理解 |

---

## 五、Scaling Laws

### LLM Power Laws 适用于 World Models

**ICML 2025 关键发现**: 研究表明 world model 的性能遵循类似 LLM 的 power law scaling：
- 模型参数 ↑ → 预测准确性 ↑（对数线性关系）
- 训练数据 ↑ → 泛化能力 ↑
- 计算量 ↑ → 任务覆盖面 ↑

DreamerV3 是第一个实验验证 world model scaling 的工作：同一架构在 150+ 任务上通用，无需调参。

### 数据瓶颈

**核心问题**: 高质量 world model 训练数据极度稀缺。

- LLM 训练数据（文本）: ~15 万亿 token 可用
- World model 训练数据（带动作标注的视频/交互）: 远少于此
- **视频数据丰富但动作标注稀缺** — YouTube 有海量视频，但缺少 "动作 → 结果" 的对应关系
- V-JEPA 2 的 100 万小时视频训练是当前最大规模，但仍以被动观看为主

**解决方向**:
1. 自监督学习（JEPA 路线，不需要标注）
2. 仿真环境生成数据（NVIDIA Cosmos/Omniverse）
3. 从互联网视频中自动提取动作-结果对
4. 游戏引擎作为 world model 训练场

### Emergent Capabilities 辩论

**乐观派**: world model 会像 LLM 一样出现涌现能力——达到某个规模阈值后突然获得新能力（如物理推理、因果理解）。Othello-GPT 的棋盘表征涌现是早期证据。

**谨慎派**: world model 的涌现更难出现，因为：
1. 物理世界比语言世界更复杂（连续 + 高维 + 多物体交互）
2. 误差累积问题在物理域更严重
3. 缺乏像"语言→语言"这样的自然训练信号

---

## 六、创意应用

### 游戏

- **Genie 3** (DeepMind): 24fps / 720p 实时生成可交互的游戏世界。从一张图片或一段文字描述生成可玩游戏。对游戏原型设计的颠覆性影响
- **Runway Worlds**: 生成可探索的 3D 环境，面向影视和游戏预可视化
- **GameNGen**: 用 Diffusion 模型实时运行 DOOM，证明 world model 可替代传统游戏引擎
- **GameGen-X**: 开放世界游戏视频生成，ICLR 2025

### 影视

- **World Labs Marble**: 从单张概念图生成可交互 3D 预可视化场景，可导出到 Unreal Engine / Unity
- **Sora / Sora 2**: 视频生成即 world simulation，已被影视行业用于 previz 和概念验证
- **Procedural Content**: World model 驱动的程序化内容生成（NPC 行为、环境变化）

### 教育 / 科学

- **安全验证**: 在 world model 中模拟危险实验（化学反应、物理碰撞），无需真实操作
- **科学模拟**: 用 world model 预测实验结果，加速科学发现
- **驾驶培训**: GAIA 系列用于自动驾驶安全场景生成和边缘情况测试

---

## 七、开源生态

### 可用的开源 World Model

| 项目 | 组织 | License | 核心能力 |
|------|------|---------|----------|
| **NVIDIA Cosmos** | NVIDIA | Apache 2.0 | World Foundation Models，视频理解+生成 |
| **V-JEPA 2** | Meta | MIT | 视频 JEPA，100 万小时预训练 |
| **LeWM** | Meta / LeCun team | MIT | JEPA World Model，1500 万参数 |
| **DreamerV3** | Danijar Hafner | MIT | 通用 RL World Model |
| **DIAMOND** | 学术 | MIT | Diffusion World Model |
| **TD-MPC2** | 学术 | MIT | 连续控制 World Model |
| **I-JEPA / V-JEPA** | Meta | MIT | 自监督视觉/视频表征 |

### 社区资源

- [awesome-world-models](https://github.com/topics/world-models) — 社区策展的 world model 资源列表
- [NVIDIA Cosmos Docs](https://github.com/NVIDIA/Cosmos) — 最完整的开源 world model 文档
- [Hugging Face World Models](https://huggingface.co/models?search=world+model) — 预训练模型集合

---

## 八、2026 下半年趋势判断

### 1. JEPA 路线将获得更多验证

LeWM 和 V-JEPA 2 的成功会吸引更多研究者投入 JEPA 方向。**2026 下半年预期**：JEPA-based world model 在机器人操作任务上取得标志性成果（超越 Diffusion Policy baseline）。

### 2. World Model + Agent 融合加速

Genie 3 → SIMA 2 的路径表明 DeepMind 正在将 world model 作为 Agent 训练环境。**预期**：至少 2 家公司发布 "World Model as Agent Training Environment" 产品。

### 3. 视频 World Model 实时化

Genie 3 已实现 24fps/720p。**预期**：1080p + 交互延迟 <100ms 的 world model 在 2026 Q4 出现，游戏/影视行业开始真正采用。

### 4. 自动驾驶 World Model 成为标配

NVIDIA Cosmos 开源 + Wayve GAIA-3 + 中国玩家跟进。**预期**：Top 10 自动驾驶公司中 8 家采用 world model 做仿真和安全测试。

### 5. Scaling Laws 争论白热化

有两种可能的结果：
- (a) 出现 world model 的"ChatGPT 时刻"— 某个大规模 world model 展示出远超预期的涌现物理推理能力
- (b) 发现 world model scaling 有天花板 — 纯数据驱动方法无法突破物理理解的瓶颈

### 6. "World Model as a Service" 空白将被填补

当前没有公司提供通用的 World Model API（"给我一个场景描述 + 动作，返回预测结果"）。NVIDIA Cosmos 最接近但仍是基础模型而非 API 服务。**预期**：至少 1 家创业公司切入这个空白。

---

## 来源与参考

- 详细论文索引见 [[raw/articles/world-model/paper-index]]
- 概念定义见 [[concepts/world-model]]
- 全景地图见 [[maps/world-model]]
- Agent 关联分析见 [[connections/world-model-to-agent]]
