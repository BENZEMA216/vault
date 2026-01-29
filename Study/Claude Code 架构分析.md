# Claude Code 架构分析

> 来源：多个逆向工程分析、官方文档、GitHub 开源项目
> - https://cc.deeptoai.com/docs/en/advanced/decoding-claude-code-analysis
> - https://www.interconnects.ai/p/claude-code-hits-different
> - https://github.com/Piebald-AI/claude-code-system-prompts
> - https://weaxsey.org/en/articles/2025-10-12/
> 阅读日期：2025-01-28

---

## 一、核心设计哲学

> "Architectural simplicity at every juncture"
> — 在每个决策点都选择架构简单性

Claude Code 的成功不在于复杂的多 Agent 系统，而在于**简单但精心设计的单循环架构 + 智能委派**。

---

## 二、整体架构

### 分层 Agent 系统

```
┌─────────────────────────────────────────────┐
│              Main Agent (Sonnet 4)          │
│    - 任务编排 & 决策                         │
│    - TodoWrite 状态管理                      │
│    - 工具调用决策                            │
└──────────────────┬──────────────────────────┘
                   │ Task 工具
     ┌─────────────┼─────────────┐
     ▼             ▼             ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│   Explore   │ │   General   │ │    Plan     │
│   (Haiku)   │ │  (Sonnet)   │ │  (Sonnet)   │
│   只读工具   │ │   全工具    │ │   只读工具   │
│   516 tok   │ │   294 tok   │ │   633 tok   │
└─────────────┘ └─────────────┘ └─────────────┘
```

**关键约束**：最多只有一层 Sub-Agent（不能再嵌套），保持架构简单。

### 主循环流程

```
用户输入 → 配额检查 → 话题检测 → Main Agent 处理
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
               直接执行工具      委派给 Sub-Agent     请求用户输入
                    │                 │                 │
                    └─────────────────┼─────────────────┘
                                      ▼
                                 输出给用户
```

---

## 三、模块化 Prompt 系统

### 不是单一 Prompt，而是可组合模块

Claude Code 使用 **110+ 个独立 prompt 片段**，根据上下文动态组装：

| 类别 | 数量 | 说明 |
|------|------|------|
| 核心系统指令 | ~10 | 基础行为规范 |
| 工具描述 | 18 | 每个 122-2,250 tokens |
| Sub-agent prompts | 5+ | Explore, Plan, General 等 |
| System reminders | ~40 | 状态通知、模式切换 |

### 动态组装

```
基础 prompt
    + 当前模式 prompt (plan mode / learning mode / delegate mode)
    + 可用工具描述
    + 可用 agent 描述
    + 系统提醒
    = 最终 system prompt
```

**模板变量**：prompt 中包含插值变量，根据运行时状态填充工具名、agent 列表等。

---

## 四、工具分层设计

### 18 个内置工具

| 层级 | 工具 | 功能 |
|------|------|------|
| **只读** (11个) | Glob, Grep, Read, WebFetch, WebSearch, TodoWrite, KillShell, BashOutput, LSP | 信息获取 |
| **编辑** (3个) | Edit, Write, NotebookEdit | 文件修改 |
| **执行** (1个) | Bash | 命令执行（带限制） |
| **规划** (2个) | EnterPlanMode, ExitPlanMode | 任务规划 |
| **协作** (2个) | Task, AskUserQuestion | Agent 调度 |

### Bash 工具的特殊限制

```
禁止：
- 内嵌 grep/find（使用专用工具）
- 相对路径（强制绝对路径）
- 未引用的文件路径

允许：
- 标准命令执行
- Git 操作
- 构建/测试命令
```

### 工具 Token 成本

```
最小：AskUserQuestion ~122 tokens
最大：Bash ~2,250 tokens（包含 Git 工作流指令）
平均：~500-800 tokens per tool
```

---

## 五、Sub-Agent 专业化

### 五类 Sub-Agent

| Agent | 模型 | 工具权限 | 职责 |
|-------|------|----------|------|
| **Explore** | Haiku | 只读 (Glob, Grep, Read, 有限 Bash) | 代码库探索、快速搜索 |
| **General-Purpose** | Sonnet | 全部 | 复杂多步研究任务 |
| **Plan** | Sonnet | 只读 | 实现策略设计 |
| **Output-Style-Setup** | — | 特定 | 修改 markdown 配置 |
| **Session-Memory** | — | Edit only | 跨会话上下文存储 |

### 委派原则

> "Use the Task tool with specialized agents when the task matches the agent's description"

Main Agent 主动将匹配的任务委派给专用 Agent，而非自己处理一切。

---

## 六、成本优化策略

### 模型选择策略

```
┌─────────────────────────────────────┐
│         50%+ 调用使用 Haiku          │
│  (Explore agent, Bash 输出摘要等)    │
├─────────────────────────────────────┤
│         核心决策使用 Sonnet          │
│  (Main Agent, 复杂推理, 上下文压缩)   │
└─────────────────────────────────────┘
```

### 成本公式（论文中的通用公式）

```
Cost_agent ≈ α × N_tok + I_tool × Cost_tool + I_mem × Cost_mem + I_retry × Cost_retry
```

Claude Code 的优化方式：
- **减少 N_tok**：上下文压缩、输出精简（≤4 行）
- **降低 Cost_tool**：使用 Haiku 处理简单任务
- **减少 I_retry**：高质量工具设计减少重试

---

## 七、上下文管理

### 五种机制

| 机制 | 功能 | 实现 |
|------|------|------|
| **Agent 隔离** | Sub-Agent 上下文与 Main Agent 分离 | 减少冗余 |
| **TodoWrite** | 实时任务状态追踪 | pending → in_progress → completed |
| **Bash 输出压缩** | Haiku 压缩冗长日志 | 构建输出、测试结果 |
| **Compact/Summarize** | 对话压缩 | Sonnet 处理，保留关键代码 |
| **Session Memory** | 跨会话持久化 | 启动摘要 ≤50 字符 |

### claude.md 上下文文件

```markdown
# CLAUDE.md

项目级配置，包含：
- 项目结构说明
- 编码规范
- 常用命令
- 特定指令
```

Main Agent 启动时自动读取，作为持久上下文。

---

## 八、搜索策略：LLM-native

### 不使用 RAG / 向量数据库

```
传统方式：
文件 → Embedding → 向量库 → 相似度检索 → 结果

Claude Code 方式：
ripgrep + jq + find → 原始结果 → LLM 语义理解
```

### 原因分析

| 方面 | RAG | LLM-native |
|------|-----|------------|
| 设置复杂度 | 高（需要 embedding、向量库） | 低（标准工具） |
| 语义理解 | 依赖 embedding 质量 | LLM 直接理解 |
| 实时性 | 需要索引更新 | 实时搜索 |
| 适用场景 | 大规模静态文档 | 动态代码库 |

---

## 九、输出哲学

### 最小输出原则

> "fewer than 4 lines unless detail requested"

```
✓ 简洁回答具体问题
✓ 代码引用格式：file_path:line_number
✓ 只处理请求的内容

✗ 主动添加额外信息
✗ 冗长解释
✗ 不必要的重复
```

### 安全约束

> "Assist with defensive security tasks only; refuse malicious code creation"

---

## 十、Git 工作流集成

### 并行执行

```bash
# 同时执行（无依赖）
git status &
git diff &
git log &
```

### 提交格式

```bash
git commit -m "$(cat <<'EOF'
Commit message here.

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
```

### Pre-commit 重试

如果 pre-commit hook 失败：
1. 分析错误
2. 修复问题
3. **创建新 commit**（不是 amend，避免覆盖之前的提交）

---

## 十一、架构对比：Claude Code vs 学术方案

| 方面 | 学术论文方案 | Claude Code 实际方案 |
|------|-------------|---------------------|
| Agent 架构 | 复杂多 Agent、树状结构 | 简单两层（Main + Sub） |
| 记忆系统 | 外部数据库、向量存储 | 文件系统 + 上下文压缩 |
| 搜索 | RAG + Embedding | ripgrep + LLM |
| 规划 | MCTS、A* 搜索 | 简单任务列表（TodoWrite） |
| 成本控制 | 理论公式 | 模型分层（Haiku/Sonnet） |

**核心洞察**：工业实践往往选择简单可控的方案，而非理论最优的复杂方案。

---

## 十二、与 Creative CoWork 的关联

根据 [[01 Creative CoWork 一句话介绍]] 的产品设计：

| Claude Code 特性 | Creative CoWork 对应 | 建议 |
|------------------|---------------------|------|
| TodoWrite 任务管理 | 上下文容器中的任务追踪 | 实现类似的任务状态机 |
| 模块化 Prompt | Skills 模块化能力 | Skills 动态加载/组合 |
| Agent 分层 | AGENT CORE + 专用 Skills | 限制嵌套层数 |
| LLM-native 搜索 | 素材/上下文智能检索 | 优先用 LLM 理解而非 RAG |
| claude.md 上下文 | 项目级配置 | 支持 .creativecowork 配置文件 |
| 动态 Prompt 组装 | GENUI 动态界面生成 | 根据 Skills 动态构建 |
| 输出精简 | 创作结果展示 | 默认简洁，按需展开 |

### 具体借鉴

```
1. 架构简单优先
   - 不追求复杂的多 Agent 嵌套
   - 最多两层（Main AGENT + 专用 Skill）

2. 成本分层
   - 简单任务用便宜模型
   - 复杂决策用强模型

3. 上下文管理
   - 项目级配置文件
   - 会话压缩
   - 任务状态追踪

4. 工具设计
   - 清晰的工具分类（只读/编辑/执行）
   - 工具描述标准化
   - 工具按需加载（参考 [[Tool Search Tool 笔记]]）
```

---

## 十三、开源资源

| 资源 | 链接 | 说明 |
|------|------|------|
| System Prompts | https://github.com/Piebald-AI/claude-code-system-prompts | 79+ 版本的 prompt 提取 |
| 逆向分析 | https://github.com/Yuyz0112/claude-code-reverse | 架构逆向工程 |
| 非官方文档 | https://cc.deeptoai.com | 详细架构解读 |

---

## 十四、关键结论

1. **简单架构胜过复杂系统**：单循环 + 一层 Sub-Agent 足以处理复杂任务
2. **工具质量比数量重要**：18 个精心设计的工具优于 100 个粗糙工具
3. **LLM 理解力被低估**：直接用 LLM 处理搜索结果，无需复杂 RAG
4. **成本优化在模型选择**：50%+ 用 Haiku 大幅降低成本
5. **上下文管理是核心竞争力**：压缩、隔离、持久化三管齐下

---

*笔记整理于 2025-01-28*
