# 模块化 System Prompt

> 参考 Claude Code 的 116 文件模块化架构设计

## 目录结构

```
prompts/
├── README.md
├── system/                    # 系统级（始终加载）~1000 tokens
│   ├── identity.md            # 身份定义
│   ├── agent-loop.md          # 执行循环
│   ├── thinking.md            # 思考机制
│   ├── communication.md       # 通信规范
│   ├── tool-policy.md         # 工具策略
│   ├── self-correction.md     # 自我纠正
│   ├── limitations.md         # 限制边界
│   └── professional-objectivity.md  # 专业客观性
│
├── tools/                     # 工具描述（始终加载）~800 tokens
│   ├── file-read.md
│   ├── file-write.md
│   ├── file-edit.md
│   ├── search-glob.md
│   ├── search-grep.md
│   ├── execute.md
│   ├── todo-write.md
│   ├── todo-update.md
│   ├── task.md
│   ├── skill-activate.md
│   └── skill-deactivate.md
│
├── agents/                    # 子代理 prompt
│   └── general.md             # 通用子代理
│
├── reminders/                 # 上下文提醒（条件加载）
│   ├── file-modified-externally.md
│   ├── task-idle.md
│   ├── token-limit-approaching.md
│   ├── skill-activated.md
│   ├── long-conversation.md
│   ├── creation-in-progress.md
│   └── tool-call-failed.md
│
└── skills/                    # 技能（按需加载）
    ├── story-writing/
    │   └── SKILL.md
    ├── prompt-engineering/
    │   └── SKILL.md
    └── code-style/
        └── SKILL.md
```

## 组装逻辑（Cache 优化）

### 核心原则

**固定内容在前，可变内容在后** —— 最大化 prompt cache 命中率。

```
┌─────────────────────────────────────┐
│  固定前缀（可缓存）                   │
│  ├── system/*     ~1,000 tokens     │
│  └── tools/*      ~800 tokens       │
├─────────────────────────────────────┤
│  可变后缀（不期望缓存）               │
│  ├── skills/*     按需加载           │
│  ├── reminders/*  条件触发           │
│  └── context      每次不同           │
└─────────────────────────────────────┘
```

### 组装代码

```python
def build_system_prompt(context):
    parts = []

    # ===== 固定前缀（高 cache 命中）=====

    # 1. system/* - 始终加载，顺序固定
    for file in sorted(glob("prompts/system/*.md")):
        parts.append(load_and_render(file))

    # 2. tools/* - 始终加载，顺序固定
    for file in sorted(glob("prompts/tools/*.md")):
        parts.append(load_and_render(file))

    # ===== 可变后缀（不影响前缀 cache）=====

    # 3. skills/* - 按需加载
    for skill in sorted(context.active_skills):  # sorted 保证顺序稳定
        parts.append(load_and_render(
            f"prompts/skills/{skill}/SKILL.md"
        ))

    # 4. reminders/* - 条件触发
    for reminder in sorted(get_triggered_reminders(context)):
        parts.append(load_and_render(
            f"prompts/reminders/{reminder}.md",
            context.variables
        ))

    # 5. context - 动态内容，放最后
    parts.append(build_context_section(context))

    return "\n\n---\n\n".join(parts)
```

### Cache 优化要点

| 要点 | 说明 |
|------|------|
| **固定前缀 ~1,800 tokens** | 超过 cache 阈值（通常 1024），可有效缓存 |
| **使用 `sorted()`** | 保证文件加载顺序稳定 |
| **可变内容放最后** | 不破坏固定前缀的 cache |
| **skills 排序** | 相同 skill 组合的请求能复用更多 cache |

### Cache 命中示意

```
请求 A: [system][tools] | [skill-X][reminder-A][context-1]
请求 B: [system][tools] | [skill-X][reminder-B][context-2]
        ↑____________↑
         固定前缀复用 cache (~1,800 tokens)
```

## 文件格式

每个模块文件使用 YAML frontmatter：

```markdown
---
name: module-name
description: 模块描述
always_load: true/false
trigger: trigger_condition  # reminders 用
variables: [VAR1, VAR2]     # 需要的变量
tokens: ~100                # 预估 token 数
---

[模块内容]
```

## Token 预算

| 类别 | 文件数 | 预估 Tokens | Cache |
|------|--------|-------------|-------|
| system/ | 8 | ~1,000 | ✓ 固定 |
| tools/ | 11 | ~800 | ✓ 固定 |
| skills/ | 0-2 | ~500-1,500 | ✗ 可变 |
| reminders/ | 0-3 | ~100-300 | ✗ 可变 |
| context | 1 | ~200-500 | ✗ 可变 |
| **固定前缀** | 19 | **~1,800** | ✓ |
| **总计** | - | **~2,500-4,000** | - |

## 与 Claude Code 对照

| Claude Code | 本设计 |
|-------------|--------|
| `system-prompt-main-system-prompt.md` | `system/identity.md` |
| `system-prompt-doing-tasks.md` | `system/agent-loop.md` |
| `system-prompt-tone-and-style.md` | `system/communication.md` |
| `system-prompt-tool-usage-policy.md` | `system/tool-policy.md` |
| `tool-description-*.md` | `tools/*.md` |
| `agent-prompt-*.md` | `agents/general.md` |
| `system-reminder-*.md` | `reminders/*.md` |

## 扩展建议

### 1. Skills 预组合

如果某些 skills 经常一起用，可以定义 bundle：

```python
SKILL_BUNDLES = {
    "creation": ["story-writing", "prompt-engineering"],
    "coding": ["code-style"],
}

# 使用
context.active_skills = SKILL_BUNDLES["creation"]
```

### 2. Context 模板化

保持格式固定，只替换变量：

```markdown
## Local Context

**Directory**: ${CWD}
**Branch**: ${BRANCH}

**Tree**:
```
${FILE_TREE}
```
```

### 3. Reminders 触发器

需要在系统层实现触发逻辑：

```python
def get_triggered_reminders(context):
    reminders = []

    if context.file_modified_externally:
        reminders.append("file-modified-externally")

    if context.task_tools_idle_time > 300:  # 5 分钟
        reminders.append("task-idle")

    if context.output_tokens_used > 0.8 * context.output_tokens_limit:
        reminders.append("token-limit-approaching")

    return reminders
```
