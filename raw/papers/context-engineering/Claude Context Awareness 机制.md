# Claude Context Awareness 机制

> 来源：[Context windows - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/context-windows)

## 概述

Context Awareness 让模型**知道自己还剩多少 token 可用**，从而更好地规划任务执行策略。

类比：没有 context awareness 的模型就像在没有时钟的烹饪比赛中竞争。

## 实现方式

### 1. 对话开始时注入总预算

```xml
<budget:token_budget>200000</budget:token_budget>
```

预算值根据配置不同：
| 配置 | Token 预算 |
|------|-----------|
| 标准 | 200K |
| Claude.ai Enterprise | 500K |
| Beta（符合条件组织） | 1M |

### 2. 每次 Tool Call 后更新剩余量

```xml
<system_warning>Token usage: 35000/200000; 165000 remaining</system_warning>
```

这个更新在**每次工具调用后**自动注入。

## 关键特性

| 要点 | 说明 |
|------|------|
| **原生训练** | 不是简单的 prompt injection，模型被训练来理解和使用这个信息 |
| **自动注入** | API 层面自动处理，开发者无需手动计算 |
| **包含图片** | Image tokens 也计入预算 |

## 支持的模型

- Claude Sonnet 4.5 / Haiku 4.5
- Claude Sonnet 4 / Opus 4 / Opus 4.1 / Opus 4.5

## 解决的问题

| 问题 | 解决方案 |
|------|---------|
| **搜索策略规划** | Token 充裕时广泛探索，紧张时优先完成关键任务 |
| **避免过早放弃** | 模型可以提前规划收尾，而不是突然中断 |

## 适用场景

- 长时间运行的 agent session
- 跨多个 context window 的工作流
- 需要精细 token 管理的复杂任务

## 自定义实现参考

```python
def inject_token_budget(messages, total_budget, used_tokens):
    remaining = total_budget - used_tokens
    budget_info = f"<system_warning>Token usage: {used_tokens}/{total_budget}; {remaining} remaining</system_warning>"
    # 在 tool result 后注入
    return messages + [{"role": "user", "content": budget_info}]
```

## 相关链接

- [Context windows - Claude API Docs](https://platform.claude.com/docs/en/build-with-claude/context-windows)
- [What's new in Claude 4.5](https://docs.claude.com/en/docs/about-claude/models/whats-new-claude-4-5)
- [Claude 4 best practices - Context awareness](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices#context-awareness-and-multi-window-workflows)
