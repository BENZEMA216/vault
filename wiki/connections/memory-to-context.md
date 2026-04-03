# 记忆研究 → 上下文工程的关联

> 记忆系统和上下文管理是同一枚硬币的两面：记忆决定"保留什么"，上下文决定"加载什么"。

## 关联总览

```
记忆研究（持久化层）              上下文工程（运行时层）
────────────────────────       ────────────────────────────
Beads / OpenClaw / CC          → 不同的上下文持久化策略
MemEvolve                      → 上下文自动进化
SWE-Pruner                     → 上下文裁剪优化
Progressive Disclosure          → 记忆的按需加载
Claude Context Awareness        → Token 预算感知
active_context.md 温度模型      → 实践中的记忆层级
```

## 1. Beads / OpenClaw / Claude Code → 不同的上下文持久化策略

三种记忆方案本质上是三种不同的**上下文持久化策略**，解决不同层面的"下次启动时还记得什么"问题。

| 方案 | 持久化策略 | 上下文质量 | 适用场景 | 来源 |
|------|-----------|-----------|----------|------|
| **Beads** | JSONL 结构化任务图 + SQLite 缓存 | 高（结构化、可查询） | 任务编排——"接下来该做什么？" | [[raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code]] |
| **OpenClaw** | Markdown 源文件 + 向量检索 + FTS5 | 中（语义模糊匹配） | 知识回忆——"我之前了解到什么？" | 同上 |
| **Claude Code** | 层级化 CLAUDE.md + 社区 Hook 插件 | 低→高（原生低，社区方案高） | 规范注入——"这个项目的规矩是什么？" | 同上 |

### 对 Creative CoWork 上下文容器的启示

[[concepts/context-container]] 需要融合三种策略：

| 上下文类型 | 对应策略 | 实现思路 |
|-----------|----------|----------|
| 创作流水线（分镜→生图→视频→剪辑） | Beads 的依赖图 + `ready` 查询 | 任务节点 + 依赖边，Agent 查"下一步做什么" |
| 素材/参考/灵感积累 | OpenClaw 的 Markdown + 混合检索 | 人类可编辑的文件 + 向量语义搜索 |
| 项目规范/风格指南/角色定义 | Claude Code 的层级注入 | 每次启动全量注入核心规范 |

### 共同趋势

三者的共识对上下文工程的启示：
- **文件系统是最佳载体**：不用纯数据库，文件可被人类直接编辑和 Git 管理
- **分层是必须的**：热数据全量注入，冷数据按需检索
- **衰减是必须的**：无限积累的记忆会淹没上下文窗口

## 2. MemEvolve → 上下文自动进化

### 论文核心

- [[raw/papers/agent-memory/MemEvolve_论文笔记]]
- 现有记忆系统是**静态的**——记忆帮 Agent 积累经验，但记忆架构本身无法自适应调整
- MemEvolve 的双层进化：内循环（经验进化）+ 外循环（架构进化）
- Training-free：通过 LLM 生成/修改记忆系统代码，不训练权重

### 对上下文工程的关联

| MemEvolve 概念 | 上下文工程对应 | Creative CoWork 应用 |
|---------------|--------------|---------------------|
| **内循环：经验进化** | 上下文内容随使用自动更新 | 项目素材库随创作过程自动扩充，旧版本自动归档 |
| **外循环：架构进化** | 上下文组织方式随项目进化 | /init 推荐的 Skills 组合随用户习惯调整 |
| **Encode（结构化记忆）** | 上下文的结构化提取 | 从对话中自动提取角色定义、风格决策、审核标准 |
| **Manage（记忆质量维护）** | 上下文清理和去重 | 自动合并重复素材、淘汰过时的风格参考 |

### 核心洞察

MemEvolve 提示一个重要方向：Creative CoWork 的上下文容器不应是**静态存储**，而应是**自进化系统**。项目进行过程中，上下文的组织方式、检索策略、分层比例都应该自动调整。

## 3. SWE-Pruner → 上下文裁剪优化

### 论文核心

- [[raw/papers/context-engineering/SWE-Pruner 论文笔记]]
- 76% 的 token 消耗在"读取"操作（对 Coding Agent）
- 0.6B 轻量模型，根据当前目标动态裁剪代码上下文
- 节省 23-54% token 成本，几乎不损失任务成功率
- 关键创新：Goal Hint（目标提示）让裁剪模型知道"当前在找什么"

### 对上下文工程的关联

| SWE-Pruner 机制 | 上下文工程对应 | Creative CoWork 应用 |
|----------------|--------------|---------------------|
| **Goal Hint** | 上下文按当前任务目标过滤 | 在"生成分镜"阶段只加载角色定义和场景参考，不加载配乐素材 |
| **Neural Skimmer** | 轻量模型预过滤上下文 | 小模型对素材库做相关性评分，只传递高相关素材给主 Agent |
| **CRF 层保持结构连贯** | 上下文裁剪保持语义完整性 | 不能只裁掉角色描述的一半，要么完整保留要么完整跳过 |
| **中间件集成** | 上下文裁剪作为 Agent 和数据之间的中间层 | 在 Context Container 和 Agent Loop 之间加入裁剪层 |

### 核心洞察

SWE-Pruner 的"模仿程序员选择性略读"思路直接适用于创作场景。创作者在处理一个分镜时，不需要看所有 60 集的素材——只需要看当前场景的角色、当前集的上下文、风格参考。裁剪层可以大幅减少 Agent 的上下文负担。

## 4. Progressive Disclosure → 记忆的按需加载

### 概念核心

- [[concepts/progressive-disclosure]]
- 信息按需分层加载，避免上下文过载
- 应用在 [[concepts/skills-system]]（按需加载 Skill）和 [[concepts/context-container]]（按需加载素材）

### 与记忆系统的关联

Progressive Disclosure 本质上是**记忆检索策略在界面层的体现**：

| 记忆层 | 加载时机 | 对应 Progressive Disclosure 层级 |
|--------|---------|--------------------------------|
| L0：项目核心身份（角色定义/风格指南） | 项目启动时全量注入 | 第一层：始终可见 |
| L1：当前任务上下文（当前集的分镜/素材） | 进入具体任务时加载 | 第二层：任务触发 |
| L2：历史素材/参考（旧集素材、废弃方案） | Agent 主动检索或用户搜索 | 第三层：按需搜索 |
| L3：归档数据（已完结项目、旧版本） | 极少访问，压缩存储 | 第四层：深度归档 |

### 与 Claude Code 社区记忆方案的对比

Claude Code 社区的分层架构与 Progressive Disclosure 高度一致：

| 社区方案 | Tier 1（热数据） | Tier 2（冷数据） |
|---------|-----------------|-----------------|
| memory-mcp | architecture 永久注入 | progress 7 天衰减，context 30 天衰减 |
| claude-mem | ~150 行 CLAUDE.md | SQLite + ChromaDB 按需检索 |
| claude-cortex | 高频访问记忆（accessCount × 1.2） | 低频记忆自动固化为长期存储 |

## 5. Claude Context Awareness → Token 预算感知

### 机制核心

- [[raw/papers/context-engineering/Claude Context Awareness 机制]]
- 对话开始注入总预算：`<budget:token_budget>200000</budget:token_budget>`
- 每次工具调用后更新剩余量：`Token usage: 35000/200000; 165000 remaining`
- 模型被训练来理解和使用这个预算信息

### 对记忆系统的关联

Token 预算感知让**记忆加载策略可以动态调整**：

| Token 剩余 | 记忆加载策略 | 行为 |
|-----------|------------|------|
| 充裕（>70%） | 宽松加载 | 广泛探索素材库，加载更多参考 |
| 中等（30-70%） | 标准加载 | 只加载当前任务直接相关的上下文 |
| 紧张（<30%） | 最小加载 | 只保留核心角色定义，触发上下文压缩 |

### 与 PreCompact Hook 的协同

Claude Code 的 PreCompact 被称为"特别优雅的设计"——把上下文压缩当作一等生命周期事件。结合 Context Awareness：

```
Token 预算感知 → 检测到 <30% 剩余 → 触发 PreCompact
→ 记忆系统在压缩前自动提取关键信息 → 写入持久化存储
→ 压缩后的上下文保留核心要素，释放窗口空间
```

这形成了一个记忆保全闭环：**上下文压缩不再意味着信息丢失，而是信息从"运行时"迁移到"持久化层"**。

## 6. active_context.md 温度模型 → 实践中的记忆层级

### 知识库实践

vault-ak 自身使用的记忆温度分层就是这些研究理论的实践验证：

| 温度 | 文件 | 对应概念 | 更新频率 |
|------|------|---------|---------|
| **热** | active_context.md | 工作记忆（Working Memory） | 每次对话更新 |
| **温** | Projects/ 目录 | 情景记忆（Episodic Memory） | 项目活跃期更新 |
| **冷** | Study/ 目录 | 语义记忆（Semantic Memory） | 偶尔添加、极少修改 |

### 与研究的映射

| 理论层 | 记忆研究对应 | 上下文工程对应 | 实践验证 |
|--------|------------|--------------|---------|
| 工作记忆 | Beads `ready` 查询结果 | Context Awareness 的当前预算状态 | active_context.md（当前优先级、阻塞项） |
| 短期记忆 | OpenClaw 的每日笔记 | SWE-Pruner 的 Goal Hint 过滤结果 | Projects/（进行中的项目文档） |
| 长期记忆 | Claude Code CLAUDE.md | Progressive Disclosure L3-L4 | Study/（论文、方法论、长期参考） |
| 元记忆 | MemEvolve 外循环 | 上下文策略的自动调整 | CLAUDE.md 本身的更新规则 |

## 综合框架：Creative CoWork 上下文-记忆统一架构

```
┌─────────────────────────────────────────────────────────────┐
│                    Creative CoWork 上下文层                    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  运行时层（上下文工程）                                  │    │
│  │  ┌───────────┐  ┌──────────┐  ┌───────────────┐   │    │
│  │  │ Context   │  │ SWE-     │  │ Progressive   │   │    │
│  │  │ Awareness │→ │ Pruner   │→ │ Disclosure    │   │    │
│  │  │ (预算感知) │  │ (裁剪)   │  │ (分层加载)    │   │    │
│  │  └───────────┘  └──────────┘  └───────────────┘   │    │
│  └────────────────────────┬────────────────────────────┘    │
│                           │                                 │
│  ┌────────────────────────▼────────────────────────────┐    │
│  │  持久化层（记忆系统）                                    │    │
│  │  ┌──────────┐  ┌──────────┐  ┌────────────────┐   │    │
│  │  │ Beads式  │  │ OpenClaw │  │ Claude Code 式 │   │    │
│  │  │ 任务图   │  │ 语义检索  │  │ 规范注入       │   │    │
│  │  └──────────┘  └──────────┘  └────────────────┘   │    │
│  │          ↑                                         │    │
│  │     MemEvolve（架构自进化）                           │    │
│  └────────────────────────────────────────────────────┘    │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  实践验证层                                           │    │
│  │  热: active_context.md                               │    │
│  │  温: Projects/（上下文容器实例）                         │    │
│  │  冷: Study/（长期知识库）                               │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

## 核心洞察

1. **记忆和上下文是同一问题的两面**：记忆解决"保留什么"，上下文解决"加载什么"。好的系统需要两者统一设计。
2. **分层是唯一共识**：所有方案（学术+工业+社区）都收敛到分层架构。
3. **自进化比静态优化更重要**：MemEvolve 的外循环思路值得应用——上下文策略本身需要随项目进化。
4. **预算感知改变一切**：当 Agent 知道自己还剩多少 token，记忆加载从"被动填满"变为"主动规划"。
5. **实践验证理论**：vault-ak 自身的温度分层模型就是这些研究理论的活体验证。

## 相关来源

- [[raw/articles/memory-research/AI Agent 记忆方案对比 - Beads vs OpenClaw vs Claude Code]]
- [[raw/papers/agent-memory/MemEvolve_论文笔记]]
- [[raw/papers/agent-memory/Distilling Feedback into Memory-as-a-Tool]]
- [[raw/papers/context-engineering/SWE-Pruner 论文笔记]]
- [[raw/papers/context-engineering/Claude Context Awareness 机制]]
- [[raw/papers/context-engineering/Context Management 技术方案汇总]]
- [[concepts/context-container]]
- [[concepts/progressive-disclosure]]
- [[concepts/agent-memory]]

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
