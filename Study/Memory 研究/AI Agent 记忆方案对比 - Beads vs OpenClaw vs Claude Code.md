# AI Agent 记忆方案对比：Beads vs OpenClaw vs Claude Code

> 整理日期：2026-02-04
> 相关项目：[[Creative CoWork]]

---

## 一、背景

AI 编程 / 创作 Agent 面临一个共同问题：**跨会话记忆缺失**。每次启动都是"失忆"状态，用户需要反复解释项目上下文。2025-2026 年间，社区涌现出多种记忆方案，思路各异。本文对比三种代表性方案：

| 方案 | 作者 | Stars | 定位 |
|------|------|-------|------|
| **[Beads (bd)](https://github.com/steveyegge/beads)** | Steve Yegge | 14.7k | 分布式 Git 任务追踪器 |
| **[OpenClaw](https://github.com/openclaw/openclaw)**（原 ClawdBot/Moltbot） | Peter Steinberger | 100k+ | 本地 AI 个人助手 |
| **Claude Code** + 社区插件 | Anthropic + 社区 | N/A | AI 编码 Agent |

---

## 二、定位差异

| 维度 | Beads | OpenClaw | Claude Code |
|------|-------|----------|-------------|
| **核心定位** | 任务追踪系统（结构化记忆） | 个人 AI 助手（通用记忆） | 编码 Agent（项目指令） |
| **记忆对象** | 任务、依赖关系、工作流 | 对话、偏好、事实、日常笔记 | 项目规范、编码约定、决策 |
| **数据模型** | DAG 图（有向无环图） | Markdown + JSONL 转录 | 层级化 Markdown |
| **协作模式** | 多 Agent / 多分支并行 | 单用户多 Agent | 单用户单会话 |
| **主要语言** | Go | TypeScript | N/A（闭源产品） |

---

## 三、记忆架构详解

### 3.1 Beads：图数据库思路

```
JSONL (.beads/issues.jsonl)   ← Git 跟踪的真实数据源
    ↕ 自动同步（500ms debounce）
SQLite 本地缓存               ← 快速查询 + 物化视图
    ↕ Pull-First 三路合并
远程 Git 仓库                  ← 多人/多 Agent 协作
```

**核心特点：**

- 记忆是**结构化的任务节点 + 依赖边**，不是自然语言
- Agent 通过 `bd ready` 查询"下一步该做什么"，而非"我之前做了什么"
- 19 种依赖类型（blocks、parent-child、conditional-blocks、waits-for 等）
- 层级化哈希 ID（`bd-a3f8.1.1`），自适应 4-6 位，防并行冲突
- 支持 Molecules 工作流模板（化学隐喻：Solid → Liquid → Vapor）
- Wisp（幽灵 issue）：临时工作永不污染 git 历史

**记忆衰减（Compaction）：**

| 级别 | 触发条件 | 效果 |
|------|----------|------|
| Tier 1 | 关闭 30+ 天，无开放依赖 | LLM 压缩到 ~200 词 |
| Tier 2 | 关闭 90+ 天，已 Tier 1 | 进一步压缩到 ~50 词 |

**同步策略（Pull-First 三路合并）：**

```
1. 加载本地状态（SQLite → 内存）
2. 加载基线状态（sync_base.jsonl）
3. git pull 拉取远程
4. 三路合并：
   - 标量字段（title/status）→ LWW（Last-Write-Wins）
   - 集合字段（labels/deps）→ Union（取并集）
   - 评论 → Append（追加去重）
5. 导入 → SQLite → 导出 → JSONL → git commit + push
```

**文件结构：**

| 文件 | 用途 | Git 跟踪 |
|------|------|----------|
| `.beads/issues.jsonl` | 当前状态 | 是 |
| `.beads/sync_base.jsonl` | 上次同步基线 | 否 |
| `.beads/beads.db` | SQLite 缓存 | 否 |
| `.beads/bd.sock` | 守护进程 Socket | 否 |

---

### 3.2 OpenClaw：文件系统即记忆

```
MEMORY.md                     ← 长期记忆（决策、偏好、事实）
memory/YYYY-MM-DD.md          ← 每日记忆笔记
JSONL Transcripts             ← 完整审计日志（每条消息/工具调用）
    ↕
SQLite (sqlite-vec + FTS5)    ← 向量索引 + 全文检索
```

**核心特点：**

- **两层记忆源**：Markdown 是真实数据源，SQLite 做检索索引
- **混合检索**：向量相似度（语义搜索）+ FTS5 关键词匹配，加权融合
  - 搜索 "authentication bug" 能同时命中语义近义词 "login issues" 和精确术语
- 语义搜索分块：~400 token 目标，80 token 重叠
- 上下文压缩前自动 flush 记忆到文件
- 索引存储在 `~/.openclaw/memory/<agentId>.sqlite`
- 文件 watcher（1.5s debounce）标记索引 dirty，异步同步

**可选后端 - QMD：**

- 本地 BM25 + 向量 + reranking
- 通过 Bun + node-llama-cpp 运行，自动下载 GGUF 模型
- 无需 Ollama 等外部依赖

**系统架构四组件：**

```
Gateway（网关，连接 Telegram/Slack 等）
    ↓
Agent（推理引擎/LLM）
    ↓
Skills（模块化能力：Shell、浏览器、文件系统）
    ↓
Memory（Markdown + 检索层）
```

---

### 3.3 Claude Code：层级化指令注入

**原生方案 - 4 层优先级：**

```
~/.claude/CLAUDE.md           ← Level 4：用户级（个人偏好）
.claude/rules/*.md            ← Level 3：项目规则（模块化）
./CLAUDE.md                   ← Level 2：项目根目录（主记忆）
Enterprise Policy             ← Level 1：企业策略（最高优先级）
```

- 支持 `@path/to/import` 导入语法
- 子目录 CLAUDE.md 按需加载（访问该子树时才注入）
- **本质是"每次启动前强制注入的 system prompt"**，不是真正的记忆

**社区记忆插件（2026 年初爆发）：**

| 项目 | 机制 | 特点 |
|------|------|------|
| **[memory-mcp](https://github.com/yuvalsuede/memory-mcp)** | Hook → Haiku 提取 → 分类存储 | architecture 永久，progress 7 天衰减，context 30 天衰减 |
| **[claude-mem](https://github.com/thedotmack/claude-mem)** | 5 个生命周期 Hook → Worker 异步处理 → SQLite + ChromaDB | 3 层渐进检索，10x token 节省。24 小时 1739 Stars |
| **claude-cortex** | PreCompact/SessionStart/SessionEnd Hook | 类脑记忆，访问频率 × 1.2 加权，短期自动固化为长期 |
| **Severance** | Hook → 嵌入 + 注释 + 知识图谱 | 语义记忆 + 关系图谱 |

**社区趋同的分层架构：**

- **Tier 1（CLAUDE.md）**：~150 行，每次启动全量注入，按 confidence × accessCount 排序
- **Tier 2（深度回忆）**：SQLite / ChromaDB 存储，通过 MCP 工具按需检索

**Hook 生命周期：**

```
SessionStart → UserPromptSubmit → PostToolUse → Stop → PreCompact → SessionEnd
```

> PreCompact 被称为"特别优雅的设计"——把上下文压缩当作一等生命周期事件，而不是需要恢复的意外。

---

## 四、核心设计哲学对比

| 设计决策 | Beads | OpenClaw | Claude Code + 社区 |
|----------|-------|----------|---------------------|
| **存储格式** | JSONL（结构化） | Markdown（自然语言） | Markdown（指令式） |
| **索引方式** | SQLite 关系查询 | 向量 + FTS5 混合 | 无索引 / SQLite+ChromaDB |
| **检索方式** | 图遍历（ready/blocked） | 语义搜索 + 关键词 | 全量注入 / MCP 按需查 |
| **同步机制** | Git 三路合并 | 文件 watcher + debounce | 无（文件即状态） |
| **多 Agent 支持** | 原生（哈希 ID 防冲突） | 支持（多 workspace） | 不支持 |
| **记忆衰减** | LLM 语义压缩（30/90 天） | 时间衰减（7/30 天） | 社区方案有 |
| **人类可读性** | 低（JSONL + 哈希 ID） | 高（纯 Markdown） | 高（纯 Markdown） |
| **守护进程** | 有（per-workspace daemon） | 有（gateway 后台服务） | 无 |
| **上下文窗口友好度** | 高（结构化 + 压缩） | 中（渐进检索） | 低（全量注入）/ 高（社区方案） |

---

## 五、性能数据

| 指标 | Beads | OpenClaw | Claude Code |
|------|-------|----------|-------------|
| 10K 任务查询 | 29ms（blocked cache） | - | - |
| 同步延迟 | <500ms（事件驱动） | 1.5s（watcher debounce） | 无同步 |
| Daemon 内存 | ~30-35MB | - | 无 daemon |
| 记忆压缩 | LLM 语义（~200 → ~50 词） | 分层衰减 | ~150 行预算分配 |

---

## 六、关键洞察

### 6.1 三者解决不同层面的问题

```
Beads    → "接下来该做什么？"（任务编排记忆）
OpenClaw → "我之前了解到什么？"（知识/事实记忆）
Claude Code → "这个项目的规矩是什么？"（规范/指令记忆）
```

一个完整的 Agent 记忆系统可能需要三者的组合。

### 6.2 Beads 的独特价值

不是"记忆"系统，是**任务编排系统**。对长周期、多步骤任务（如重构大模块），结构化的依赖图比自然语言记忆有效得多。多 Agent 并行的 Git 三路合并也是其他方案没有的。

### 6.3 OpenClaw 的记忆最"人性化"

Markdown 作为记忆源，人类可以直接编辑、用 Obsidian 管理。向量 + 关键词混合检索适合模糊回忆场景。但记忆质量依赖 LLM 提取准确度。

### 6.4 Claude Code 社区爆发说明什么

2026 年初 claude-mem 24 小时 1739 Stars，说明"跨会话记忆"是 AI 编码 Agent 最强烈的未满足需求之一。Anthropic 原生提供了 Hook 机制但没有做完整的记忆方案，把这个空间留给了社区。

### 6.5 共同趋势

- **文件系统是记忆的最佳载体**：三者都用文件（JSONL / Markdown）而非纯数据库
- **分层是必须的**：热数据全量注入，冷数据按需检索
- **衰减是必须的**：无限积累的记忆会淹没上下文窗口
- **Git 是天然的同步/版本管理方案**

---

## 七、对 Creative CoWork 的启示

Creative CoWork 的"上下文容器"概念比这三者都更广——不只是任务追踪或对话记忆，而是**项目级的全量上下文聚合**（素材、生成结果、参考、对话历史）。可借鉴的点：

| 来源 | 可借鉴的设计 | 适用场景 |
|------|------------|----------|
| Beads | 依赖图 + `ready` 查询 | 创作流水线（分镜 → 配图 → 配音 → 合成） |
| Beads | Wisp（临时任务不污染历史） | Agent 试验性操作 |
| Beads | 记忆衰减（LLM 语义压缩） | 长期项目的旧版本/旧素材管理 |
| OpenClaw | Markdown 源文件 + 向量检索 | 创作者友好的记忆界面 |
| OpenClaw | 混合检索（语义 + 关键词） | 素材/参考的模糊搜索 |
| Claude Code | Hook 生命周期（PreCompact） | Agent 上下文压缩前自动保存关键信息 |
| Claude Code 社区 | 分层记忆（热数据注入 + 冷数据检索） | 平衡上下文窗口和信息完整性 |

---

## 参考链接

- [Beads GitHub](https://github.com/steveyegge/beads)
- [OpenClaw Memory 官方文档](https://docs.openclaw.ai/concepts/memory)
- [OpenClaw Memory Deep Dive](https://snowan.gitbook.io/study-notes/ai-blogs/openclaw-memory-system-deep-dive)
- [OpenClaw Architecture Guide](https://vertu.com/ai-tools/openclaw-clawdbot-architecture-engineering-reliable-and-controllable-ai-agents/)
- [Claude Code Memory 官方文档](https://code.claude.com/docs/en/memory)
- [Persistent Memory for Claude Code (DEV)](https://dev.to/suede/the-architecture-of-persistent-memory-for-claude-code-17d)
- [claude-mem GitHub](https://github.com/thedotmack/claude-mem)
- [memory-mcp GitHub](https://github.com/yuvalsuede/memory-mcp)
- [OpenClaw Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)
