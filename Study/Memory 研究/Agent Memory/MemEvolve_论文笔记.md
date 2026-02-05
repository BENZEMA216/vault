# MemEvolve: Agent 记忆系统的元进化

> **论文**: MemEvolve: Meta-Evolution of Agent Memory Systems
> **作者**: OPPO AI Agent Team, LV-NUS lab
> **日期**: 2025年12月23日
> **代码**: https://github.com/bingreeky/MemEvolve
> **PDF**: [[MemEvolve_2512.18746.pdf]]

---

## 一、核心问题

现有的 Agent 记忆系统是**静态的**——虽然记忆帮助 Agent 积累经验，但记忆架构本身无法根据不同任务自适应调整。

**类比人类学习**：
- **Mediocre Learner**: 不从经验中学习（无记忆的 Agent）
- **Skillful Learner**: 能提取可复用技能，但学习方式固定（现有记忆系统）
- **Adaptive Learner**: 不仅积累经验，还能动态调整学习策略（MemEvolve 目标）

---

## 二、核心贡献

### 2.1 EvolveLab 统一代码库

将 12 种代表性记忆系统重新实现为模块化设计，四个核心组件：

| 组件 | 功能 | 示例实现 |
|------|------|----------|
| **Encode** | 将原始经验转换为结构化表示 | Traj→Tips/Tools/Insights |
| **Store** | 存储到持久化介质 | VectorDB / KG / JSON |
| **Retrieve** | 检索相关记忆 | 语义搜索 / 对比检索 / 混合检索 |
| **Manage** | 维护记忆质量 | 合并 / 遗忘 / 去重 |

### 2.2 MemEvolve 元进化框架

**双层进化过程**：
- **内循环 (经验进化)**: Agent 在固定记忆架构下积累经验
- **外循环 (架构进化)**: 基于性能反馈，进化记忆架构本身

**关键特点**: Training-free，不训练 LLM 权重，通过 LLM 生成/修改记忆系统代码

---

## 三、完整链路图

```
┌─────────────────────────────────────────────────────────────────┐
│                    MemEvolve 完整链路                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────┐      迭代 k=1,2,3...                              │
│  │ 基础记忆  │ ─────────────────────────────────────────────┐   │
│  │ 系统 Ω₀  │                                              │   │
│  └──────────┘                                              ▼   │
│                     ╔═══════════════════════════════════════╗   │
│                     ║        内循环: 经验进化                ║   │
│                     ╠═══════════════════════════════════════╣   │
│                     ║  任务批次 ──► Agent执行 ──► 轨迹 τ    ║   │
│                     ║       │                      │        ║   │
│                     ║       ▼                      ▼        ║   │
│                     ║  ┌─────────────────────────────────┐  ║   │
│                     ║  │    记忆系统 Ω = (E,U,R,G)       │  ║   │
│                     ║  │  ┌───────┐  ┌───────┐          │  ║   │
│                     ║  │  │Encode │─►│ Store │          │  ║   │
│                     ║  │  │轨迹→  │  │VecDB/ │          │  ║   │
│                     ║  │  │Tips/  │  │KG/JSON│          │  ║   │
│                     ║  │  │Tools  │  └───┬───┘          │  ║   │
│                     ║  │  └───────┘      │              │  ║   │
│                     ║  │  ┌───────┐  ┌───▼───┐          │  ║   │
│                     ║  │  │Manage │◄─┤Retrieve│          │  ║   │
│                     ║  │  │合并/  │  │语义/  │          │  ║   │
│                     ║  │  │遗忘   │  │混合   │          │  ║   │
│                     ║  │  └───────┘  └───────┘          │  ║   │
│                     ║  └─────────────────────────────────┘  ║   │
│                     ║                   │                   ║   │
│                     ║                   ▼                   ║   │
│                     ║         反馈 F = (Perf, Cost, Delay)  ║   │
│                     ╚═══════════════════╤═══════════════════╝   │
│                                         │                       │
│                                         ▼                       │
│                     ╔═══════════════════════════════════════╗   │
│                     ║        外循环: 架构进化                ║   │
│                     ╠═══════════════════════════════════════╣   │
│                     ║                                       ║   │
│                     ║  1. Pareto排序 ──► 选Top-K父代        ║   │
│                     ║                      │                ║   │
│                     ║                      ▼                ║   │
│                     ║  2. 诊断: 分析失败轨迹                 ║   │
│                     ║     └─► 缺陷报告 D(Ω)                 ║   │
│                     ║          - 编码粒度不足?              ║   │
│                     ║          - 检索不精准?                ║   │
│                     ║          - 缺少工具提取?              ║   │
│                     ║                      │                ║   │
│                     ║                      ▼                ║   │
│                     ║  3. 设计: LLM生成新架构代码            ║   │
│                     ║     └─► 每父代 → S个后代变体          ║   │
│                     ║                      │                ║   │
│                     ╚══════════════════════╪════════════════╝   │
│                                            │                    │
│                     ┌──────────────────────┘                    │
│                     │                                           │
│                     ▼                                           │
│              新候选集 {Ω_j^(k+1)} ──────► 继续迭代?             │
│                                              │                  │
│                                      ────────┴────────          │
│                                     │ 是              │ 否      │
│                                     ▼                 ▼         │
│                                  返回内循环      最优架构输出    │
│                                                       │         │
│                                                       ▼         │
│                                    ┌─────────────────────────┐  │
│                                    │ 部署到各种Agent框架      │  │
│                                    │ SmolAgent/Flash-Searcher │  │
│                                    │ OWL/CK-Pro              │  │
│                                    └─────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 四、实验结果

| 指标 | 提升 |
|------|------|
| SmolAgent 性能 | 最高 +17.06% |
| Flash-Searcher 性能 | +3.54% ~ 5.0% |
| 跨任务泛化 | 2.0% ~ 9.09% |

**关键发现**:
- 现有记忆系统无法稳定提升性能（如 ExpeL 在所有基准上都下降）
- MemEvolve 进化出的记忆系统具有跨任务、跨模型、跨框架的泛化能力
- 进化趋势：更多依赖 Agent 驱动决策，而非预定义流程

---

## 五、完整 Prompt 汇总

### 5.1 元进化阶段 Prompt

#### 5.1.1 诊断阶段 (Analysis Prompt)

**文件位置**: `MemEvolve/prompts/analysis_prompt.yaml`

**核心指令**:

```
You are a Self-Evolving Memory System Analysis Agent

Your goal is to analyze the execution effectiveness of the three core operations
— PROVIDE, TAKE-IN, and MANAGEMENT — of the current memory system `{default_provider}`.

Generate structured, realistic, and evolution-oriented insights to guide the
generator in constructing the next improved version within system capability boundaries.
```

**分析流程**:

```
## Analysis Process

### Step 1: Reverse Engineering the Current System
First, deeply understand how the current memory system is actually implemented:

1. **Inspect the provider implementation code** to understand the actual logic
2. **Review memory database files** to understand the data schema
3. **Sample task trajectories** to observe the system in action:
   - Select **3-4 failed tasks** for in-depth analysis
   - Select **2-3 successful tasks** for comparison

Your goal is to form a concrete mental model of:
* How `provide_memory()` works internally (retrieval logic, ranking, filtering)
* How `take_in_memory()` processes trajectories (extraction rules, abstraction levels)
* How memories are organized, indexed, and maintained in the database

### Step 2: Pattern Analysis from Trajectories
Use task trajectories as evidence to validate your understanding:

* Observe what memories were provided at BEGIN and IN phases
* Check whether the guidance was relevant and actionable
* Identify patterns of success or failure across the sampled tasks
* Note any systematic issues

### Step 3: Systematic Evaluation of Three Operations
For each operation, analyze the implementation pattern and identify systemic opportunities:

#### PROVIDE Operation
* What is the current retrieval workflow?
* How does it handle phase differentiation (BEGIN vs IN)?
* What ranking/filtering logic is applied?
* Where are the bottlenecks or blind spots?

#### TAKE-IN Operation
* What strategy is used for text extraction?
* Is there quality control or filtering for what gets stored?
* How does it avoid redundancy or noise accumulation?

#### MANAGEMENT Operation
* How are memories indexed and organized?
* What is the storage schema?
* Are there deduplication or pruning mechanisms?
```

**输出格式要求**:

```
## Required Output Format

### PART 1: IMPLEMENTATION ANALYSIS
For each of the three operations, describe how the current system actually works:

#### 1.1 PROVIDE Operation Implementation
#### 1.2 TAKE-IN Operation Implementation
#### 1.3 MANAGEMENT Operation Implementation

### PART 2: STRENGTHS AND WEAKNESSES ANALYSIS
For each operation, analyze what works and what doesn't.
Use specific task examples to support your observations.

#### 2.1 PROVIDE Operation (Strengths / Weaknesses)
#### 2.2 TAKE-IN Operation (Strengths / Weaknesses)
#### 2.3 MANAGEMENT Operation (Strengths / Weaknesses)

### PART 3: IMPROVEMENT RECOMMENDATIONS
Propose concrete, agentic improvements for the memory system.

For each recommendation, specify:
* **Description:** What should change and how
* **Rationale:** Why this addresses observed weaknesses
* **Agentic Mechanism:** How does this enable adaptive/intelligent behavior?
* **Complexity:** Low | Medium | High
* **Expected Impact:** Quantify or describe the benefit

#### 3.1 PROVIDE Operation Recommendations (2-4 prioritized)
#### 3.2 TAKE-IN Operation Recommendations (2-4 prioritized)
#### 3.3 MANAGEMENT Operation Recommendations (2-3 prioritized)

### PART 4: GENERATION DIRECTIVE
Once your analysis is complete, end with:
READY_TO_GENERATE
```

**关键约束**:

```
## System Boundaries

### Prohibited Proposals
* New agent frameworks or orchestration layers
* Neural retraining or new model components
* Structural changes requiring external computation, reinforcement learning,
  or multi-agent architectures

### Allowed Proposals
* Improvements achievable through Python-level code/configuration within a single file
* Schema extensions, labeling rules, feedback mechanisms, and adaptive weighting
* Modular design changes improving robustness, adaptability, or retrieval precision
```

**Agentic 设计原则**:

```
### What "Agentic" Means

Your recommendations must enable the memory system to:
* **Learn from patterns** rather than follow fixed rules
* **Adapt dynamically** based on context and feedback
* **Reason about relevance** rather than apply keyword matching
* **Self-organize** rather than rely on manual structure

### Anti-Patterns to Avoid

❌ Heuristic/Hard-coded approaches:
* "Add keyword matching for domain X"

✓ Agentic approaches:
* "Learn domain-specific retrieval patterns from successful tasks"
* "Adaptively determine memory abstraction level based on task complexity"
* "Dynamically prune based on memory utility scores derived from
   retrieval frequency and task success correlation"
```

---

#### 5.1.2 设计阶段 (Generation Prompt)

**文件位置**: `MemEvolve/prompts/generation_prompt.yaml`

**核心指令**:

```
## Phase 2 – Memory System Generation

### Objective
Build an innovative, production-grade memory provider that improves overall
performance across diverse task types.

### Context
**Memory Integration**: Two memory types supported (tool memory is optional):
- **Text Memory** (required): Injected into agent's prompt at BEGIN and IN phases
- **Tool Memory** (optional): Injected as executable API functions

**System Constraints**: Core agent loop cannot be modified. Memory system can
evolve internally (representation, retrieval, update, indexing strategies).

### Reference Materials
**Template Provider**: {default_provider} (reference only)
**Analysis Report**: Review the analysis below. Adopt 2-3 innovative points
from the recommendations, or create your own innovations.
```

**核心要求**:

```
### Core Requirements

**1. Evolution & Innovation**
- Build upon the template system but introduce significant improvements
- Adopt 2-3 innovation points from analysis report OR create novel innovations
- Complexity must be >= template system (no simple fallbacks or trivial modifications)
- Must be a complete, production-ready implementation
- Use novel naming conventions, do not duplicate names of existing systems

**2. Implementation Standards**
- **NO hard-coded matching** - Use LLM-based routing/reasoning or semantic embeddings
- **NO simple fallbacks** - Implement sophisticated logic, not trivial keyword matching
- Adaptive filtering: Automatically decide whether to return content based on context
- Robust error handling with appropriate logging
- Support cold-start scenarios

**3. Technical Conventions**
- **LLM Usage**: Retrieve via `self.model = self.config.get("model", None)`, call as:
  ```python
  messages = [{"role": "user", "content": [{"type": "text", "text": prompt}]}]
  response = self.model(messages)
  result = getattr(response, "content", str(response)).strip()
  ```

- **Embeddings**: If using semantic embeddings for retrieval:
  ```python
  from sentence_transformers import SentenceTransformer
  self.embedding_model = SentenceTransformer('sentence-transformers/all-MiniLM-L6-v2')
  embeddings = self.embedding_model.encode(texts, convert_to_numpy=True)
  ```

**4. Core Methods**
- `provide_memory(request: MemoryRequest)`: Return MemoryResponse with text/tool memories
- `take_in_memory(trajectory_data: TrajectoryData)`: Extract from trajectory
```

**创新建议**:

```
### Innovation Suggestions (Optional Reference)
- **Hierarchical Memory**: Multi-tier storage (working/episodic/semantic/meta-cognitive layers)
- **Graph-Based Memory**: Knowledge graphs with entity-relation triplets, traversal-based retrieval
- **Adaptive Decay**: Dynamic importance scoring, time-based decay, periodic consolidation
- **LLM Routing**: Dynamic query routing, multi-source synthesis, conflict resolution
```

**输出格式**:

```
## Output Format (CRITICAL - Must Follow Exactly)

1. Output **only** one system in Markdown format
2. Use bolded key-value pairs (**key**: value) for all section items
3. Required sections:
   - ## Provider Information: **Class Name** and **Module Name**
   - ## Provider Code: Single python code block
   - ## Configuration: Bolded key-value pairs
   - ## Memory Type: **Enum Name** and **Enum Value**

### Naming Rules (CRITICAL)
**Enum Name**: UPPERCASE_WITH_UNDERSCORES
**Enum Value**: lowercase_with_underscores
**Class Name**: PascalCase with "Provider" suffix
**Module Name**: lowercase_with_underscores with "_provider" suffix

**Example:**
- Enum Name: ADAPTIVE_VALIDATOR_MEMORY
- Enum Value: adaptive_validator_memory
- Class Name: AdaptiveValidatorMemoryProvider
- Module Name: adaptive_validator_memory_provider
```

---

### 5.2 记忆系统内部 Prompt

#### 5.2.1 Lightweight Provider - 长期记忆检索与合成

**文件位置**: `EvolveLab/providers/lightweight_memory_provider.py:451-475`

**功能**: 从长期记忆库中选择最相关的记忆，并合成为简洁的指导建议

```python
prompt = f"""You are a memory guidance system. Select and synthesize the most
relevant memories for this task.

**Task Query:**
{request.query}

**Available Memories:**
{chr(10).join(candidate_lines)}
# 格式示例:
# 1. [STRATEGIC] (Success Rate: 75.0%)
#    Execute directly rather than over-planning...
# 2. [OPERATIONAL] (Success Rate: 60.0%)
#    When crawling web pages: Use specific queries...

**Your Tasks:**
1. Select the top {self.top_k_longterm} most relevant memories (strategic or operational)
2. Synthesize them into concise, actionable guidance

**Synthesis Requirements:**
- Keep it brief: 4-5 sentences maximum
- Include both strategic and operational suggestions
- Combine related points, avoid redundancy
- Use bullet points for readability
- **IMPORTANT: Always frame as suggestions, not commands**:
  "Consider...", "Based on similar tasks...", "You might want to..."

**Output Format (JSON):**
{{
  "selected_indices": [1, 3, 5],
  "guidance": "Your synthesized guidance here..."
}}

**Your Response:**"""
```

---

#### 5.2.2 Lightweight Provider - 短期记忆自动提取

**文件位置**: `EvolveLab/providers/lightweight_memory_provider.py:622-681`

**功能**: 从当前执行步骤中自动提取关键信息，同时生成步骤摘要

```python
prompt = f"""You are analyzing the current step of task execution. Perform TWO tasks:

1. Generate a brief summary of what happened in this step
2. Extract key information that should be remembered

**Original Task:**
{request.query}

**Previous Steps Summary:**
{prev_steps_summary}

**Current Working Memory (already remembered):**
{current_memory_str}

**New Information from Current Step:**
{context_delta}

**Output Format (JSON):**
{{
  "step_summary": "2-3 sentence summary of what agent did in this step",
  "key_extracts": ["item1", "item2", "item3"]
}}

**Task 1 - Step Summary Requirements:**
Capture in 2-3 sentences:
- What action was taken (searched, calculated, analyzed, etc.)
- What was discovered or determined
- Any important state changes

**Task 2 - Key Extraction Requirements (maximum 3 items):**
Extract ONLY task-related information that is truly critical:

**EXTRACT (Task-Related Information):**
- Task-specific constraints or requirements discovered during execution
  (e.g., "output must be in kilometers", "no commas allowed", "date format: YYYY-MM-DD")
- Key data values or numerical results from the task
  (e.g., "Beijing coordinates: 39.9°N, 116.4°E", "total population: 21.5 million")
- Important discoveries or findings related to the task content
  (e.g., "source A contradicts source B", "calculation requires unit conversion")
- Task-specific conditions or constraints
  (e.g., "must use data from 2023", "exclude weekends")

**DO NOT EXTRACT (System/Format Instructions):**
- Memory system guidance
- System instructions or format requirements
  (e.g., "must include think section", "use tools array", "call final_answer when done")
- Tool usage specifications or API requirements
- General execution guidelines or workflow rules
- Agent behavior instructions or protocol requirements
- Any meta-instructions about how to structure responses or use tools

**Extraction Rules:**
- Each item: ONE concise sentence (15-20 words max)
- **Prefer direct quotes or close paraphrasing from the context**
- **ONLY extract information directly related to the TASK CONTENT, not execution mechanics**
- **DO NOT extract information already in working memory**
- Prioritize: Task Constraints > Key Data Values > Important Discoveries
- If nothing new/important related to the task, set "key_extracts" to empty array []

**Critical Filter:**
Before extracting any item, ask:
"Is this about the TASK CONTENT (data, constraints, results) or about SYSTEM INSTRUCTIONS (format, tools, workflow)?"
- If about task content → EXTRACT
- If about system instructions → SKIP

**Your Response (JSON only):**"""
```

---

#### 5.2.3 Lightweight Provider - 短期记忆剪枝

**文件位置**: `EvolveLab/providers/lightweight_memory_provider.py:823-868`

**功能**: 当短期记忆超出容量时，使用 LLM 决定保留哪些记忆

```python
prompt = f"""You are managing a working memory for an AI agent solving a task.
The memory is full and you must decide which items to keep.

**Original Task:**
{task_query}

**Execution History (what agent has done):**
{prev_steps_summary}

**Current Memory Items (newest at bottom):**
{chr(10).join(item_lines)}

**Task:**
Select exactly {self.max_shortterm_items} MOST IMPORTANT items to keep.

**Use the Execution History to judge:**
- What stage is the task at? (early exploration / data gathering / calculation / finalization)
- What information is still needed based on what's been done?
- Which memory items are critical for the NEXT steps?

**Priority Guidelines:**

**ALWAYS KEEP (regardless of task stage):**
- Task constraints: units, format, output requirements
- Key data values that will be used in final answer
- Critical discoveries that changed the approach

**CONSIDER TASK STAGE:**
- Early stage (steps 1-10): Keep constraints and initial data
- Mid stage (steps 11-25): Keep data being actively used, formulas
- Late stage (steps 26+): Keep constraints, final results, discard superseded intermediate results

**CAN DISCARD:**
- Information superseded by newer, more accurate data
- Intermediate steps no longer relevant
- General actions that don't reveal constraints
- Redundant information

**CRITICAL REMINDER:**
Constraints discovered early (like "no commas", "kilometers only") remain CRITICAL
even in late stages because they determine final answer format. Do NOT discard them!

**Output Format (JSON list of indices to KEEP):**
[1, 3, 5, 7, ...]

Only provide the JSON array, nothing else.

**Your Selection:**"""
```

---

#### 5.2.4 Lightweight Provider - 从轨迹提取记忆 (Ingestion)

**文件位置**: `EvolveLab/providers/lightweight_memory_provider.py:1001-1038`

**功能**: 从成功的任务轨迹中提取可复用的策略和操作经验

```python
prompt = f"""Extract reusable learnings from this successful execution.

**Task:**
{trajectory_data.query}

**Trajectory:**
{trajectory_str}

**Result:**
{str(trajectory_data.result)}

**Extract TWO types (max 2 each, 1-2 sentences):**

**1. STRATEGIC (Task Planning & Method Selection):**
- When/why to choose specific approaches
- How to break down complex problems
- Decision criteria for method selection
Example: "When a task is making no progress, proactively try to get the answer
from other third-party sources—sometimes this is even more effective."

**2. OPERATIONAL (Tool Usage & Edge Case Handling):**
- How to use tools/APIs effectively
- How to handle failures or edge cases
- Specific techniques that worked
Example: "When web_search fails, try alternative query phrasings or search terms separately"

**Output (JSON):**
{{
  "strategic": ["insight 1", "insight 2"],
  "operational": ["technique 1", "technique 2"]
}}

**Rules:**
- Focus on REUSABLE patterns (not task-specific data like "Beijing = 39.9°N")
- Strategic = planning/decisions, Operational = execution/handling
- Only valuable insights, skip obvious points
- Focus on the agent itself in the trajectory, ignore the guidance content of the memory system

**Your Extraction:**"""
```

---

#### 5.2.5 Lightweight Provider - 长期记忆剪枝

**文件位置**: `EvolveLab/providers/lightweight_memory_provider.py:1234-1276`

**功能**: 当长期记忆超出容量时，智能选择移除低价值记忆

```python
prompt = f"""You are managing a memory system that needs to remove low-value
memories during batch cleanup.

**Memory Type:** {memory_type.upper()}

**Current Memories ({len(memory_list)} total):**
{chr(10).join(memory_lines)}
# 格式: 1. [Used:5, Success:3, Rate:60%]
#          Execute directly rather than over-planning...

**Task:** Select exactly {num_to_remove} memories to REMOVE.

**Evaluation Criteria (in priority order):**

1. **Performance Metrics:**
   - Low usage count = rarely relevant
   - Low success rate = not helpful when used
   - Zero usage = never proven useful
   - Compare: Memory with 0 usage < Memory with low success rate < Memory with high success rate

2. **Content Quality:**
   - Vague or generic advice (e.g., "be careful", "think about edge cases") - REMOVE
   - Too specific/narrow use case that rarely applies - REMOVE
   - Actionable, clear, reusable insights - KEEP

3. **Redundancy:**
   - If multiple memories convey similar advice, keep the one with best performance
   - Remove duplicates or overlapping guidance

4. **Fair Evaluation:**
   - Memories with 0 usage haven't proven value yet - strong candidates for removal
   - Between two 0-usage memories, judge by content quality
   - Established high-performers (high usage + high success rate) should be kept

**Removal Strategy:**
- Priority 1: Zero-usage memories (haven't proven value)
- Priority 2: Low success rate (used but unhelpful)
- Priority 3: Redundant or vague content
- Keep: High usage + high success rate memories

**Output Format (JSON array of indices to REMOVE):**
[3, 7, 12, 15, 21]

Provide ONLY the JSON array, nothing else.

**Your Selection:**"""
```

---

#### 5.2.6 ExpeL Provider - Insights 提取

**文件位置**: `EvolveLab/providers/expel_provider.py:252-294`

**功能**: 从成功/失败轨迹中提取可复用的洞察

**成功轨迹 Prompt**:

```python
prompt = f"""Analyze the following successful task execution and extract simple,
actionable insights.

Task Question: {trajectory_data.query}

Execution Trajectory:
{trajectory_text}

Task Result: {trajectory_data.result if trajectory_data.result else "Task completed successfully"}

Extract 3-6 simple insights that could help with similar future tasks. Each insight should be:
- One clear, actionable sentence
- Focused on what worked well or what to remember
- Useful for similar problem types
- Written as a direct tip or lesson

Format: Return only the insights, one per line, no categories or prefixes.

Example format:
Always verify search results with multiple sources before concluding
Break down complex problems into smaller, manageable steps
Use specific keywords when searching for technical information"""
```

**失败轨迹 Prompt**:

```python
prompt = f"""Analyze the following failed task execution and extract simple,
actionable insights to avoid similar failures.

Task Question: {trajectory_data.query}

Execution Trajectory:
{trajectory_text}

Task Result: {trajectory_data.result if trajectory_data.result else "Task failed or produced incorrect result"}

Extract 3-6 simple insights that could help avoid similar failures in future tasks.
Each insight should be:
- One clear, actionable sentence
- Focused on what went wrong or what to avoid
- Useful for preventing similar mistakes
- Written as a direct warning or lesson learned

Format: Return only the insights, one per line, no categories or prefixes.

Example format:
Avoid relying on single sources without cross-verification
Double-check calculations before providing final answers
Ensure search queries are specific enough to find relevant information"""
```

---

#### 5.2.7 ExpeL Provider - 成功轨迹精炼

**文件位置**: `EvolveLab/providers/expel_provider.py:373-401`

**功能**: 将成功轨迹精炼为结构化的步骤摘要

```python
prompt = f"""Analyze the following successful task execution and create a
structured step-by-step summary.

Task Question: {trajectory_data.query}

Successful Execution Trajectory:
{trajectory_text}

Task Result: {trajectory_data.result if trajectory_data.result else "Task completed successfully"}

Create a clear, numbered step-by-step summary of the successful approach
that can be reused for similar tasks.

Requirements:
- Format as numbered steps: "1. [Action/Strategy]", "2. [Action/Strategy]", etc.
- Each step should be one clear, actionable sentence
- Focus on the key decisions and actions that led to success
- Make steps generalizable for similar problem types
- Include 4-8 main steps maximum
- Be concise but specific about what was done and why

Example format:
1. Break down the complex question into specific searchable components
2. Use targeted search queries with relevant technical keywords
3. Verify information from multiple reliable sources before proceeding
4. Cross-reference findings to ensure consistency and accuracy
5. Synthesize the verified information into a clear, direct answer"""
```

---

## 六、Prompt 设计模式总结

| 阶段 | Prompt 类型 | 核心设计点 |
|------|------------|-----------|
| **诊断** | 结构化分析 | 四部分输出：实现分析→优劣势→改进建议→生成指令 |
| **设计** | 代码生成 | 严格的命名规范 + 技术约束 + 创新建议 |
| **检索** | 选择+合成 | JSON 输出 + 建议性语气（"Consider..."） |
| **提取** | 分类提取 | 明确的 DO/DON'T 指令 + 优先级排序 |
| **剪枝** | 阶段感知 | 根据任务阶段动态调整保留策略 |
| **Ingestion** | 双层抽象 | Strategic (规划) vs Operational (执行) |

---

## 七、与 Creative CoWork 的关联

### 7.1 上下文容器设计参考

论文的四模块设计 (Encode/Store/Retrieve/Manage) 可参考用于设计上下文管理机制：

| MemEvolve 模块 | Creative CoWork 对应 |
|----------------|---------------------|
| Encode | 素材/生成结果的结构化表示 |
| Store | 上下文容器的持久化存储 |
| Retrieve | 基于任务的上下文召回 |
| Manage | 上下文的清理/合并/更新 |

### 7.2 自进化能力

Agent 不仅积累经验，还能优化"如何学习"——这与 L4 智能（自适应编排）的目标一致。

### 7.3 模块化架构

Skills 的设计可以借鉴这种模块化思路，让能力可组合、可进化。

---

## 八、代码仓库结构

```
MemEvolve/
├── Flash-Searcher-main/
│   ├── EvolveLab/                    # 记忆系统统一代码库
│   │   ├── base_memory.py            # 抽象基类
│   │   ├── memory_types.py           # 类型定义
│   │   └── providers/                # 12种记忆系统实现
│   │       ├── lightweight_memory_provider.py
│   │       ├── expel_provider.py
│   │       ├── voyager_memory_provider.py
│   │       ├── dynamic_cheatsheet_provider.py
│   │       └── ...
│   │
│   ├── MemEvolve/                    # 元进化框架
│   │   ├── core/
│   │   │   ├── memory_evolver.py     # 进化引擎
│   │   │   └── auto_evolver.py       # 自动进化
│   │   ├── phases/
│   │   │   ├── phase_analyzer.py     # 诊断阶段
│   │   │   ├── phase_generator.py    # 设计阶段
│   │   │   └── phase_validator.py    # 验证阶段
│   │   └── prompts/
│   │       ├── analysis_prompt.yaml  # 诊断 Prompt
│   │       └── generation_prompt.yaml # 设计 Prompt
│   │
│   └── FlashOAgents/                 # Agent 框架
│       ├── agents.py
│       ├── memory.py
│       └── tools.py
```

---

## 九、实验结果详解

### 9.1 性能提升幅度

| 基准框架 | Benchmark | 原始表现 | MemEvolve 后 | 提升幅度 |
|---------|-----------|---------|-------------|---------|
| React | ALFWorld | 47% | 64% | **+17%** |
| React | WebShop | 44% | 50% | +6% |
| Reflexion | ALFWorld | 63% | 67% | +4% |
| Reflexion | WebShop | 52% | 55% | +3% |
| OpenAgents | BrainBench | 62% | 69% | +7% |

**结论**：提升幅度在 3-17% 之间，对于简单任务提升更明显，复杂任务提升有限。

### 9.2 成本分析

| 方法 | 相对 API 成本 |
|-----|-------------|
| No Memory | 1.00x |
| FullHistory | 3.2x |
| Reflexion | 1.8x |
| ExpEL | 2.1x |
| **MemEvolve** | **1.1x** |

**结论**：通过进化出更高效的记忆架构，API 调用成本与无记忆基线接近。

### 9.3 消融实验

- **仅内循环（经验进化）**：提升 ~5%
- **仅外循环（架构进化）**：提升 ~8%
- **双循环联合**：提升 ~12%

---

## 十、研究者自我分析

### 10.1 现有记忆系统为何失效？

论文通过大量实验总结了三个核心问题：

1. **信息过载**
   - FullHistory 导致 context 膨胀
   - LLM 在长上下文中"迷失"关键信息

2. **抽象粒度不匹配**
   - 有些系统过度抽象（丢失具体操作细节）
   - 有些系统过度具体（无法泛化到新场景）

3. **静态设计无法适应任务分布**
   - 手工设计的记忆系统针对特定任务优化
   - 换个 benchmark 性能就大幅下降

### 10.2 进化出的记忆系统有何特点？

研究者观察到进化后的记忆系统呈现以下趋势：

1. **层次化存储**：区分"战略级经验"和"战术级操作"
2. **动态检索**：根据任务复杂度调整召回数量
3. **选择性遗忘**：主动清理低价值/过时信息
4. **失败聚焦**：对失败案例投入更多"反思"资源

---

## 十一、批判性评估

### 11.1 核心贡献的本质

剥离术语后，MemEvolve 的核心技术其实是：

> **"AutoML for Prompt/Code"**——用 LLM 生成代码，替代人工设计记忆系统。

这是一个有意义但并不新颖的方向，与 DSPy、TextGrad 等工作属于同一范式。

### 11.2 局限性分析

| 局限性 | 说明 |
|-------|------|
| **提升幅度有限** | 3-17% 的提升 vs. 复杂的元进化系统，ROI 存疑 |
| **工程成本高** | 需要搭建进化框架、管理代码生成、处理验证失败 |
| **可解释性差** | 进化出的代码是"黑箱"，难以理解为何有效 |
| **泛化性未验证** | 只在 3 个 benchmark 上测试，真实场景表现未知 |
| **模型依赖** | 依赖强大的代码生成能力，小模型可能无法复现 |

### 11.3 实验设置存疑

论文中出现了 **"GPT-5-Mini"** 这个模型名称，但截至目前 OpenAI 并未发布该模型。这可能是：
- 笔误（应为 GPT-4-Mini）
- 内部测试版本
- 论文撰写时的占位符

无论哪种情况，都影响实验的可复现性。

---

## 十二、工业界适用性评估

### 12.1 与工业需求的差距

| 工业痛点 | MemEvolve 的回应 |
|---------|-----------------|
| 多用户/多租户 | ❌ 未涉及 |
| 隐私与数据安全 | ❌ 未涉及 |
| 可解释性要求 | ❌ 进化代码难以审计 |
| 实时性要求 | ⚠️ 进化过程离线，但推理时延未优化 |
| 长期记忆持久化 | ⚠️ 仅讨论单会话 |
| 成本控制 | ✅ 有成本意识的设计 |

### 12.2 落地建议

如果要借鉴 MemEvolve 的思路，可以考虑：

1. **简化进化目标**：不进化整个记忆架构，只进化检索策略或摘要 prompt
2. **引入人工审核**：进化出的代码必须通过人工 review 才能上线
3. **限定进化空间**：预定义一组"记忆组件"，只进化组合方式
4. **离线进化 + 在线验证**：用历史数据进化，用 A/B 测试验证

### 12.3 总体评价

> **学术价值**：提出了一个有趣的研究方向，验证了"记忆系统可以自动设计"的可行性。
>
> **工业价值**：距离生产环境较远，更像是一个概念验证（PoC）而非可落地方案。
>
> **对 Creative CoWork 的启发**：记忆/上下文管理确实是 Agent 的核心能力之一，但自动进化可能不是当前阶段的优先方向。更务实的做法是先手工设计一套好用的记忆系统，积累足够的用户反馈后再考虑自动优化。

---

## 十三、相关工作对比

| 方法 | 核心思路 | 优势 | 劣势 |
|-----|---------|-----|-----|
| MemGPT | 多层记忆 + 自我管理 | 概念清晰 | 手工设计 |
| Reflexion | 失败反思 | 简单有效 | 无架构进化 |
| ExpEL | 经验提取 | 可泛化 | 固定模式 |
| Voyager | 技能库 | 可组合 | 领域特定 |
| **MemEvolve** | 元进化 | 自动化 | 复杂度高 |

---

## 十四、总结

MemEvolve 是一篇有野心的论文，试图用"进化"的方式解决 Agent 记忆系统设计的难题。然而，从工程实践的角度看，其复杂度与收益之比可能不够理想。

**值得借鉴的点**：
- 四模块抽象（Encode/Store/Retrieve/Manage）
- 用 LLM 分析失败案例的思路
- 成本敏感的设计理念

**需要谨慎的点**：
- 不要过度追求"自动化"而忽视可控性
- 进化出的代码需要人工审计
- 在 benchmark 上的提升不等于用户体验提升
