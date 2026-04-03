# 0527 - 【思考】AGENT技术实现外的AGENT产品思路

<callout emoji="round_pushpin" background-color="light-orange" border-color="light-orange">
极客公园对Lovart 创始人陈冕的访谈：[mp.weixin.qq.com](https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FF1PSFWocr7Nd1JHqZlm3Vg)
晚点对 Youware 创始人明超平的访谈：[mp.weixin.qq.com](https%3A%2F%2Fmp.weixin.qq.com%2Fs%2FCTzqphisNyyj11DVglZO3A)
ARC 浏览器创始人昨晚写的 ARC 浏览器复盘文章：[browsercompany.substack.com](https%3A%2F%2Fbrowsercompany.substack.com%2Fp%2Fletter-to-arc-members-2025)
</callout>

# 总结
## 为什么要做 AGENT？
四渡赤水不是为了搭桥过河玩才渡的，做 AGENT 也不是为了做个 AGENT 玩才做的。
三个产品为什么长现在这个样子，都是由于主创团队有自己的 **bet和想法**
- Dia 在赌两个点
  - 浏览器是被忽视的重要产品和场景，好好做一定是一个很大的机会点
  - AI 会杀死过去的浏览器，一定要探索新的浏览器
- Youware 在赌 Vibe coding 会成为新的内容创作方式，新的内容需要新的内容消费平台。这会是一个平台级机会
-  Lovart 是最像为了融资做事的团队，lovart 也是我使用时间最短的产品，在激情的产品体验期后，带给我的是长久的沉闷。Lovart 的 YouTube 宣传片更像是一个完整的产品，想要做的很多，实现的比较草率就交付了。
  - 基于 figma 的逻辑给设计师做一个AI 版本真的是合理的吗？ Figma 真的不会被杀死吗？未来的设计工具真的是 lovart 的形态吗？很多问题都没法解答，主创在接受采访表达的东西没有体现出自己和别人的认知差距

那么我在 bet 什么呢？ 我为啥做 AGENT 呢？
- 我其实只有一个朴素的相信：创作或者任何其他生产工作，绝大部分人的智力水平已经远不如 AI 了，AGENT 是把 LLM 智能比较自然地注入到产物中的一种方式
- 这也说明了为啥我一直特别抵触 workflow：我也是人，我绞尽脑汁写一个 workflow/复杂的 SP 在未来的某一天就是底模的一个笑话罢了

## 他们分别的产品主张是什么样的呢？
- Dia：做简单的、好用的 AI 容器，只是这个容器恰好是浏览器
- Youware：做有趣的，能够激发 vibe coder 创作欲望的社区

我的产品主张是啥呢？其实也比较简单，我就是想让用户用即梦的感受变得 **简单&爽。**
我到目前为止做的最满意的feature还是在 WEB 上线 deepseek 的时候，一次给了用户 4 个 Prompt并且把 WEB 的前端限制放松了一些，让用户有了 Prompt 可以一直点点点。这体验真挺爽的。
做 AGENT 的痛苦原因也很容易就找到了，在需求实现上的妥协，每一次妥协都是一点痛苦，每一个不能国内上线的海外模型都是一点点痛苦。

# 阅读 - 只是我的记录而已，推荐看原文～
## ARC 与 Dia
### ARC做错了什么？
1. 数据显示有问题，但是没有尽早承认
1. AI改造太慢了
1. 有灵感和想法，却因为自我奇怪的ego没有更快动手
### ARC起源
- 产品出发点：浏览器对人来说贼几把重要，但是之前的浏览器产品没有被投入足够的重视、关注和智力投入
### ARC从哪里开始失败的
- 新奇税：ARC太新了，对大部分用户来说，学习成本过高
- 指标表征：ARC更像是一个垂类品味工具，而不是一个大众浏览器
<quote-container>
 Only 5.52% of DAUs use more than one Space regularly. Only 4.17% use Live Folders (including GitHub Live Folders). It's 0.4% for one of our favorite features, Calendar Preview on Hover.
</quote-container>

- ARC 太渐进了，缺少真实的 technical unlock
- 保持一个大众产品的爱和初心
<quote-container>
So when people ask how venture capital influenced us -- or why we didn't just charge for Arc and run a profitable business -- I get it. They're fair questions. But to me, they miss the forest for the trees. If the goal was to build a small, profitable company with a great team and loyal customers, we wouldn't have chosen to try and build the successor to the web browser -- the most ubiquitous piece of software there is.** The point of this was always bigger for us: to build good, cared for software that could have an impact for people at real scale.**
</quote-container>

### 为啥不是集成功能，而是一个新产品
1. Dia 是一个修复ARC错误的机会：
  简单 > 新奇
  <quote-container>
  This is now the idea behind Dia: hide complexity behind familiar interfaces.
  </quote-container>

  速度非常重要
1. 整了半天，发现ARC屎山太大，解决不了了
### Building Dia
1. 不要假装AI不会颠覆你的行业
  1. 判断是传统浏览器会 die，并不是需求不存在了，而是需求被满足的环境发生了很大的改变
1. AI 浏览器和WEB 浏览器的三点大不同：
  1. Web Page 不再是主要交互页面/容器，会变成被AI 调用的工具&上下文
  1. 新的交互页面从熟悉的旧内容开始初步演化
  <quote-container>
   In this new world, two opposing forces are simultaneously true.
  </quote-container>

## Lovart
小失望，感觉主创和我身边的同学们没有认知差距，在实现上也没有很强烈的巧思（其实 youtube 视频里是有的，但是实现的产品没太有了）
## Youware
<callout emoji="heart" background-color="light-orange" border-color="light-orange">
真的很好玩，摸鱼的时候我能刷很久....
</callout>

Vibe coder 是那些不会写代码，但有创作欲和创造力的非开发者——比如设计师、产品经理，也包括我自己。
我一直在服务创作者，而今天的新创作方式就是 coding。怎么激发这类创作？《福格行为模型》的总结依然有效：动机、能力、触发器。
目前的 AI 产品都太效率导向，这个世界也需要更多元的东西。我们今天在思考这些取舍，我希望工具足够易用、效果足够好，也希望用户在能享受作为人和创作者的创造过程。有趣总是重要的。
苹果的方式就是只展示屏幕使用时间，展示事实比评价用户更高明、优雅。
这段经历对我影响挺大。今天做社区，我更明确一点：我们可以不喜欢某种内容，不推荐它，但不该用平台价值观去直接评判它。社区调性、文化，就是通过一系列小选择一点点长出来的。
