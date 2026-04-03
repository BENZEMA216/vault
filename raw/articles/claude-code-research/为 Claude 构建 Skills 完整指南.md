# 为 Claude 构建 Skills 完整指南

> 原文：[The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)
> 来源：Anthropic 官方
> 翻译日期：2026-02-10

---

## 目录

- [引言](#引言)
- [第一章：基础](#第一章基础)
- [第二章：规划与设计](#第二章规划与设计)
- [第三章：测试与迭代](#第三章测试与迭代)
- [第四章：分发与共享](#第四章分发与共享)
- [第五章：模式与排障](#第五章模式与排障)
- [第六章：资源与参考](#第六章资源与参考)
- [附录 A：快速检查清单](#附录-a快速检查清单)
- [附录 B：YAML Frontmatter 参考](#附录-byaml-frontmatter-参考)
- [附录 C：完整 Skill 示例](#附录-c完整-skill-示例)

---

## 引言

Skill 是一组指令——打包为一个简单的文件夹——教 Claude 如何处理特定任务或工作流。Skill 是为你的特定需求定制 Claude 的最强大方式之一。与其在每次对话中重新解释你的偏好、流程和领域专业知识，Skill 让你教一次 Claude，之后每次都能受益。

Skill 在你有可重复工作流时特别强大：根据规格说明生成前端设计、使用一致的方法论进行研究、创建符合团队风格指南的文档，或编排多步骤流程。它们与 Claude 的内置能力（如代码执行和文档创建）配合良好。对于正在构建 MCP 集成的开发者，Skill 增加了另一个强大的层——帮助将原始工具访问能力转化为可靠、优化的工作流。

本指南涵盖了构建有效 Skill 所需的一切——从规划和结构到测试和分发。无论你是为自己、团队还是社区构建 Skill，你都能在全文中找到实用模式和真实世界的案例。

**你将学到：**

- Skill 结构的技术要求和最佳实践
- 独立 Skill 和 MCP 增强工作流的模式
- 我们在不同用例中观察到的有效模式
- 如何测试、迭代和分发你的 Skill

**适用人群：**

- 希望 Claude 一致地遵循特定工作流的开发者
- 希望 Claude 遵循特定工作流的高级用户
- 希望在组织内标准化 Claude 使用方式的团队

### 本指南的两条路径

构建独立 Skill？重点关注**基础**、**规划与设计**以及 Category 1-2。增强 MCP 集成？**"Skills + MCP"** 部分和 Category 3 适合你。两条路径共享相同的技术要求，你可以选择与自己用例相关的部分。

**你能从本指南中获得什么：** 读完后，你将能在一次工作中构建一个功能完整的 Skill。使用 skill-creator，预计大约 15-30 分钟即可构建和测试你的第一个可用 Skill。

让我们开始吧。

---

## 第一章：基础

### 什么是 Skill？

一个 Skill 是一个文件夹，包含：

- **SKILL.md**（必需）：Markdown 格式的指令，带 YAML frontmatter
- **scripts/**（可选）：可执行代码（Python、Bash 等）
- **references/**（可选）：按需加载的文档
- **assets/**（可选）：输出中使用的模板、字体、图标

### 核心设计原则

#### 渐进式披露（Progressive Disclosure）

Skill 使用三级系统：

- **第一级（YAML frontmatter）**：始终加载到 Claude 的系统提示中。提供刚好足够的信息让 Claude 知道何时应该使用每个 Skill，而不会将全部内容加载到上下文中。
- **第二级（SKILL.md 正文）**：当 Claude 认为该 Skill 与当前任务相关时加载。包含完整的指令和指导。
- **第三级（链接文件）**：Skill 目录中捆绑的附加文件，Claude 仅在需要时才选择导航和发现。

这种渐进式披露在保持专业能力的同时最小化了 token 使用量。

#### 可组合性（Composability）

Claude 可以同时加载多个 Skill。你的 Skill 应该能与其他 Skill 良好协作，而不是假设自己是唯一可用的能力。

#### 可移植性（Portability）

Skill 在 Claude.ai、Claude Code 和 API 上表现一致。创建一次，即可在所有平台上使用，无需修改——前提是环境支持 Skill 所需的依赖。

### 面向 MCP 构建者：Skills + 连接器

> 💡 构建不依赖 MCP 的独立 Skill？跳到[规划与设计](#第二章规划与设计)——你随时可以回来。

如果你已经有一个可用的 MCP 服务器，你已经完成了最困难的部分。Skill 是其上的知识层——捕获你已经知道的工作流和最佳实践，让 Claude 能一致地应用它们。

#### 厨房类比

MCP 提供**专业厨房**：访问工具、食材和设备的能力。

Skill 提供**菜谱**：关于如何创造有价值成果的分步指令。

两者结合，让用户能完成复杂任务，而无需自己弄清每一个步骤。

#### 它们如何协作

| MCP（连接性） | Skills（知识） |
|---|---|
| 将 Claude 连接到你的服务（Notion、Asana、Linear 等） | 教 Claude 如何有效使用你的服务 |
| 提供实时数据访问和工具调用 | 捕获工作流和最佳实践 |
| Claude **能做**什么 | Claude **该怎么做** |

#### 这对你的 MCP 用户为什么重要

**没有 Skill 时：**

- 用户连接了你的 MCP 但不知道下一步做什么
- 大量 "如何用你的集成做 X" 的支持工单
- 每次对话从零开始
- 结果不一致，因为用户每次提示方式不同
- 用户怪你的连接器，而真正的问题是缺乏工作流指导

**有 Skill 时：**

- 预构建的工作流在需要时自动激活
- 一致、可靠的工具使用
- 最佳实践嵌入每次交互
- 降低你的集成的学习曲线

---

## 第二章：规划与设计

### 从用例出发

在编写任何代码之前，确定 2-3 个你的 Skill 应该实现的具体用例。

好的用例定义：

> **用例：项目 Sprint 规划**
>
> **触发条件：** 用户说"帮我规划这个 sprint"或"创建 sprint 任务"
>
> **步骤：**
> 1. 通过 MCP 从 Linear 获取当前项目状态
> 2. 分析团队速率和产能
> 3. 建议任务优先级排序
> 4. 在 Linear 中创建任务，带正确的标签和估时
>
> **结果：** 完整规划的 sprint，任务已创建

问自己：
- 用户想要完成什么？
- 这需要什么多步骤工作流？
- 需要哪些工具（内置的还是 MCP）？
- 应该嵌入什么领域知识或最佳实践？

### 常见 Skill 用例分类

在 Anthropic，我们观察到三种常见用例：

#### Category 1：文档与资产创作

**用途：** 创建一致的、高质量的输出物，包括文档、演示文稿、应用、设计、代码等。

**真实案例：** `frontend-design` skill（另见 docx、pptx、xlsx 和 ppt 的 skill）

> "创建独特的、生产级前端界面，具有高设计质量。用于构建 Web 组件、页面、artifact、海报或应用程序。"

**关键技术：**
- 嵌入式风格指南和品牌标准
- 用于一致输出的模板结构
- 最终完稿前的质量检查清单
- 不需要外部工具——使用 Claude 的内置能力

#### Category 2：工作流自动化

**用途：** 受益于一致方法论的多步骤流程，包括跨多个 MCP 服务器的协调。

**真实案例：** `skill-creator` skill

> "创建新 Skill 的交互式向导。引导用户完成用例定义、frontmatter 生成、指令编写和验证。"

**关键技术：**
- 带验证门的分步工作流
- 常见结构的模板
- 内置审查和改进建议
- 迭代优化循环

#### Category 3：MCP 增强

**用途：** 为 MCP 服务器提供的工具访问增加工作流指导。

**真实案例：** `sentry-code-review` skill（来自 Sentry）

> "使用 Sentry 的错误监控数据（通过其 MCP 服务器）自动分析和修复 GitHub Pull Request 中检测到的 bug。"

**关键技术：**
- 按顺序协调多个 MCP 调用
- 嵌入领域专业知识
- 提供用户原本需要手动指定的上下文
- 常见 MCP 问题的错误处理

### 定义成功标准

如何知道你的 Skill 在正常工作？

这些是理想化的目标——粗略的基准而非精确阈值。追求严谨，但接受会有一定程度的基于感觉的评估。我们正在积极开发更健壮的测量指导和工具。

**定量指标：**

- **Skill 在 90% 的相关查询上触发**
  - 如何测量：运行 10-20 个应该触发你 Skill 的测试查询。跟踪它自动加载 vs 需要显式调用的次数。
- **在 X 次工具调用内完成工作流**
  - 如何测量：在有/无 Skill 的情况下比较相同任务。统计工具调用次数和总 token 消耗。
- **每个工作流 0 次失败 API 调用**
  - 如何测量：测试运行期间监控 MCP 服务器日志。跟踪重试率和错误码。

**定性指标：**

- **用户不需要提示 Claude 下一步该做什么**
  - 如何评估：测试期间记录你需要重新引导或澄清的频率。向 beta 用户征求反馈。
- **工作流无需用户纠正即可完成**
  - 如何评估：运行相同请求 3-5 次。比较输出的结构一致性和质量。
- **跨会话结果一致**
  - 如何评估：新用户能否在最少指导下第一次就完成任务？

### 技术要求

#### 文件结构

```
your-skill-name/
├── SKILL.md           # 必需 - 主 skill 文件
├── scripts/           # 可选 - 可执行代码
│   ├── process_data.py
│   └── validate.sh
├── references/        # 可选 - 文档
│   ├── api-guide.md
│   └── examples/
└── assets/            # 可选 - 模板等
    └── report-template.md
```

#### 关键规则

**SKILL.md 命名：**
- 必须完全是 `SKILL.md`（大小写敏感）
- 不接受变体（SKILL.MD、skill.md 等）

**Skill 文件夹命名：**
- 使用 kebab-case：`notion-project-setup` ✅
- 不能有空格：`Notion Project Setup` ❌
- 不能有下划线：`notion_project_setup` ❌
- 不能有大写：`NotionProjectSetup` ❌

**不要包含 README.md：**
- 不要在 skill 文件夹内包含 README.md
- 所有文档放在 SKILL.md 或 references/ 中
- 注意：通过 GitHub 分发时，你仍然需要一个仓库级别的 README 给人类用户——参见[分发与共享](#第四章分发与共享)。

#### YAML Frontmatter：最重要的部分

YAML frontmatter 是 Claude 决定是否加载你的 Skill 的依据。要做好这一部分。

**最小必需格式：**

```yaml
---
name: your-skill-name
description: What it does. Use when user asks to [specific phrases].
---
```

这就是你开始所需的全部。

#### 字段要求

**name（必需）：**
- 仅 kebab-case
- 不能有空格或大写
- 应与文件夹名匹配

**description（必需）：**
- 必须同时包含：
  - Skill 做什么
  - 何时使用（触发条件）
- 1024 字符以内
- 不能有 XML 标签（< 或 >）
- 包含用户可能会说的具体任务
- 如果相关，提及文件类型

**license（可选）：**
- 开源 Skill 时使用
- 常见：MIT、Apache-2.0

**compatibility（可选）：**
- 1-500 字符
- 指明环境要求：如目标产品、所需系统包、网络访问需求等

**metadata（可选）：**
- 任意自定义键值对
- 建议包含：author、version、mcp-server
- 示例：

```yaml
metadata:
  author: ProjectHub
  version: 1.0.0
  mcp-server: projecthub
```

#### 安全限制

**Frontmatter 中禁止的内容：**
- XML 尖括号（< >）
- 名称中包含 "claude" 或 "anthropic"（保留字）

原因：Frontmatter 出现在 Claude 的系统提示中。恶意内容可能注入指令。

### 编写有效的 Skill

#### Description 字段

引用 Anthropic 工程博客的话："这些元数据……提供刚好足够的信息让 Claude 知道何时应该使用每个 Skill，而不会将全部内容加载到上下文中。"这是渐进式披露的第一级。

**结构：** `[做什么] + [何时使用] + [关键能力]`

**好的 description 示例：**

```yaml
# 好 - 具体且可操作
description: 分析 Figma 设计文件并生成开发交接文档。当用户上传 .fig 文件、
  要求 "design specs"、"component documentation" 或 "design-to-code handoff" 时使用。

# 好 - 包含触发短语
description: 管理 Linear 项目工作流，包括 sprint 规划、任务创建和状态跟踪。
  当用户提到 "sprint"、"Linear tasks"、"project planning" 或要求 "create tickets" 时使用。

# 好 - 清晰的价值主张
description: PayFlow 的端到端客户入驻工作流。处理账户创建、支付设置和订阅管理。
  当用户说 "onboard new customer"、"set up subscription" 或 "create PayFlow account" 时使用。
```

**坏的 description 示例：**

```yaml
# 太模糊
description: Helps with projects.

# 缺少触发条件
description: Creates sophisticated multi-page documentation systems.

# 太技术化，没有用户触发条件
description: Implements the Project entity model with hierarchical relationships.
```

#### 编写主体指令

在 frontmatter 之后，用 Markdown 编写实际指令。

**推荐结构：**

```markdown
---
name: your-skill
description: [...]
---

# Your Skill Name

## Instructions

### Step 1: [第一个主要步骤]
清楚解释会发生什么。

```bash
python scripts/fetch_data.py --project-id PROJECT_ID
预期输出：[描述成功的样子]
```

（根据需要添加更多步骤）

## Examples

Example 1: [常见场景]
用户说："Set up a new marketing campaign"

操作：
1. 通过 MCP 获取现有活动
2. 使用提供的参数创建新活动

结果：活动已创建，带确认链接

（根据需要添加更多示例）

## Troubleshooting

错误：[常见错误消息]
原因：[为什么会发生]
解决方案：[如何修复]

（根据需要添加更多错误案例）
```

#### 指令编写最佳实践

**要具体、可操作**

✅ 好的：
```
运行 `python scripts/validate.py --input {filename}` 来检查数据格式。
如果验证失败，常见问题包括：
- 缺少必填字段（添加到 CSV 中）
- 无效的日期格式（使用 YYYY-MM-DD）
```

❌ 坏的：
```
在继续之前验证数据。
```

**清晰引用捆绑资源**

```
在编写查询之前，查阅 `references/api-patterns.md` 了解：
- 速率限制指导
- 分页模式
- 错误码和处理
```

**使用渐进式披露**

保持 SKILL.md 专注于核心指令。将详细文档移到 `references/` 并链接过去。（参见[核心设计原则](#核心设计原则)了解三级系统如何工作。）

**包含错误处理**

```markdown
## Common Issues

### MCP Connection Failed
如果你看到 "Connection refused"：
1. 验证 MCP 服务器是否运行：检查 Settings > Extensions
2. 确认 API key 有效
3. 尝试重新连接：Settings > Extensions > [Your Service] > Reconnect
```

---

## 第三章：测试与迭代

Skill 可以根据需要在不同严谨度级别上测试：

- **在 Claude.ai 手动测试** - 直接运行查询并观察行为。快速迭代，无需设置。
- **在 Claude Code 中脚本化测试** - 自动化测试用例，可在变更后重复验证。
- **通过 Skills API 编程测试** - 构建评估套件，系统化地运行预定义测试集。

选择与你的质量要求和 Skill 可见度匹配的方法。小团队内部使用的 Skill 与部署给数千企业用户的 Skill 有不同的测试需求。

> **专业提示：先在单个任务上迭代，再扩展**
>
> 我们发现最有效的 Skill 创建者会在单个有挑战性的任务上迭代，直到 Claude 成功，然后将获胜的方法提取为 Skill。这利用了 Claude 的上下文内学习能力，比广泛测试提供更快的信号。一旦你有了可用的基础，再扩展到多个测试用例以获得覆盖率。

### 推荐测试方法

基于早期经验，有效的 Skill 测试通常覆盖三个领域：

#### 1. 触发测试

**目标：** 确保你的 Skill 在正确的时机加载。

**测试用例：**
- ✅ 在明显的任务上触发
- ✅ 在改述的请求上触发
- ❌ 不在无关话题上触发

**示例测试套件：**

应该触发：
- "Help me set up a new ProjectHub workspace"
- "I need to create a project in ProjectHub"
- "Initialize a ProjectHub project for Q4 planning"

不应该触发：
- "What's the weather in San Francisco?"
- "Help me write Python code"
- "Create a spreadsheet"（除非 ProjectHub skill 处理电子表格）

#### 2. 功能测试

**目标：** 验证 Skill 产出正确的输出。

**测试用例：**
- 生成有效的输出
- API 调用成功
- 错误处理正常工作
- 边缘情况被覆盖

**示例：**

```
测试：创建包含 5 个任务的项目
Given: 项目名 "Q4 Planning"，5 个任务描述
When: Skill 执行工作流
Then:
  - 项目在 ProjectHub 中创建
  - 5 个任务以正确属性创建
  - 所有任务链接到项目
  - 无 API 错误
```

#### 3. 性能对比

**目标：** 证明 Skill 比基线提升了结果。

使用[定义成功标准](#定义成功标准)中的指标。对比可能如下：

**基线对比：**

| | 无 Skill | 有 Skill |
|---|---|---|
| 交互方式 | 用户每次提供指令 | 自动执行工作流 |
| 来回消息数 | 15 轮 | 仅 2 个澄清问题 |
| 失败 API 调用 | 3 次需要重试 | 0 次 |
| Token 消耗 | 12,000 | 6,000 |

### 使用 skill-creator skill

`skill-creator` skill 在 Claude.ai 的插件目录中可用，也可下载用于 Claude Code——它可以帮助你构建和迭代 Skill。如果你有 MCP 服务器并知道你的 2-3 个核心工作流，你可以在一次工作中构建和测试一个功能完整的 Skill——通常只需 15-30 分钟。

**创建 Skill：**
- 从自然语言描述生成 Skill
- 产出格式正确的 SKILL.md 和 frontmatter
- 建议触发短语和结构

**审查 Skill：**
- 标记常见问题（模糊的 description、缺少触发条件、结构问题）
- 识别潜在的过度/不足触发风险
- 根据 Skill 的既定目的建议测试用例

**迭代改进：**
- 使用你的 Skill 并遇到边缘情况或失败后，将这些案例带回 skill-creator
- 示例："Use the issues & solution identified in this chat to improve how the skill handles [specific edge case]"

**使用方式：**

```
"Use the skill-creator skill to help me build a skill for [your use case]"
```

注意：skill-creator 帮助你设计和优化 Skill，但不执行自动化测试套件或产出定量评估结果。

### 基于反馈迭代

Skill 是活的文档。计划基于以下反馈进行迭代：

**触发不足的信号：**
- Skill 该加载时没有加载
- 用户手动启用它
- 关于何时使用它的支持问题

→ **解决方案：** 给 description 增加更多细节和细微差异——这可能包括关键词，特别是技术术语

**触发过度的信号：**
- Skill 在不相关的查询上加载
- 用户禁用它
- 对目的感到困惑

→ **解决方案：** 添加负向触发条件，更具体化

**执行问题：**
- 结果不一致
- API 调用失败
- 需要用户纠正

→ **解决方案：** 改进指令，增加错误处理

---

## 第四章：分发与共享

Skill 使你的 MCP 集成更完整。当用户比较连接器时，有 Skill 的连接器提供更快的价值路径，给你相对于仅有 MCP 的替代品的竞争优势。

### 当前分发模型（2026 年 1 月）

**个人用户获取 Skill 的方式：**
1. 下载 skill 文件夹
2. 打包文件夹为 zip（如需要）
3. 上传到 Claude.ai：Settings > Capabilities > Skills
4. 或放置在 Claude Code 的 skills 目录中

**组织级 Skill：**
- 管理员可以在工作区范围内部署 Skill（2025 年 12 月 18 日上线）
- 自动更新
- 集中管理

### 通过 API 使用 Skill

对于编程用例——如构建应用、agent 或利用 Skill 的自动化工作流——API 提供对 Skill 管理和执行的直接控制。

**关键能力：**
- `/v1/skills` 端点用于列出和管理 Skill
- 通过 `container.skills` 参数将 Skill 添加到 Messages API 请求
- 通过 Claude Console 进行版本控制和管理
- 与 Claude Agent SDK 配合构建自定义 agent

**何时通过 API vs Claude.ai 使用 Skill：**

| 用例 | 最佳平台 |
|---|---|
| 终端用户直接与 Skill 交互 | Claude.ai / Claude Code |
| 开发期间手动测试和迭代 | Claude.ai / Claude Code |
| 个人的临时工作流 | Claude.ai / Claude Code |
| 应用程序编程使用 Skill | API |
| 规模化生产部署 | API |
| 自动化流水线和 agent 系统 | API |

注意：API 中的 Skill 需要 Code Execution Tool beta，它提供 Skill 运行所需的安全环境。

相关实现细节请参见：
- Skills API 快速入门
- 创建自定义 Skill
- Agent SDK 中的 Skill

### 开放标准

我们已将 Agent Skills 发布为开放标准。像 MCP 一样，我们相信 Skill 应该跨工具和平台可移植——无论你使用 Claude 还是其他 AI 平台，同一个 Skill 都应该能工作。也就是说，某些 Skill 被设计为充分利用特定平台的能力；作者可以在 Skill 的 compatibility 字段中注明这一点。我们一直与生态系统成员合作制定标准，早期采用令人兴奋。

### 推荐做法

从在 GitHub 上托管你的 Skill 开始——使用公开仓库、清晰的 README（给人类访客——这与你的 skill 文件夹分开，skill 文件夹不应包含 README.md）和带截图的使用示例。然后在你的 MCP 文档中添加一个部分，链接到 Skill，解释为什么两者结合使用更有价值，并提供快速入门指南。

**1. 在 GitHub 上托管**
- 开源 Skill 用公开仓库
- 清晰的 README 和安装说明
- 使用示例和截图

**2. 在你的 MCP 仓库中记录**
- 从 MCP 文档链接到 Skill
- 解释两者结合使用的价值
- 提供快速入门指南

**3. 创建安装指南**

```markdown
## 安装 [Your Service] skill
1. 下载 skill：
   - 克隆仓库：`git clone https://github.com/yourcompany/skills`
   - 或从 Releases 下载 ZIP
2. 安装到 Claude：
   - 打开 Claude.ai > Settings > Skills
   - 点击 "Upload skill"
   - 选择 skill 文件夹（已打包为 zip）
3. 启用 skill：
   - 开启 [Your Service] skill
   - 确保你的 MCP 服务器已连接
4. 测试：
   - 问 Claude："Set up a new project in [Your Service]"
```

### 定位你的 Skill

你如何描述你的 Skill 决定了用户是否理解其价值并实际尝试。在编写关于你的 Skill 的内容时——无论是 README、文档还是营销材料——记住这些原则。

**聚焦结果，而非特性：**

✅ 好的：
> "ProjectHub skill 让团队在几秒内搭建完整项目工作区——包括页面、数据库和模板——而不是花 30 分钟手动设置。"

❌ 坏的：
> "ProjectHub skill 是一个包含 YAML frontmatter 和 Markdown 指令的文件夹，调用我们的 MCP 服务器工具。"

**强调 MCP + Skills 的故事：**

> "我们的 MCP 服务器让 Claude 访问你的 Linear 项目。我们的 Skill 教 Claude 你团队的 sprint 规划工作流。两者结合，实现 AI 驱动的项目管理。"

---

## 第五章：模式与排障

这些模式来自早期采用者和内部团队创建的 Skill。它们代表我们观察到的有效通用方法，而非规定性模板。

### 选择你的方法：问题优先 vs. 工具优先

想象一下 Home Depot。你可能带着问题走进去——"我需要修理厨柜"——员工给你指向正确的工具。或者你可能挑了一把新电钻，问如何用它来完成你的具体工作。

Skill 的工作方式相同：

- **问题优先**："我需要搭建项目工作区" → 你的 Skill 编排正确的 MCP 调用序列。用户描述结果；Skill 处理工具。
- **工具优先**："我连了 Notion MCP" → 你的 Skill 教 Claude 最优工作流和最佳实践。用户有工具访问；Skill 提供专业知识。

大多数 Skill 倾向某个方向。知道哪种框架适合你的用例有助于选择下面正确的模式。

### Pattern 1：顺序工作流编排

**适用场景：** 你的用户需要按特定顺序执行的多步骤流程。

**示例结构：**

```markdown
## Workflow: Onboard New Customer

### Step 1: Create Account
调用 MCP 工具：`create_customer`
参数：name, email, company

### Step 2: Setup Payment
调用 MCP 工具：`setup_payment_method`
等待：支付方式验证

### Step 3: Create Subscription
调用 MCP 工具：`create_subscription`
参数：plan_id, customer_id（来自 Step 1）

### Step 4: Send Welcome Email
调用 MCP 工具：`send_email`
模板：welcome_email_template
```

**关键技术：**
- 显式步骤排序
- 步骤间依赖
- 每阶段验证
- 失败回滚指令

### Pattern 2：多 MCP 协调

**适用场景：** 工作流跨越多个服务。

**示例：设计到开发交接**

```markdown
### Phase 1: Design Export（Figma MCP）
1. 从 Figma 导出设计资产
2. 生成设计规格说明
3. 创建资产清单

### Phase 2: Asset Storage（Drive MCP）
1. 在 Drive 中创建项目文件夹
2. 上传所有资产
3. 生成可分享链接

### Phase 3: Task Creation（Linear MCP）
1. 创建开发任务
2. 将资产链接附加到任务
3. 分配给工程团队

### Phase 4: Notification（Slack MCP）
1. 将交接摘要发布到 #engineering
2. 包含资产链接和任务引用
```

**关键技术：**
- 清晰的阶段分离
- MCP 之间的数据传递
- 进入下一阶段前验证
- 集中式错误处理

### Pattern 3：迭代优化

**适用场景：** 输出质量随迭代而提升。

**示例：报告生成**

```markdown
## Iterative Report Creation

### Initial Draft
1. 通过 MCP 获取数据
2. 生成初稿报告
3. 保存到临时文件

### Quality Check
1. 运行验证脚本：`scripts/check_report.py`
2. 识别问题：
   - 缺失的章节
   - 不一致的格式
   - 数据验证错误

### Refinement Loop
1. 解决每个已识别的问题
2. 重新生成受影响的部分
3. 重新验证
4. 重复直到质量阈值达标

### Finalization
1. 应用最终格式
2. 生成摘要
3. 保存最终版本
```

**关键技术：**
- 显式质量标准
- 迭代改进
- 验证脚本
- 知道何时停止迭代

### Pattern 4：上下文感知工具选择

**适用场景：** 相同的目标，根据上下文使用不同工具。

**示例：文件存储**

```markdown
## Smart File Storage

### Decision Tree
1. 检查文件类型和大小
2. 确定最佳存储位置：
   - 大文件（>10MB）：使用云存储 MCP
   - 协作文档：使用 Notion/Docs MCP
   - 代码文件：使用 GitHub MCP
   - 临时文件：使用本地存储

### Execute Storage
根据决策：
- 调用相应的 MCP 工具
- 应用特定服务的元数据
- 生成访问链接

### Provide Context to User
解释为什么选择了该存储方式
```

**关键技术：**
- 清晰的决策标准
- 回退选项
- 对选择的透明解释

### Pattern 5：领域专业智能

**适用场景：** 你的 Skill 添加了超越工具访问的专业知识。

**示例：金融合规**

```markdown
## Payment Processing with Compliance

### Before Processing (Compliance Check)
1. 通过 MCP 获取交易详情
2. 应用合规规则：
   - 检查制裁名单
   - 验证管辖权许可
   - 评估风险等级
3. 记录合规决策

### Processing
IF 合规通过：
- 调用支付处理 MCP 工具
- 应用适当的欺诈检查
- 处理交易
ELSE：
- 标记以供审查
- 创建合规案例

### Audit Trail
- 记录所有合规检查
- 记录处理决策
- 生成审计报告
```

**关键技术：**
- 领域专业知识嵌入逻辑
- 行动前合规检查
- 全面记录
- 清晰治理

### 排障

#### Skill 无法上传

**错误：** "Could not find SKILL.md in uploaded folder"

**原因：** 文件名不完全是 SKILL.md

**解决方案：**
- 重命名为 SKILL.md（大小写敏感）
- 用 `ls -la` 验证应显示 SKILL.md

**错误：** "Invalid frontmatter"

**原因：** YAML 格式问题

常见错误：

```yaml
# 错误 - 缺少分隔符
name: my-skill
description: Does things

# 错误 - 引号未关闭
name: my-skill
description: "Does things

# 正确
---
name: my-skill
description: Does things
---
```

**错误：** "Invalid skill name"

**原因：** 名称有空格或大写

```yaml
# 错误
name: My Cool Skill

# 正确
name: my-cool-skill
```

#### Skill 不触发

**症状：** Skill 从不自动加载

**修复：** 修改你的 description 字段。参见 [description 字段的写法](#description-字段)获取好/坏示例。

**快速检查清单：**
- 是否太笼统？（"Helps with projects" 不会生效）
- 是否包含用户实际会说的触发短语？
- 是否提及了相关的文件类型？

**调试方法：** 问 Claude："When would you use the [skill name] skill?" Claude 会引用 description。根据缺失的内容调整。

#### Skill 触发过于频繁

**症状：** Skill 在不相关的查询上加载

**解决方案：**

1. 添加负向触发条件

```yaml
description: Advanced data analysis for CSV files. Use for statistical modeling,
  regression, clustering. Do NOT use for simple data exploration
  (use data-viz skill instead).
```

2. 更具体化

```yaml
# 太宽泛
description: Processes documents

# 更具体
description: Processes PDF legal documents for contract review
```

3. 澄清范围

```yaml
description: PayFlow payment processing for e-commerce. Use specifically for
  online payment workflows, not for general financial queries.
```

#### 指令不被遵循

**症状：** Skill 加载了但 Claude 不遵循指令

**常见原因：**

1. **指令太冗长**
   - 保持指令简洁
   - 使用要点和编号列表
   - 将详细参考移到单独文件

2. **指令被埋没**
   - 将关键指令放在顶部
   - 使用 `## Important` 或 `## Critical` 标题
   - 必要时重复关键点

3. **语言模糊**

```markdown
# 坏
Make sure to validate things properly

# 好
CRITICAL: Before calling create_project, verify:
- Project name is non-empty
- At least one team member assigned
- Start date is not in the past
```

**高级技巧：** 对于关键验证，考虑打包一个脚本来进行程序化检查，而不是依赖语言指令。代码是确定性的；语言解释不是。参见 Office skills 了解这种模式的示例。

4. **模型"懒惰"**

添加显式鼓励：

```markdown
## Performance Notes
- Take your time to do this thoroughly
- Quality is more important than speed
- Do not skip validation steps
```

注意：将这些添加到用户提示中比添加到 SKILL.md 中更有效。

#### MCP 连接问题

**症状：** Skill 加载了但 MCP 调用失败

**检查清单：**

1. **验证 MCP 服务器已连接**
   - Claude.ai：Settings > Extensions > [Your Service]
   - 应显示 "Connected" 状态

2. **检查认证**
   - API key 有效且未过期
   - 授予了正确的权限/范围
   - OAuth token 已刷新

3. **独立测试 MCP**
   - 让 Claude 直接调用 MCP（不用 Skill）
   - "Use [Service] MCP to fetch my projects"
   - 如果这也失败了，问题在 MCP 而非 Skill

4. **验证工具名称**
   - Skill 引用了正确的 MCP 工具名称
   - 检查 MCP 服务器文档
   - 工具名称是大小写敏感的

#### 大上下文问题

**症状：** Skill 似乎变慢或响应质量下降

**原因：**
- Skill 内容太大
- 同时启用了太多 Skill
- 所有内容被加载而非渐进式披露

**解决方案：**

1. **优化 SKILL.md 大小**
   - 将详细文档移到 references/
   - 链接引用而非内联
   - 保持 SKILL.md 在 5,000 词以内

2. **减少启用的 Skill 数量**
   - 评估是否同时启用了超过 20-50 个 Skill
   - 建议选择性启用
   - 考虑为相关能力创建 Skill "包"

---

## 第六章：资源与参考

如果你在构建第一个 Skill，从 Best Practices Guide 开始，然后根据需要参考 API 文档。

### 官方文档

**Anthropic 资源：**
- Best Practices Guide
- Skills Documentation
- API Reference
- MCP Documentation

**博客文章：**
- Introducing Agent Skills
- Engineering Blog: Equipping Agents for the Real World
- Skills Explained
- How to Create Skills for Claude
- Building Skills for Claude Code
- Improving Frontend Design through Skills

### 工具和实用程序

**skill-creator skill：**
- 内置于 Claude.ai 并可用于 Claude Code
- 可从描述生成 Skill
- 审查并提供建议
- 使用："Help me build a skill using skill-creator"

**验证：**
- skill-creator 可以评估你的 Skill
- 问："Review this skill and suggest improvements"

### 示例 Skill

**公开 Skill 仓库：**
- GitHub：anthropics/skills
- 包含 Anthropic 创建的可自定义 Skill

### 获取支持

**技术问题：**
- 一般问题：Claude Developers Discord 社区论坛

**Bug 报告：**
- GitHub Issues：anthropics/skills/issues
- 包含：Skill 名称、错误消息、复现步骤

---

## 附录 A：快速检查清单

使用此清单在上传前后验证你的 Skill。如果你想更快开始，使用 skill-creator skill 生成初稿，然后过一遍此清单确保没有遗漏。

### 开始之前

- [ ] 确定了 2-3 个具体用例
- [ ] 确定了工具（内置或 MCP）
- [ ] 审查了本指南和示例 Skill
- [ ] 规划了文件夹结构

### 开发过程中

- [ ] 文件夹以 kebab-case 命名
- [ ] SKILL.md 文件存在（准确拼写）
- [ ] YAML frontmatter 有 `---` 分隔符
- [ ] name 字段：kebab-case，无空格，无大写
- [ ] description 包含"做什么"和"何时用"
- [ ] 任何地方都没有 XML 标签（< >）
- [ ] 指令清晰且可操作
- [ ] 包含错误处理
- [ ] 提供了示例
- [ ] 清晰链接了参考文件

### 上传之前

- [ ] 在明显的任务上测试了触发
- [ ] 在改述的请求上测试了触发
- [ ] 验证了不在无关话题上触发
- [ ] 功能测试通过
- [ ] 工具集成正常（如适用）
- [ ] 压缩为 .zip 文件

### 上传之后

- [ ] 在真实对话中测试
- [ ] 监控过度/不足触发
- [ ] 收集用户反馈
- [ ] 迭代 description 和指令
- [ ] 更新 metadata 中的版本

---

## 附录 B：YAML Frontmatter 参考

### 必需字段

```yaml
---
name: skill-name-in-kebab-case
description: What it does and when to use it. Include specific trigger phrases.
---
```

### 所有可选字段

```yaml
name: skill-name
description: [required description]
license: MIT                              # 可选：开源许可
allowed-tools: "Bash(python:*) Bash(npm:*) WebFetch"  # 可选：限制工具访问
metadata:                                 # 可选：自定义字段
  author: Company Name
  version: 1.0.0
  mcp-server: server-name
  category: productivity
  tags: [project-management, automation]
  documentation: https://example.com/docs
  support: support@example.com
```

### 安全说明

**允许的：**
- 任何标准 YAML 类型（字符串、数字、布尔值、列表、对象）
- 自定义 metadata 字段
- 长 description（最多 1024 字符）

**禁止的：**
- XML 尖括号（< >）——安全限制
- YAML 中的代码执行（使用安全 YAML 解析）
- 以 "claude" 或 "anthropic" 为前缀的 Skill 名称（保留字）

---

## 附录 C：完整 Skill 示例

有关演示本指南模式的完整、生产就绪的 Skill：

- **文档 Skill** — PDF、DOCX、PPTX、XLSX 创建
- **示例 Skill** — 各种工作流模式
- **合作伙伴 Skill 目录** — 查看来自 Asana、Atlassian、Canva、Figma、Sentry、Zapier 等合作伙伴的 Skill

这些仓库保持更新，包含超出本文覆盖范围的额外示例。克隆它们，根据你的用例修改，并将它们用作模板。
