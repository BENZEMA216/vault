# 0327 - 【学习】Tracing the thoughts of a large language model

又是被 4o 折磨的一天，有一种 LOL 开新赛季的感觉，段位清零，重新慢慢学习啦～ 看看 Anthropic 哥哥有啥新的研究和观察

https://transformer-circuits.pub/2025/attribution-graphs/biology.html
# Motivation
1. LLM 不可感知/不可解释，所以我们要试着解释它！
1. 提出一些问题
  1. Claude 有没有自己的*语言系统*，就类似中国人心里默念的是中文，Claude 内心有没有 in-head language？
  1. Claude 作为一个自回归模型似乎只是 next token prediction，可是是真的这样吗？ 有没有一些提前计划呢？
  1. Claude 可以一步一步地写下自己的推理过程，但是推理是真的吗？ 还是一种 fabrication，只是为了合理性在捏造
# 核心观点/观察
- Claude sometimes thinks in a conceptual space that is shared between languages, suggesting it has a kind of universal "language of thought." We show this by translating simple sentences into multiple languages and tracing the overlap in how Claude processes them. **确实有一种类似大模型思考语言在模型的"脑海""里**
- **Claude will plan what it will say many words ahead, and write to get to that destination.** We show this in the realm of poetry, where it thinks of possible rhyming words in advance and writes the next line to get there. This is powerful evidence that even though models are trained to output one word at a time, they may think on much longer horizons to do so.
- Claude, on occasion, will give a plausible-sounding argument designed to agree with the user rather than to follow logical steps. We show this by asking it for help on a hard math problem while giving it an incorrect hint. We are able to "catch it in the act" as it makes up its fake reasoning, providing a proof of concept that our tools can be useful for flagging concerning mechanisms in models.
- **Claude, on occasion, will give a plausible-sounding argument designed to agree with the user rather than to follow logical steps.（确实爱撒点谎来迎合）** We show this by asking it for help on a hard math problem while giving it an incorrect hint. We are able to "catch it in the act" as it makes up its fake reasoning, providing a proof of concept that our tools can be useful for flagging concerning mechanisms in models.
# A tour of AI biology
- 下图主要是验证了，在做"小"的反义词这个任务时，相同的核心特征被激活了；并且这种不同人类语言下的共享模块，在随着模型尺寸变大而变多；
- 这说明这种概念上的"语言"是存在的。

---

- 原本的猜测是，模型直到生成 rabbit 的时候，才会激活对应的概念，那么就会出现一个多路的"电路"，一路用于押韵需求，一路用于句子有意义；
- **但是**，实际上的情况是，在换行符推理之后，就已经开始思考 rabbit 的概念了，并且最终为了押韵而完成了整句话的推理

- 当研究者把 rabbit 的 internel state 里的部分去掉，那么 Claude 会用一个 habit 来押韵，并且完成类似的推理过程。这证明这个机制是可预期的。
---


- 模型并不是只通过 Pre-train 数据来拟合数据的，而是有一个复杂的，多路的计算过程
  - 一路进行粗略计算
  - 一路进行精确值的调整
- 即**模型通过模拟人们编写的解释来学习解释数学，但它必须学会直接在"脑海中"进行数学运算，而没有任何此类提示，并为此发展自己的内部策略。**

---

- Claude 的推理过程并不是可以完全信赖的（这他妈不废话）

---

- 当我们向 Claude 提出一个需要多步骤推理的问题时，我们可以确定 Claude 思维过程中的中间概念步骤。在达拉斯示例中，我们观察到 Claude 首先激活表示"Dallas is in Texas"的特征，然后将其连接到一个单独的概念，指示"德克萨斯州的首府是奥斯汀"。换句话说，**该模型正在***结合***独立事实来得出答案**，而不是反刍一个记住的答案。

---

- 会有一个 Can't answer的开关特征，来对模型是否能够回答问题进行控制，当模型对概念不熟悉的时候，会进行拒绝，熟悉概念会回答。

- 当 Claude **认识一个名字但对这个人一无所知时**，就会发生这种失误。在这种情况下，"已知实体"功能可能仍会激活，然后取消默认的"不知道"功能 — 在这种情况下，这是错误的。一旦模型决定它需要回答这个问题，它就会继续进行虚构：生成一个看似合理但不幸的是不真实的回答。
---

关于越狱
- 语法连贯和安全机制两个部分在打架，
