---
title: Agent RL 框架
type: concept
created: 2026-04-21
updated: 2026-04-21
tags: [rl, agent, framework]
sources: [raw/papers/2026/MiniMax-M2.5-技术博客.md]
---

# Agent RL 框架：Forge

## 来源

MiniMax M2.5（2026-02-12）技术博客。

## Forge 框架核心设计

MiniMax 自研的原生 Agent RL 框架，目标是支持**任意 Agent 脚手架与环境**的高效 RL 优化。

### 设计原则

1. **完全解耦**：引入中间层，将底层训推引擎与 Agent 逻辑分离
2. **任意脚手架接入**：不依赖特定 Agent 实现，支持 Claude Code / OpenCode / Cline / Cursor 等多种脚手架
3. **异步调度优化**：平衡系统吞吐与样本的 off-policyness（策略陈旧问题）
4. **树状合并训练**：将相似轨迹合并，40 倍训练加速

### 与传统 RL 框架的区别

传统 RL 框架针对单一环境优化，Forge 则是环境无关的通用框架——同一个框架可以在编程 Agent、搜索 Agent、办公 Agent 等不同任务上切换训练。

## 关键技术突破

- **Off-policy 修正**：异步调度不可避免带来 off-policy，Forge 通过特定修正策略控制策略陈旧的影响
- **Scaling 验证**：在数十万个 Agent 脚手架与环境上训练，模型能力随算力和任务数近线性提升

## 相关概念

- [[CISPO算法]] — MoE 训练稳定性
- [[过程奖励]] — 过程奖励机制
- [[模型自我进化]] — 强化学习的 scaling 特性
