# LLM Reasoning 论文索引

> 更新时间: 2026-04-06

## S-Tier

| 论文 | 作者 | 年份 | 会议 | 引用 | 链接 |
|------|------|------|------|------|------|
| Tree of Thoughts: Deliberate Problem Solving with LLMs | Yao, Yu et al. | 2023 | **NeurIPS 2023** | **3,634** | [arxiv.org/abs/2305.10601](https://arxiv.org/abs/2305.10601) |
| Chain-of-Thought Prompting Elicits Reasoning in LLMs | Wei et al. (Google) | 2022 | **NeurIPS 2022** | **6,000+** | [arxiv.org/abs/2201.11903](https://arxiv.org/abs/2201.11903) |

## A-Tier

| 论文 | 作者 | 年份 | 会议 | 引用 | 链接 |
|------|------|------|------|------|------|
| RAP: Reasoning via Planning with LLM as World Model | Hao et al. | 2023 | **EMNLP 2023** | ~500 | [arxiv.org/abs/2305.14992](https://arxiv.org/abs/2305.14992) |
| LATS: Language Agent Tree Search | Zhou et al. | 2023 | **ICML 2024** | **409** | [arxiv.org/abs/2310.04406](https://arxiv.org/abs/2310.04406) |

## 已有索引中的相关论文

- `raw/papers/reasoning/Multi-Persona Thinking 论文笔记.md` — 多角色 Prompt 消除 LLM 偏见
- `raw/papers/reasoning/DrZero_论文笔记.md` — 无训练数据的自进化搜索 Agent

## 分类说明

- **Tree of Thoughts** 核心贡献是 BFS/DFS 搜索策略用于 LLM 推理，不是 world modeling
- **RAP** 和 **LATS** 同时出现在 World Model 索引中，因为它们将 LLM 当作 world model 用于规划
- **Chain-of-Thought** 是所有 LLM 推理方法的奠基之作
