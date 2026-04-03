# Harness Engineering → Creative CoWork 产品映射

> 从通用 Agent Harness 工程实践到创意场景产品设计：每个 Harness 模式在 Creative CoWork 中的具体落地。

## 映射总览

```
Harness Engineering（通用模式）       Creative CoWork（创意场景落地）
─────────────────────────────       ─────────────────────────────────
CLAUDE.md / AGENTS.md               → Skills 系统 SKILL.md
Feedforward controls                → /init 机制（三层推荐）
Feedback controls                   → Self-verification（质量检查）
Context firewall (sub-agents)       → 子 Agent 架构（素材/创意/执行）
Filesystem memory                   → 上下文容器
Harnessability                      → Studio 概念（场景化约束）
GAN-inspired eval                   → 发散/收敛模式
Safe autonomy                       → 权限设计（用户 approve）
Spec-driven development             → Brief 分析（需求 → spec）
```

## 详细映射

### 1. CLAUDE.md / AGENTS.md → Skills 系统 SKILL.md

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 项目/目录级配置文件，声明 Agent 行为规范 | SKILL.md 定义每个 Skill 的能力边界和行为规范 |
| 粒度 | CLAUDE.md（项目级）+ AGENTS.md（目录级） | SKILL.md 按 Skill 粒度，每个创意能力一份声明 |
| 内容 | 代码风格、禁止操作、工具偏好 | 输入格式、输出标准、可调用工具、质量基线 |
| 加载 | 启动时自动加载项目 + 当前目录配置 | /init 推荐后按需加载选中 Skill 的配置 |

**分析**：CLAUDE.md 是 Claude Code 的 feedforward harness 核心——在编码开始前就设定规范。Creative CoWork 将这一模式从"一个项目一份配置"扩展为"一个能力一份配置"，SKILL.md 让每个创意 Skill（视频生成、图片编辑、音频合成）都有独立的行为声明，实现更细粒度的 harness 控制。

### 2. Feedforward Controls → /init 机制（三层推荐）

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 执行前施加的约束和引导 | /init 在任务开始前推荐 Skills + 设定工作流 |
| 机制 | System Prompt、工具白名单、权限预设 | 三层推荐（L1 索引 → L2 详情 → L3 完整加载） |
| 目的 | 降低出错概率，缩小行为空间 | 帮助用户快速进入正确的创意工作流 |
| 时机 | Agent 启动阶段 | 用户发起新任务的第一步 |

**分析**：/init 是 Creative CoWork 最重要的 feedforward 控制。它不仅设定了 Agent 的工具和行为边界，还通过三层渐进式推荐（[[concepts/progressive-disclosure]]）引导用户选择合适的创意路径。这比编码场景的 feedforward 更复杂，因为创意任务的"正确路径"比编码任务更模糊，需要推荐而非硬编码。

### 3. Feedback Controls → Self-verification（视频生成质量检查）

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 执行中/后的检查和修正 | 视频生成后的多维质量评估 |
| 机制 | 跑测试、linter、trajectory critics | 画面一致性、运动流畅度、风格匹配度检查 |
| 触发 | 自动（每次生成后）或按需 | 每次视频生成完成后自动触发 |
| 修正 | 错误重试、参数调整 | 质量不达标时调整参数重新生成 |

**分析**：编码场景的 feedback 有天然优势——测试和 linter 是 Computational 的，结果确定性高。创意场景的 feedback 更多停留在 Inferential 领域（"这个视频好不好"需要 LLM 判断），这是 Creative CoWork 的核心技术挑战。解法方向：尽可能将质量检查推向 Computational（技术指标：分辨率、帧率、时长）+ Inferential 辅助（风格一致性、创意质量）。

### 4. Context Firewall (Sub-agents) → 子 Agent 架构

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | Sub-agent 隔离任务上下文，防止 noise 积累 | 创意子 Agent 各自独立上下文 |
| 结构 | Main + Explore（只读）+ Plan（规划） | Main + 素材探索 Agent + 创意规划 Agent + 执行 Agent |
| 隔离 | 子 Agent 只返回结构化结果 | 素材搜索结果、方案摘要回传主 Agent |
| 价值 | 主 Agent 上下文不被子任务细节污染 | 创意主循环保持清晰，不被素材检索细节干扰 |

**分析**：Context firewall 在创意场景尤为重要。素材探索可能涉及大量图片/视频的元数据和描述，如果这些全部进入主 Agent 上下文，会迅速耗尽 context budget。通过子 Agent 隔离，素材探索的过程（浏览了 100 张图片）和结果（推荐这 3 张）被分离，主 Agent 只接收精炼后的结果。

### 5. Filesystem Memory → 上下文容器

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 文件系统作为跨 session 记忆层 | 项目级上下文容器持久化创意状态 |
| 格式 | JSON > Markdown，结构化优先 | 项目配置 + 创意历史 + 风格偏好 |
| 范围 | progress files、scratchpad、CLAUDE.md | 品牌资产、历史生成记录、用户偏好 |
| 生命 | 跨 session 持久化 | 跨任务持久化，项目级别 |

**分析**：编码场景的 filesystem memory 主要记录项目规范和进度。创意场景扩展了记忆的维度——不仅记录"做了什么"，还记录"风格偏好"、"品牌规范"、"历史创意方向"。上下文容器是 filesystem memory 在创意场景的产品化包装，让用户（而非开发者）能够管理和复用创意记忆。

### 6. Harnessability → Studio 概念（场景化约束）

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 任务本身被 harness 约束的适合程度 | Studio 为特定创意场景预设 harness 配置 |
| 评估 | 有确定性验证手段的任务 harnessability 高 | 有明确输出规范的创意场景更适合 Agent |
| 应用 | 选择适合 Agent 化的任务 | 每个 Studio 针对特定场景优化 harness |
| 例子 | 编码（高）vs 开放式写作（低） | 电商素材 Studio（高）vs 自由创作 Studio（低） |

**分析**：Harnessability 概念帮助 Creative CoWork 做场景优先级决策。电商素材生成（有品牌规范、尺寸要求、文案模板）的 harnessability 远高于自由艺术创作。Studio 概念本质上是为不同 harnessability 等级的任务提供不同强度的 harness 配置。

### 7. GAN-inspired Eval → 发散/收敛模式

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | Generator + Evaluator 对抗式评估 | 发散 Agent 生成多个方案 + 收敛 Agent 评估筛选 |
| 生成 | Generator 生成候选方案 | 创意发散：多个方向、多个风格、多个变体 |
| 评估 | Evaluator 打分 + 反馈改进方向 | 创意收敛：根据 Brief 要求评估和排序 |
| 循环 | 迭代直到评估通过 | 发散/收敛交替直到用户满意 |

**分析**：GAN-inspired eval 在创意场景有天然的适配性。创意工作本身就是"发散 → 收敛"的循环。Harness engineering 的贡献是将这个直觉结构化——发散阶段用不同的 System Prompt 和温度参数鼓励多样性，收敛阶段用评估 rubric 和 Brief 对齐进行筛选。

### 8. Safe Autonomy → 权限设计（用户 approve 生成结果）

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 定义 Agent 自主行为的安全边界 | 哪些生成操作需要用户确认 |
| 层级 | 允许 / 需确认 / 禁止 | 草稿自动生成 / 最终稿需确认 / 发布需审批 |
| 成本 | 高成本操作需确认（文件删除、API 调用） | 高 token 消耗操作需确认（视频生成、批量渲染） |
| 可配 | 用户可调整自主级别 | 用户可设定"信任度"逐步放开自主权 |

**分析**：创意场景的 safe autonomy 有独特考量——错误生成的成本不是"代码 bug"而是"浪费的算力和时间"。一次视频生成可能消耗大量 GPU 时间和 token，因此权限设计需要考虑资源成本维度，这在编码场景中不太突出。

### 9. Spec-driven → Brief 分析（用户需求 → 结构化 spec）

| 维度 | Harness 原模式 | Creative CoWork 落地 |
|------|---------------|---------------------|
| 定义 | 用结构化规范驱动 Agent 行为 | 将模糊的创意需求转化为结构化 Brief |
| 输入 | 用户描述需求 / Issue / PRD | 用户描述创意想法 / 参考图 / 口头要求 |
| 转化 | 需求 → 技术 spec → 实现计划 | 创意想法 → 结构化 Brief → 执行方案 |
| 验收 | spec 中的 acceptance criteria | Brief 中的质量标准和风格要求 |

**分析**：Spec-driven development 在创意场景面临更大挑战——创意需求天然模糊（"我想要一个有感觉的视频"）。Brief 分析是 Creative CoWork 将 spec-driven 思想适配创意场景的关键创新：通过引导式对话将模糊需求逐步结构化，同时保留创意的弹性空间。

## 总结：Harness Engineering 对 Creative CoWork 的核心启示

1. **Feedforward 优先**：/init + SKILL.md + Studio 在任务开始前就设好 harness，减少运行时意外
2. **Computational 验证为锚**：尽可能将质量检查推向可计算的维度（技术指标），减少对 LLM 判断的依赖
3. **Context firewall 必备**：创意场景的上下文更容易爆炸（多模态素材），子 Agent 隔离是刚需
4. **Harnessability 决定优先级**：优先 Agent 化高 harnessability 的创意场景（电商素材 > 自由创作）
5. **记忆层产品化**：将 filesystem memory 从开发者工具升级为用户可管理的"上下文容器"

## 关联文章

- [[connections/claude-code-to-creative]] — Claude Code 架构迁移的详细对比
- [[maps/harness-engineering]] — Harness Engineering 全景地图
- [[maps/creative-cowork-product]] — Creative CoWork 产品全景

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
