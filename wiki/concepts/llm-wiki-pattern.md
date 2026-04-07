# LLM Wiki Pattern

> Karpathy 提出的个人知识库范式：raw 数据 → LLM 编译 → markdown wiki → CLI 操作 → Obsidian 浏览。本 vault 自身就是这个范式的实践。

## 核心要点

- **三层架构**：`raw/`（immutable 原始素材，LLM 只读）+ `wiki/`（LLM 编译产物，人只读）+ `schema`（CLAUDE.md/AGENTS.md 定义结构与流程）
- **三种操作**：**Ingest**（读 source → 摘要 → 更新索引 → 关联 10-15 个页面 → append log）、**Query**（搜相关页 → 合成答案 → 回流为新页面）、**Lint**（检查矛盾、过时、孤儿、断链、缺口）
- **双索引**：`_index.md`（按主题分类的 catalog）+ `log.md`（append-only 时间线，可 `grep "^## \[" log.md | tail -5` 解析）
- **范式区别**：搜索（RAG，每次重新发现）vs **编译**（LLM 增量构建持久化产物，知识只编译一次）
- **来源**：Karpathy 2026 gist [442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)，灵感来自 Vannevar Bush 1945 的 Memex

## 详细说明

**为什么是 wiki 而不是 RAG**：传统 RAG 在每次查询时把 raw 数据重新喂给 LLM，知识从未沉淀。LLM Wiki 把"压缩与关联"这个动作前置到 ingest 阶段——**LLM 拥有 wiki，人只读 wiki**。一次源材料的 ingest 可能 touch 10-15 个 wiki 页面（concepts、maps、connections、index、summaries、log），bookkeeping 这件人类觉得无聊的事，LLM 不会嫌烦。

**为什么人写不下来**：cross-reference 维护、stale 检查、矛盾发现、orphan 页面追踪——这些都是 quadratic 复杂度的工作。人维护 100 篇 note 已经吃力，LLM 在一次 lint pass 里可以扫完整库。**维护成本 → 0** 是这个范式跑通的前提。

**人/LLM 分工**：人的工作是 curate sources（选择读什么）、direct analysis（提好问题）；LLM 的工作是其他全部——读、摘、归类、链接、检查。这跟 Cursor/Copilot 把人当主、AI 当辅的范式相反。

**与 [[concepts/knowledge-agent-network]] 的关系**：LLM Wiki 是个体节点的内部结构，KAN 是把多个节点互联成网络。Wiki 是 KAN 的前置条件——没有"compiled"知识，节点之间的查询就退化成跨人 RAG，没有质量提升。

## 在知识库中的出现

| 来源 | 上下文 |
|------|------|
| `raw/articles/llm-wiki-pattern.md` | Karpathy gist 全文摘要：三层、三操作、index/log 双索引、Memex 引用 |
| `raw/articles/llm-wiki-product-opportunity.md` | 把 pattern 产品化为 Obsidian 插件的机会分析（评分 23/30）、与 Notion AI/NotebookLM/Smart Connections 的差异化 |
| `raw/articles/knowledge-agent-network-idea.md` | 把 LLM Wiki 作为 KAN Phase 1 的前置 |
| `CLAUDE.md` (本 vault) | 完整的 schema 实现：raw/wiki/output 三目录、ingest/query/lint 三流程、log 格式 |
| `wiki/_index.md` + `wiki/log.md` | pattern 的真实双索引 |

## 关联概念

- [[concepts/knowledge-agent-network]] — pattern 的网络化演化
- [[concepts/context-engineering]] — wiki 是给 LLM 的 context 编译产物
- [[concepts/progressive-disclosure]] — `_index.md` 入口 + 按需读 concept 是渐进式披露
- [[connections/llm-wiki-to-agent-network]] — 从 personal wiki 到 KAN 的演化路径
- [[connections/harness-to-kan]] — RC harness 中的 stub 渲染、dynamic injection 都可以反哺 LLM Wiki

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
