# SWE-Pruner: Self-Adaptive Context Pruning for Coding Agents

> 论文链接：https://arxiv.org/pdf/2601.16746
> 机构：上海交通大学 LLMSE Lab、中山大学、字节跳动
> 日期：2026.01

## 一句话总结

训练一个 0.6B 的轻量模型，根据 Agent 当前目标动态裁剪代码上下文，在几乎不损失任务成功率的前提下节省 23-54% token 成本。

---

## 核心问题

Coding Agent 在处理软件工程任务时：
- **76% 的 token 消耗在"读取"操作**（cat、grep 等）
- 上下文过长导致：推理成本高、注意力稀释、幻觉增加
- 现有压缩方法（如 LLMLingua）基于 PPL 等固定指标，**破坏代码语法结构**

---

## 核心方法

### 设计灵感

模仿程序员"选择性略读"代码的行为——根据当前目标快速定位相关部分，而非逐行阅读。

### 三个关键组件

#### 1. Goal Hint（目标提示）

Agent 在读取文件时生成自然语言问题描述当前需求：
- "MRO 解析逻辑在哪里实现？"
- "错误处理是如何进行的？"

通过给工具增加 `context_focus_question` 参数传递给 Skimmer。

#### 2. Lightweight Neural Skimmer（0.6B）

- **Backbone**：Qwen3-Reranker-0.6B
- **问题建模**：将裁剪问题形式化为重排序问题
- **Token 级别打分**：$s_i = F(q, x_i | C; \theta)$
- **行级别聚合**：对每行内所有 token 分数取平均 $\bar{s}_j = \frac{1}{|T_j|} \sum_{t \in T_j} s_t$
- **CRF 层**：建模相邻行的保留/裁剪决策依赖，保持结构连贯性
- **阈值过滤**：$\bar{s}_j > \tau$ 则保留该行（$\tau = 0.5$）

#### 3. 中间件集成

```
Agent 发起 cat/grep
    → 原始输出
    → SWE-Pruner 裁剪
    → 返回精简上下文给 Agent
```

### 训练数据构建

- **来源**：GitHub Code 2025 数据集
- **生成**：Qwen3-Coder-30B 生成 query + 行级别保留标注
- **任务分类**：9 种 agentic 任务类型
  - code-summarize、code-refactor、find-relevant-part
  - code-optimize、code-locate、code-explain
  - code-debug、feature-addition、code-completion
- **质量过滤**：LLM-as-Judge（Qwen3-Next-80B）
- **最终规模**：61,184 高质量样本

---

## 实验结果

### 多轮 Agent 任务

**SWE-Bench Verified（500 个真实 GitHub issue）**

| 模型 | 成功率 | Token 减少 | 成本节省 | 轮次减少 |
|------|--------|-----------|---------|---------|
| Claude Sonnet 4.5 | 70.2%（-0.4%） | **23.1%** | 26.8% | 18.2% |
| GLM 4.6 | 54.8%（-0.6%） | **38.3%** | 36.4% | 25.7% |

**SWE-QA（仓库级问答）**：Token 减少 29-54%，答案质量持平

### 单轮任务

| 任务 | 压缩比 | 性能 |
|------|--------|------|
| Long Code Completion (8x) | **10.92x** | ES 57.58 |
| Long Code QA (8x) | **14.84x** | Acc 58.71% |

### 与其他方法对比

| 方法 | 成功率 | Token |
|------|--------|-------|
| 无压缩 | 62% | 0.972M |
| LLMLingua2 | 54%↓ | 0.856M |
| RAG | 50%↓ | 0.771M |
| **SWE-Pruner** | **64%**↑ | **0.670M** |

### 关键发现

1. **Token 级别压缩破坏代码结构**：LLMLingua2 的 AST 正确率仅 0.29%，SWE-Pruner 保持 87.3%
2. **聚焦上下文提升决策质量**：Agent 探索性操作减少，决策更果断
3. **延迟开销可忽略**：Skimmer 推理 <100ms

---

## 为什么有效

1. **行级别**而非 token 级别 → 保持语法结构完整
2. **任务感知**而非固定压缩率 → 动态适应 Agent 当前目标
3. **轻量级**（0.6B） → 推理成本远低于省下的 token

---

## 局限性

- 目前只在 **Python 仓库**上验证
- 轻量模型引入少量延迟（虽然可忽略）
- 未与 Agent 历史压缩方法（如 AgentFold）对比（解决的是不同问题）

---

## 对 Creative CoWork 的启发

### 可借鉴

- **核心 insight**：目标导向的动态上下文管理能显著提升效率
- **Goal Hint 机制**：让 Agent/用户明确当前意图，指导上下文筛选
- **轻量模型预筛选 + 大模型精读**的级联架构

### 需要调整

| SWE-Pruner | 创作场景适配 |
|------------|-------------|
| 行级别裁剪 | 素材/片段级别筛选 |
| 硬裁剪（删除） | **软排序（分层可见）**——保留 serendipity |
| 单模态（代码） | 多模态（图文音视频） |
| 收敛目标（解决问题） | 发散目标（探索可能性） |

### 核心差异

创作任务不能简单"裁剪"——"不相关"的素材可能是灵感来源。应该从**删除**转向**智能组织**：

```
核心上下文（直接展示）
  ↓
扩展上下文（折叠/按需展开）
  ↓
意外素材（保留 serendipity）
```

---

## 相关工作

- **Prompt 压缩**：LLMLingua、Selective Context、LongCodeZip
- **Agent 上下文管理**：COMPASS、ACON、AgentDiet、AgentFold
- **代码检索**：RepoCoder、UniXCoder

---

## 引用

```bibtex
@article{wang2026swepruner,
  title={SWE-Pruner: Self-Adaptive Context Pruning for Coding Agents},
  author={Wang, Yuhang and Shi, Yuling and Yang, Mo and others},
  journal={arXiv preprint arXiv:2601.16746},
  year={2026}
}
```
