---
title: "DeepSeek-R1 深度解读"
type: concept
tags: [deepseek, 推理, rl, GRPO, 蒸馏]
sources:
  - type: arxiv
    id: "2501.12948"
    url: "https://arxiv.org/abs/2501.12948"
created: 2026-04-20
updated: 2026-04-26
---

# DeepSeek-R1 深度解读

## 核心贡献

R1 是 DeepSeek 最具影响力的论文，证明了**纯 RL 可不依赖 SFT 激发出 LLM 的推理能力**，是 2025 年初最重要的 RL+LLM 论文。

## 关键技术

### 1. DeepSeek-R1-Zero：纯 RL 的自我进化

R1-Zero 直接在 base model（DeepSeek-V3-Base）上用 **GRPO**（Group Relative Policy Optimization）进行 RL 训练，**完全不经过 SFT**。奖励信号只有：
- **格式奖励**：要求模型输出包含 `<think>` 和 `</think>` 标签
- **答案正确性奖励**：基于规则的答案验证（数学有标准答案，编程可执行验证）

结果是惊人的：模型自发涌现出**反思（reflection）**和**自我验证（self-verification）**行为——这些能力从未被显式编程，是通过 RL 奖励自然进化出来的。

### 2. RL 算法：GRPO

GRPO 是 DeepSeek 自行研发的 RL 算法，替代了 PPO。核心思路：
- 对同一个 prompt 生成 G 个 response 组
- 用组内相对排名计算 advantage：优于平均则正奖励，劣于平均则负奖励
- 简化了 value network，降低计算开销

### 3. DeepSeek-R1：多阶段 RL + 冷启动

R1 相比 Zero 的改进是增加了**冷启动阶段**：
1. 用少量高质量 SFT 数据（包含 CoT 示例）做冷启动
2. 在冷启动模型上做面向推理的 RL
3. 再做 RLHF（奖励模型 + rejection sampling）进一步对齐

冷启动解决的是 Zero 存在的一些可读性问题（如语言混杂）。

### 4. 蒸馏：小模型也能有推理能力

DeepSeek 将 R1 的推理能力通过**知识蒸馏**迁移到小模型：
- 用 R1 生成的 800K CoT 数据微调 Qwen/LLaMA 系列
- **关键发现**：蒸馏比从头做 RL 更好（后者小模型往往训崩或收敛差）
- 7B 蒸馏模型就能超越 GPT-4o——但这是靠 SFT 蒸馏，不是小模型自己 RL

## 实验结果

| Benchmark | DeepSeek-R1-Zero | DeepSeek-R1 | GPT-4o |
|-----------|-----------------|-------------|--------|
| AIME 2024 | 71.0% | 79.8% | 74.6% |
| MATH-500 | 86.7% | 97.3% | 96.4% |
| SWE-Bench | 49.2% | 49.2% | 49.3% |

结论：R1 在数学推理上显著超越 GPT-4o；软件工程与 GPT-4o 持平。

## RL 工程角度的关键问题

**Q: R1-Zero 的 RL 训练是否稳定？**
论文提到 Zero 在训练中期出现了 reward plateau（收益高原），模型行为陷入重复模式。通过增加 "absolve" token 让模型学会放弃当前推理路径才得以突破。这说明即使对 DeepSeek 这样的团队，**无监督的纯 RL 训练仍存在 non-trivial 的工程难题**。

**Q: GRPO vs PPO — 工程取舍？**
GRPO 省去了单独训练 critic model 的开销，但 group size G 直接影响 variance。G 太小 → advantage 估计不准；G 太大 → 生成成本高。DeepSeek 的 G 值选择细节未公开，这是工程上很关键的超参数。

**Q: 为什么蒸馏有效但直接 RL 训练小模型不行？**
论文明确指出：小模型（≤7B）直接做 RL 训练，模型往往无法学到有效的推理策略，容易崩溃或陷入无意义重复。这与 Process Reward Model（PRM）在小模型上的脆弱性是同一类问题——**小模型的信用分配（credit assignment）能力有限**。

## 对 RL 研究的意义

1. **验证了 RL scaling hypothesis**：扩展 RL 计算量（不是模型参数）可以解锁新能力
2. **展示了 LLM 自我进化的可能性**：无需人工标注，模型自己学会"思考如何思考"
3. **推动了推理方向的研究热潮**：R1 之后国内几乎所有大厂都出了自己的推理模型

## 局限性

- 数学/代码之外的能力（如开放域对话）R1 并不总是超越非推理模型
- 推理过程的 CoT 可解释性好，但无法保证事实准确性
- 蒸馏数据 800K 是闭源 R1 模型生成的——DeepSeek 没有开源推理过程数据
