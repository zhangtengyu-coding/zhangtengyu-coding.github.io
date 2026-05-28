---
title: MoE 模型架构
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [rl, moe, architecture]
sources: [raw/papers/2025/Qwen2.5-Technical-Report.md, raw/papers/2026/Qwen3.6系列发布.md]
---

# MoE 模型架构

## 概述

Mixture of Experts（专家混合）是一种稀疏激活架构——模型包含大量"专家"网络，但每次推理时只激活其中一小部分，从而在总参数量很大的情况下保持高效推理。

## 关键参数

| 参数 | 含义 |
|------|------|
| 总参数量 | 模型包含的所有参数总量 |
| 活跃参数量 | 每次推理实际激活的参数 |
| 激活比例 | 活跃/总参，如 Qwen3.6-35B-A3B 为 35B/3B ≈ 8.6% |

## Qwen MoE 系列

### Qwen2.5 MoE
- Qwen2.5-57B-A14B：570 亿总参数，140 亿活跃（≈24.6%）
- Qwen2.5-14B-A14B：140 亿总参数，活跃参数待确认

### Qwen3.6 MoE
- **Qwen3.6-35B-A3B**：350 亿总参数，30 亿活跃（≈8.6%）
  - 比 Qwen3.5-35B-A3B Agent 编程能力大幅超越
  - HuggingFace 开源，可本地部署

## MoE 的训练挑战

MoE 的稀疏激活使得不同专家可能在不同训练阶段收到差异很大的梯度，导致训练不稳定。解决方案：
- [[CISPO算法]]（MiniMax）：通过重要性采样裁剪控制更新幅度
- Expert Loading：确保各专家均匀收到训练信号

## MoE 与 RL Scaling

MiniMax 的 RL Scaling 数据显示：MoE 模型在 Agent RL 框架（Forge）中，能力随算力和任务数的增加近线性提升——说明 MoE 架构非常适合大规模 RL 训练。

## 相关概念

- [[CISPO算法]] — MoE 训练稳定性
- [[模型自我进化]] — MoE 是 M2.7 的基础架构
- [[模型自我进化]] — 算力与能力的 scaling
