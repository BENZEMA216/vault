# Prompt 管理

> AGENT 的 Prompt 模块化管理，参考 [[Claude Code 架构分析]]

## 设计理念

不使用单一的大 Prompt，而是使用 **可组合的 Prompt 模块**，根据上下文动态组装。

## 目录结构

```
.prompt/
├── README.md              # 本文件
├── system/                # 基础 System Prompt
│   ├── base.md            # 核心行为规范
│   └── safety.md          # 安全约束
│
├── agents/                # Agent 描述
│   ├── main.md            # Main Agent prompt
│   ├── explore.md         # Explore Sub-Agent（Haiku，只读）
│   ├── execute.md         # Execute Sub-Agent（Sonnet，全工具）
│   └── plan.md            # Plan Sub-Agent（Sonnet，只读）
│
├── skills/                # Skill 描述模板
│   ├── _template.md       # Skill 描述模板
│   └── ...                # 各 Skill 的描述
│
├── modes/                 # 模式 Prompt
│   ├── create.md          # 创作模式
│   └── explore.md         # 探索模式
│
└── reminders/             # 系统提醒
    ├── context_limit.md   # 上下文超限提醒
    └── ...                # 其他提醒
```

## Prompt 组装流程

```
┌─────────────────────────────────────────────────────────────┐
│                    最终 System Prompt                        │
├─────────────────────────────────────────────────────────────┤
│  1. system/base.md          # 基础行为规范                   │
│  2. system/safety.md        # 安全约束                       │
│  3. agents/{agent}.md       # 当前 Agent 的角色描述          │
│  4. modes/{mode}.md         # 当前模式                       │
│  5. [动态生成] 可用工具描述   # 根据加载的 Skills 生成         │
│  6. [动态生成] 项目上下文     # CONTEXT FILES 内容            │
│  7. reminders/*.md          # 系统提醒（按需）               │
└─────────────────────────────────────────────────────────────┘
```

## 组装伪代码

```python
def assemble_prompt(agent_type, mode, skills, context_files, reminders=[]):
    prompt = ""

    # 1. 基础 prompt
    prompt += read(".prompt/system/base.md")
    prompt += read(".prompt/system/safety.md")

    # 2. Agent 角色
    prompt += read(f".prompt/agents/{agent_type}.md")

    # 3. 当前模式
    prompt += read(f".prompt/modes/{mode}.md")

    # 4. 可用工具（动态生成）
    for skill in skills:
        prompt += generate_tool_description(skill)

    # 5. 项目上下文
    for file in context_files:
        prompt += f"<context_file name='{file.name}'>\n{file.content}\n</context_file>"

    # 6. 系统提醒
    for reminder in reminders:
        prompt += read(f".prompt/reminders/{reminder}.md")

    return prompt
```

## 变量插值

Prompt 中可以使用 `{{变量名}}` 语法，运行时替换：

| 变量 | 说明 | 示例 |
|------|------|------|
| `{{project_name}}` | 当前项目名 | "西西弗斯短片" |
| `{{available_tools}}` | 可用工具列表 | "read_file, write_file, generate_image" |
| `{{context_files}}` | 上下文文件列表 | "storyboard.md, style_guide.md" |
| `{{current_task}}` | 当前任务 | "生成第3个分镜的视觉场景" |

## 编写规范

1. **单一职责**：每个 prompt 文件只负责一个功能
2. **可组合**：prompt 之间不应有硬依赖
3. **清晰边界**：用 XML 标签区分不同部分
4. **版本控制**：重要改动需要记录变更原因

## 示例：Main Agent Prompt

```markdown
# Main Agent

你是 Story Agent，一个专业的 AI 创作助手。

## 角色定位

- 帮助创作者完成视频/动画项目
- 理解创作意图，编排工具完成任务
- 在需要时委派给专用 Sub-Agent

## 可用的 Sub-Agent

- **Explore**：快速搜索和探索，用于查找素材、理解项目结构
- **Execute**：执行复杂任务，有完整工具权限
- **Plan**：规划任务，拆解复杂需求

## 决策原则

1. 简单任务直接执行
2. 需要探索时委派给 Explore
3. 复杂多步任务委派给 Execute
4. 需要规划时委派给 Plan

## 当前项目

项目名：{{project_name}}
可用工具：{{available_tools}}
```

## 参考

- [[Claude Code 架构分析]] - 模块化 Prompt 系统
- [[04 Creative CoWork - MVP需求]] - 5.3 模块化 Prompt 系统
