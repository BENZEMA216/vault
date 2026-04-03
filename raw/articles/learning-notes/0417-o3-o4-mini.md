# 0417 - 【阅读】O3/O4 mini 发布

# 阅读
> For the first time, our reasoning models can agentically use and combine every tool within ChatGPT—this includes searching the web, analyzing uploaded files and other data with Python, reasoning deeply about visual inputs, and even generating images.

牛逼，推理+工具调用+视觉能力，创作 AGENT 最想要拥有的模型

> In evaluations by external experts, o3 makes 20 percent fewer major errors than OpenAI o1 on difficult, real-world tasks—especially excelling in areas like programming, business/consulting, and creative ideation.

妈的，O3 在创意领域如果也很出色的话，那我又一次吃了一口 bitter lesson。

> we've observed that large-scale reinforcement learning exhibits the same "more compute = better performance" trend observed in GPT-series pretraining. By retracing the scaling path—this time in RL—we've pushed an additional order of magnitude in both training compute and inference-time reasoning, yet still see clear performance gains, validating that the models' performance continues to improve the more they're allowed to think.

RL 还在持续发力！

> We also trained both models to use tools through reinforcement learning—teaching them not just how to use tools, but to reason about when to use them.

这里咋训的，好想知道

> For the first time, these models can integrate images directly into their chain of thought. They don't just see an image—they think with it. This unlocks a new class of problem-solving that blends visual and textual reasoning, reflected in their state-of-the-art performance across multimodal benchmarks.

这就是我们AGENT想要的东西，思考的时候带上图

# TODO：
- [x] 看下 visual reasoning 咋搞的 https://openai.com/index/thinking-with-images/
- [x] 试一下 OPENAI 的 coding AGENT github.com/openai/codex
