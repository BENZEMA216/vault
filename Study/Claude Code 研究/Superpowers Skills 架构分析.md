# Superpowers Skills 架构分析

> 项目地址：[obra/superpowers](https://github.com/obra/superpowers)
> 定位：面向 AI 编程代理的 agentic skills 框架和软件开发方法论
> 版本：4.2.0 | Stars：47K+ | 作者：Jesse Vincent
> 分析日期：2025-02-08

---

## 一、项目概述

Superpowers 给 AI coding agent（Claude Code / Codex / OpenCode）注入一套"强制性工作流技能"，让 agent 在写代码之前先思考、先规划、先测试，而不是直接跳入实现。

核心理念：**把 LLM 当成需要"纪律手册"的工程师，用精心设计的 Markdown 文档来传递工作流和约束，而不是用代码逻辑。**

---

## 二、目录结构

```
superpowers/
├── .claude-plugin/          # Claude Code 插件清单
│   ├── plugin.json          # 插件元数据 (name, version, author)
│   └── marketplace.json     # 市场注册信息
├── agents/                  # 代理定义（角色型 agent）
│   └── code-reviewer.md     # 代码审查员 agent
├── commands/                # 斜杠命令（快捷方式）
│   ├── brainstorm.md
│   ├── execute-plan.md
│   └── write-plan.md
├── hooks/                   # 生命周期钩子
│   ├── hooks.json           # 钩子注册表
│   ├── session-start.sh     # 会话启动时注入上下文
│   └── run-hook.cmd         # Windows 兼容脚本
├── lib/                     # 共享代码
│   └── skills-core.js       # 技能发现/加载核心库
├── skills/                  # 所有技能定义
│   ├── brainstorming/
│   ├── dispatching-parallel-agents/
│   ├── executing-plans/
│   ├── finishing-a-development-branch/
│   ├── receiving-code-review/
│   ├── requesting-code-review/
│   ├── subagent-driven-development/
│   ├── systematic-debugging/
│   ├── test-driven-development/
│   ├── using-git-worktrees/
│   ├── using-superpowers/
│   ├── verification-before-completion/
│   ├── writing-plans/
│   └── writing-skills/
├── tests/                   # 测试套件
└── docs/                    # 文档
```

---

## 三、Skill 定义格式

每个 skill 就是一个文件夹，核心只有一个 `SKILL.md`，部分 skill 有额外支撑文件：

| Skill | 文件 |
|-------|------|
| `brainstorming/` | `SKILL.md` |
| `writing-plans/` | `SKILL.md` |
| `test-driven-development/` | `SKILL.md` + `testing-anti-patterns.md` |
| `subagent-driven-development/` | `SKILL.md` + `implementer-prompt.md` + `spec-reviewer-prompt.md` + `code-quality-reviewer-prompt.md` |
| `systematic-debugging/` | `SKILL.md` + `root-cause-tracing.md` + `defense-in-depth.md` + `condition-based-waiting.md` + `find-polluter.sh` |
| `writing-skills/` | `SKILL.md` + `anthropic-best-practices.md` + `persuasion-principles.md` + `testing-skills-with-subagents.md` + `examples/` |

### SKILL.md 格式 = YAML Frontmatter + Markdown 正文

```yaml
---
name: brainstorming
description: Use when the user wants to build something new...
---
# Brainstorming

## Overview
核心原则，1-2 句话

## When to Use
- 触发条件（症状、场景）
- 何时不用

## The Process / Core Pattern
- 具体步骤
- 流程图（用 graphviz dot 语法）

## Red Flags
- 什么思维模式意味着 agent 在"合理化偷懒"

## Common Rationalizations
| 借口 | 现实 |

## Integration
- 依赖/关联的其他 skills（用 superpowers:skill-name 格式引用）
- "REQUIRED SUB-SKILL" 标记强依赖

## Verification Checklist
```

### 关键设计决策

- Frontmatter **只有两个字段**：`name` 和 `description`
- `description` 以 "Use when..." 开头，描述**何时使用**而非做什么
- 刻意不在 description 里摘要流程 —— 测试发现 LLM 会"走捷径"按摘要执行而跳过阅读完整内容
- 不用 `@` 语法引用其他 skill 文件（避免强制加载消耗 context），用 `superpowers:skill-name` 格式让 agent 按需加载
- 大量使用 graphviz 流程图表达决策逻辑
- 纪律型 skill 包含大量"反合理化"内容（rationalization tables, red flags）

---

## 四、三层加载机制

### Layer 1: SessionStart Hook — 引导注入

```json
// hooks/hooks.json
{
  "hooks": {
    "SessionStart": [{
      "matcher": "startup|resume|clear|compact",
      "hooks": [{
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/hooks/session-start.sh"
      }]
    }]
  }
}
```

`session-start.sh` 在每次会话启动时执行，将 `skills/using-superpowers/SKILL.md` 的**完整内容**注入到 context 中，包裹在 `<EXTREMELY_IMPORTANT>` 标签里。**这是唯一一个启动时全量加载的 skill。**

### Layer 2: skills-core.js — 技能发现引擎

| 函数 | 用途 |
|------|------|
| `findSkillsInDir(dir, sourceType, maxDepth)` | 递归扫描目录，找所有包含 SKILL.md 的文件夹 |
| `resolveSkillPath(skillName, superpowersDir, personalDir)` | 解析 skill 名称到文件路径，支持命名空间 |
| `extractFrontmatter(filePath)` | 提取 YAML frontmatter |
| `stripFrontmatter(content)` | 去掉 frontmatter 返回正文 |
| `checkForUpdates(repoDir)` | 检查远程是否有更新 |

**覆盖机制（Shadowing）：** 个人 skills（`~/.claude/skills`）优先于 superpowers skills。同名 skill 存在于个人目录时使用个人版本，可用 `superpowers:skill-name` 前缀强制使用 superpowers 版本。

### Layer 3: 按需加载（Skill Tool）

Agent 通过 `Skill` 工具按 name 加载某个 skill。**没有预注册的 registry/manifest**。发现机制完全依赖：
1. 启动时注入的 `using-superpowers` 告诉 agent 有哪些 skills 可用
2. Agent 根据任务上下文判断调用哪个 skill
3. 调用 Skill 工具加载完整 SKILL.md 内容

---

## 五、触发/路由机制

**核心设计：没有自动化的条件匹配引擎。** 路由完全依赖 LLM 自身的判断力 + 强烈的指令压力。

`using-superpowers` 是路由核心，规定：

> "If you think there is even a 1% chance a skill might apply to what you are doing, you ABSOLUTELY MUST invoke the skill."

### 防止 LLM "合理化跳过" 的手段

| 常见借口 | 文档中的纠正 |
|---------|-------------|
| "这个任务太简单不需要" | "简单任务正是 TDD 最有价值的地方" |
| "我已经知道该怎么做" | "知道怎么做 ≠ 会做对" |
| "用户赶时间" | "返工比按流程更慢" |
| "只是小改动" | "小改动也能引入 bug" |

### Commands 快捷命令

`commands/` 目录下的命令是极简的转发器：

```yaml
---
description: "You MUST use this before any creative work..."
disable-model-invocation: true
---
Invoke the superpowers:brainstorming skill and follow it exactly
```

斜杠命令只是将用户指令映射到对应的 skill 调用。

---

## 六、Skills 编排

定义了一个**线性但可分支的工作流 pipeline**，通过 skills 之间的显式文档引用实现编排：

```
brainstorming
    ↓ （设计完成后）
using-git-worktrees              ← 创建隔离工作区
    ↓
writing-plans                    ← 产出实施计划
    ↓ （用户选择执行方式）
    ├── subagent-driven-development   ← 同会话，子代理逐任务执行
    └── executing-plans               ← 新会话，批量执行+检查点
            ↓ （每个任务中）
            test-driven-development   ← TDD 循环
            ↓ （任务间）
            requesting-code-review    ← 代码审查
            receiving-code-review     ← 处理审查反馈
            ↓ （全部完成）
finishing-a-development-branch        ← 合并/PR/清理
```

**异常路径：**
- `systematic-debugging` — 遇到 bug 时切入
- `dispatching-parallel-agents` — 多个独立问题时并行分派
- `verification-before-completion` — 任何"声称完成"之前必须执行

**编排方式是"文档级引用"：** 每个 skill 在 Integration 部分用 `REQUIRED SUB-SKILL` 标记必须配合使用的其他 skills。不是代码层面的依赖注入，而是给 LLM 的指令级编排。**没有显式的 pipeline/workflow engine。**

---

## 七、子代理机制（最精密的部分）

### 架构

```
主控 Agent（Controller）
    ↓ 读取计划，提取所有任务
    ↓ 为每个任务：
    │
    ├── 1. 分派 Implementer 子代理（用 Task 工具）
    │       → 提供完整任务文本（不让子代理读文件）
    │       → 子代理可以提问 → 主控回答
    │       → 实现 + 测试 + 提交 + 自审查
    │
    ├── 2. 分派 Spec Reviewer 子代理
    │       → 独立读代码，对比 spec
    │       → 不信任 implementer 的报告
    │       → Pass/Fail → Fail 则 implementer 修复 → 重审
    │
    └── 3. 分派 Code Quality Reviewer 子代理
            → 仅在 spec 审查通过后
            → 使用 code-reviewer agent 模板
            → Pass/Fail → Fail 则修复 → 重审
```

### Prompt 模板

三个模板文件在 `skills/subagent-driven-development/` 目录：

- **`implementer-prompt.md`**：包含完整任务描述占位符 + "先提问再动手"指令 + 自审查清单 + 汇报格式
- **`spec-reviewer-prompt.md`**：明确说"不信任 implementer 的报告"，必须独立读代码，输出 pass/fail + 具体问题
- **`code-quality-reviewer-prompt.md`**：指向 code-reviewer 模板，需填入 `{BASE_SHA}` / `{HEAD_SHA}` / `{WHAT_WAS_IMPLEMENTED}` 等占位符

### 关键设计决策

1. **每任务一个新子代理**：避免上下文污染
2. **双阶段审查**：先 spec 合规性，再代码质量（顺序不可颠倒）
3. **审查循环**：不通过 → 修复 → 重审 → 直到通过
4. **主控提供完整文本**：子代理不需要读取计划文件
5. **子代理可以提问**：实现前和实现中都可以暂停提问
6. **不并行分派实现子代理**：避免代码冲突

---

## 八、架构总结

| 维度 | Superpowers 的选择 |
|------|-------------------|
| Skill 定义 | 纯 Markdown + YAML frontmatter，无代码 |
| 发现机制 | 文件系统扫描（找 SKILL.md），无注册表 |
| 加载机制 | 按需加载（Skill 工具），仅 using-superpowers 全量注入 |
| 路由/触发 | 完全依赖 LLM 判断 + 强指令压力，无规则引擎 |
| 编排 | 文档级引用（REQUIRED SUB-SKILL），无 pipeline engine |
| 子代理 | 通过 Task 工具分派，prompt 模板驱动，双阶段审查 |
| 覆盖机制 | 个人 skills 目录优先于 superpowers skills |
| 参数化 | Prompt 模板占位符 + agent 上下文推断 |
| 纪律保障 | 大量反合理化内容（rationalization tables, red flags） |
| 跨平台 | Claude Code（插件）、Codex（符号链接）、OpenCode（符号链接） |

---

## 九、对 Creative CoWork 的借鉴

### 1. Skill 定义格式：可直接复用

"SKILL.md = frontmatter + markdown" 模式非常适合创作场景：

```yaml
---
name: storyboard-writing
description: Use when the user wants to create a storyboard for short video...
---
# 分镜脚本撰写
## When to Use
## The Process
## Quality Checklist
```

创作者可以用 Markdown 自己写 skill，不需要懂代码。跟 Creative CoWork "用户可自由加载的模块化能力" 定位完美匹配。

### 2. "路由表 + 按需加载" 模式

不要一次把所有 skill 塞进 context。而是：
- 启动时只注入一个 **skill 目录**（name + 一句话 description）
- Agent 按需加载完整 skill 内容
- Context 开销极小，skill 数量可以无限扩展

### 3. 反合理化设计是精髓

Creative CoWork 的 skills 也应该包含：
- **Red Flags**：什么时候 agent 可能在走捷径？
- **Rationalization Table**：常见借口 vs 现实纠正
- **Verification Checklist**：完成前必须检查的清单

对创作质量至关重要 —— 防止 AI 生成"看起来像回事但实际空洞"的内容。

### 4. 子代理 "实现+审查" 分离模式

可以迁移到创作场景：

```
创作 Agent（写初稿）
  → 审稿 Agent（独立检查质量/一致性）
  → 风格 Agent（检查品牌调性）
```

**关键：审查 agent 不信任创作 agent 的自述，必须独立看作品。**

### 5. Shadowing（覆盖）机制

Creative CoWork 应该支持三层覆盖：
- **平台内置 skills**（官方维护的高质量 skill）
- **团队 skills**（公司/团队共享的定制 skill）
- **个人 skills**（创作者自己写的 skill）
- 同名时优先级：个人 > 团队 > 平台

### 6. 编排方式的取舍

Superpowers 选择了"纯文档编排，无代码逻辑"，在 LLM 能力足够时很优雅。但 Creative CoWork 可能需要更确定性的编排：
- 创作流程更复杂（多模态：文案→图片→视频→配音）
- 创作者对"流程可控性"要求更高
- 需要支持"部分重做"（比如只重新生成第3个分镜的图片）

**建议**：混合模式 —— skill 定义用 Markdown（低门槛），但 **workflow 编排用代码/配置**（可靠性）。

### 7. Creative CoWork 需要额外设计的部分

| Superpowers 没有的 | Creative CoWork 需要的 |
|-------------------|----------------------|
| 多模态支持 | 图片/视频/音频 skill |
| 参数化 skill | skill 接受参数（如"风格=赛博朋克"） |
| skill 间数据传递 | 上下文容器中的中间产物流转 |
| 版本管理 | skill 版本 + 回滚 |
| 权限控制 | 哪些 skill 可以调用哪些外部 API |
| 执行进度 | 长流程的进度反馈（视频渲染等） |
| 条件分支 | 基于中间结果的动态路由 |
