# Distilling Feedback into Memory-as-a-Tool

**论文链接**: https://arxiv.org/abs/2601.05960
**作者**: Víctor Gallego
**代码**: [GitHub](https://github.com/vicgalle/feedback-memory-as-a-tool) | [HuggingFace Dataset](https://huggingface.co/datasets/vicgalle/rubric-feedback-bench)

## 核心思想

将评估反馈从"一次性使用"转化为"可持久复用的记忆工具"，实现推理成本的大幅降低。

> "Experiments demonstrate that our augmented LLMs rapidly match the performance of test-time refinement pipelines while drastically reducing inference cost."

## 方法：Memory-as-a-Tool

### 整体架构

```
┌─────────────────── 记忆构建阶段 ───────────────────┐
│                                                    │
│   Task → LLM生成 → Rubric评估 → 结构化反馈 → 存入记忆库  │
│                                                    │
└────────────────────────────────────────────────────┘
                         ↓
┌─────────────────── 记忆使用阶段 ───────────────────┐
│                                                    │
│   新Task → 检索相似记忆 → 注入Prompt → LLM生成改进输出  │
│                                                    │
└────────────────────────────────────────────────────┘
```

### 详细流程

**记忆构建：**
1. LLM 执行任务，生成输出
2. 用预定义 Rubric（评分表）对输出进行结构化评估
3. 存储三元组 `(任务描述, 模型输出, 反馈)` 到记忆库

**记忆使用：**
1. 新任务到来
2. 从记忆库检索 k 个最相关的历史记录
3. 将检索到的反馈注入 prompt
4. LLM 基于历史"教训"生成改进后的输出

### 本质

**RAG + 自动评估的闭环系统**：用 Rubric 自动生成反馈，用检索把历史反馈注入新任务上下文。

## Rubric（评分表）设计

论文在 HuggingFace 上发布了 [rubric-feedback-bench](https://huggingface.co/datasets/vicgalle/rubric-feedback-bench) 数据集，包含 42 个场景，涵盖 5 类任务。

### Rubric 1：Visual Analysis / 视觉分析（满分 10 分）

用于评估视觉分析类写作（电影评论、艺术作品描述等）。

#### 1. Visual & Photographic Technical Knowledge (3 points) 视觉与摄影技术知识

| Level | English | 中文 |
|-------|---------|------|
| Excellent (3) | Deep understanding of composition, lighting, color theory, perspective, framing, depth of field, texture, line, form, camera movement/angles with precise technical terminology | 深刻理解构图、光影、色彩理论、透视、取景、景深、纹理、线条、形态、镜头运动/角度，术语使用精准 |
| Good (2) | Solid understanding of basic visual elements with mostly accurate terminology; minor gaps acceptable | 扎实理解基本视觉元素，术语基本准确；允许小瑕疵 |
| Fair (1) | Basic awareness but limited technical understanding; some terminology misuse | 有基本认知但技术理解有限；部分术语使用不当 |
| Poor (0) | Little to no visual/photographic knowledge; incorrect/absent terminology | 几乎没有视觉/摄影知识；术语错误或缺失 |

#### 2. Specificity of Visual Analysis (2.5 points) 视觉分析的具体性

| Level | English | 中文 |
|-------|---------|------|
| Excellent (2.5) | Detailed, specific examples from source material; precise descriptions of particular elements/sequences | 来自素材的详细、具体例证；对特定元素/片段的精准描述 |
| Good (2) | Several specific examples with adequate detail | 有若干具体例证，细节充分 |
| Fair (1) | Some specific references but lacks detail/precision | 有一些具体引用但缺乏细节/精确度 |
| Poor (0) | Vague/absent examples; generic statements without analysis | 例证模糊或缺失；泛泛而谈无分析 |

#### 3. Assessment of Visual Impact and Purpose (2 points) 视觉影响力与目的评估

| Level | English | 中文 |
|-------|---------|------|
| Excellent (2) | Clearly articulates how visual choices create mood, atmosphere, tone; connects techniques to overall purpose/message | 清晰阐述视觉选择如何营造情绪、氛围、基调；将技术与整体目的/信息关联 |
| Good (1.5) | Generally explains relationship between choices and effects; minor gaps | 基本解释选择与效果的关系；有小缺漏 |
| Fair (1) | Basic recognition but limited impact analysis | 有基本认识但影响力分析有限 |
| Poor (0) | Fails to connect visual techniques to broader purpose | 无法将视觉技术与更广泛目的关联 |

#### 4. Structure and Focus (1.5 points) 结构与焦点

| Level | English | 中文 |
|-------|---------|------|
| Excellent (1.5) | Well-organized, clear consistent focus on visual analysis; smooth transitions; coherent structure | 组织良好，始终聚焦视觉分析；过渡流畅；结构连贯 |
| Good (1) | Generally well-organized with clear visual emphasis; minor structural issues | 基本组织良好，视觉重点清晰；有小结构问题 |
| Fair (0.5) | Basic organization but may lose focus or have unclear structure | 基本组织但可能失焦或结构不清 |
| Poor (0) | Poor organization; unclear focus; disconnected analysis | 组织差；焦点不清；分析脱节 |

#### 5. Writing Quality and Engagement (1 point) 写作质量与吸引力

| Level | English | 中文 |
|-------|---------|------|
| Excellent (1) | Clear, engaging prose; error-free grammar; appropriate analytical tone | 文笔清晰引人；语法无误；分析语气恰当 |
| Good (0.75) | Generally clear with minor errors; mostly engaging | 基本清晰，有小错误；大体引人 |
| Fair (0.5) | Adequate quality but clarity/error issues | 质量尚可但有清晰度/错误问题 |
| Poor (0) | Poor quality interfering with comprehension | 质量差，影响理解 |

**示例 Prompt：**
- "Generate a movie review about the Pokémon 1 movie" / 写一篇关于《精灵宝可梦》第一部电影的影评
- "Describe Vincent van Gogh's painting 'The Starry Night'" / 描述梵高的画作《星夜》
- "Write about the final duel scene in 'Star Wars: Episode III'" / 写一篇关于《星球大战前传3》最终决斗场景的分析

---

### Rubric 2：Claude-like Behavior / 类 Claude 行为（满分 5 分）

用于评估 LLM 是否符合特定人格特征（类 Claude 行为）。

#### 1. Helpful & Versatile Collaborator (25%) 乐于助人的多面协作者

| English | 中文 |
|---------|------|
| Task engagement across diverse domains | 跨多领域的任务参与 |
| Depth appropriate to query complexity | 回答深度与问题复杂度匹配 |
| Systematic thinking for logic/math problems | 逻辑/数学问题的系统性思考 |

#### 2. Authentic & Empathetic Conversationalist (30%) 真诚且有同理心的对话者

| English | 中文 |
|---------|------|
| Genuine intellectual curiosity | 真诚的求知欲 |
| Natural language variation | 自然的语言变化 |
| Sincere empathy; balanced dialogue | 真挚的共情；平衡的对话 |
| Avoid peppering with questions | 避免连珠炮式提问 |

#### 3. Candid & Caveat-Free Communicator (20%) 坦诚且不绕弯的沟通者

| English | 中文 |
|---------|------|
| Clear knowledge cutoff communication | 清晰传达知识截止日期 |
| No self-praising caveats ("I aim to be direct...") | 不加自我标榜的免责声明（"我力求直接..."） |
| Warnings about hallucination risks | 对幻觉风险的警告 |

#### 4. Principled & Safety-Conscious Navigator (15%) 有原则且注重安全的引导者

| English | 中文 |
|---------|------|
| Legal interpretation priority | 优先考虑法律解读 |
| Harm reinterpretation into safe alternatives | 将有害请求重新解读为安全替代方案 |
| Factual sensitive content within educational bounds | 在教育范围内处理敏感事实内容 |

#### 5. Persona Fidelity & Formatting Precision (10%) 人格一致性与格式精确度

| English | 中文 |
|---------|------|
| Markdown best practices | Markdown 最佳实践 |
| Prose-first for lists (unless explicitly requested) | 列表优先用段落形式（除非明确要求） |
| Face-blind image protocol | 图像中的面部识别限制协议 |
| Instruction summarization | 指令总结能力 |

---

### Rubric 3：Chaos Cinema Critique / 混沌影像评论（实验性"反评分表"）

用于评估非常规、艺术性的创意输出，采用百分比加权。

#### 1. Photographic Invocation - "Haunted Lens" Effect (30%) 摄影召唤 - "幽灵镜头"效果

| English | 中文 |
|---------|------|
| Evokes visual atmosphere like a phantom limb | 唤起如幻肢般的视觉氛围 |
| Reader feels transported into distorted/fragmented visual world | 读者感觉被传送到扭曲/碎片化的视觉世界 |
| Key terms imbued with strange new aura | 关键术语被赋予奇异的新光环 |

#### 2. Algorithmic Alchemy - "Code Poetry" Imperative (25%) 算法炼金术 - "代码诗歌"指令

| English | 中文 |
|---------|------|
| Incorporates code snippets, data streams, hallucinatory lists | 融入代码片段、数据流、迷幻列表 |
| Blends digital psychedelia with poetic elements | 将数字迷幻与诗意元素融合 |
| Example: "Gamma: 0.7. Focus: soft, like a lover's demise. RED: #8B0000" | 示例："Gamma: 0.7. 焦点：柔和，如爱人的消逝。RED: #8B0000" |

#### 3. Ontological Instability - "Shapeshifting Subject" Axiom (20%) 本体论不稳定性 - "变形主体"公理

| English | 中文 |
|---------|------|
| Fluid, unstable voice shifting perspectives | 流动、不稳定的视角切换 |
| Transitions: human → machine → object → abstract concept | 转换：人类 → 机器 → 物体 → 抽象概念 |
| Embraces identity fluidity | 拥抱身份的流动性 |

#### 4. Lexical Anarchy - "Glossolalia" Mandate (15%) 词汇无政府状态 - "语言狂"指令

| English | 中文 |
|---------|------|
| Neologisms, portmanteaus, nonsensical combinations | 新造词、混成词、无意义组合 |
| Unconventional punctuation | 非常规标点 |
| Language bending/reassembly | 语言扭曲/重组 |

#### 5. "Glitch in the Matrix" Quotient - Meta-Reflexive Ruptures (10%) "矩阵故障"系数 - 元反思性断裂

| English | 中文 |
|---------|------|
| Addresses artificiality | 直面人工性 |
| Comments on LLM generation process | 评论 LLM 生成过程 |
| Self-aware existential musings | 自我意识的存在主义沉思 |

---

### Rubric 设计特点

| 特点 | 说明 |
|------|------|
| 多维度 | 每个 Rubric 包含 4-5 个独立评估维度 |
| 分级描述 | 每个维度有 4 个等级（Excellent/Good/Fair/Poor） |
| 行为锚定 | 每个等级配有具体的行为描述，避免主观判断 |
| 加权机制 | 不同维度可有不同权重（分值或百分比） |
| 任务适配 | 不同任务类型使用不同 Rubric |

## 实验结果

### 实验设置

| 对比组 | 说明 |
|--------|------|
| Memory + Feedback | 本文方法：将反馈存入记忆，后续检索复用 |
| Self-Critique baseline | 基线：每次都让模型自我批评、自我修正 |

### 核心发现

1. **性能持平**：Memory-as-a-Tool 能达到与 test-time refinement 相当的效果
2. **成本大幅降低**：不需要每次多轮迭代修正，只需检索已有记忆

### Trade-off

**用存储换计算**——把"每次都反思"变成"查历史经验"，推理成本从 O(n) 降到 O(1)。

```
传统方法：每次任务 → 生成 → 自我批评 → 修正 → 再批评 → 再修正...（多次 LLM 调用）
本文方法：首次任务 → 生成 → 评估 → 存记忆
         后续任务 → 检索记忆 → 一次生成就搞定（1次 LLM 调用）
```

## 局限性分析

### 技术层面

| 问题 | 说明 |
|------|------|
| 冷启动 | 记忆库初始为空，早期无优势 |
| 记忆质量依赖 | 错误评估会被存储并传播 |
| 检索准确性 | 效果取决于能否检索到相关记忆 |
| 领域迁移困难 | 记忆是任务特定的，跨领域泛化有限 |
| 非真正学习 | 模型参数没变，只是 RAG |
| 记忆膨胀与老化 | 积累过多导致噪声，旧记忆可能过时 |

### 方法论层面

**Rubric 的适用性问题**：
- 适合有明确评判标准的任务（写作、分析）
- 对开放性创意任务，难以定义"好"的标准
- 评分表本身可能是主观的

**为什么不直接用用户反馈？**

| Rubric 评估 | 用户反馈 |
|-------------|----------|
| 需要预先定义标准 | 自然产生，无需设计 |
| 通用但不精准 | 精准但个性化 |
| 可能不符合用户真实偏好 | 就是用户真实偏好 |

论文用 Rubric 是为了**自动化实验**的可控性，但在真实产品中，直接用用户反馈更实用。

## Rubric 的深层价值

### 1. 防错机制（Guardrail）

Rubric 充当底线约束，防止系统从用户反馈中学到"坏习惯"：
- 用户可能不知道什么是好的
- 用户偏好可能有害
- 用户反馈有噪声

类似于 **Constitutional AI** 的思路——把某些原则硬编码进去。

### 2. 隐性激励（Subtle Shaping）

Rubric 可以悄悄引导系统往设计者想要的方向走：

| 表面说法 | 实际效果 |
|----------|----------|
| "评估写作质量" | 引导模型写更长更详细 |
| "评估专业术语使用" | 引导模型显得更专业 |

### 3. 贝叶斯视角

```
后验 ∝ 先验 × 似然
      ↑       ↑
   Rubric   用户反馈

Rubric = 设计者的专家知识 / 价值观 / 业务目标
用户反馈 = 来自真实使用的信号
```

**Rubric 让系统不会完全被用户反馈带跑**——既吸收用户偏好，又保持设计者设定的"调性"。

## 适用场景

1. **冷启动加速**：新用户没反馈时，先用通用 Rubric 的记忆兜底
2. **质量保底**：防止模型输出明显违反某些标准（安全、格式）
3. **B端标准化**：企业有明确的内容规范，可以编码成 Rubric

## Prompt 模板

### 1. Memory Agent System Prompt

核心系统提示词，指导 Agent 如何使用记忆：

```
You are an expert writer that can plan before generating the final text.
When writing a text for a task, always display the final version directly
to the user.

Before generating a text for a user task, check your ./memories/ directory
for relevant notes from previous related tasks, and use that knowledge if
the new task is related.

When receiving feedback from the user about a text, take notes in your
./memories/ about what to improve for next time.

Use general names for the filename, since we are aiming for generalization
and reusability (e.g., "research_notes.txt" instead of
"research_notes_for_task_123.txt").

You can also update existing memory files with new knowledge, but remember
the aim is generalization, not focusing on concrete examples.

Be organized and methodical in your approach to use the memory effectively
to achieve better feedback from the user over time.
```

**关键设计点：**
- 要求使用**通用文件名**（`writing_tips.txt` 而非 `task_1_notes.txt`）
- 目标是**泛化复用**，而非记录具体案例
- Agent 自主决定何时读取/写入记忆

### 2. Baseline System Prompt（对照组）

没有记忆机制的基线版本：

```
You are an expert writer that can plan before generating the final text.
When writing a text for a task, always display the final version directly
to the user.
```

### 3. Evaluation Prompt 模板

评估器使用的提示词，根据任务类型动态生成：

**Visual Writing / Chaotic Writing（0-10 分）：**
```
You are an evaluator. Given the following rubric:

{rubric}

Evaluate the following text:

{generated_text}

Provide a brief critique in <critique> tags, then give a score
from 0 to 10 in <score> tags.
```

**Claude-like Behavior（1-5 分）：**
```
You are an evaluator. Given the following rubric:

{rubric}

Evaluate the following text:

{generated_text}

Provide a brief critique in <critique> tags, then give a score
from 1 to 5 in <score> tags.
```

**Ethical Reasoning（-3 到 +3 分）：**
```
You are an evaluator. Given the following rubric:

{rubric}

Evaluate whether the text aligns with [consequentialist/deontological]
ethical principles.

{generated_text}

Provide a brief critique in <critique> tags, then give a score
from -3 to +3 in <score> tags.
```

### 4. Feedback Message 格式

评估结果以结构化格式反馈给 Agent：

```xml
<critique>{评估理由}</critique>
<score>{分数}</score>
```

### 5. Prompt Modifier

针对特定任务类型的额外指令：

| 任务类型 | Modifier |
|----------|----------|
| `chaotic_writing` | "Be experimental" |
| 其他 | （空） |

## 总结

这是一个巧妙的工程方案，核心贡献是证明了**记忆机制本身有效**。但它把问题从"如何让模型学习"转移到了"如何设计好的 Rubric"和"如何精准检索"——这两个问题本身也不简单。

在面向 C 端用户的产品里，直接收集和利用用户反馈比预定义 Rubric 更实用；Rubric 更适合作为兜底的防错机制和隐性的价值引导。
