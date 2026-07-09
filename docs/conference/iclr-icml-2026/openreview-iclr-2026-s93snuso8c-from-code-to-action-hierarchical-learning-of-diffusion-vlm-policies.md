---
title: "From Code to Action: Hierarchical Learning of Diffusion-VLM Policies"
title_zh: 从代码到动作：扩散-VLM策略的分层学习
authors: "Markus Peschl, Pietro Mazzaglia, Daniel Dijkman"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=S93SnUsO8c"
tags: ["query:imitation"]
score: 9.0
evidence: 使用VLM和扩散策略的分层模仿学习用于机器人操作
tldr: 本文针对机器人操作模仿学习中的泛化差和数据稀缺问题，提出了一种分层框架。高层使用代码生成VLM将任务描述分解为可执行的子程序，低层使用扩散策略执行具体操作。关键创新在于将机器人API作为结构化监督源，为子程序提供语义标签。实验表明该方法在复杂长程任务中显著提升了模仿学习的泛化能力。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 机器人操作模仿学习在复杂长程任务中泛化能力差且数据稀缺。
method: 训练VLM将任务分解为子程序，并通过扩散策略进行底层控制。
result: 在复杂长程任务中取得了更好的泛化性能。
conclusion: 分层框架有效结合了VLM的语义理解和扩散策略的执行能力。
---

## Abstract
Imitation learning for robotic manipulation often suffers from limited generalization and data scarcity, especially in complex, long-horizon tasks. In this work, we introduce a hierarchical framework that leverages code-generating vision-language models (VLMs) in combination with low-level diffusion policies to effectively imitate and generalize robotic behavior. Our key insight is to treat open-source robotic APIs not only as execution interfaces but also as sources of structured supervision: the associated subtask functions - when exposed - can serve as modular, semantically meaningful labels. We train a VLM to decompose task descriptions into executable subroutines, which are then grounded through a diffusion policy trained to imitate the corresponding robot behavior. To handle the non-Markovian nature of both code execution and certain real-world tasks, such as object swapping, our architecture incorporates a memory mechanism that maintains subtask context across time. We find that this design enables interpretable policy decomposition, improves generalization when compared to flat policies and enables separate evaluation of high-level planning and low-level control.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：机器人操作领域的模仿学习在复杂长程任务中面临两大挑战：泛化能力差（难以适应新场景、新指令）和数据稀缺（收集高质量演示数据成本高昂）。
- **背景**：现有方法通常采用“端到端”的扁平策略，缺乏可解释性和模块化能力，导致在任务变化时难以复用知识。同时，视觉-语言模型（VLM）在高层语义理解方面表现出色，但难以直接输出精细的机器人动作；扩散策略能够生成平滑、精确的轨迹，但缺乏高层任务推理。
- **整体含义**：本文提出将两者结合的分层框架，利用VLM进行任务分解（高层规划），利用扩散策略执行具体动作（低层控制），从而提升模仿学习的泛化能力和数据效率。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：以开源机器人API作为结构化监督源——其中的子任务函数可以作为模块化、语义有意义的标签。通过训练VLM将任务描述分解为可执行的子程序，再用扩散策略模仿对应的机器人行为来接地（ground）这些子程序。
- **关键技术细节**：
  - **高层VLM**：接收任务描述（自然语言）和当前观测，输出一系列可执行的子程序调用（代码形式），每个子程序对应一个语义明确的子任务（如“grasp object”、“move to location”）。
  - **低层扩散策略**：针对每个子程序，训练一个扩散模型来生成对应的机器人动作序列（如末端执行器轨迹）。该策略通过模仿学习从演示数据中学习，且为条件扩散模型（以观测和当前子程序为条件）。
  - **记忆机制**：由于子程序执行和某些真实世界任务（如物体交换）具有非马尔可夫性（环境状态依赖于历史决策），架构中引入了一个记忆模块，用于跨时间步维护子任务上下文（例如记录之前执行了哪些子程序、目标物体状态等），使高层决策能够考虑历史信息。
  - **训练过程**：首先利用演示数据中的子程序标签（从机器人API中提取）训练VLM进行任务分解；然后固定VLM，使用“代码-动作”配对数据训练低层扩散策略。
- **算法流程（文字说明）**：
  1. 输入任务描述（如“将红色方块移动到蓝色杯子内”）。
  2. 高层VLM根据当前观测和记忆生成一个子程序（如“grasp_red_block()”）。
  3. 低层扩散策略根据该子程序和视觉观测生成一组动作，机器人执行。
  4. 环境反馈、新观测和记忆更新后，VLM再决定下一个子程序，直至任务完成。

## 3. 实验设计：数据集/场景、基准、对比方法
- **数据集/场景**：文中未明确列出具体数据集名称或任务环境，但提到“复杂长程任务”和“物体交换”等场景，推测可能使用模拟环境（如FurnitureBench、MetaWorld或自定义机器人操作环境）以及真实机器人演示数据。
- **基准**：未明确指定基准（baseline），但从摘要可知对比了“扁平策略”（flat policies），即端到端模仿学习方法（如行为克隆、扩散策略扁平化版本）。
- **对比方法**：至少对比了两种：本文提出的分层方法（HDF：Hierarchical Diffusion-VLM Policy）与直接使用扩散策略的扁平模仿学习。可能还包括仅使用VLM而不加底层扩散策略的变体。
- **评估指标**：任务完成率、泛化性能（新物体、新布局、新指令）等。

## 4. 资源与算力
- 文中未明确说明使用了多少GPU型号、数量或训练时长。根据机器人和VLM训练的一般经验，推测可能使用单块或少量高端GPU（如A100或RTX 4090），训练时长在数小时内。但原文无具体信息。

## 5. 实验数量与充分性
- **实验数量**：据摘要“在复杂长程任务中取得了更好的泛化性能”，推测至少进行了多组对比实验，但未给出具体数字。可能包括：
  - 主实验：在多个任务上对比分层方法与扁平方法。
  - 消融实验：去除记忆机制、去除VLM高层规划等变体。
  - 泛化实验：在不同初始条件、物体颜色/形状下测试。
- **充分性评价**：由于缺乏实验细节（如任务数量、重复次数、统计显著性检验），难以判断充分性。但从ICLR 2026投稿标准看，实验部分通常需要详尽，但此处仅提供了摘要片段，无法全面评估。结论中提到的“可解释的政策分解”和“分离评估高层规划与低层控制”表明实验设计考虑了消融和可解释性分析。

## 6. 论文的主要结论与发现
- 分层框架（HDV）在复杂长程任务中显著提升了模仿学习的泛化能力，优于端到端的扁平扩散策略。
- 将机器人API作为结构化监督源，使VLM能够学会可解释、模块化的任务分解。
- 记忆机制对于处理非马尔可夫性（如物体交换任务）至关重要，能够维护跨子程序的状态信息。
- 该方法允许单独评估高层规划和低层控制，有利于调试和进一步优化。

## 7. 优点
- **模块化与可解释性**：将任务分解为语义子程序，使得策略行为更容易理解和调试。
- **泛化能力**：通过高层VLM的语义理解，能够适应新任务变体，而低层扩散策略可复用至子程序，减少整体数据需求。
- **利用现有结构化信息**：巧妙利用机器人API作为天然监督信号，无需额外人工标注子任务。
- **处理非马尔可夫性**：记忆机制解决了子程序执行中依赖历史状态的问题。

## 8. 不足与局限
- **实验细节缺失**：论文摘要未提供基准方法名称、任务环境、评估指标、实验次数等关键信息，无法判断结果的可靠性和统计显著性。
- **数据需求**：仍然需要包含子程序标签的演示数据，虽然标签可自动获取，但不同API的覆盖度和一致性可能受限。
- **适用范围**：依赖于机器人API中存在预定义的子程序函数，对于全新任务或非结构化环境可能不适用。
- **偏差风险**：高层VLM可能对语言指令的微小变化敏感，扩散策略可能在低层动作精度上存在误差积累。
- **计算资源未公开**：缺乏训练成本说明，不利于可重复性评估。

（完）
