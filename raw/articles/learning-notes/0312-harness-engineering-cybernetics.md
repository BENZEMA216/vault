# Harness Engineering 就是控制论

> 来源：[@dotey 推文](https://x.com/dotey/status/2031231856071372830) 翻译/解读 @odysseus0z 文章
> 日期：2026-03-10

## 核心论点

OpenAI 提出的 "Harness Engineering" 本质上就是诺伯特·维纳 1948 年提出的**控制论（Cybernetics）**在 AI 编程时代的复现。工程师的角色从"写代码"转变为"设计环境、构建反馈回路、将架构约束编成规则"，让 AI agent 在约束框架内生成代码。

## 三个历史类比

| 时代 | 旧模式 | 新模式 |
|------|--------|--------|
| 18世纪 瓦特蒸汽机 | 工人手动调节阀门 | 离心式调速器自动调节 |
| 云原生时代 Kubernetes | 工程师手动重启服务 | 编写声明式规格，Controller 自动维护 |
| AI 时代 | 开发者手写代码 | 设计约束框架，AI agent 生成代码 |

三者的共同模式：**人从"直接执行者"变成"反馈系统的设计者"。**

## 关键洞察

1. **Agent 失败的根因不是能力不足，而是判断标准没有外化** — 跳过文档、测试和架构约束，AI 就无法学习团队标准
2. **必须校准"传感器和执行器"** — 明确定义质量标准、测试框架、编码规范，这些就是控制论中的传感器
3. **生成-验证的不对称性** — P vs NP 的实际应用：生成代码容易，验证代码正确性难。未来需要超越机器本身能力的评判标准

---

## 参考文献逐条总结

### [1] OpenAI — Harness Engineering: Leveraging Codex in an Agent-First World

OpenAI 2026 年 2 月发布。一个小团队用 5 个月时间，**完全不手写代码**，用 Codex agent 生成了约 100 万行生产级代码并交付 beta 产品。

**四大支柱**：
- **Context Architecture**：分层渐进式文档，结构化目录充当 single source of truth
- **Agent Specialization**：每个 agent 有限定 prompt 和受限工具集
- **Persistent Memory**：基于文件系统而非对话历史
- **Structured Execution**：研究 → 计划 → 执行 → 验证

架构约束通过 linter 和结构测试强制依赖层级 Types → Config → Repo → Service → Runtime → UI。还有"垃圾回收 agent"定期巡检文档不一致和架构违规。

> 参考：[OpenAI 原文](https://openai.com/index/harness-engineering/) | [InfoQ 报道](https://www.infoq.com/news/2026/02/openai-harness-engineering-codex/) | [Martin Fowler 分析](https://martinfowler.com/articles/exploring-gen-ai/harness-engineering.html)

### [2] 瓦特的离心式调速器 (Centrifugal Governor)

1788 年 James Watt 将离心调速器应用于蒸汽机。两个旋转飞球通过离心力向外摆动 → 转速升高时飞球升高 → 联动节流阀减少蒸汽流量 → 发动机减速。纯机械反馈回路，无需人工干预即可自动稳定转速。

维纳正是在研究瓦特调速器时，从希腊语"舵手"（κυβερνήτης）中得到灵感，将学科命名为 Cybernetics。调速器就是控制论的物理原型。

> 参考：[Wikipedia](https://en.wikipedia.org/wiki/Centrifugal_governor)

### [3] Kubernetes Controller 架构

K8s Controller 实现**非终止控制循环**：持续将集群当前状态推向期望状态。

核心模式：`Watch → Observe → Reconcile → Report`

```
期望状态(spec) → Controller 比较 → 当前状态(status) → 采取行动 → 循环
```

类比恒温器：设定温度 = desired state，当前室温 = current state，空调开关 = 控制动作。多个专门化 Controller 各管一类资源，通过 label 和 ownerReference 协调。

> 参考：[K8s 文档](https://kubernetes.io/docs/concepts/architecture/controller/)

### [4] 诺伯特·维纳与控制论 (Cybernetics, 1948)

维纳定义控制论为**"动物与机器中的控制与通信的科学研究"**。

- **反馈回路**：系统用自身表现的信息调整行为，如舵手根据航向偏移持续调整舵位
- **自我调节**：系统通过反馈机制在变化条件下维持特定目标
- **跨学科**：同一原理适用于机械系统（调速器）、生物系统（神经反射）、信息系统（K8s、AI agent）

> 参考：[Wikipedia](https://en.wikipedia.org/wiki/Cybernetics)

### [5] Anthropic — Building a C Compiler with Parallel Claude Agents

Nicholas Carlini 用 **16 个并行 Claude Opus 实例**，~2000 次 session、$2 万 API 成本，从零构建了 10 万行 Rust C 编译器，能编译 Linux 6.9。

**关键经验**：
- **测试质量是根基** — agent 会朝你设的 metric 优化，测试必须近乎完美
- **为 AI 约束做设计** — 减少 context 污染、提供清晰文档
- **并行 + 角色分工** — 编译核心、去重、性能优化、设计审查由不同 agent 负责
- **锁机制协调** — `current_tasks/` 目录防止重复工作

成果：GCC torture test 99% 通过率，能编译 QEMU/FFmpeg/SQLite/PostgreSQL，能跑 Doom。局限：新 feature 频繁 break 已有代码，说明当前模型缺乏系统级整体一致性。

> 参考：[Anthropic 原文](https://anthropic.com/engineering/building-c-compiler)

### [6] GitHub Commit — claudes-c-compiler 初始脚手架

Commit `a28ff299`，作者是 **Claude Opus 4.6**（不是人类）。内容是锁定"初始编译器脚手架"任务：完整目录结构（frontend/ir/passes/backend/common/driver）、Lexer/Parser/Sema 基础、x86_64 后端 + ELF 生成、ARM64/RISC-V stub。

这个 commit 本身就是 agent 自主工作的实证：AI 自己创建任务锁、定义目标、执行开发。

> 参考：[GitHub Commit](https://github.com/anthropics/claudes-c-compiler/commit/a28ff299)

### [7] P vs NP 问题

计算机科学最大未解问题：**每个能快速验证的问题，是否也能快速求解？**

- **P 类**：存在多项式时间算法可以求解的问题
- **NP 类**：给定答案后可在多项式时间内验证的问题
- **核心不对称性**：解数独很难，验证数独答案很容易

主流共识 P ≠ NP，即验证本质上比生成容易。对 Harness Engineering 的启示：AI 生成代码容易，验证正确性难，因此核心挑战是**构建足够好的验证系统**。

> 参考：[Wikipedia](https://en.wikipedia.org/wiki/P_versus_NP_problem)

### [8] Training Verifiers to Solve Math Word Problems (arXiv: 2110.14168)

OpenAI 2021 年论文。提出 GSM8K 数据集（8500 道小学数学应用题）和验证器训练方法：不追求模型一次生成正确答案，而是生成多个候选解，训练 verifier 从中筛选最优解。验证方法在数据扩展方面优于直接微调。

支撑"生成-验证不对称性"论点：与其让 AI 一次做对，不如多次尝试 + 验证器筛选。这正是 Harness Engineering 的理论基础。

> 参考：[arXiv](https://arxiv.org/abs/2110.14168)

---

## 逻辑链

```
控制论 (1948, 维纳) [4]
  ├─ 物理原型：瓦特调速器 [2]
  ├─ 云原生实践：K8s Controller [3]
  └─ AI 工程实践：Harness Engineering [1]
        ├─ 理论支撑：P vs NP 不对称性 [7] + Verifier 论文 [8]
        └─ 实践验证：Carlini C 编译器 [5][6]
```

**一句话总结**：Harness Engineering 不是新发明，而是控制论在 AI 编程时代的必然回归——工程师的核心价值从"直接执行"转向"设计反馈系统"。
