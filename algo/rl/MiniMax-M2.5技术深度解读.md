---
title: MiniMax M2.5 技术深度解读
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [model-release, process-reward, cispo, forge, minimax, agent-rl]
sources: [raw/papers/2026/MiniMax-M2.5-技术博客.md]
---

# MiniMax M2.5 技术深度解读

## 一句话总结

MiniMax M2.5 是真正工程化的 Agent 原生模型，通过 Forge 框架实现 40 倍训练加速、用 CISPO 算法解决 MoE 大规模 RL 训练稳定性、用 Process Reward 解决长上下文信用分配，在 SWE-Bench 和 MLE-Bench 上做到当时全球第一。

## 核心数据

| 指标 | 数值 | 意义 |
|------|------|------|
| SWE-Bench Verified | 80.2% | **当时全球第一** |
| Multi-SWE-Bench | 51.3% | **全球第一** |
| BrowseComp | 76.3% | 复杂搜索任务 |
| GDPval-MM 胜率 | 59.0% | 多模态理解 |
| 速度提升 | 37%（vs M2.1） | 22.8min vs 31.3min |
| 推理速度 | 100 TPS | Token per second |
| 输入成本 | $0.3/M token | 性价比 |

> "在过去 108 天里，我们陆续更新了 M2、M2.1 和 M2.5，模型的进步速度超过了我们原本的预期。"
> "1 万美金可以让 4 个 Agent 连续工作一年。"

## 四大核心技术

### 1. Forge 框架：原生 Agent RL 训练

MiniMax 透露 Forge 是他们的**原生 Agent RL 框架**，实现了 40 倍训练加速。

关键信息（官方博客原文有限，以下是推断）：
- 传统 RL 训练需要大量人工设计 curriculum
- Forge 框架让 RL 训练流程自动化程度更高
- 40 倍加速意味着：原来需要 1 个月的训练，现在不到 1 天

### 2. CISPO 算法：MoE 训练稳定性

**全称（推断）**：Clip-based Importance Sampling Policy Optimization

**解决的核心问题**：
- MoE 模型有数十万个专家模块，在 RL 训练中参数漂移更严重
- 策略更新时，大参数模型的更新幅度更大，容易导致训练崩溃
- CISPO 通过重要性采样裁剪（importance sampling clipping）控制策略更新幅度

**原理**：
```
标准 PPO/GRPO：策略更新幅度无硬限制
CISPO：引入裁剪机制，限制每次更新的最大幅度
效果：即使在数十万专家的环境下也能稳定训练
```

### 3. Process Reward（过程奖励）

这是 M2.5 最值得关注的方法创新，详细解读见 [[过程奖励]]。

核心思想：在 Agent 任务中，对每个关键步骤分别给奖励，而不是只在最终结果给一个分数。

### 4. RISE 评估体系

**RISE = Real Interactive Search Evaluation**

关键特点：
- 由**人类专家**设计真实任务，而不是机器生成
- 任务覆盖：代码搜索、技术调研、多步骤工具调用
- 对抗性强，不容易被 benchmark overfitting

## 实验分析

### SWE-Bench 80.2% 的意义

SWE-Bench 是当前最具权威性的代码 Agent benchmark。80.2% 意味着：
- 官方称"当时全球第一"（2026 年 2 月）
- 超越 Claude 3.5 Sonnet（~53%）、GPT-4o（~49%）等
- 但这是 MiniMax 自己测的，没有第三方验证

### Multi-SWE-Bench 51.3% 的挑战

Multi-SWE-Bench 比标准 SWE-Bench 难得多：
- 标准：单一代码库，1 个 issue → 1 个 PR
- Multi：多个代码库，需要跨 repo 理解和修改
- 51.3% 在这个难度下已经很强

### 速度优化的工程价值

- **37% 速度提升**：M2.1 用 31.3 分钟完成某个任务，M2.5 用 22.8 分钟
- **100 TPS**：这个推理速度非常快，支持真正的实时 Agent 交互
- **$0.3/M token**：成本控制良好

## 局限性

1. **没有正式论文**：所有信息来自官方博客，无法验证技术细节
2. **Forge 框架细节缺失**：官方只说"40 倍加速"，没有说具体怎么做到的
3. **CISPO 算法没有细节**：只是提了一下名字，连全称都没有官方说明
4. **Benchmark 可信度**：所有数字都是 MiniMax 自己测的，没有第三方验证

## 对 RL 训练工程师的意义

1. **[[CISPO算法]]** 的重要性：MoE + RL 是趋势，但训练不稳定是核心难题，CISPO 提供了解决思路
2. **[[过程奖励]]** 在长任务中的必要性：传统 outcome reward 在长 Agent 任务中不够用
3. **[[AgentRL框架]]** 的工程化：Forge 框架证明了原生 Agent RL 训练的可行性
4. **训练成本控制**：1 万美金跑 4 个 Agent 一年，这个成本估算对 RL 训练系统的设计很有参考价值

## 相关概念

- [[CISPO算法]] — MoE 训练稳定性保障
- [[过程奖励]] — 长上下文信用分配方案
- [[AgentRL框架]] — Forge 框架是 Agent RL 的工程基础
- [[CISPO算法]] — M2.5 的核心技术路径
- [[MoE架构]] — M2.5 使用 MoE 架构

## 关联公司/模型

- MiniMax（MiniMax Pte. Ltd.）
- M2.7 是 M2.5 的下一代：[[MiniMax-M2.7-自我进化深度解读]]
