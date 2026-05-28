---
title: GLM-4 / ChatGLM 系列深度解读
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [model-release, gln, chatglm, peft, thu,智谱]
sources: [raw/papers/2024/GLM-4系列.md]
---

# GLM-4 / ChatGLM 系列深度解读

## 一句话总结

GLM-4 系列（清华大学 KEG 实验室 + 智谱AI）是中国最早的千亿参数开源模型家族，从 GLM-130B（2022）到 GLM-4 All Tools（2024），演进路径清晰：稠密模型 → PEFT 适配 → 多模态 + Agent。但截至 2026 年，GLM-5 产品已上线却无技术论文，是主要遗憾。

## 论文概览

| 项目 | 内容 |
|------|------|
| 核心论文 | ChatGLM: A Family of Large Language Models from GLM-130B to GLM-4 All Tools |
| arXiv ID | 2406.12793 |
| 作者 | Team GLM（清华大学 KEG 实验室 + 智谱AI） |
| 发布时间 | 2024-06-18 |

## 技术演进路径

```
GLM-130B（2022）
  → ChatGLM（2023）：130B + PEFT（SFT/LoRA）
  → ChatGLM2（2023）：GLM + RoPE + MHA（Multi-Head Attention）
  → ChatGLM3（2023）：推理优化 + 工具调用
  → GLM-4 All Tools（2024）：多模态 + Agent + LongContext
  → GLM-4 Voice（2024-12）：语音交互专项
  → GLM-5（2026-04）：产品上线，无论文
```

## 核心技术解析

### GLM（General Language Model）架构

GLM 是清华大学自研的 Transformer 变体，核心设计：

- **Autoregressive Blank Infilling（自回归填空）**：随机遮盖文本片段，让模型从双向上下文中预测这些片段，融合了 MLM 和 AR 的优点
- **Single sequence prediction**：不像 MLM 需要多片段独立预测，GLM 用单个序列完成
- 相比 BERT 的 [MASK] token 方式，GLM 的方式更接近 GPT 的自回归生成

### ChatGLM 的 PEFT 路线

GLM-130B 有 130B 参数，不可能全参数微调。ChatGLM 选择了 PEFT（Parameter-Efficient Fine-Tuning）路线：

- **ChatGLM（2023）**：SFT（监督微调）
- **ChatGLM2/3**：LoRA + 量化（QLoRA），在消费级 GPU 上可微调
- 这个选择让学术界可以低成本研究千亿模型

### ChatGLM2 的 RoPE + MHA 升级

- **RoPE（Rotary Position Embedding）**：更好的位置编码，支持长上下文
- **MHA（Multi-Head Attention）**：标准的多头注意力

### ChatGLM3 的工具调用

ChatGLM3 引入了 function calling 能力：
- 可以调用外部 API
- 可以使用代码解释器
- 开始了从"对话模型"到"Agent"的转型

### GLM-4 All Tools

2024 年的 GLM-4 All Tools 整合了：
- **多模态**：文本 + 图片 + 代码
- **Agent 能力**：工具调用 + 自动化任务
- **LongContext**：更长的上下文窗口

## 衍生论文

| 论文 | arXiv ID | 方向 |
|------|---------|------|
| GLM-4-Voice | 2412.02612 | 语音交互 |
| ChatGLM-Math | 2404.02893 | 数学推理 |
| ChatGLM-RLHF | 2404.00934 | RLHF 对齐 |

**GLM-4-Voice** 特别值得关注：这是中国团队在语音 LLM 方向的重要探索，比 GPT-4o 的语音交互更早发布论文。

## 实验分析

原始资料中没有具体 benchmark 数字，以下基于已知信息推断：

- GLM-130B 当时（2022）和 GPT-3 175B 相比，在中文任务上有竞争力
- ChatGLM 系列在中国市场的本地化部署中广泛使用
- GLM-4 All Tools 的工具调用能力对标 GPT-4 的 function calling

**关键问题：GLM-4 的 benchmark 数字在哪里？**

arXiv:2406.12793 这篇论文的技术报告里应该有，但原始资料中没有具体数字。这篇论文的发表时间是 2024-06-18，而 GLM-4 All Tools 是 2024 年底/2025 年初发布的，所以论文反映的是更早版本的能力。

## GLM-5 的现状（截至 2026-04）

**GLM-5 产品已上线（智谱官网），但：**
- ❌ 无技术论文
- ❌ 无技术博客
- ❌ 技术细节完全未知

这是一个很大的问题——竞争对手（Qwen、DeepSeek、 MiniMax）都有技术博客或论文，而 GLM-5 的神秘感让人无法评估其真实能力。

## 局限性

1. **GLM-5 无技术文档**：最大的局限性，无法评估 GLM-5 的真实技术水平和创新能力
2. **GLM-130B 的时代局限**：2022 年的稠密千亿模型，和 2024-2026 年的 MoE 模型相比，训练成本劣势明显
3. **PEFT vs 全参数微调**：LoRA 等 PEFT 方法在某些任务上不如全参数微调，尤其是需要大幅度行为改变的任务
4. **Benchmark 对比缺失**：没有和 Qwen2.5、DeepSeek 系列的直接对比

## 对 RL 训练工程师的意义

1. **GLM 架构的独特性**：GLM 的填空目标函数和标准 GPT 不同，理解这个差异对选择预训练基座有参考价值
2. **PEFT 的工程价值**：ChatGLM 的 LoRA 适配经验对垂直领域 RL 微调有直接参考价值
3. **Agent 能力的演进**：从 ChatGLM2（工具调用）到 GLM-4 All Tools 的演进，展示了让 LLM 学会使用工具的渐进路径
4. **GLM-4-Voice**：语音 LLM 的论文值得关注，语音交互 + RL 是下一个方向

## 相关概念

- [[MoE架构]] — GLM-4 之后是否转向 MoE 未知，但这是大模型的趋势
- [[AgentRL框架]] — GLM-4 All Tools 的 Agent 能力是演进方向
- [[CISPO算法]] — GLM-4-RLHF 论文值得关注
- [[Qwen2.5技术报告深度解读]] — Qwen 系列是 GLM 的主要竞争对手

## 关联公司/模型

- 清华大学 KEG 实验室（entity）
- 智谱AI（entity）
- GLM-4 Voice（arXiv:2412.02612）—— 语音 LLM 方向
- GLM-5 —— 无论文，无法分析
