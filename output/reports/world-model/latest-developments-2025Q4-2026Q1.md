# World Models 最新进展：2025.10 - 2026.04

> 研究时间: 2026-04-06
> 核心判断：不是增量进步，而是范式转折点

---

## 一、最大结构性事件：LeCun 创办 AMI Labs

- 2025.12 离开 Meta FAIR（12 年），创办 AMI Labs
- 2026.03 完成 **$1.03B seed round**，估值 $3.5B — 欧洲最大种子轮
- 投资者：NVIDIA、Bezos Expeditions、Cathay Innovation
- CEO: Alexandre LeBrun（前 Nabla）; CSO: Saining Xie（前 NYU/Meta）
- AMI Labs CEO 预言："六个月内，每家公司都会自称 world model 公司来融资"

**信号意义**：LeCun 不再只是写论文呼吁 — 他在用十亿美元赌注说 "LLM 是死胡同，world models 才是 AGI 的路"

## 二、LeWM：15M 参数的技术突破（2026.03）

[arxiv.org/abs/2603.19312](https://arxiv.org/abs/2603.19312)

- 第一个从 raw pixels 端到端稳定训练的 JEPA
- SIGReg regularizer（Cramer-Wold 定理）解决 representation collapse
- 超参数 6 → **1 个**
- 每帧 = 单个 192-dim token（比 DINO-WM 少 **200x tokens**）
- Planning: **~1秒** vs DINO-WM 47秒（**48x 加速**）
- 仅 15M 参数，单 GPU 几小时训练

## 三、JEPA vs Autoregressive：走向融合

2026 年共识：**两者分层互补**

| JEPA | Autoregressive |
|------|----------------|
| 极高效率、latent reasoning | 视觉保真度、长程一致性 |
| Robotics planning、agent decision | 模拟环境、内容创作 |

新进展：
- **VL-JEPA**（2025.12）：JEPA 扩展到 vision-language，1.6B 参数匹配 InstructBLIP，推理 2.85x 快
- **seq-JEPA**（NeurIPS 2025）：JEPA + autoregressive memory 融合

## 四、Sora 之死：Video Gen ≠ World Model 定论（2026.03.24）

- OpenAI 关闭 Sora：巅峰 100 万用户暴跌至 <50 万，日运营成本 ~$100 万
- Disney 取消 $10 亿投资计划
- 研究证明视频生成模型无法仅从视觉数据发现基本物理定律
- **新共识**：衡量 world model 不再是"视频多真实"，而是 **"closed-loop decision making 中多有用"**
- 核心指标转向：controllability, action-conditioning, policy relevance

## 五、Genie 3：证实与局限

**证实**：
- 文本 → 可交互 photorealistic 3D 环境（24fps/720p）
- 2026.01 作为 Project Genie 向 Google AI Ultra 用户开放
- Waymo 基于 Genie 3 构建 Waymo World Model 用于生产仿真

**暴露的局限**：
- 物理不准确（雪地运动不符合物理规律）
- 一致性窗口仅数分钟
- **Reliability Paradox**：可用于证伪（Agent 在仿真中失败 = 不可靠），不可用于验证

## 六、V-JEPA 2 实际验证

- 预训练：100 万+ 小时视频
- Fine-tuning：仅 62 小时无标签机器人视频
- Zero-shot cross-lab transfer 到两个不同实验室的 Franka 机械臂
- 抓取成功率 **80%** vs Octo 的 **15%**
- Planning: **16 秒/action** vs Cosmos **4 分钟**

## 七、自动驾驶：第一个 Killer App

- **Waymo World Model**（2026.02）：Genie 3 改造，生成极端 corner case，周付费乘次 >50 万
- **Wayve GAIA-3**：150 亿参数，2026 London robotaxi 试运营（与 Uber 合作）
- **Tesla**：不用 world model 仿真，纯真实数据路线，2026.01 开始无人监督运营

## 八、中国生态

三大技术流派：
1. 自回归 Transformer（Genie 3 路线）
2. 自回归扩散 Transformer（已实现消费级 GPU 24fps）
3. 空间原生/3D 引导

关键玩家：
- **腾讯混元 HY-World 1.5** → WorldPlay 引擎 → 混元 3.0 将整合 world modeling
- **蚂蚁灵博 LingBot-VA**：首个"自回归视频-动作" world model，30-50 演示学新技能，RoboTwin 2.0 **>90%** 成功率
- **吉利 WAM**（World Action Model）：CES 2026，面向智驾

## 九、Scaling 辩论新共识："Densing Law"

- **[Densing Law](https://www.nature.com/articles/s42256-025-01137-0)**（Nature Machine Intelligence）：参数效率密度每 **3.5 个月翻倍**
- DeepSeek V3: 671B 总参数但仅 37B active，训练 $560 万
- **新共识**："Bigger is better" 不再是主旋律，**数据质量 + 架构效率 + 推理成本** 是核心竞争力

## 十、World Model x Agent

**已有进展**：
- World Labs [World API](https://www.worldlabs.ai/blog/announcing-the-world-api)（2026.01）— 第一个 WMaaS 接口
- NVIDIA Cosmos Policy — 直接连接 world model 到 robot policy
- Vision-Language-Action 模型（PaLM-E, RT-2, OpenVLA）

**仍然缺失**：
- Agent 所需的 persistent memory, safety validation, trusted identity 层
- Agent 和 World Model 的交互协议未标准化
- 50% 已部署 agent 完全孤立运行

## 十一、物理推理评估新标准

- **Physics-RW**：真实视频评估，覆盖热力学、电磁学、光学（不只是力学）
- **WoW-World-Eval**：人类偏好 + Inverse Dynamics 双视角 Turing Test
- **PhysBench**：VLM 物理理解全面评估
- SOTA 模型在区分运动轨迹时**接近随机准确率**

---

## 五个核心洞察

1. **Video Gen ≠ World Model 已成定论** — Sora 之死是标志，指标从"视觉逼真"转向"closed-loop 可用性"
2. **JEPA 从理论走向实践** — LeWM 15M 参数 + 48x 加速证明了 LeCun 的直觉，但 JEPA+AR 融合是大方向
3. **WMaaS 是新基础设施机会** — World API 是第一信号，但 agent 所需的 memory/safety/identity 层仍空白
4. **效率革命：Densing Law** — 参数效率每 3.5 月翻倍，"小而精"不是妥协而是趋势
5. **自动驾驶是第一个 killer app** — Waymo 每周 50 万付费乘次，world model 已在生产中运行
