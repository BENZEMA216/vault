# World Model 产品全景与商业机会分析

> 研究日期: 2026-04-03 | Deep Research by BENZEMA216

---

## TL;DR

World Model 正在成为继 LLM 之后最重要的 AI 范式转移。2025-2026 年，该领域累计融资超过 **$80 亿**，涵盖视频/游戏模拟、自动驾驶、机器人、3D 空间智能和工业数字孪生五大赛道。核心玩家包括 Google DeepMind (Genie 3)、NVIDIA (Cosmos)、Runway (GWM-1)、World Labs (Marble)、AMI Labs (LeCun)、Wayve、Skild AI 等。中国方面商汤、腾讯、昆仑万维已入场，但整体落后硅谷 6-12 个月。

**最大的未填补空白**: World Model 的 Agent 化平台层 — 将 World Model 能力包装为可被 AI Agent 调用的服务基础设施。

---

## 一、视频/游戏 World Simulator

### 1.1 Google DeepMind — Genie 3

| 维度 | 详情 |
|------|------|
| **产品** | [Genie 3](https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/) — 通用 World Model |
| **发布** | 2025 年 8 月；2026 年 2 月开放给 Google AI Ultra 用户 |
| **能力** | 从文本生成可实时交互的 3D 世界，720p @ 24fps，物理自学习 |
| **技术** | Autoregressive foundation world model，从视频数据中学习物理规律 |
| **商业模式** | 绑定 Google AI Ultra 订阅（$249.99/月） |
| **差异化** | 第一个真正实时交互的通用 World Model；被 TIME 评为 [2025 最佳发明](https://time.com/collections/best-inventions-2025/7318419/google-deepmind-genie-3/) |
| **定位** | DeepMind 视其为通向 AGI 的关键路径 |

### 1.2 Decart — Oasis

| 维度 | 详情 |
|------|------|
| **产品** | [Oasis](https://oasis-model.github.io/) — 实时可玩 World Model（类 Minecraft）；[Mirage](https://decart.ai/) — 实时视频变换 |
| **融资** | [$100M Series B](https://fortune.com/2025/08/07/exclusive-decart-raises-100-million-at-a-3-1-billion-valuation-chasing-the-future-of-real-time-creative-ai/)（2025.8），估值 $3.1B，总融资 $153M |
| **投资方** | Sequoia Capital, Benchmark, Zeev Ventures, Aleph VC |
| **技术** | GPU 极致优化 + 实时推理，20fps，每帧 0.04s 生成，无需传统游戏引擎 |
| **商业模式** | 面向创意 AI 的实时视频平台（Mirage 产品化） |
| **差异化** | 速度 — 比传统 text-to-video 快 100x；以色列团队，GPU 优化能力极强 |

### 1.3 Odyssey

| 维度 | 详情 |
|------|------|
| **产品** | [Odyssey Explorer](https://techcrunch.com/2025/05/28/odysseys-new-ai-model-streams-3d-interactive-worlds/) — 流式交互 3D 世界 |
| **融资** | [$27M 总融资](https://techcrunch.com/2025/05/28/odysseys-new-ai-model-streams-3d-interactive-worlds/)（$18M Series A），GV, EQT Ventures |
| **创始人** | Oliver Cameron & Jeff Hawke（前自动驾驶从业者） |
| **技术** | "因果式"生成 — 仅基于过去事件预测帧，40ms 延迟，真正开放式交互 |
| **差异化** | 自研 360° 背包相机系统采集真实世界数据；Ed Catmull（Pixar 联合创始人）为董事 |
| **商业模式** | 面向影视、游戏、沉浸式媒体的 B2B 工具 |

### 1.4 Runway — GWM-1

| 维度 | 详情 |
|------|------|
| **产品** | [GWM-1](https://runwayml.com/research/introducing-runway-gwm-1) — 三个变体：Worlds（环境探索）/ Avatars（对话角色）/ Robotics（机器人训练） |
| **融资** | [$315M](https://siliconangle.com/2026/02/10/world-model-startup-runway-closes-315m-funding-round/)（2026.2），估值 $5.3B |
| **技术** | 基于 Gen-4.5 的 autoregressive model，实时逐帧生成，支持 action-conditioned 控制 |
| **商业模式** | SaaS 订阅 + 企业 SDK（Robotics 版本通过 Python SDK 提供） |
| **差异化** | 三合一产品矩阵覆盖创意 + 机器人场景；已有庞大创意用户基础 |

### 1.5 World Labs — Marble

| 维度 | 详情 |
|------|------|
| **产品** | [Marble](https://www.worldlabs.ai/) — 可导航、持久化 3D 环境生成 |
| **融资** | [$1.23B 总融资](https://theaiinsider.tech/2026/02/19/fei-fei-lis-world-labs-raises-1b-in-fresh-funding-to-advance-development-of-world-models/)：$230M（2024.9）+ $1B（2026.2），估值约 $5B |
| **投资方** | AMD, Autodesk ($200M), NVIDIA, Fidelity, Emerson Collective, Sea |
| **创始人** | 李飞飞 (Fei-Fei Li) — AI 领域最有影响力的学者之一 |
| **技术** | Large World Model，spatial intelligence，从文本/图片/视频/3D 布局生成可导航 3D 世界 |
| **商业模式** | Freemium ($0-$95/月)，支持导出到 Unreal Engine / Unity |
| **差异化** | "Chisel" 编辑工具支持专业级 3D 工作流；游戏/VFX/VR/机器人全覆盖 |

### 1.6 AMI Labs — Yann LeCun

| 维度 | 详情 |
|------|------|
| **产品** | 尚未发布产品，专注 World Model 基础研究 |
| **融资** | [$1.03B seed](https://techcrunch.com/2026/03/09/yann-lecuns-ami-labs-raises-1-03-billion-to-build-world-models/)（2026.3），估值 $3.5B — 欧洲历史最大 seed 轮 |
| **投资方** | Cathay Innovation, Greycroft, Hiro Capital, HV Capital, Bezos Expeditions, Eric Schmidt |
| **创始人** | Yann LeCun（图灵奖得主），Alex LeBrun (CEO)，Saining Xie (CSO) |
| **技术路线** | 反 LLM 路线 — 主张 World Model 才是通向 AGI 的正确道路 |
| **差异化** | 最纯粹的 World Model 赌注；巴黎总部，欧洲 AI 标杆 |

### 1.7 其他重要玩家

| 公司 | 产品 | 融资 | 备注 |
|------|------|------|------|
| **Luma AI** | Ray3（推理式视频模型） | [$900M Series C](https://lumalabs.ai/press/luma-ai-raises-900-million-series-c-led-by-humain-and-partners-on-2-gigawatt-ai-supercluster-in-saudi-arabia)，估值~$4B | 3000万用户；沙特 HUMAIN 领投 |
| **General Intuition** | 游戏数据训练 Agent 空间推理 | [$133.7M seed](https://techcrunch.com/2025/10/16/general-intuition-lands-134m-seed-to-teach-agents-spatial-reasoning-using-video-game-clips/) | Medal.tv spinoff，20亿/年游戏视频 clip |
| **Overworld** | Waypoint-1（消费级 GPU 本地运行） | [$4.5M pre-seed](https://kindredventures.com/announcement/overworld-building-real-time-world-models/) | 前 Stability AI 团队；本地优先 |
| **Tencent Hunyuan** | HY World 1.5 | 内部项目 | 开源，24fps，支持键鼠控制 |

---

## 二、NVIDIA Cosmos

| 维度 | 详情 |
|------|------|
| **产品** | [NVIDIA Cosmos](https://www.nvidia.com/en-us/ai/cosmos/) — World Foundation Model Platform |
| **发布** | CES 2025（2025.1） |
| **定位** | Physical AI 的基础设施层 — 服务自动驾驶 + 机器人 |
| **训练规模** | 9 万亿 tokens，来自 2000 万小时真实世界视频 |
| **模型分层** | Nano（边缘设备）/ Super（基线）/ Ultra（最高质量） |
| **下载量** | 200 万+ 下载（截至 2026.1） |
| **开放性** | 开放模型许可，可通过 NVIDIA API catalog / NGC / Hugging Face 获取 |
| **最新版本** | Cosmos Reason 2（2026.2）— 增强推理能力 |
| **客户** | 1X, Agile Robots, Agility, Figure AI, Foretellix, Fourier, Galbot, Skild AI, XPENG, Uber, Waabi 等 |

### Cosmos 的战略意义

Cosmos 不是一个产品，而是一个 **平台层策略**：

1. **锁定生态** — 所有用 Cosmos 训练的机器人/自动驾驶公司都依赖 NVIDIA GPU
2. **数据飞轮** — 开放模型吸引开发者 → 产生更多数据 → 模型更好 → 卖更多 GPU
3. **全栈覆盖** — Cosmos (World Model) + Isaac Sim (仿真) + Omniverse (数字孪生) + GPU (硬件)

---

## 三、自动驾驶 World Model

### 3.1 Wayve — GAIA 系列

| 维度 | 详情 |
|------|------|
| **产品** | [GAIA-3](https://wayve.ai/thinking/gaia-3/) — 自动驾驶评估 World Model |
| **融资** | [$1.5B Series D](https://wayve.ai/press/series-d/)（2026.2），估值 $8.6B |
| **投资方** | Microsoft, NVIDIA, Uber, Mercedes-Benz, Nissan, Stellantis |
| **技术** | 150 亿参数 latent diffusion World Model；5x 计算量 + 10x 数据（vs GAIA-2）；覆盖 9 国 3 大洲 |
| **用途** | 不是直接驾驶，而是生成逼真仿真场景用于安全评估 |
| **差异化** | 从 "模拟" 转向 "评估" — World Model 作为自动驾驶的测试基础设施 |

### 3.2 Waabi

| 维度 | 详情 |
|------|------|
| **产品** | [Waabi World](https://waabi.ai/)（仿真器）+ Waabi Driver（端到端驾驶 AI） |
| **融资** | [$1B](https://www.founderstoday.news/waabi-secures-1-billion-funding/)（$750M Series C + $250M Uber milestone capital） |
| **投资方** | Khosla Ventures, G2 Venture Partners, Uber |
| **创始人** | Raquel Urtasun — 多伦多大学教授，前 Uber ATG 首席科学家 |
| **商业模式** | Robotaxi（与 Uber 合作部署 25,000+ 辆）+ 自动驾驶卡车 |
| **差异化** | Simulation-first 路线 — 90%+ 训练在仿真中完成 |

### 3.3 Tesla

| 维度 | 详情 |
|------|------|
| **产品** | Neural World Simulator（内部工具） |
| **数据规模** | 车队每天生成 500 年驾驶数据，8 个高帧率摄像头/车 |
| **技术** | 神经网络生成视频引擎，同时模拟 8 路摄像头画面；支持注入对抗事件 |
| **用途** | 训练 FSD 下一版本（v14）；预测周围道路使用者行为 |
| **差异化** | 数据量无人能敌 — 数十亿 token/30 秒 |

---

## 四、机器人 World Model

### 4.1 融资全景

| 公司 | 融资 | 估值 | 核心产品 |
|------|------|------|---------|
| **[Skild AI](https://www.skild.ai/)** | $1.83B 总计 | $14B | 通用机器人基础模型 — "共享大脑" |
| **[Figure AI](https://www.figure.ai/)** | $675M+ | $39B | Figure 02 人形机器人（已在 BMW 工厂部署） |
| **[Physical Intelligence (π)](https://www.physicalintelligence.company/)** | $1B+ | $2.4B+ | π0.5 VLA 模型 — 进入陌生家庭即可工作 |
| **[1X Technologies](https://www.1x.tech/)** | $123.5M+，拟融 $1B | ~$10B | NEO 人形机器人（家庭助手） |

### 4.2 World Model 在机器人中的应用

1. **Sim-to-Real Transfer** — 在 World Model 中训练策略，迁移到真实机器人
2. **合成数据生成** — 用 World Model 生成海量训练场景
3. **预测 & 规划** — 机器人通过 World Model 预测动作后果，再做决策
4. **跨形态泛化** — Skild AI 的模型可在不同机器人形态间共享

### 4.3 关键基础设施

- **NVIDIA Cosmos** — 统一合成数据生成 + 视觉推理 + 动作仿真
- **NVIDIA Isaac Sim** — 基于 PhysX 的机器人仿真
- **NVIDIA GR00T N** — 人形机器人基础模型
- **Runway GWM Robotics** — 通过 Python SDK 生成机器人训练数据

---

## 五、3D/空间 World Model

### 5.1 核心玩家

| 公司 | 技术路线 | 状态 |
|------|---------|------|
| **World Labs (Marble)** | Large World Model + Spatial Intelligence | 已商业化，$5B 估值 |
| **Luma AI** | 多模态 World Model（视频/音频/文本） | 已商业化，$4B 估值 |
| **Google DeepMind (Genie 3)** | Foundation World Model | 研究预览 → 产品化中 |

### 5.2 技术演进

| 技术 | 状态（2026） | 应用 |
|------|-------------|------|
| **NeRF** | 成熟，但逐渐让位于 3DGS | 3D 重建、VR |
| **3D Gaussian Splatting (3DGS)** | 2025 年成为主导技术 | AR/VR、机器人 SLAM |
| **Large World Models** | 快速发展 | 游戏、影视、机器人 |
| **Spatial AI** | 早期商业化 | 自动驾驶、无人机 |

---

## 六、工业 World Model / 数字孪生

### 6.1 主要玩家

| 公司 | 产品 | 定位 |
|------|------|------|
| **NVIDIA Omniverse** | 数字孪生平台 | 基础设施层 |
| **[Siemens](https://news.siemens.com/en-us/digital-twin-composer-ces-2026/)** | Digital Twin Composer | 工业元宇宙 |
| **[Dassault Systèmes](https://www.nextplatform.com/2026/02/04/dassault-and-nvidia-bring-industrial-world-models-to-physical-ai/)** | Virtual Twin + NVIDIA 合作 | 工业 World Model |
| **Bentley Systems** | iTwin Platform | 基础设施数字孪生 |

### 6.2 关键案例

- **PepsiCo** — 用数字孪生重建每台机器、传送带、操作路径，AI Agent 在仿真中测试变更，90% 问题在上线前发现
- **BMW** — Figure 02 人形机器人在数字孪生工厂中完成部署前测试
- **Dassault + NVIDIA** — 定义工业 Physical AI 计算堆栈：共享的、物理验证的数字孪生 World Model

---

## 七、中国生态

### 7.1 主要玩家

| 公司 | 产品 | 状态 |
|------|------|------|
| **[商汤科技](https://finance.sina.com.cn/stock/t/2025-07-28/doc-infhziex0195347.shtml)** | 绝影开悟 World Model 3.0 + 悟能具身平台 | 中国世界模型综合评价第一；开源 |
| **[腾讯](https://worldsimulator.ai/blog/articles/best-ai-world-models)** | 混元 HY World 1.5 | 国内首个开放实时体验的 World Model，开源 |
| **[昆仑万维](https://www.qbitai.com/2025/02/253855.html)** | Matrix-Zero | 中国首家同时推出 3D 场景 + 可交互视频生成的公司 |
| **字节跳动** | [Seedance 2.0](https://www.asiafinancial.com/bytedance-rolls-out-seedance-2-0-as-openai-scraps-sora) / Seaweed | 视频生成（7B 参数）→ OpenAI 关闭 Sora 后的最大赢家 |
| **大晓机器人**（商汤生态） | 开悟 3.0 + ACE 具身范式 | 首个面向商业应用的开源 World Model |

### 7.2 中国 vs 硅谷差距分析

| 维度 | 中国 | 硅谷 |
|------|------|------|
| **融资规模** | 较小（无单轮 $1B 级 World Model 融资） | AMI $1B, World Labs $1B, Wayve $1.5B |
| **技术路线** | 大模型增强 + 物理引擎融合 | 纯 World Model Foundation Model |
| **商业化** | 开源 + 云服务 | Freemium SaaS + 企业级 SDK |
| **数据优势** | 字节的 200M CapCut 用户 | Tesla 500 年/天驾驶数据，Medal 20 亿 clip/年 |
| **差距** | 落后 6-12 个月 | 引领方向 |

### 7.3 Sora 关闭的连锁反应

OpenAI [于 2026 年 3 月关闭 Sora](https://www.valleytechlogic.com/2026/03/27/so-long-sora-chatgpt-pulls-the-plug-on-ai-video-generation-platform-amidst-a-1-billion-dollar-pull-out-by-disney/)，转向将底层技术用于 robotics simulation 和 world-modelling 研究。字节跳动 Seedance 2.0 迅速填补空白，利用 CapCut 生态的 2 亿用户基础，将生成式视频转化为消费者习惯。

---

## 八、投资全景

### 8.1 World Model 专项融资（2024-2026）

| 公司 | 轮次 | 金额 | 估值 | 时间 |
|------|------|------|------|------|
| World Labs | Series A + B | $1.23B | ~$5B | 2024.9 + 2026.2 |
| AMI Labs | Seed | $1.03B | $3.5B | 2026.3 |
| Wayve | Series D | $1.5B | $8.6B | 2026.2 |
| Waabi | Series C + | $1B | — | 2025-2026 |
| Skild AI | 多轮 | $1.83B | $14B | 2024-2026 |
| Luma AI | Series C | $900M | ~$4B | 2025.11 |
| Physical Intelligence | A + B | $1B+ | $2.4B+ | 2024-2025 |
| Figure AI | 多轮 | $675M+ | $39B | 2024-2025 |
| Runway | — | $315M | $5.3B | 2026.2 |
| General Intuition | Seed | $133.7M | — | 2025.10 |
| Decart | Series B | $153M 总 | $3.1B | 2025.8 |
| 1X Technologies | Series B + | $123.5M+ | ~$10B | 2024-2025 |
| Odyssey | Series A | $27M | — | 2025 |
| Overworld | Pre-seed | $4.5M | — | 2025 |

**合计已知融资: >$80 亿**

### 8.2 关键投资方分布

| 投资方 | 投资标的 |
|--------|---------|
| **NVIDIA** | Cosmos 生态 + World Labs + Wayve + Skild AI |
| **Bezos Expeditions** | AMI Labs + Physical Intelligence + Skild AI |
| **Sequoia Capital** | Decart + Skild AI |
| **Khosla Ventures** | Waabi + General Intuition |
| **SoftBank** | Skild AI（领投 $1.4B） |
| **a16z** | Luma AI + 更广泛 AI 布局 |
| **Microsoft** | Wayve |
| **Uber** | Wayve + Waabi（战略合作） |

### 8.3 宏观投资趋势

- [Q1 2026 全球创投 $300B](https://news.crunchbase.com/venture/record-breaking-funding-ai-global-q1-2026/)，其中 AI 占 80%（$242B）
- AI 占 2025 年所有风投的 41%（历史最高）
- [a16z 管理资产达 $900 亿](https://news.crunchbase.com/venture/a16z-15b-new-funds-american-dynamism-ben-horowitz/)
- World Model 从"研究方向"变成"投资主题"，与 Physical AI / 具身智能高度重叠

---

## 九、World Model 产品机会分析

### 9.1 当前竞争格局总结

```
                    ┌─────────────────────┐
                    │   Foundation Model   │
                    │  (AMI, DeepMind,     │
                    │   NVIDIA Cosmos)     │
                    └────────┬────────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
    ┌─────────▼──────┐ ┌────▼─────┐ ┌──────▼──────┐
    │ Creative/Media │ │ Physical │ │  Industrial │
    │ (Runway, Luma, │ │   AI     │ │  (Siemens,  │
    │  World Labs,   │ │(Wayve,   │ │  Dassault,  │
    │  Decart)       │ │ Skild,   │ │  Omniverse) │
    └────────────────┘ │ Figure)  │ └─────────────┘
                       └──────────┘
```

### 9.2 未填补的机会空白

#### 空白 1: World Model Agent Platform（最大机会）

**问题**: 当前 World Model 要么是面向人类的创意工具（Marble、Runway），要么是面向特定领域的训练基础设施（Cosmos、GAIA）。没有一个平台将 World Model 能力包装为 **AI Agent 可调用的 API 服务**。

**机会**:
- Agent 需要"预测行动后果"的能力 → World Model 正是此能力
- 构建 World-Model-as-a-Service：Agent 发送 action → 返回 predicted outcome
- 类比: LLM API (OpenAI) 之于 World Model API
- 适用场景: 电商 Agent 预测用户行为、物流 Agent 模拟路径、金融 Agent 模拟市场

#### 空白 2: 垂直行业 World Model

**问题**: 现有 World Model 都是通用型（学习"整个物理世界"）。垂直行业（医疗、建筑、农业）的专用 World Model 几乎为零。

**机会**:
- 医疗 World Model — 模拟手术过程和药物反应
- 建筑 World Model — 从设计图直接生成可交互的建筑环境
- 零售 World Model — 模拟店铺布局对消费者行为的影响

#### 空白 3: Edge World Model

**问题**: 绝大多数 World Model 需要大量 GPU（8-32 GPU/请求）。只有 Overworld 在尝试 consumer GPU 上运行。

**机会**:
- 手机端/边缘设备运行的轻量 World Model
- 用于 AR 眼镜、机器人本体端
- 模型蒸馏 + 量化技术应用

#### 空白 4: World Model 数据基础设施

**问题**: 训练 World Model 需要海量高质量视频数据。数据采集、标注、合规是巨大瓶颈。

**机会**:
- 视频数据采集平台（类似 Scale AI 但面向 World Model）
- 游戏数据 → World Model 的桥梁（General Intuition 方向但更平台化）
- 合成数据质量评估工具

#### 空白 5: World Model Evaluation & Benchmarking

**问题**: 没有标准化的 World Model 评估框架。各家自说自话。

**机会**:
- 建立行业标准 benchmark（类似 LLM 的 MMLU/HumanEval）
- 物理一致性、时间连贯性、交互响应性的量化评估
- Wayve 的 GAIA-3 已经在往 evaluation 方向走

### 9.3 与 Agent 系统的连接点

这是 **最关键的交叉点**：

| Agent 能力需求 | World Model 可提供 | 当前状态 |
|---------------|-------------------|---------|
| 预测行动后果 | 仿真未来状态 | 无产品化 API |
| 空间理解 | 3D 环境感知 | World Labs Marble 有基础 |
| 物理推理 | 物理规律建模 | Cosmos/Genie 3 有能力 |
| 多步规划 | 回滚/重试模拟 | 技术可行，无产品 |
| 环境适应 | 场景生成/变换 | Runway GWM Robotics 初步 |

**核心论点**: 下一代 Agent 的核心差异化不是"更好的语言理解"，而是"更好的世界理解"。World Model 是 Agent 从"文本操作者"进化为"物理世界操作者"的关键基础设施。

### 9.4 与创意 AI / 内容创作的连接

| 方向 | 机会 | 参考 |
|------|------|------|
| **AI 游戏工作室** | World Model 直接生成可玩游戏 | Decart Oasis, Odyssey |
| **影视预可视化** | 导演用文本描述 → 实时生成拍摄环境 | Runway GWM Worlds |
| **交互式内容** | "可玩的视频" — 新媒体形态 | Odyssey "interactive video" |
| **虚拟空间设计** | 建筑/室内设计的 AI 化 | World Labs Marble |
| **教育仿真** | 历史场景/科学实验的沉浸式体验 | Genie 3 定位 |

---

## 十、关键结论

### 10.1 竞争格局

1. **基础设施层** 已被 NVIDIA (Cosmos + Omniverse + Isaac) 锁定 — 几乎不可能挑战
2. **Foundation Model 层** 是大资本的游戏 — AMI ($1B), World Labs ($1.2B), DeepMind (无限资源)
3. **应用层** 仍然大开 — 真正的产品化和商业化刚刚开始
4. **Agent + World Model** 的交叉点 — 几乎无人占据

### 10.2 时间窗口

- 2025: 技术验证年 — Genie 3, Cosmos, GAIA-3 证明 World Model 可行
- 2026: 产品化元年 — Marble, GWM-1 开始商业化；AMI Labs 成立
- 2027（预测）: 平台战争 — 谁能成为 World Model 的 "OpenAI"？

### 10.3 对 BENZEMA216 的启示

**最值得关注的交叉机会**: World Model 作为 Agent 的 "Physics Engine"

- 不需要自己训练 Foundation Model（用 Cosmos/Genie 3）
- 做的是 **中间层** — 将 World Model 能力封装为 Agent 可调用的服务
- 与你现有的 Agent 经济研究 + Agent Service 化方向天然契合
- 具体方向: Agent World Model API / Agent 仿真测试环境 / Agent 决策验证层

---

*本报告基于公开信息整理，数据截至 2026-04-03。*
