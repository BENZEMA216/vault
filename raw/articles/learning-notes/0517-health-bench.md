# 0517 - 【学习】health bench 健康领域评估 WIP

https://openai.com/index/healthbench/
## 为什么需要 HealthBench？
现有医疗 AI 评估存在三个问题：
- 脱离真实场景：多数评估用选择题或简短问题，无法模拟医生与患者的真实对话（如急诊判断、跨国医疗咨询等）。
- 缺乏专家验证：评估结果可能不符合医生的专业判断。
- 天花板太低：简单评估已被现有模型 "吃透"，无法激励模型持续进步。

## 理想态
HealthBench 基于我们的信念，即对健康领域的 AI 系统进行评估应该是：
- Meaningful: Scores reflect real-world impact. This should go beyond exam questions to capture complex, real-life scenarios and workflows that mirror the ways individuals and clinicians interact with models.有意义：分数反映了实际影响。这应该超越考试问题，以捕捉复杂的真实场景和工作流程，这些场景和工作流程反映了个人和临床医生与模型交互的方式。
- Trustworthy: Scores are faithful indicators of physician judgment. Evaluations should reflect the standards and priorities of healthcare professionals, providing a rigorous foundation for improving AI systems.值得信赖：分数是医生判断的忠实指标。评估应反映医疗保健专业人员的标准和优先事项，为改进 AI 系统提供严格的基础。
- Unsaturated: Benchmarks support progress. Current models should show substantial room for improvement, offering model developers incentives to continuously improve performance.Unsaturated：基准测试支持进度。当前模型应显示出巨大的改进空间，为模型开发人员提供持续改进性能的激励。

HealthBench 中的 5,000 个对话模拟了 AI 模型与个人用户或临床医生之间的交互。模型的任务是对用户的最后一条消息提供最佳响应。HealthBench 中的对话是通过合成生成和人类对抗测试产生的。它们被创建为逼真且类似于大型语言模型的实际使用：它们是多轮次和多语言的，捕获一系列外行和医疗保健提供者角色，跨越一系列医学专业和上下文，并根据难度进行选择。有关示例，请参阅下面的轮播。

HealthBench 是一项评分量规评估，其中每个模型响应都根据一组医生编写的针对该对话的评分量规标准进行评分。每个标准都概述了理想的回答应该包括或避免什么，例如，要包含的特定事实或要避免不必要的技术术语。每个标准都有一个相应的分值，加权以匹配医生对该标准重要性的判断。HealthBench 包含 48,562 个唯一的评分量规标准，广泛涵盖了模型性能的特定方面。模型响应由基于模型的评分器 （GPT-4.1） 进行评估，以评估是否满足每个评分量规标准，并且响应根据满足的标准的总分与最高可能分数之比获得总分。
