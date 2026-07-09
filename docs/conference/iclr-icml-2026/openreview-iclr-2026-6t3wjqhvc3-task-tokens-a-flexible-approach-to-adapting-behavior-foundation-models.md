---
title: "Task Tokens: A Flexible Approach to Adapting Behavior Foundation Models"
title_zh: 任务令牌：一种灵活适应行为基础模型的方法
authors: "Ron Vainshtein, Zohar Rimon, Shie Mannor, Chen Tessler"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=6T3wJQhvc3"
tags: ["query:imitation"]
score: 8.0
evidence: 适应机器人控制的模仿学习基础模型
tldr: 本文提出任务令牌（Task Tokens）方法，用于适应基于模仿学习的行为基础模型到特定机器人任务，避免繁琐的提示工程，同时保持模型灵活性。在多种任务上展示出优越性能。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 行为基础模型在零样本生成鲁棒行为时需精确提示工程，可能导致次优结果。
method: 在Transformer架构中集成可学习的任务令牌，无需改变基础模型结构。
result: 在机器人控制任务上相比直接提示工程获得更优性能。
conclusion: Task Tokens提供了一种灵活有效的方式来定制行为基础模型。
---

## Abstract
Recent advancements in imitation learning for robotic control have led to transformer-based behavior foundation models (BFMs) that enable multi-modal, human-like control for humanoid agents. These models generate solutions when conditioned on high-level goals or prompts, for example, walking to a coordinate when conditioned on the position of the robot's pelvis. While excelling at zero-shot generation of robust behaviors, BFMs often require meticulous prompt engineering for specific tasks, potentially yielding suboptimal results. In this work, we introduce ``Task Tokens'' - a method to effectively tailor BFMs to specific tasks while preserving their flexibility. Our approach integrates naturally within the transformer architecture of BFMs. Task Tokens trains a task-specific encoder (tokenizer), with the original BFM remaining untouched. Our method reduces trainable parameters per task by up to $\times 125$ and converges up to $\times 6$ faster compared to standard baselines. In addition, by keeping the original BFM unchanged, Task Tokens enables utilizing the pre-existing encoders. This allows incorporating user-defined priors, balancing reward design and prompt engineering.
We demonstrate Task Tokens' efficacy across various tasks, including out-of-distribution scenarios, and show their compatibility with other prompting modalities. Our results suggest that Task Tokens offer a promising approach for adapting BFMs to specific control tasks while retaining their generalization capabilities.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：基于 Transformer 的行为基础模型（BFM）在机器人控制中通过高维提示（如骨盆位置坐标）实现零样本生成鲁棒行为，但通常需要精确的提示工程才能适应特定任务，且容易导致次优性能。
- **整体含义**：提出一种灵活的适配方法——任务令牌（Task Tokens），在不改变预训练 BFM 结构的前提下，有效定制模型到具体任务，保留其泛化能力。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：在 BFM 的 Transformer 架构中集成可学习的任务令牌（Task Tokens），相当于为每个任务训练一个轻量级编码器（tokenizer），而原始基础模型权重完全冻结。
- **关键技术细节**：
  - 为每个目标任务训练一个独立的任务编码器（tokenizer），其输出作为附加令牌输入到 Transformer。
  - 不需要修改基础模型参数，仅更新任务令牌相关参数。
  - 可与已有提示模态（如坐标向量、语言指令等）兼容，支持用户定义先验知识。
- **算法流程**（文字说明）：输入原始观察和任务描述 → 通过任务令牌编码器生成任务令牌嵌入 → 与预训练 BFM 的标准输入拼接 → 模型输出控制动作。

## 3. 实验设计
- **数据集/场景**：机器人控制任务，包括分布外（out-of-distribution）场景。摘要未列出具体环境名称，但提到多种任务。
- **Benchmark**：未明确公开 Benchmark 名称，可能与现有机器人控制基准（如 RoboSuite、MetaWorld、D4RL 等）相关。
- **对比方法**：对比标准基线方法（可能包括直接提示工程、微调整个模型、适配器等）。摘要指出 Task Tokens 每任务可训练参数减少 125 倍，收敛速度快 6 倍。

## 4. 资源与算力
- 论文摘要及元数据未提及使用的 GPU 型号、数量、训练时长等具体算力信息。仅在结果中提到“收敛速度提升 6 倍”，但无绝对算力数据。**这是本报告无法回答的部分**。

## 5. 实验数量与充分性
- **实验数量**：从摘要推断至少包含多个不同任务（如行走、坐标导航等）的对比实验，以及分布外场景测试、提示模态兼容性实验。但未说明具体消融实验数量。
- **充分性**：摘要声称在多种任务上“展示出优越性能”，并验证了与多种提示模态的兼容性。但缺少与更多基线（如 Prompt Tuning、Adapter、LoRA 等）的对比细节，也未给出统计显著性检验。整体实验设计看似合理，但公开信息有限，**客观性和公平性需阅读全文进一步确认**。

## 6. 主要结论与发现
- 任务令牌可有效定制行为基础模型：参数量减少 125 倍，收敛速度快 6 倍。
- 保持基础模型不变，既能利用预训练编码器，也能融合用户先验，平衡奖励设计与提示工程。
- 在分布外任务上依然有效，并且与其他提示方式兼容。
- **结论**：Task Tokens 是一种灵活、高效的 BFM 适配方案，兼具泛化能力与任务特化能力。

## 7. 优点
- **方法亮点**：不修改预训练模型，避免了灾难性遗忘；可训练参数极少，训练速度快。
- **灵活性强**：支持用户定义先验，兼容多种提示模态，适合实际部署。
- **理论简洁**：自然地嵌入 Transformer 结构，原理易懂，易于实现。

## 8. 不足与局限
- **实验覆盖局限**：仅靠摘要无法判断是否在多种真实机器人或高维任务上验证，也未与 prompt tuning、adapter 等流行适配方法进行详尽比较。
- **偏差风险**：可能存在对特定任务或提示设计的选择偏差；收敛速度提升在更大模型规模上是否保持未知。
- **应用限制**：对于需要大幅修改基础模型分布的任务，标记化方法可能不如完全微调有效；依赖原始 BFM 的质量和覆盖范围。
- **资源信息缺失**：未提供算力细节，不利于可复现性评估。

（完）
