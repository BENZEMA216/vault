# /init 机制

> Creative CoWork 的 Skill 初始化系统，用户进入项目时自动推荐相关 Skills，三层推荐逐步深入。

## 核心要点

- 用户进入项目时自动触发，推荐与当前项目相关的 Skills
- 三层推荐策略：模板匹配（快速）→ 元数据评估（中等）→ LLM 结构化推理（深度）
- 干扰规避：通过 confusable_with 声明避免相似 Skill 之间的误推荐
- 进化机制：根据使用数据调整推荐权重，越常用的 Skill 推荐越靠前
- 参考了 Superpowers 的 session hook 机制，但做了创意场景的适配

## 详细说明

/init 机制是 Creative CoWork 在用户体验层面的关键设计。当用户打开或进入一个项目时，系统需要快速理解项目上下文并推荐最相关的 Skills。如果推荐太少，用户可能不知道系统有哪些能力；如果推荐太多或不准确，用户会感到噪音过大。/init 机制通过三层递进的推荐策略平衡了这个矛盾。

第一层是模板匹配：根据项目类型（视频项目、漫剧项目、设计项目等）直接映射预设的 Skill 集合，速度最快但灵活性最低。第二层是元数据评估：分析项目中已有的文件类型、工具使用记录、历史操作等元数据，筛选出可能相关的 Skills。第三层是 LLM 结构化推理：将项目上下文和候选 Skills 一起交给 LLM，让模型判断哪些 Skills 最适合当前场景，这一层最准确但成本最高。

干扰规避是一个精巧的设计细节。某些 Skills 名称或功能相似（比如"故事板 Skill"和"分镜 Skill"），容易导致误推荐。每个 Skill 的元数据中包含 confusable_with 字段，声明与自己容易混淆的其他 Skills，推荐引擎在做决策时会参考这些声明避免同时推荐容易混淆的选项。进化机制则确保推荐系统随使用而改善：用户实际激活和使用的 Skill 会被记录，使推荐权重逐步向真实使用模式靠拢。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/0205-init-mechanism]] | 完整的 /init 机制设计文档，定义了三层推荐策略和进化机制 |
| [[raw/0201-tech-research]] | 技术基础研究，为 /init 机制提供了底层技术选型依据 |
| [[raw/04-mvp-requirements]] | MVP 需求文档中引用了 /init 机制作为项目入口体验 |
| [[raw/superpowers-skills-analysis]] | Superpowers 的 session hook 架构分析，作为 /init 机制的参考对比 |

## 关联概念

- [[concepts/skills-system]] — /init 机制是 Skills 系统的入口，决定哪些 Skills 被推荐给用户
- [[concepts/progressive-disclosure]] — /init 的三层推荐本身就是渐进式披露的应用
- [[concepts/tool-routing]] — /init 推荐 Skills 与工具路由加载工具共享延迟加载的设计理念

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
