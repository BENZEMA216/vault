# Context Management 技术方案汇总

> 整理日期：2026-02-05

## 概述

LLM 的 context window 是有限的，随着对话/任务进行，context 会逐渐累积直至耗尽。Context Management 就是解决这个问题的一系列技术。

**核心挑战**：
- Token 成本随 context 增长
- Context window 耗尽导致任务中断
- "Lost in the middle" 现象：context 增长时性能下降 15-47%（Stanford 研究）

---

## 一、主流技术方案

### 1. Sliding Window（滑动窗口）

**原理**：保持固定大小的 context window，只保留最近的 N 轮对话。

```python
# 伪代码
def sliding_window(messages, window_size=10):
    return messages[-window_size:]
```

**优点**：简单、可预测
**缺点**：丢失早期重要信息
**适用**：简单 query-response 场景

### 2. LLM Summarization（摘要压缩）

**原理**：用另一个 LLM 将旧的对话压缩成摘要。

```python
# Google ADK 实现
from google.adk.apps.app import App, EventsCompactionConfig
from google.adk.apps.llm_event_summarizer import LlmEventSummarizer

summarizer = LlmEventSummarizer(llm=Gemini(model="gemini-2.5-flash"))

app = App(
    name='my-agent',
    root_agent=root_agent,
    events_compaction_config=EventsCompactionConfig(
        compaction_interval=3,  # 每 3 个事件触发压缩
        overlap_size=1,         # 保留 1 个重叠事件
        summarizer=summarizer
    )
)
```

**优点**：保留语义信息
**缺点**：
- 可能引入幻觉
- 摘要 API 调用额外消耗 7%+ 成本
- JetBrains 研究发现：摘要会让 agent 执行时间延长 ~15%

### 3. Observation Masking（观察遮蔽）

**原理**：用占位符替换旧的环境观察输出，但保留完整的 action 和 reasoning 历史。

**JetBrains 研究结果**（SWE-bench Verified，500 实例）：
- 成本降低 50%+
- 在 5 个测试设置中的 4 个优于 summarization
- Qwen3-Coder 480B：solve rate 提升 2.6%，成本降低 52%

**为什么比 Summarization 好**：保持了 agent 的推理链完整性。

### 4. Prompt Compression（提示压缩）

#### LLMLingua（Microsoft）

**原理**：用小模型（如 GPT2-small）计算 token 的困惑度（perplexity），删除不重要的 token。

**架构**：
1. Budget Controller - 预算控制
2. Iterative Token-level Compression - 迭代 token 级压缩
3. Alignment - 对齐

**性能**：
- 最高 20x 压缩率，性能损失仅 1.5%
- LongLLMLingua：在 NaturalQuestions 上性能提升 21.4%，token 减少 4x
- LooGLE benchmark：成本降低 94%

**与 Selective-Context 对比**：
- Selective-Context 基于短语级 self-information
- LLMLingua 采用 coarse-to-fine 框架
- 在 GSM8K 上，20x 压缩率下 LLMLingua 比 Selective-Context 高 33.10 分

### 5. Hybrid Approach（混合方案）

**典型配置**：
```
ConversationBufferWindowMemory  # 最近几轮完整保留
    +
ConversationSummaryMemory       # 旧对话摘要
    +
VectorStoreRetrieverMemory      # 长期记忆语义检索
```

**客服场景最佳实践**：
- 滑动窗口保留最近 8-10 轮对话
- RAG 按需检索知识库
- 超过 20 轮时触发摘要

---

## 二、Claude Context Awareness

Claude 4.5 的独特方案：让模型**感知**自己的 token 预算。

```xml
<!-- 开始时注入 -->
<budget:token_budget>200000</budget:token_budget>

<!-- 每次 tool call 后更新 -->
<system_warning>Token usage: 35000/200000; 165000 remaining</system_warning>
```

**与其他方案的区别**：不压缩 context，而是让模型自己决定如何分配剩余预算。

详见：[[Claude Context Awareness 机制]]

---

## 三、方案对比

| 方案 | 压缩率 | 性能影响 | 实现复杂度 | 最佳场景 |
|------|--------|----------|-----------|----------|
| Sliding Window | 固定 | 丢失早期信息 | 低 | 简单对话 |
| Summarization | 5-10x | 可能幻觉 | 中 | 长对话 |
| Observation Masking | 2-3x | 保持推理链 | 中 | Coding Agent |
| LLMLingua | 20x | 1.5% 损失 | 高 | RAG、长文档 |
| Hybrid | 可变 | 综合最优 | 高 | 生产系统 |
| Context Awareness | 无压缩 | 无损失 | 低 | Claude 专属 |

---

## 四、实践建议

1. **简单场景**：滑动窗口 + 关键信息提取
2. **Coding Agent**：Observation Masking（JetBrains 推荐）
3. **长文档 RAG**：LLMLingua 压缩 + 语义检索
4. **生产系统**：Hybrid 方案
5. **Claude 系列**：利用原生 Context Awareness

---

## 参考资料

- [JetBrains Research: Efficient Context Management](https://blog.jetbrains.com/research/2025/12/efficient-context-management/)
- [Google ADK Context Compaction](https://google.github.io/adk-docs/context/compaction/)
- [LLMLingua - Microsoft](https://github.com/microsoft/LLMLingua)
- [LLMLingua Paper](https://arxiv.org/abs/2310.05736)
- [LongLLMLingua Paper](https://arxiv.org/abs/2310.06839)
- [Prompt Compression Survey (NAACL 2025)](https://github.com/ZongqianLi/Prompt-Compression-Survey)
- [Claude Context Windows](https://platform.claude.com/docs/en/build-with-claude/context-windows)
