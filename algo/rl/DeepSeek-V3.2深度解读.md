---
title: "DeepSeek-V3.2 深度解读"
type: concept
tags: [deepseek, V3.2, DSA, 推理, agent]
sources:
  - type: arxiv
    id: "2512.02556"
    url: "https://arxiv.org/abs/2512.02556"
created: 2026-04-20
updated: 2026-04-26
---

# DeepSeek-V3.2 深度解读

## 核心定位

V3.2 是 V3 的更新版，核心改进方向从"基础能力"转向**推理 + Agent 能力**。关键词：
- DeepSeek Sparse Attention (DSA)：稀疏注意力降低长文本计算复杂度
- Scalable RL Framework：针对推理和 Agent 任务的 RL
- Multi-token Prediction 扩展到 post-training 阶段

## 核心技术

### DSA (DeepSeek Sparse Attention)

标准 Transformer 的 attention 是 O(n²) 复杂度，长上下文场景（如 128K+ tokens）计算量爆炸。

DSA 的思路：**不是所有 token 都做全连接 attention，而是稀疏地选择部分 token 做 attention**。具体策略论文没有详细描述，但核心指标是：
- 计算复杂度大幅降低
- 长上下文性能保留（论文声称"preserving model performance"）

### Multi-Token Prediction 进入 Post-training

V3 的 MTP 只在预训练阶段使用。V3.2 将 MTP 扩展到 post-training（RL 阶段），使得推理时可以直接生成多个 token 而非逐个生成。

**RL 意义**：MPO 作为 RL 的辅助目标，可以在推理阶段提供更快的 token 生成——这对 Agent 场景（需要大量 tool use 调用）非常重要。

### Scalable RL Framework

V3.2 明确提出了**可扩展的 RL 框架**，使用可验证奖励信号（verifiable reward signals）来提升推理和 Agent 性能。

关键问题：**这里说的 RL 是 GRPO 还是其他算法？PPO？** 论文没有明确说明，但从 R1 的经验看，大概率是基于 GRPO 的改进版。

## 推理能力的来源争议

这里有一个重要问题：**V3.2 的推理能力是从 V3 继续 RL 微调来的，还是从 R1 蒸馏来的？**

从时间线看：
- V3 发布：2024-12-27
- R1 发布：2025-01-22
- V3.2 发布：2025-12-02

V3.2 在 R1 之后 11 个月，如果 V3.2 的推理能力是 R1 级别，则说明 DeepSeek 已经将 R1 的 RL 推理训练方法规模化地迁移到了基础模型后训练流程中。

## 实验结果

论文声称 V3.2 在推理、代码、Agent benchmark 上与前沿闭源模型持平。但**没有给出具体数字对比表**，这是该论文最被诟病的地方——更像是一个产品发布公告而非严肃学术论文。

## 对 RL 研究者的意义

V3.2 的真正价值在于：它展示了 **RL 推理能力可以「附着」在任意基座模型上**，不是只有特定架构才能做推理。但具体方法（R1 的 RL 流程能否直接复用到 V3.2 上？）细节太少无法判断。
