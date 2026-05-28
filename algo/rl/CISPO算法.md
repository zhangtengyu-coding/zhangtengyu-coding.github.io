---
title: CISPO 算法
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [rl, algorithm, moe]
sources: [raw/papers/2026/MiniMax-M2.5-技术博客.md]
---

# CISPO 算法

## 来源

MiniMax M2.5 技术博客（2026-02-12）：用于 MoE 模型大规模强化学习训练的稳定性保障。

## 核心内容

- **全称**：CISPO（疑似 Clip-based Importance Sampling Policy Optimization）
- **用途**：解决 MoE 模型在大规模 RL 训练中的不稳定问题
- **原理**：通过重要性采样裁剪（clipping）控制策略更新的幅度，防止大参数模型在强化学习过程中的剧烈参数漂移

## 在 M2.5 中的应用

M2.5 使用 MoE 架构，在数十万个 Agent 脚手架与真实环境中进行大规模 RL 训练，CISPO 算法保障了训练稳定性。

## 相关概念

- [[过程奖励]] — 过程奖励机制，解决长上下文信用分配
- [[AgentRL框架]] — MiniMax 的 Forge 框架
- [[MoE架构]] — MoE 架构是 CISPO 的应用背景
