# Knowledge Agent Network (KAN)

> 从个人 LLM Wiki 演化到互联的 Agent 知识网络：每个人的 wiki 暴露为 MCP/Wire 节点，节点之间可发现、可查询、可结算。

## 核心要点

- **本质**：每个人的 [[concepts/llm-wiki-pattern|LLM-compiled Wiki]] 是一个**可被其他 Agent 查询的节点**。个人化 Wikipedia + Agent 互联 + 知识经济。
- **四阶段演进**：
  1. **Phase 1 - Personal LLM Wiki** (Obsidian 插件) — 已实践
  2. **Phase 2 - Wiki Agent** (wiki-mcp-server, Dongzhe 已部署到 Railway) — 已部署
  3. **Phase 3 - Agent 网络** (A2A Card 发现 + 主题路由)
  4. **Phase 4 - Knowledge Economy** (定价、分润、声誉、marketplace)
- **填补 AGNTCY 缺的层**：[[concepts/agent-communication]] 已研究的 AGNTCY/MCP/A2A 协议栈缺 economic layer，KAN 正好补这层
- **网络拓扑**：放弃全连接（噪音）和关注制（人工），选 **主题路由**——按知识领域自动匹配，最 agent-native
- **冷启动策略**：不从 C 端起步，从 3-5 人的"知识圈"先用起来（Dongzhe + xz1220 + 车昊轩 + ...）
- **评分卡**：Phase 1 23/30，Phase 4 28/30——到 Phase 4 是 all-in 级别的方向

## 详细说明

**最小有用单元**不是"互查"（这只是 group chat 变体），而是：**我深度研究的 Agent Communication wiki > ChatGPT 对该话题的回答**。这个差距 = 价值。把这个差距变成可被全网 Agent 调用的服务，就是 KAN 的核心命题。

**激励机制四层级**：互惠（小圈子，你查我我查你）→ 声誉（被查次数 = 领域权威度，类学术引用）→ 经济（按次收费，类 API marketplace）→ 组合（免费互查 + 深度查询付费）。Phase 1-2 走互惠 + 声誉，Phase 4 才上经济。

**与 World Model 创业的对比**：相比车昊轩的 World Model 路线（技术风险极高、资金需求 $1B 级、6-12 月到 MVP），KAN 是低风险高天花板的 Dongzhe-native 选项——技术风险中（LLM API + 协议都已有）、资金需求低（插件起步）、2-4 周到 MVP，且 Dongzhe 在 Agent Economy 研究上有独一份的优势。

**已对齐的研究**：MCP/A2A 通信协议（Wiki Agent 之间的 transport）、Agent Economy（查询计价与结算）、A2A Agent Card（每个 Wiki Agent 的身份卡）、Harness Engineering（查询的可靠性与验证）、Context Engineering（跨 Agent 查询的 context 管理）——KAN 把所有 Dongzhe 已有研究串成一个产品愿景。

## 在知识库中的出现

| 来源 | 上下文 |
|------|------|
| `output/reports/knowledge-agent-network-idea.md` | 完整愿景：四阶段、技术架构图、与现有方案对比、评分卡 |
| `output/reports/knowledge-agent-network-design.md` | 网络生效机制：冷启动、激励、拓扑、Step 0-4 启动路径 |
| `output/reports/llm-wiki-product-opportunity.md` | Phase 1 的产品化 (Obsidian 插件)、与 Notion AI/NotebookLM 差异化 |
| `output/reports/richard-chien/deep-profile.md` | RC 的 slock.ai = "agents and humans collaborate as equals" 是 KAN 的协作介质参考 |
| `output/reports/richard-chien/code-review.md` | Wire 协议、agent-vault placeholder、tldr stub 渲染都是 KAN 协议层可借鉴的设计 |

## 关联概念

- [[concepts/llm-wiki-pattern]] — KAN 节点的内部结构（前置条件）
- [[concepts/agent-communication]] — KAN 通信层用 MCP/A2A
- [[concepts/agent-native-im]] — slock.ai 等 IM 是 KAN 可能的 UX 层
- [[concepts/agent-tool-concurrency]] — KAN 跨节点查询必须并发，否则体验崩坏
- [[connections/llm-wiki-to-agent-network]] — 演化路径
- [[connections/harness-to-kan]] — 可借鉴的 harness 设计模式
- [[connections/communication-to-economy]] — KAN Phase 4 = Agent Economy 在知识域的实例

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
