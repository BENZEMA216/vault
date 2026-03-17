# MetaClaw: Meta-Learning + RL 流程分析

> 来源：[aiming-lab/MetaClaw](https://github.com/aiming-lab/MetaClaw)
> 记录日期：2026-03-17

## 核心架构

MetaClaw 是一个 coding agent 框架，通过 **skill 进化（meta-update）+ GRPO RL 微调（inner-update）** 实现 continual meta-learning，全程异步不阻塞用户。

## 完整 Pipeline

```
用户对话
   ↓
┌─ api_server.py (透明代理) ──────────────────────────┐
│  1. 拦截请求                                          │
│  2. skill_manager.py 检索相关 skills → 注入 prompt    │
│  3. 转发给 LLM，返回响应给用户                         │
│  4. 异步存入 replay buffer (ConversationSample)       │
└───────────────────────────────────────────────────────┘
   ↓ 异步
┌─ prm_scorer.py (Process Reward Model) ───┐
│  对每条对话的响应质量打分                    │
│  打分完的 sample 标记 reward 入 buffer      │
└──────────────────────────────────────────┘
   ↓ batch 攒满时触发
┌─ trainer.py (GRPO 训练) ────────────────────────────┐
│  1. 从 buffer 取一批有 reward 的 samples              │
│  2. 用 GRPO 算法做 RL 策略更新（LoRA 微调）           │
│  3. （可选）OPD: 大模型 teacher 的 token 分布做蒸馏    │
│  4. 热替换 LoRA 权重，代理立即用新权重                  │
└──────────────────────────────────────────────────────┘
   ↓ 失败对话触发
┌─ skill_evolver.py (Skill 进化) ─────────────────────┐
│  1. 识别低分/失败对话                                  │
│  2. 用 evolver LLM 从失败中提炼新 skill（Markdown）   │
│  3. 写入 skill 库，skill_generation 版本号 +1         │
│  4. flush replay buffer 中旧版本的 samples            │
└──────────────────────────────────────────────────────┘
```

## Meta-Learning 机制

MetaClaw 的 meta-learning **不是**经典 MAML 那种二阶梯度，而是通过 **skill generation 版本控制**实现隐式的 support/query 分离：

```
Skill Generation v1 时期的对话 → 相当于 support set
     ↓ skill 进化，版本升到 v2
Skill Generation v2 时期的对话 → 相当于 query set
     ↓ 只用 v2 的数据做梯度更新
```

- 每次 skill 进化（版本 bump）后，**旧 buffer 被 flush**，只保留新版 skill 下产生的数据
- 保证模型始终在「当前最新策略」产生的数据上训练，避免 off-policy 问题
- 本质是 **continual meta-learning**：skill 进化 = meta-update，RL 训练 = inner-update

## RL 训练细节（GRPO）

| 环节 | 实现 |
|------|------|
| **数据收集** | 对话自动存入 replay buffer，每条标记 `skill_generation` |
| **Reward** | `prm_scorer.py` 异步打分，process-level reward（不只看最终结果） |
| **策略更新** | GRPO（Group Relative Policy Optimization），batch 满了就训练 |
| **参数高效** | LoRA 微调，训练完热替换权重 |
| **调度** | MadMax 模式下 `scheduler.py` + `idle_detector.py` 检测空闲窗口才跑训练 |

## OPD（On-Policy Distillation）

可选的 teacher-student 蒸馏机制：

```
Student（小模型）生成响应
       ↓
Teacher（大模型）对同一 prompt 生成 token 分布
       ↓
RL loss + KL(student ‖ teacher) × λ  → 联合优化
```

- 大模型知识加速小模型的 RL 收敛
- KL 惩罚系数 λ 可配置
- Teacher 通过 OpenAI-compatible `/v1/completions` endpoint 提供服务

## 关键代码文件

| 文件 | 职责 |
|------|------|
| `metaclaw/api_server.py` | 透明代理，拦截用户对话 |
| `metaclaw/skill_manager.py` | Skill 检索和 prompt 注入 |
| `metaclaw/skill_evolver.py` | 从失败对话提炼新 skill |
| `metaclaw/trainer.py` | GRPO RL 训练循环 |
| `metaclaw/prm_scorer.py` | Process Reward Model 打分 |
| `metaclaw/scheduler.py` | 训练窗口调度 |
| `metaclaw/idle_detector.py` | 空闲检测（MadMax 模式） |
| `metaclaw/rollout.py` | Rollout 管理 |

## 核心 Insight

> **Skill 进化当作 meta-update，RL 微调当作 inner-update**，通过版本控制实现 continual learning，而非传统 MAML 二阶优化。整个训练完全异步，不阻塞用户使用。
