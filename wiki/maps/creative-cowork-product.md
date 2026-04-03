# Creative CoWork 产品全景

> Creative CoWork — "创作者的 Claude Code"，AI 创作工作台

## 产品定位

Creative CoWork 面向 [[concepts/super-creators]]（超创），提供上下文聚合 + Agent + 可定制 Studio 的 AI 创作工作台。

## 核心架构

[Diagram showing: 上下文容器 → Agent (with Skills) → GENUI → Studio]

核心组件：
1. **[[concepts/context-container]]** — 项目级上下文聚合
2. **[[concepts/agent-loop]]** + **[[concepts/creative-agent-design]]** — 创意Agent循环（发散/收敛模式）
3. **[[concepts/skills-system]]** + **[[concepts/init-mechanism]]** — 模块化能力加载
4. **[[concepts/genui]]** — 动态生成界面
5. **[[concepts/sub-agent-architecture]]** — 分层Agent委托

## 产品演进

| 阶段 | 文档 | 要点 |
|------|------|------|
| 愿景 | [[raw/projects/creative-cowork/01 Creative CoWork - DEMO思路]] | 核心架构、定位 |
| 深化 | [[raw/projects/creative-cowork/02 Creative CoWork - 产品深化]] | 9个战略问题、用户定义 |
| 技术 | [[raw/projects/creative-cowork/0201 - 技术研究]] | OpenClaw、Agent Skills标准、上下文策略 |
| 机制 | [[raw/projects/creative-cowork/0205 - init 机制设计]] | /init 三层推荐、进化机制 |
| 需求 | [[raw/projects/creative-cowork/04 Creative CoWork - MVP需求]] | 完整功能清单（研发核心文档） |
| 设计 | [[raw/projects/creative-cowork/06 Creative CoWork - 侧边栏设计方案]] | V6绿色主题、Sandbox架构 |

## 技术参考

- [[raw/articles/claude-code-research/Claude Code 架构分析]] — 架构蓝本
- [[raw/articles/claude-code-research/Agent System Prompt 研究]] — Agent SP 对标分析
- [[raw/articles/claude-code-research/Creative CoWork Skills 架构启发]] — Skills 适配方案

## 用户验证

- [[raw/projects/user-interview/漫剧访谈 LSW - 总结]] — 角色一致性、上下文分散
- [[raw/projects/user-interview/漫剧访谈 三界动画 - 总结]] — 单人全链路、抽卡效率
- [[raw/projects/user-interview/漫剧访谈 艺柏AILab - 总结]] — 剪辑优先、品牌广告
- [[raw/projects/user-interview/漫剧访谈-福州畅读 - 总结]] — 出海、赛马策略

## 里程碑

- **0215**：可试用版本，3-5 个导演朋友能用它完成真实项目

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
