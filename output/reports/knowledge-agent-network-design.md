# Knowledge Agent Network：网络如何生效

> 来源: 知识库 Query 讨论 (2026-04-07)
> 前置: knowledge-agent-network-idea.md

---

## 核心问题

一个人的 wiki agent 有用但不性感。网络才性感——网络怎么生效？

## 1. 冷启动：第一批节点从哪来？

不从 C 端个人做起，从**小圈子**开始：

- Dongzhe（Agent Economy / Communication / Harness）
- 后端兄弟 xz1220（创业方法论 / CI 竞品）
- 车昊轩（World Model / Video Generation）
- 再拉 2-3 个研究不同方向的朋友

3-5 个人的知识网络先自己用起来。类似早期 Facebook 从一个宿舍开始。

## 2. 激励：查询的另一头怎么获益？

| 模式 | 类比 | 适用阶段 |
|------|------|---------|
| **互惠** | 你查我的，我也查你的 | Step 1-2（小圈子） |
| **声誉** | 被查次数 = 领域权威度 | Step 2-3（类学术引用） |
| **经济** | 按查询次数收费 | Step 4（API marketplace） |
| **组合** | 免费互查 + 深度查询收费 | 长期 |

## 3. 网络拓扑

```
A) 全连接（每个人查每个人）→ 噪音太多
B) 关注制（我选择关注谁）→ 类似 Twitter/RSS
C) 主题路由（按知识领域自动匹配）→ 最 Agent-native ✓
```

**C 最有意思**：问一个问题，网络自动找到最相关的 wiki agent 回答，你不需要知道是谁的知识。

## 4. 和现有网络的类比

- Twitter = 人的 feed → 这是 **Agent 的 feed**
- GitHub = 代码协作网络 → 这是 **知识协作网络**
- Agent Card = 知识 profile（研究方向 / 深度 / 被引用次数）

## 5. 网络启动路径

```
Step 0: 自己的 wiki agent 跑通 ✅

Step 1: "知识圈"（3-10 人）
        朋友间手动加对方 MCP endpoint
        验证: 互查比直接问 ChatGPT 好吗？

Step 2: "知识目录"（10-100 人）
        公开的 Agent Card 列表，按领域分类
        验证: 陌生人会来查吗？

Step 3: "知识路由"（100+ 人）
        问题自动路由到最相关的 wiki agent
        引入声誉/引用机制
        验证: 网络效应出现了吗？（查询量 vs 节点数超线性增长）

Step 4: "知识经济"
        查询定价 + 分润 + marketplace
        高质量 wiki 有收入
```

## 6. 最小有用单元

**不是"互查"（group chat 变体），而是：我的深度研究变成可被全网 Agent 调用的知识服务。**

我的 Agent Communication 研究 > ChatGPT 对这个话题的回答。这个差距 = 价值。

## 7. 需要继续想的问题

- 隐私权限怎么设计（公开/朋友/私密）
- 质量保证（错误知识传播怎么防）
- 知识更新的同步机制
- 定价模型（按次？订阅？互惠免费？）
