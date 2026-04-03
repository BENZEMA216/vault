# 0814 - 【想想】Plan Mode / Auto accept 的思考和想法

## 对 Claude Code 的感悟和体会

> 资料：
> 1. [Claude Code Best Practice](https://www.anthropic.com/engineering/claude-code-best-practices)
> 2. 0807 - 【学习】细看一下 CC 的实现主要是 Prompt
> 3. [也许是 Coding Agent 的参考答案 -- 看完 Claude Code 第 257 个请求后](https://bytetech.info/articles/7527928017570496555)
> 4. Aime 1.5：构建能够动态演进的下一代 AI 智能体
> 5. 0615 - 【学习】多种 AGENT 架构 Devin、Anth、Google

### 我对 Plan Mode 的理解
- **观点一，Plan Mode 的核心目的**：是抛开工具，在特定的 SP 下，完成对用户任务更细节的分析，给出更高质量的todo list、.md文件等，来帮助后续 AGENT 更好的执行任务。
- **观点二，Plan Mode 的实现：很简单就是 SP**
  - 可以看资料 3，claude code 整体都是通过 PE 加上简单的执行链路（仅有对 todolist 的遵循）去进行的。大道至简。
  - 所以基于他的实现，plan mode 实际上就是通过一个 SP 加上不感知其他工具来完成的，然后在通过上下文，让后续执行过程感知到。
  - claude.md 就是在每次项目初始化的时候放在最开头，项目初始化后，所有信息都是用上下文来进行感知（除非触发压缩了）
### 我对 hook 的理解
- **观点一，自定义指令的核心目的是**：稳定执行一个 todolist 或者工具链，或者以特定手法使用某个工具
- **观点二，自定义指令的实现是：**还是通过SP手段构成的类似 MCP 工具，也有 args，并通过特殊指令来实现
```json
{
    "content": [
        {
            "type": "text",
            "text": "<command-message>optimize is running…</command-message>\n<command-name>/optimize</command-name>\n<command-args>draft.md</command-args>"
        },
        {
            "type": "text",
            "text": "Analyze this code for performance issues and suggest optimizations:\n\n\nARGUMENTS: draft.md"
        }
    ]
}
```

### 我对 sub agent 的理解
- **观点一，subagent 的目的是完成一个隔离上下文能完成的子任务，并行提高效率**
- **观点二，subagent 的实现是通过把 subagent 构建成工具，并通过异步执行的方式来进行。**

## 创作 AGENT 的想法

> 先抄Claude Code 的思想和理念，基于现有场景做实验，来感受效果的变化，再想怎么上产品。

自定义指令和subagent 都是为了稳定和效率，我们先研究 Plan mode 和上下文。
### 实验方案
1. 通过隔离工具的 LLM，让 LLM 和用户沟通，尝试完成下列任务：
  1. **明确任务执行的所有子步骤**，子步骤具体细粒度可能分场景，我们都用作 60s 广告作为例子，子步骤可能是：
    1. 确定广告脚本
    1. 广告分镜
    1. 完成动态分镜
    1. 生图
    1. 生视频（可能就做到这儿）
    1. 配音配乐剪辑 巴拉巴拉
  1. **明确任务执行的标准**（是否需要重新抽卡）
    1. 主体一致性
    1. 创意性达到 XXXX，或者必须使用 XXX 的手法。
    1. 任务完成的完备性 等
  1. 上述任务的完成可能需要 搜索以及不停和用户的讨论，并最终通过上下文和 .md文档承载
1. 完成 Plan mode 后，把步骤给 host，把标准给 reviewer，然后看看在复杂任务上的完成度。
### 自定义指令 和 subagent
- 自定义指令可以稳定地让用户拿到某个特定效果，或能承载用户某个独特用法
- subagent 就是提效的，先忽略。
