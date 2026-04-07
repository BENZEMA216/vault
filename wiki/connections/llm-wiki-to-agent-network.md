# LLM Wiki Pattern → Agent Network 演化路径

> 把 Karpathy 的 [[concepts/llm-wiki-pattern|个人 LLM Wiki 范式]] 沿四个 phase 自然推到 [[concepts/knowledge-agent-network|Knowledge Agent Network]]，最后落到 Knowledge Economy。每一步都有现成的工程依据，不是空想。

## 演化路径

```
Phase 1: Personal LLM Wiki (AK pattern)        ← 已实践（本 vault 自身）
        ingest / query / lint / log
              ↓
Phase 2: Wiki Agent (MCP server)               ← 已部署（wiki-mcp-server on Railway）
        把 wiki 暴露为可被任何 LLM 调用的 endpoint
              ↓
Phase 3: Knowledge Agent Network               ← 协议设计中
        多个 Wiki Agent 互相发现 / 查询 / 路由
              ↓
Phase 4: Knowledge Economy                     ← 远期，all-in 级别
        定价 / 分润 / 声誉 / marketplace
```

## Phase 1: 为什么 AK pattern 是前置条件

**搜索 vs 编译，是根本不同的范式**。如果跳过 Phase 1 直接做"互联的 RAG"，节点之间的查询质量等于通用 RAG 之和——没有任何提升，网络效应不成立。

LLM Wiki Pattern 的核心是**把"压缩与关联"前置到 ingest 阶段**：一次源材料的 ingest 可能 touch 10-15 个 wiki 页面（concepts、maps、connections、index、summaries、log）。这种 quadratic bookkeeping 是 LLM 而非人能承担的工作。

**节点的内在质量**才是 KAN 网络效应的源头。Phase 1 必须先跑通，证明 "compiled wiki" 比 "raw notes + 搜索" 在被查询时给出明显更好的答案。

详见 [[concepts/llm-wiki-pattern]] 和 `raw/articles/llm-wiki-pattern.md`。

## Phase 2: 为什么 MCP/Wire 是正确的协议 substrate

**MCP** 已经是 LLM 调用外部工具的事实标准（Anthropic / Cursor / Cline / Kimi CLI 都支持），把 wiki 暴露为 MCP server 立刻获得：
- 任何 LLM client 都能调用
- 不绑定特定 host
- 工具调用的标准化（schema、错误处理、流式返回）

**Wire** ([[maps/agent-harness-implementations|RC 的 kimi-cli wire 协议]]) 是 MCP 的近亲——JSON-RPC over stdio + capability negotiation + 双向 RPC + cancellation + replay。比 MCP 更进一步的是**反向 RPC**（agent 反向问 client），这正是 KAN 节点之间需要的对等通信。

Dongzhe 已经把 wiki-mcp-server 部署到 Railway，**Phase 2 已完成第一个节点**。这是 KAN 的实证起点，不是空想。

## Phase 3: 为什么 KAN 的协议层应该直接借鉴 Wire / agent-vault / tldr

详细的 8 条迁移建议在 [[connections/harness-to-kan]] 里。简短版：

| KAN 需要 | 现成的工程依据 |
|---|---|
| 节点之间通信 | Wire JSON-RPC + initialize + 双向 RPC |
| 跨节点引用不传原文 | agent-vault `<agent-vault:key>` placeholder 模式 |
| 高效查询响应格式 | tldr-vscode WikiStub 渲染 |
| 并发查询调度 | kosong sync-handle/async-future + Claude Code per-tool `isConcurrencySafe` |
| 节点 metadata 合并 | Kimi CLI AGENTS.md leaf-first 32KB |
| 周期性 reminder | kimi-cli dynamic injection |
| 准入与策略 | Claude Code hooks 系统 |

**这意味着 KAN MVP 的工程量从 6 个月压到 4-6 周**——每一个核心组件都有 production-tested 参考实现。

## Phase 4: 为什么 Knowledge Economy 填的是 AGNTCY 缺的层

[[concepts/agent-communication]] 已经研究过 AGNTCY/MCP/A2A 完整协议栈，结论是：**经济层缺失**。当前的 agent 协议都是"如何通话"和"如何协作"，没有"如何结算价值"。

KAN Phase 4 正好补这层：
- **定价**：按次 / 订阅 / 互惠免费
- **分润**：节点之间的 query 收入分成
- **声誉**：被查次数 = 领域权威度（类学术引用）
- **marketplace**：知识服务的 discovery 层

详见 [[connections/communication-to-economy]]——KAN Phase 4 是把那条 connection 在"知识"这个垂直域里实例化。

## 与 [[concepts/multi-agent-simulation]] 的连接：需求侧 testbed

KAN 是供给侧（每个节点提供 compiled knowledge），而 OASIS / MiroFish 这种 1M agent 仿真是**合成需求侧**。把 OASIS 当成"市场需求侧的合成用户群"，让真实 KAN 节点在 OASIS 模拟出的"百万合成消费者/评论者"前先做：
- 压力测试（节点能不能扛住 1k QPS）
- 定价探索（不同价格下查询量曲线）
- 内容策略验证（哪些 topic 被查询最多）
- 产品 vs 用户反馈循环

**这是 OASIS 在 KAN stack 里的正确位置——需求侧的 testbed，而不是 KAN 本身**。OASIS 当前 21+ action 全是社交语义，缺经济原语，正好和 KAN 互补。

## 与 Richard Chien 的协议优先哲学

RC 的工程哲学（详见 `raw/articles/richard-chien/deep-profile.md`）是 **"先做协议、再做框架、最后才是产品"**——CQHTTP → OneBot → NoneBot 是这条线的第一个验证（13,000+ stars 的中文 chatbot 生态），Wire → kosong → kimi-cli 是第二个验证。

**LLM Wiki → KAN 是第三条同构路径**：
- Phase 1 = 先把"compile knowledge"这个原语做对（pattern）
- Phase 2 = 做出 framework（wiki-mcp-server + Wire-compatible）
- Phase 3 = 协议成型，多节点互联
- Phase 4 = 经济层 + marketplace 才是最终产品

**走 RC 的路径更可能成功**，因为协议层先成熟，product 层才有空间。

## 与 [[concepts/world-model|World Model 创业]] 的对比

| 维度 | KAN (LLM Wiki 演化) | World Model 创业 |
|---|---|---|
| 技术风险 | 中（LLM API + 协议都已有） | 极高（基模研发） |
| 资金需求 | 低（插件 + MCP server 起步） | 极高（对标 $1B AMI Labs） |
| 时间到 MVP | 2-4 周 | 6-12 个月 |
| 市场验证 | 可快速验证（vault demo + Discord 反馈） | 需要长期研发 |
| 天花板 | 知识经济（巨大） | 世界模拟（巨大） |
| Dongzhe 角色 | 核心（Agent Economy 是关键层） | 辅助（填 Layer 2） |

KAN 是 Dongzhe-native 的下注——已有的 [[concepts/agent-communication]] / Agent Economy / [[concepts/harness-engineering]] / [[concepts/context-engineering]] 全部能复用，且不依赖任何尚未成熟的基模技术。

## 关联

- [[concepts/llm-wiki-pattern]] — Phase 1 范式
- [[concepts/knowledge-agent-network]] — Phase 3-4 完整构想
- [[concepts/agent-communication]] — Phase 2-3 通信层基础
- [[concepts/multi-agent-simulation]] — 需求侧 testbed
- [[connections/harness-to-kan]] — Phase 3 工程实现的具体借鉴
- [[connections/communication-to-economy]] — Phase 4 经济层的母 connection

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
