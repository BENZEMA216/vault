# Creative CoWork Skills 架构启发

> 来源：基于 [[为 Claude 构建 Skills 完整指南]] 的分析与讨论
> 日期：2026-02-10

---

## 一、背景

Anthropic 发布了 Agent Skills 的完整构建指南，将 Skills 定义为"教 AI 处理特定任务的指令包"，并作为开放标准推广。Creative CoWork 的 Skills 概念（用户可自由加载的模块化能力）与之高度对齐，但创作场景有其独特需求。

本文记录将 Anthropic 通用方案适配到创作领域时的结构性思考。

---

## 二、核心论断

**Anthropic 的 Skills 是"指令驱动"的，创作场景的 Skills 是"素材驱动"的。**

Anthropic 方案的隐含假设：Skill 的核心价值在文本指令里。这对"帮我在 Linear 创建 Sprint"这类任务完全够用——本质上是 **文本输入 → API 调用 → 文本输出**。

但创作者的工作流不同。一个"千川短视频"Skill 中，纯文本指令可能只占 30%，**剩下 70% 是素材、Prompt、预设和参考物**。

---

## 三、Anthropic Skills 结构 vs 创作 Skills 结构

### Anthropic 通用结构

```
your-skill-name/
├── SKILL.md        ← 核心，一切围绕它
├── scripts/        ← 辅助，跑验证脚本
├── references/     ← 辅助，API 文档
└── assets/         ← 边缘，模板字体
```

### 创作 Skills 结构（提案）

```
千川短视频/
├── SKILL.md              # 工作流编排 + 触发条件
├── prompts/              # ← 新增：一等公民
│   ├── 画面生成.md       #   图片/视频 Prompt 模板库
│   ├── 配音方案.md       #   TTS 参数 + 文案模板
│   └── 配乐关键词.md     #   AI 音乐生成的 Prompt
├── presets/              # ← 新增：参数预设
│   ├── 抖音.yaml         #   平台规格（尺寸、时长、编码）
│   ├── 小红书.yaml
│   └── 视频号.yaml
├── examples/             # ← 升级：多模态参考
│   ├── 爆款案例A/
│   │   ├── 分镜.md
│   │   ├── 成片截图.png
│   │   └── 数据表现.md   #   播放量、完播率等
│   └── 爆款案例B/
├── assets/               # 品牌素材包
│   ├── 字体/
│   ├── logo/
│   └── 调色板.yaml
├── references/           # 领域知识
│   ├── 平台审核规则.md
│   └── 行业最佳实践.md
└── scripts/              # 自动化脚本
    └── 爆量检查.py
```

---

## 四、三个结构性差异详解

### 差异 1：`prompts/` 应该是一等公民

Anthropic 没有这个概念，因为它的场景不涉及"AI 生图/生视频"。但对创作者来说，**Prompt 就是核心生产资料**——相当于程序员的代码。

Prompt 还需要版本管理。同一个画面描述，不同模型的写法完全不同：

```yaml
# prompts/画面生成.md

## 产品展示-近景
- flux: "product close-up, studio lighting, white background, 8k..."
- midjourney: "/imagine product photography, macro lens, --ar 9:16 --v 6..."
- sd3: "best quality, product photo, commercial photography..."
```

**关键点**：Prompt 模板需要按模型版本维护，同一语义意图对应多个模型的不同表达。

### 差异 2：`presets/` — 平台规格和参数预设

Anthropic 的 Skills 没有"目标平台"的概念。但创作内容**必然有投放目标**：

```yaml
# presets/抖音.yaml
video:
  aspect_ratio: "9:16"
  resolution: "1080x1920"
  duration: { min: 7, max: 60, sweet_spot: 15 }
  fps: 30
  codec: h264
audio:
  sample_rate: 44100
  loudness: -14 LUFS
content:
  hook_time: 3s        # 前3秒必须有钩子
  cta_position: last_5s # 行动号召放最后5秒
```

**关键点**：同一个 Skill 切换 preset 就能适配不同平台，工作流逻辑不变。这是创作场景特有的"一鱼多吃"需求。

### 差异 3：`examples/` — 从文本示例升级为多模态基准

Anthropic 的 examples 是纯文本："用户说 X → 执行 Y → 结果 Z"。

创作场景的 examples 必须是**可感知的**——你没法用文字描述"什么叫好的画面构图"。需要：

- 参考图片（风格基准）
- 成片截屏（质量基准）
- 短视频片段（节奏基准）
- 数据表现（效果基准——这条视频为什么算爆款）

**关键点**：多模态参考物对 AI 理解"好的标准"至关重要，尤其是当 Agent 需要**自检输出质量**的时候。

---

## 五、渐进式加载的适配

Anthropic 的三级渐进式披露是其最精巧的设计，创作 Skills 可以这样映射：

| 层级 | 加载时机 | Anthropic 内容 | 创作 Skills 内容 |
|------|---------|---------------|-----------------|
| L1 | 始终加载（判断是否激活） | YAML frontmatter | frontmatter：名称、描述、触发条件、支持的平台列表 |
| L2 | Skill 激活时 | SKILL.md body | SKILL.md body（工作流步骤）+ 对应平台的 preset |
| L3 | 执行具体步骤时 | references/ 文档 | 对应环节的 prompts、examples、assets（按需拉取） |

**核心区别在 L3**：Anthropic 的 L3 可能就是读一个 API 文档。创作 Skill 的 L3 是在"生成分镜第 3 帧"时才拉取"产品近景的 Prompt 模板 + 参考图"。L3 更重、更碎片化、更多模态。

---

## 六、五种设计模式在创作场景的映射

Anthropic 提出的五种模式直接映射到创作工作流：

| Anthropic 模式 | 创作场景举例 |
|----------------|------------|
| 顺序工作流编排 | 文案 → 分镜 → 配音 → 配乐 → 合成 |
| 多服务协调 | 同时调用 TTS + AI 音乐 + 图片生成 + 视频合成 |
| 迭代优化 | 生成初稿 → AI 自检 → 修改 → 再检 → 直到达标 |
| 上下文感知选择 | 根据内容类型自动选模型（写实用 Flux、二次元用 Niji） |
| 领域专业智能 | 嵌入爆量检查清单、平台审核规则、行业最佳实践 |

---

## 七、开放架构决策：自包含 vs 引用

### 问题

Skill 应该自包含所有素材，还是只引用上下文容器里的素材？

### 分析

| 方案 | 优点 | 缺点 |
|------|------|------|
| 自包含（Anthropic 方式） | 可独立分发、开箱即用 | 素材体积大、无法个性化 |
| 引用上下文容器 | 轻量、个性化 | 依赖项目上下文、不可独立分发 |

### 倾向：混合模式

- **Skill 自带**：通用模板和 Prompt（可分发的方法论部分）
- **执行时优先**：使用项目上下文容器中的品牌素材（个性化部分）

这样一个"千川短视频"Skill 可以被分享给所有超创，但每个人跑出来的结果用的是自己的品牌资产。

```
执行时资源解析优先级：
1. 项目上下文容器中的素材（品牌 logo、专属字体、调色板）
2. Skill 自带的通用素材（兜底模板）
3. 系统默认素材（最终兜底）
```

---

## 八、与 Creative CoWork 其他概念的关联

| 概念 | 与 Skills 的关系 |
|------|-----------------|
| **上下文容器** | Skills 执行时从上下文容器拉取项目素材，L3 加载的来源之一 |
| **AGENT** | Agent 读取 L1 判断激活哪些 Skills，加载 L2 获取工作流，按需拉取 L3 |
| **Studio** | Studio 是场景化工作空间模板，可预装一组 Skills + 对应 presets |
| **GENUI** | 根据激活的 Skills 动态生成界面（Anthropic 方案完全没有这个维度） |
| **SANDBOX** | 对应 Anthropic 的 MCP——执行 Skills 调用的底层能力 |

---

## 九、待深挖问题

1. **Prompt 模板的版本管理机制**：当 AI 模型更新时，Prompt 可能失效，如何做到平滑迁移？
2. **多模态 examples 的存储和加载**：图片、视频参考物的体积远大于文本，L3 加载策略需要更精细的设计。
3. **Skill 的评估标准**：Anthropic 用"触发率 90%"、"0 失败 API 调用"来衡量。创作 Skill 如何衡量——"画面质量评分"？"爆量概率"？
4. **GENUI 与 Skills 的联动**：Skills 是否应该包含 UI 描述？还是 GENUI 完全根据 Skills 的元信息自动推断？
5. **Skills 市场的可行性**：超创是否愿意为他人的创作方法论付费？MCN 是否愿意为标准化 Skills 付费？
