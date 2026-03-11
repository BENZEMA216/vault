# Karpathy AutoResearch

> 2026-03-08 开源，GitHub 几天 8000+ stars

## 是什么

给 AI agent 一个真实的 LLM 训练环境，让它自主跑实验。修改代码 → 训练 5 分钟 → 检查是否改善 → keep/discard → 循环。你睡觉，它工作。

核心：~630 行 Python，单 GPU，单文件，单指标。

## 核心设计哲学

### 1. "你不再写 Python，你写 Markdown"

研究者不再直接改 `train.py`，而是编写 `program.md`（给 agent 的指令）。人类从 researcher 变成 research manager。

> You're not touching any of the Python files like you normally would as a researcher. Instead, you are programming the `program.md` Markdown files that provide context to the AI agents and set up your autonomous research org.

### 2. 固定时间预算（5 分钟）

- **实验可比性**：不管 agent 怎么改模型大小、架构，结果在同一时间尺度直接可比
- **平台自适应**：自动找到"你的硬件 5 分钟能达到的最优模型"
- 代价：不同人的结果不可互相比较（但这不是重点）

### 3. Simplicity Criterion

> All else being equal, simpler is better. Removing something and getting equal or better results is a great outcome — that's a simplification win.

- 0.001 提升 + 20 行 hacky code → 不值得
- 0.001 提升来自删代码 → 必须保留
- ~0 提升但代码更简洁 → 保留

### 4. NEVER STOP 原则

Agent 启动后永不停止、永不问人。跑到没 idea 就 think harder。

### 5. 三文件架构

| 文件 | 角色 | 谁改 |
|------|------|------|
| `prepare.py` | 数据准备、tokenizer、evaluation（只读） | 无人 |
| `train.py` | 模型、优化器、训练循环 | Agent |
| `program.md` | Agent 的指令和研究策略 | Human |

指标：`val_bpb`（validation bits per byte），与 vocab size 无关。

## 实验循环

```
LOOP FOREVER:
  1. 读当前 git 状态
  2. 修改 train.py（一个实验 idea）
  3. git commit
  4. 跑训练 → run.log（5 min）
  5. 提取 val_bpb
  6. 更好 → keep（保留 commit）
     更差 → discard（git reset）
     crash → 修复或跳过
  7. 记录到 results.tsv（不 commit）
  8. 回到 1
```

## 关键实验发现

### 规模
- 一晚：~126 个实验
- 两天连续跑：~700 次自主修改

### Agent 发现的优化（人类 20 年经验漏掉的）
1. QKnorm scaler — sharpen attention
2. Value Embeddings 正则化
3. Banded attention 加宽
4. AdamW betas 修正
5. Weight decay schedule 调优
6. Initialization 调优

### 量化效果
- Loss: 0.9979 → 0.9697
- Time to GPT-2: 2.02h → 1.80h（11% 提升）
- 改进 transfer 到更大的 depth-24 模型

### 反直觉
> "The model got better by getting simpler"

## 未来愿景：SETI@home 式分布式研究

> The goal is not to emulate a single PhD student, it's to emulate a research community of them.

从单线程同步 → 异步大规模多 agent 协作：
- 多 agent 从同一 seed repo 出发，各自探索不同方向
- Distributed task sharding、result deduplication、cross-agent memory

## 我的思考

1. **研究 = 搜索**：固定评估标准和时间预算下，ML 研究本质是代码空间搜索，LLM agent 天然适合
2. **Prompt 即组织架构**：`program.md` 是研究组织的 SOP，人类设计搜索策略而非执行搜索
3. **Simplicity bias 关键**：不显式约束，agent 会无限堆叠 hack；simplicity criterion 让搜索偏向 elegant solutions
4. Shopify CEO Tobi Lutke 已在内部项目复用，报告 19% 提升

## 参考
- [GitHub Repo](https://github.com/karpathy/autoresearch)
- [program.md](https://github.com/karpathy/autoresearch/blob/master/program.md)
- [Karpathy original tweet](https://x.com/karpathy/status/2029701092347630069)
- [SETI@home vision tweet](https://x.com/karpathy/status/2030705271627284816)
