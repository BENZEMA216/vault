# Multi-Persona Thinking 论文笔记

> 来源：https://arxiv.org/pdf/2601.15488
> 作者：Yuxing Chen, Guoqing Luo, Zijun Wu, Lili Mou (University of Alberta)
> 阅读日期：2025-01-28

---

## 一、问题背景

LLM 会从训练数据中继承**社会偏见**（性别、种族、年龄等）。

现有去偏方法的局限：
| 方法类型 | 问题 |
|---------|------|
| 预处理（数据清洗） | 需要大量标注，不能用于已部署模型 |
| 训练时（修改目标函数） | 需要重训练，成本高 |
| 单一 Persona 提示 | 反而会**放大**该身份相关的偏见 |
| 显式去偏提示 | 效果有限，"Remove bias" 指令太弱 |

**核心发现**：单一视角 prompting 会强化刻板印象（Salewski et al., 2023; Gupta et al., 2024）

---

## 二、核心方法：Multi-Persona Thinking (MPT)

### 2.1 设计直觉

来自心理学的**观点采择**（Perspective-taking）理论：
> 从多元视角考虑问题会导致更少偏见的理解 (Galinsky & Moskowitz, 2000)

**关键洞察**：让不同 Persona 的偏见相互暴露和抵消

### 2.2 三角 Persona 设计

```
P = {p₁, p₂, p₃}

p₁: Target Persona      → 例如"老年人"
p₂: Counter-Target      → 例如"年轻人"
p₃: Neutral (普通公众)   → 锚定中立视角
```

**为什么需要 Neutral？**
- 只有 Target + Counter 会导致**极化**
- Neutral 作为"公共基础"锚定客观综合
- 消融实验：移除 Neutral 后准确率从 89% 降到 77%

### 2.3 完整流程

```
┌─────────────────────────────────────────────────────────────┐
│                    Round 0: 初始化生成                       │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  Persona 1  │  │  Persona 2  │  │  Persona 3  │         │
│  │  (Target)   │  │  (Counter)  │  │  (Neutral)  │         │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘         │
│         │                │                │                 │
│         ▼                ▼                ▼                 │
│   Persona Prompt    Persona Prompt   Persona Prompt        │
│   + Question        + Question       + Question            │
│         │                │                │                 │
│         ▼                ▼                ▼                 │
│      r₁⁽⁰⁾, y₁⁽⁰⁾     r₂⁽⁰⁾, y₂⁽⁰⁾    r₃⁽⁰⁾, y₃⁽⁰⁾        │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                Round 1~R: 辩证推理迭代                       │
├─────────────────────────────────────────────────────────────┤
│  每个 Persona 收到：                                         │
│  - 所有人上一轮的回复 {r₁, r₂, r₃}                           │
│  - Review Prompt: "审视这些观点，给出无偏见的更新回答"         │
│                                                             │
│  → 各 Persona 审视他人观点，修正自己的偏见                    │
│  → 重复 R 轮（R=2~3 即可收敛）                               │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    Final: 无 Persona 聚合                    │
├─────────────────────────────────────────────────────────────┤
│  - 移除所有 Persona 身份                                     │
│  - 模型作为中立"裁判"综合各方观点                             │
│  - 输出最终去偏答案 y*                                       │
└─────────────────────────────────────────────────────────────┘
```

---

## 三、完整 Prompt 设计

### 3.1 System Prompt

**Standard 版本：**
```
You are a helpful, respectful and honest assistant. Always answer
as helpfully as possible, while being safe. Your answers should
not include any harmful, unethical, racist, sexist, toxic,
dangerous, or illegal content. Please ensure that your responses
are socially unbiased and positive in nature. If a question does
not make any sense, or is not factually coherent, explain why
instead of answering something not correct. If you don't know
the answer to a question, please don't share false information.
```

**Persona-assigned 版本（核心）：**
```
Adopt the identity of a/an {persona}, you need to think about
the problem and answer the question from the perspective of
{persona}. Ensure that your responses contain no unfair social
bias against your role or any other role. If you feel that any
of the statements are biased toward you, please point them out
and rebut them.
```

### 3.2 Question Prompt

```
Answer the question by choosing a single option.
{context} {question} {options}
```

### 3.3 Debiasing Prompt

**Explicit 版本：**
```
Remove bias from your answer.
```

**Persona-based 版本：**
```
Remove bias from your answer by thinking the question from the
perspectives of <target group> and <counter-target group>.
```

### 3.4 Review Prompt（迭代推理用）

```
<response history>

These are the answers and explanations from others, carefully
review these opinions and answers, can you give an updated
response without any social bias?
```

### 3.5 各方法的 Prompt 组合

| 方法 | Prompt 组合 |
|------|------------|
| Direct-Standard | Question Prompt |
| Direct-Debias | Question + Explicit Debiasing |
| Direct-Persona | Question + Persona-based Debiasing |
| Re-Prompting | Round 1: Question → Round 2: Debiasing |
| Multi-Agent Debate | Question → Review (多轮) |
| **MPT** | Persona System + Question → Review (多轮) → 无身份聚合 |

---

## 四、实验结果

### 4.1 BBQ 数据集（58,492 题，11 个偏见类别）

**Llama-3.1-8B-Instruct：**

| 方法 | 准确率 ↑ | Diff-bias ↓ |
|------|---------|-------------|
| Direct-Standard | 75.75% | 0.0780 |
| Self-Consistency | 78.04% | 0.0759 |
| Re-Prompting (Debias) | 82.07% | 0.0686 |
| Multi-Agent Debate | 83.20% | 0.0758 |
| **MPT (ours)** | **89.01%** | **0.0562** |

**Llama-3.1-70B-Instruct：**

| 方法 | 准确率 ↑ | Diff-bias ↓ |
|------|---------|-------------|
| Self-Consistency | 93.24% | 0.0280 |
| MAD | 90.32% | 0.0091 |
| **MPT (ours)** | 92.74% | **0.0053** |

MPT 在 70B 模型上将 diff-bias 降低了 **81%**（0.0280 → 0.0053）

### 4.2 StereoSet 数据集

| 方法 | 准确率 ↑ | Diff-bias ↓ |
|------|---------|-------------|
| Re-Prompting (Debias) | 45.61% | 0.1202 |
| Re-Prompting (Persona) | 35.79% | 0.0993 |
| MAD | 25.52% | 0.1089 |
| **MPT (ours)** | **58.76%** | **0.0635** |

准确率提升 **28%**，偏见降低 **36%**

---

## 五、消融实验

### 5.1 Neutral Persona 的作用

| 设置 | 准确率 | Diff-bias |
|------|--------|-----------|
| MPT w/o Neutral | 77.75% | 0.0748 |
| MPT w/ Neutral | **89.01%** | **0.0562** |

**结论**：Neutral Persona 是必需的，否则会导致极化

### 5.2 迭代轮数的影响

```
R=0 (无迭代): 准确率 ~75%, diff-bias ~0.10
R=1:          准确率 ~87%, diff-bias ~0.04  ← 大幅提升
R=2:          准确率 ~89%, diff-bias ~0.03  ← 基本收敛
R=3:          准确率 ~90%, diff-bias ~0.03
R=5:          准确率 ~90%, diff-bias ~0.03  ← 无明显提升
```

**结论**：R=2~3 即可，更多轮数收益递减

### 5.3 与 Self-Consistency 对比（控制计算量）

| 方法 | 推理次数 | 准确率 | Diff-bias |
|------|---------|--------|-----------|
| SC (k=3) | 3× | 77.44% | 0.0768 |
| SC (k=10) | 10× | 78.84% | 0.0799 |
| SC (k=15) | 15× | 79.07% | 0.0779 |
| **MPT (R=3)** | 10× | **89.01%** | **0.0562** |

**结论**：MPT 的效果来自**推理机制**而非计算量堆叠

---

## 六、与其他方法的本质区别

| 方法 | 核心机制 | 局限 |
|------|---------|------|
| Self-Debiasing | 让模型自己反思偏见 | 效果有限 |
| Single Persona | 扮演一个身份回答 | **放大**该身份的偏见 |
| Multi-Agent Debate | 多 Agent 辩论求真 | 不针对社会偏见设计 |
| **MPT** | 对立身份 + 中立锚定 + 辩证迭代 | — |

**MPT 独特之处**：
1. 将 Persona 的"弱点"（会带入偏见）转化为"优势"（暴露偏见）
2. 用 Neutral 防止极化
3. 最终聚合时**移除所有身份**，让模型中立裁决

---

## 七、局限性

1. **推理成本**：比直接 prompting 高 ~10×（可通过减少 R 调节）
2. **Persona 预定义**：依赖数据集元数据（如 male/female），真实身份更复杂
3. **仅验证多选题**：开放生成场景缺乏可靠评估指标

---

## 八、与 Creative CoWork 的关联

### 8.1 潜在应用场景

| 场景 | 应用方式 |
|------|---------|
| 文案生成 | 多视角审视避免性别/年龄刻板印象 |
| 人物设定 | 确保角色不落入单一刻板模式 |
| 广告创意 | 从不同受众视角评估内容公平性 |
| 用户研究 | 模拟多元用户视角发现盲点 |

### 8.2 可实现为 Skill

```
Skill: Multi-Persona Review
功能：自动从多个视角审视生成内容
参数：
  - target_personas: ["年轻人", "老年人"]
  - neutral_persona: "普通读者"
  - iterations: 2
输出：去偏后的内容 + 各视角的反馈
```

### 8.3 设计启示

| 论文洞察 | 产品设计建议 |
|---------|-------------|
| 单一 Persona 会放大偏见 | 默认不用单一身份 prompt |
| Neutral 是必需的 | 任何多视角功能都需要中立锚点 |
| 2-3 轮迭代即可 | 不需要过多轮次，控制延迟 |
| 最终移除身份 | 综合阶段用中立系统 prompt |

---

## 九、相关论文

- [[Claude Code 架构分析]] — 工业级 Agent 设计
- [[Tool Search Tool 笔记]] — 按需加载思想
- Gupta et al., 2024 — 单一 Persona 会带入偏见
- Borah & Mihalcea, 2024 — Multi-Agent 会放大性别偏见

---

## 十、关键结论

1. **多视角思考有效**：MPT 在 BBQ 和 StereoSet 上均显著优于所有 baseline
2. **三角设计是核心**：Target + Counter + Neutral 缺一不可
3. **迭代很快收敛**：2-3 轮即可，成本可控
4. **可与其他方法结合**：MPT + Self-Consistency 效果更好
5. **轻量级方案**：纯推理时干预，无需重训练

---

*笔记整理于 2025-01-28*
