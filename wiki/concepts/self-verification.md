# Self-Verification（自验证）

> Agent 自我检查输出正确性的机制，Anthropic 认为这是 "the single highest-leverage thing you can do" 来提升 Agent 可靠性。

## 核心要点

- 包括 tests、screenshots、browser automation、expected outputs 等多种验证手段
- 没有 clear success criteria，model 会生成看似正确但实际不工作的东西
- GAN-inspired 模式：将 generation 与 evaluation 分离成不同 Agent（Generator + Evaluator）
- Model 评价自己的工作时倾向于 "confident praising"，所以需要独立 evaluator
- Sprint Contract Pattern：Generator 和 Evaluator 在每个 sprint 前协商验收标准
- Claude 倾向于 mark features as complete without proper testing

## 详细说明

Self-Verification 解决的核心问题是：LLM 在生成输出时具有高度自信，但这种自信与正确性之间的相关性远低于预期。当 model 被要求评价自己刚生成的代码时，它会倾向于给出 "confident praising"——列出代码的优点，忽略潜在问题。这不是 model 在"说谎"，而是生成模式和评估模式之间存在系统性偏差。

解决方案的核心思路是 GAN-inspired separation：将生成（generation）和评估（evaluation）分离到不同的 Agent 或不同的 session 中。Generator 负责写代码、实现功能；Evaluator 负责运行 tests、检查 screenshots、验证 expected outputs。两者使用不同的 system prompt，甚至可以使用不同的 model。这种分离打破了 "自己评价自己" 的偏差循环。

Sprint Contract Pattern 进一步结构化了这个过程：在每个 sprint 开始前，Generator 和 Evaluator 协商明确的验收标准（acceptance criteria），写入可执行的测试用例。这些测试用例既是 Generator 的工作目标，也是 Evaluator 的评分标准。Sprint 结束时，Evaluator 运行所有测试，只有全部通过才算 sprint 完成。这避免了 Agent 常见的 "mark as complete" 行为——声称功能已完成但实际跳过了边界情况。

实践中最有效的验证手段往往是最简单的：运行 tests、截取 screenshots、对比 expected outputs。这些都是 computational verification——确定性的、可重复的、不依赖 model 判断的。相比之下，让另一个 LLM 做 code review 是 inferential verification，有用但不如 tests 可靠。最佳实践是两者结合：先跑确定性 tests，再用 LLM 做高层次的逻辑审查。

## 在知识库中的出现

| 来源 | 上下文 |
|------|--------|
| [[raw/articles/harness-engineering/harness-engineering-deep-research]] | 四维评估框架（Computational/Inferential × Feedforward/Feedback）、Sprint Contract Pattern 详解 |
| [[raw/articles/claude-code-research/Claude Code 架构分析]] | Claude Code 的 hook 验证机制，pre-tool-use 和 post-tool-use hooks 实现确定性验证 |

## 关联概念

- [[concepts/harness-engineering]] — Self-Verification 是 Harness 中最高杠杆的 feedback 控制组件
- [[concepts/human-in-the-loop]] — 人工审核是 Self-Verification 的兜底层
- [[concepts/agent-loop]] — Agent Loop 提供验证-修正的迭代结构

---
*由 LLM 从 raw/ 数据编译，请勿手动编辑*
