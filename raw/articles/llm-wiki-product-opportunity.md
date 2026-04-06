# LLM Wiki 产品机会分析：从 Obsidian 插件切入

> 来源: 知识库 Query 讨论 (2026-04-06)
> 背景: AK 说 "there is room here for an incredible new product instead of a hacky collection of scripts"

---

## 痛点

AK Wiki 当前用户全是技术人拼凑出来的：
- 需要 Claude Code / Codex（$200/月起）
- 需要会配 CLAUDE.md / AGENTS.md
- 需要自己搭 Obsidian + 插件
- 需要自己写 ingest/query/lint 流程

**"头发着火"但"只有程序员能灭火"的场景。**

## 核心差异化

| 产品 | 模式 | 差异 |
|------|------|------|
| Notion AI | 在笔记上加 AI 问答 | 不编译，不积累 |
| NotebookLM | RAG（上传→问答） | 不建 wiki，不关联，无 lint |
| Obsidian + AI 插件 | 需要手动组织 | 人还是得做 bookkeeping |
| Mem.ai / Reflect | AI 搜索笔记 | 搜索 ≠ 编译 |
| **AK Wiki Product** | **LLM 拥有 wiki，人只读** | **知识自动积累、关联、更新** |

**搜索 vs 编译，是根本不同的范式。**

## 评分卡（30 分制）

| 维度 | 分数 | 备注 |
|------|------|------|
| 问题频率 | **5** | 每天都在读和积累知识 |
| 问题强度 | **4** | 知识不积累 = 每次从零开始 |
| 付费意愿 | **3** | NotebookLM 免费是压力，专业场景可付 $30-100/月 |
| 市场规模 | **4** | 知识管理 TAM >$10B（Notion $10B 估值） |
| 独特优势 | **3** | 深度使用经验，vault 本身就是最好的 demo |
| 现有方案质量 | **4** | 没人做 LLM-compiled wiki |
| **总分** | **23/30** | 细化（值得验证） |

## 最小切入点：Obsidian 插件

**为什么是插件而不是产品**：
- Obsidian 600 万+用户，不需要获客
- 前端已有（Obsidian = IDE）
- 文件系统已有（本地 markdown）
- 只需要做 LLM 编译层
- 产品经理可以 operate（不需要搭完整 infra）

## MVP 功能（砍到最少）

### V0.1 只做 ingest
- 右键文件/URL → "Ingest to Wiki"
- 调用 LLM API 生成摘要 + 更新索引
- 用户配置 API Key

### V0.2 加 query
- Command palette → "Query Wiki"
- LLM 读 _index.md + concepts → 回答

### V0.3 加 lint
- 手动触发 → 检查断链、孤儿、矛盾

## Obsidian AI 插件竞品

| 插件 | 做什么 | 差距 |
|------|--------|------|
| Smart Connections | 向量搜索相似笔记 | 搜索 ≠ 编译 |
| Copilot | AI 问答 | 不维护 wiki |
| Text Generator | AI 续写 | 和知识库无关 |
| **没有人做 LLM-compiled wiki** | | **空白** |

## 分工

| 环节 | 谁做 |
|------|------|
| Obsidian 插件开发 | 后端兄弟（TypeScript，Obsidian API） |
| LLM 编排逻辑 | Dongzhe（已在 Claude Code 跑通完整流程） |
| 产品定义 | Dongzhe（自己就是用户） |
| 分发 | Obsidian 社区插件市场 |

## 验证路径

1. **本周**: vault 录 2 分钟 demo，发 Obsidian Discord + Reddit r/ObsidianMD
2. **看反应**: 100+ upvotes 或 50+ "我想要" = 信号成立
3. **两周内**: 后端兄弟写最简插件（ingest only），用自己的 vault dogfooding
4. **一个月**: 提交 Obsidian 社区插件，看自然增长

**成本 $0，风险两周，信号来自社区反馈。**

## 风险

1. **NotebookLM 威胁**: Google 可能加 wiki 编译功能
2. **LLM 成本**: 每次 ingest 读全文 + 更新 10-15 页，token 不低
3. **冷启动**: 空 wiki 没价值，需要用户先投入 raw 数据
4. **AK 自己可能做**: 他说了 "incredible new product"，但历史上不做产品

## 扩展路径

- 插件验证成功 → 独立产品（脱离 Obsidian）
- 个人版 → 团队版（Slack/飞书 → 自动编译团队 wiki）
- 通用版 → 垂直版（投资人/研究员/法律合规）
