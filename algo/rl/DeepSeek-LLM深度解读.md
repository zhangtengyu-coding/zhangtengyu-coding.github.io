---
title: "DeepSeek LLM（7B/67B）深度解读"
type: concept
tags: [deepseek, 缩放定律, 预训练, Scaling Law]
sources:
  - type: arxiv
    id: "2401.02954"
    url: "https://arxiv.org/abs/2401.02954"
created: 2026-04-20
updated: 2026-04-26
---

# DeepSeek LLM（7B/67B）深度解读

## 核心定位

DeepSeek LLM 是 DeepSeek 的**第一代开源模型**，专注于缩放定律研究。核心发现：

1. **Compute-optimal scaling 结论与 Chinchilla 不完全一致**：DeepSeek 发现 compute budget 的大小会影响最优 model/data ratio
2. 在 7B 和 67B 规模上验证了 scaling laws
3. DeepSeek LLM 67B 训练 2T tokens，在多数 benchmark 上超越 LLaMA-2 70B

这是 DeepSeek 团队的**方法论论文**——重点不是模型架构创新，而是回答"如何科学地 scaling LLM"。

## Scaling Laws 的关键发现

DeepSeek 的 scaling 实验覆盖 1B ~ 200B 参数，每个规模训练到 2T tokens。核心发现：

**发现 1：最优模型大小与数据量的比例受 compute budget 影响**

经典 Chinchilla 论文（2022）认为给定 compute budget，应该按 1:1 的参数:token 比来 scaling（即 compute-optimal）。

DeepSeek 的发现是：**这个比例不是固定的**。当 compute budget 很大时，可以相对多放数据；当 compute budget 受限时，模型规模相对更重要。

**发现 2：训练时间（tokens 数）比模型参数更值得 scaling**

DeepSeek 建议：与其快速训练一个大模型然后停下，不如训练一个相对小一点的模型更长的时间。

**发现 3：DeepSeek 67B 在 2T tokens 后仍没有饱和迹象**

这意味着更大规模的模型和更长的训练（更多 tokens）还有提升空间。

## 架构：标准 Dense Transformer

与同期 LLaMA/Mistral 一样，DeepSeek LLM 采用标准 transformer 架构（不是 MoE）。这说明 DeepSeek 在早期就想清楚了 MoE 是下一阶段的方向——V2 随即转向 MoE。

## 对 RL 研究的间接意义

DeepSeek LLM 的价值不在 RL 本身，而在**为后续模型奠定预训练基础**：
- DeepSeek 团队的 scaling 经验直接支撑了 V2/V3 的 14.8T token 预训练
- 67B 规模上的 scaling 结论帮助团队决定 V3 的激活参数比例（37B/671B ≈ 5.5%）
- R1 的成功建立在 V3 强大基座之上，而 V3 的基座是 scaling 研究的产物

## 局限性

- DeepSeek LLM 67B 训练 2T tokens 的最终能力仍然弱于同规模 LLaMA-2 70B（虽然大多数 benchmark 领先，但差距不大）
- 论文的 scaling law 分析基于 1B~200B，**能否外推到 671B MoE 未知**
- 纯dense模型，推理成本高，不是 RL 训练的经济选择
