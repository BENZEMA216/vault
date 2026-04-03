# PaperBanana: Automating Academic Illustration for AI Scientists

> 论文链接：https://arxiv.org/abs/2601.23265
> 阅读日期：2026-02-02

## 基本信息

- **作者**：Dawei Zhu, Rui Meng, Yale Song, Xiyu Wei, Sujian Li, Tomas Pfister, Jinsung Yoon
- **机构**：Google Research, 北京大学
- **提交时间**：2026年1月30日
- **领域**：cs.CL, cs.CV

## 一句话总结

用多 Agent 框架自动生成学术论文中的方法论插图，并提供了一个 292 案例的评估基准。

---

## 方法架构

```
检索 Agent → 内容规划 Agent → 风格规划 Agent → 渲染 Agent → 自我批评 → 迭代优化
     ↑                                                              |
     └──────────────────────────────────────────────────────────────┘
```

| Agent | 职责 |
|-------|------|
| Reference Retrieval | 检索参考图像/风格样本 |
| Content Planning | 规划插图内容结构 |
| Style Planning | 规划视觉风格 |
| Image Rendering | 调用图像生成模型渲染 |
| Self-Critique | 迭代优化评估 |

**技术栈**：VLM + 图像生成模型 + Agent 编排（具体模型未在摘要中披露）

---

## PaperBananaBench

| 维度 | 内容 |
|------|------|
| 规模 | 292 个测试案例 |
| 数据来源 | NeurIPS 2025 论文中的方法论图表 |
| 评估指标 | Faithfulness（保真度）、Conciseness（简洁性）、Readability（可读性）、Aesthetics（美学） |
| 评估方式 | **未明确**（大概率是 LLM-as-Judge 自动评估） |

### 与同类 Benchmark 对比

| Benchmark | 规模 | 评估维度 | 评估方式 |
|-----------|------|----------|----------|
| PaperBananaBench | 292 | 4维 | 未明确 |
| SridBench | 1,120 | 6维 | 人类专家 + MLLM |
| VisJudge-Bench | 3,090 | 6维 | 专家评分 |
| VisBench | - | 7维 | 未明确 |

---

## 评价

### 优点
- 定义了「学术方法论插图自动生成」这个具体问题
- 提供了可复现的评估基准
- 证明了端到端生成的可行性

### 局限
1. **架构无创新** — 检索+规划+生成+迭代是标准 Agent 设计模式
2. **Benchmark 规模偏小** — 292 vs 同类工作 1000+
3. **评估方式不透明** — 摘要未提及人类评估或一致性验证
4. **数据来源单一** — 仅 NeurIPS 2025，领域多样性存疑

### 定性判断

> 这是一篇 **应用/系统论文**，而非方法创新论文。主要价值在于定义问题 + 提供 benchmark + 证明可行性。如果期待技术深度，会失望。

---

## 相关工作

| 论文 | 特点 | 链接 |
|------|------|------|
| VisPainter | 输出可编辑矢量图，三模块架构 | https://arxiv.org/abs/2510.27452 |
| SridBench | 1120样本，13学科，6维评估 | https://arxiv.org/abs/2505.22126 |
| VisJudge-Bench | 3090样本，专家评分，与 GPT-5 对比 | https://arxiv.org/abs/2510.22373 |

---

## 与 Creative CoWork 的关联

- **参考价值有限**：架构过于 general，缺乏可借鉴的技术细节
- **场景可参考**：学术插图生成是 AI 辅助创作的一个垂直场景
- **评估思路**：4维评估框架（保真/简洁/可读/美学）可作为创作类产品的评估参考

---

## 待确认问题

- [ ] 具体使用什么图像生成模型？
- [ ] 评估是否有人类验证？
- [ ] Benchmark 标注协议和评分 rubric 是什么？
- [ ] 代码/数据是否开源？
