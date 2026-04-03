# Claude Code → Creative CoWork 架构迁移

> 从 AI 编码工具到 AI 创作工作台：哪些保留、哪些改造、哪些替换。

## 迁移总览

```
Claude Code（编码场景）          Creative CoWork（创作场景）
─────────────────────────      ─────────────────────────────
单循环 + 智能委托              → 创意循环（发散/收敛模式）
116 文件模块化 SP              → 创意 Prompt 模块化
18 Tools（读写文件等）          → Creative Tools（MCP 多模型接入）
Sub-agents（Explore/Plan）     → Creative Sub-agents
Superpowers Skills             → Creative Skills + /init 推荐
CLAUDE.md 层级配置             → 项目级上下文容器
Terminal UI                    → GENUI 动态生成界面 + Studio
```

## 1. 单循环 + 委托 → 创意循环（发散/收敛）

### Claude Code 原设计

- [[raw/articles/claude-code-research/Claude Code 架构分析]]
- 单主循环：用户输入 → 配额检查 → 话题检测 → Main Agent 处理 → 输出
- 智能委托：Main Agent（Sonnet 4）→ Sub-Agent（Explore/Haiku, Plan/Sonnet, General/Sonnet）
- 最多一层嵌套，架构保持简单

### Creative CoWork 改造

| 维度 | Claude Code | Creative CoWork | 改造原因 |
|------|------------|-----------------|----------|
| 循环目标 | 收敛——尽快找到正确答案 | 发散+收敛——先生成多种可能性，再筛选精炼 | 创意工作需要探索空间 |
| 终止条件 | 任务完成或需要用户介入 | 发散阶段：用户选择方向；收敛阶段：达到质量标准 | 创意没有"唯一正确答案" |
| 循环节奏 | 快速迭代 | 慢发散（探索）+ 快收敛（执行） | 用户需要时间审美判断 |

**保留**：单循环 + 委托的基本模式。不搞复杂的多 Agent 协调。
**改造**：在循环内增加显式的发散/收敛阶段切换，由 [[concepts/creative-agent-design]] 定义切换触发条件。

来源：[[concepts/agent-loop]]、[[raw/projects/creative-cowork/reference/Creative Agent System Prompt 模板]]

## 2. 116 文件模块化 → 创意 Prompt 模块化

### Claude Code 原设计

- [[raw/articles/claude-code-research/Claude Code System Prompt 详解]]
- 110+ 个独立 prompt 片段，根据上下文动态组装
- 分类：核心系统指令（~10）、工具描述（18 个，每个 122-2,250 tokens）、Sub-agent prompts（5+）、System reminders（~40）
- 目录结构：system/ + tools/ + agents/ + skills/ + reminders/

### Creative CoWork 改造

| 模块层 | Claude Code | Creative CoWork | 状态 |
|--------|------------|-----------------|------|
| system/ | 编码行为规范、安全准则 | 创作行为规范、审美原则、品牌调性 | **替换** |
| tools/ | 文件读写、Bash、Grep 等 18 工具 | 生图/生视频/配乐/剪辑等创意工具（MCP） | **替换** |
| agents/ | Explore/Plan/General 三种角色 | 构思/分镜/生成/审核等创意角色 | **改造** |
| skills/ | 编码技能（commit、review 等） | 创意技能（故事短片、角色设定等） | **替换** |
| reminders/ | Git 状态、配额提醒等 | 项目进度、素材状态、风格提醒等 | **改造** |
| 组装逻辑 | 按模式（plan/learn/delegate）动态组装 | 按创作阶段（构思/执行/审核）动态组装 | **保留** |

**保留**：模块化架构本身、目录分层思路、动态组装机制。
**替换**：所有模块的具体内容。

来源：[[concepts/modular-prompt-architecture]]

## 3. Tools → Creative Tools（MCP）

### Claude Code 原设计

- 18 个内置工具：Read、Write、Edit、Bash、Grep、Glob、TodoWrite、Task 等
- 每个工具有详细的 JSON Schema 参数定义和使用指令
- 工具是硬编码的，版本跟随产品发布

### Creative CoWork 改造

| Claude Code Tool | Creative CoWork 对应 | 状态 |
|-----------------|---------------------|------|
| Read/Write/Edit | 素材读取/保存/编辑 | **改造**（从文件操作到素材操作） |
| Bash | 生成命令执行（API 调用封装） | **改造** |
| Grep/Glob | 素材/资产搜索 | **改造** |
| TodoWrite | 项目进度追踪 | **保留** |
| Task（Sub-agent） | Creative Task（创意子任务委托） | **改造** |
| N/A | 生图工具（即梦/MJ/Nano） | **新增** |
| N/A | 生视频工具（可灵/智影/海螺） | **新增** |
| N/A | 配乐工具（Sono 等） | **新增** |
| N/A | 配音工具（TTS/真人） | **新增** |

**关键变化**：Claude Code 工具是内置的；Creative CoWork 通过 MCP 协议动态接入外部工具，使工具集可扩展。

来源：[[raw/projects/creative-cowork/0201 - 技术研究]]

## 4. Sub-agents → Creative Sub-agents

### Claude Code 原设计

- [[raw/articles/claude-code-research/Claude Code 架构分析]]
- 三种 Sub-agent：
  - **Explore**（Haiku）：只读工具，516 tokens SP，用于代码探索
  - **Plan**（Sonnet）：只读工具，633 tokens SP，用于方案规划
  - **General**（Sonnet）：全工具，294 tokens SP，用于通用任务
- 约束：最多一层嵌套

### Creative CoWork 改造

| Claude Code Sub-agent | Creative Sub-agent | 工具权限 | 典型场景 |
|----------------------|-------------------|----------|----------|
| Explore | **参考搜索 Agent** | 只读（搜索素材库/参考库） | 风格参考、灵感收集 |
| Plan | **创意规划 Agent** | 只读（分析项目状态） | 分镜规划、风格定义 |
| General | **执行生成 Agent** | 全工具（调用生成 API） | 批量生图/生视频 |
| N/A | **质量审核 Agent** | 只读（对比参考） | 角色一致性检查、风格匹配评分 |

**保留**：一层嵌套约束、Main Agent 编排的基本模式。
**改造**：Sub-agent 角色从编码场景适配到创作场景。
**新增**：质量审核 Agent（对应用户访谈中"筛选"的强需求）。

来源：[[concepts/sub-agent-architecture]]

## 5. Superpowers Skills → Creative Skills

### Claude Code 原设计

- [[raw/articles/claude-code-research/Superpowers Skills 架构分析]]
- SKILL.md + YAML frontmatter 标准
- Skill 发现：按 trigger 条件自动匹配
- 社区生态：commit、review、brainstorm、ship 等

### Creative CoWork 改造

| 维度 | Superpowers Skills | Creative Skills | 状态 |
|------|-------------------|-----------------|------|
| 标准 | SKILL.md + YAML frontmatter | 保持相同标准 | **保留** |
| 内容 | 编码技能（commit/review/ship） | 创意技能（构思/分镜/生成/配乐） | **替换** |
| 扩展 | 无额外目录 | +prompts/（提示词模板）+presets/（预设配置） | **扩展** |
| 发现 | 按 trigger 匹配 | /init 三层推荐（项目类型/用户历史/社区热门） | **改造** |
| 实例 | N/A | 即梦视频 Agent 的全套 Skills | **新增** |

**保留**：SKILL.md 标准、模块化加载机制。
**扩展**：prompts/ 和 presets/ 目录结构（[[raw/projects/creative-cowork/04 Creative CoWork - MVP需求]] 第 6 节定义）。
**改造**：发现机制从被动 trigger 到 [[concepts/init-mechanism]] 的主动推荐。

来源：[[concepts/skills-system]]、[[raw/articles/claude-code-research/Creative CoWork Skills 架构启发]]、[[raw/articles/claude-code-research/为 Claude 构建 Skills 完整指南]]

## 6. CLAUDE.md → 上下文容器

### Claude Code 原设计

- 4 层优先级：Enterprise Policy → 项目根 CLAUDE.md → 项目规则 rules/*.md → 用户级 ~/.claude/CLAUDE.md
- 子目录 CLAUDE.md 按需加载
- 本质是"每次启动前强制注入的 system prompt"

### Creative CoWork 改造

| 维度 | CLAUDE.md | Context Container | 状态 |
|------|-----------|-------------------|------|
| 范围 | 项目规范和编码约定 | 项目全量上下文（素材/结果/参考/历史） | **大幅扩展** |
| 内容类型 | 纯文本 | 文本 + 图片 + 视频 + 音频 | **扩展** |
| 加载方式 | 启动时全量注入 | 按需分层加载（[[concepts/progressive-disclosure]]） | **改造** |
| 持久化 | 文件系统（Git 管理） | 文件系统 + 索引（借鉴 OpenClaw 的向量+FTS5） | **扩展** |
| 协作 | 单用户 | 多用户（导演 + 抽卡师） | **新增** |

**保留**：文件系统作为数据源的设计哲学。
**大幅扩展**：从"文本指令"到"项目级全量上下文聚合"。

来源：[[concepts/context-container]]、[[raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code]]

## 7. Terminal UI → GENUI + Studio

### Claude Code 原设计

- 纯 Terminal 界面，文本输入/输出
- 用 Markdown 渲染结果

### Creative CoWork 改造

这是最大的差异点——完全替换。

| Claude Code | Creative CoWork | 原因 |
|------------|-----------------|------|
| Terminal 文本 | [[concepts/genui]] 动态生成界面 | 创作工作需要视觉化，不能纯文本 |
| 无预设界面 | Studio 场景化工作空间 | 不同创作类型需要不同布局 |
| 文件树导航 | 素材网格/时间线视图 | 素材浏览不能用文件树 |

来源：[[raw/projects/creative-cowork/06 Creative CoWork - 侧边栏设计方案]]

## 迁移决策汇总

| 组件 | 保留 | 改造 | 替换 | 新增 |
|------|------|------|------|------|
| Agent Loop | 单循环+委托模式 | 加入发散/收敛 | - | - |
| Prompt 架构 | 模块化+动态组装 | - | 所有模块内容 | - |
| Tools | TodoWrite | Read/Write/Bash 适配 | 编码工具 | 创意生成工具集 |
| Sub-agents | 一层嵌套约束 | 角色适配 | - | 质量审核 Agent |
| Skills | 标准+加载机制 | 发现→推荐 | 编码技能 | prompts/+presets/ |
| 配置/上下文 | 文件系统思路 | 按需加载 | 纯文本→多媒体 | 协作能力 |
| UI | - | - | Terminal→GENUI | Studio |

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
