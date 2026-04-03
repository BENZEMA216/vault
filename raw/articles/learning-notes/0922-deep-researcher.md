# 0922 - 【阅读】Deep researcher with test-time diffusion

> https://research.google/blog/deep-researcher-with-test-time-diffusion/

## 问题出发点：

## 主要流程

1. 生成一个研究计划（结构化的），包含一定的目的指导，提供最初的信息收集方向
1. 迭代搜索
  1. AGENT1 进行搜索（根据 Context）
  1. AGENT2 根据搜索到的文档进行总结回答
1. 生成最终的报告

1. A0 代表一些出示答案，这些答案可以类似是 seed，用来探索更大的搜索空间
1. 有一个评估者，进行 feedback
1. 每一个大难辩题都会进行微调
1. 最终融合出答案
