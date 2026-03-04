# 0513 - 【学习】 MetaMorph 通过质量训练获取多模态理解 & 生成能力

https://www.doubao.com/pdf/4967767834256386/https%3A%2F%2Farxiv.org%2Fpdf%2F2412.14164?from_source=from_extention_read_pdf_arxiv&pdf_reading=1

# 大纲 - Intro
- VPIT 能做什么：enables a pretrained LLM to quickly morph into an unified autoregressive model capable of generating both text and visual tokens
- 为什么能生成和推理呢？
  - VPiT teaches an LLM to predict **discrete text tokens and continuous visual tokens from any input sequence of image and text data** curated in an instruction-following format.
- 几个有趣认知
  - 视觉生成是视觉理解的副产物，有理解很容易就能生成
  - 我们发现理解能力和生成能力是相互促进的，但理解数据比生成数据更能有效地提升这两种能力
  - 大语言模型可能具有很强的 "先验" 视觉能力，通过相对简单的指令调优过程，就能有效地应用于视觉理解和生成任务。
# 大概怎么做的
## 训练

**模型结构是这样：**
- 首先 text head 就是本来 LLM 的
- 同时附加一个独立的 vision head，来做 visual token 的预测，然后混一个 Diffusion，用来吧连续 token 转图像

**训练语料对：**
- Input：图文交织
  - TEXT INPUT 通过一个标准的 LLM tokenizer完成向量化（离散的）
  - Visual INPUT 通过一个预训练的 视觉 encoder 完成向量化（离散的）
- Output：图文交织
**详细数据如下，当你理解图文交织之后，其实很好想象这里的数据构造**

## 推理

图文混合输入，先推理离散文本 token 和连续视觉 token，然后过不同的 decoder 还原成文+图（连续）
# 论文里的有趣发现
## 生成能力是理解能力的附属品，很容易解锁

联合数据训练，FID 下降得快，FID分数越低表示生成图像与真实图像越接近。

一个意思，数据越混，训练效果越好
## 视觉理解和生成是相互促进的

这是在控制变量地看，什么样的数据对模型效果提升最大；
结论是，图文交织的理解数据和生成的关系很紧密

这张图的意思，这种数据协同作用，在不同尺寸的 LLM 上，都能验证存在（对于 LLM 来说，训练前这两类能力都是无）

## 视觉理解理解数据更重要

颜色越深效果越强，生成数据扩量变化不明显，但是VQA 数据增加，效果变化明显

## 某些理解任务更重要

P 值越高，代表这类 VQA 数据和生成任务更紧密
- 以视觉为中心的任务影响最高（看画面主体）
- 其次是表格类的数据（阅读 PDF）
- 再次是高分辨率的问答任务（等于你需要细看某个东西）
- 再次是偏难怪的高浓度知识图文数据

# 看下模型表现
## 总体

不管论文的结论，那都是扯，从我们的角度有几个可看的
- **MM 模型图像生成能力强于 SD1.5**，等于一个 8B 的玩意已经强于 Diffusion 的 baseline 了
- VQA 能力显著弱于 GPT-4V
- 别的没啥感知啊，感觉就是通过一个高 ROI 的方式，让 LLM 获取了生成的能力，没了。
## 有趣的是，AR 可以直接做 reasoning 咯

这是可以预见的，等于在模型内，就可以把 LLM 的知识能力和生成能力融合
