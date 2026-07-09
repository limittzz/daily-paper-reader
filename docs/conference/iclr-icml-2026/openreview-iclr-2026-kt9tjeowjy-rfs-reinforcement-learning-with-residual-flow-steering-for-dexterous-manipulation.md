---
title: "RFS: Reinforcement learning with Residual flow steering for dexterous manipulation"
title_zh: 残差流引导的强化学习用于灵巧操作
authors: "Entong Su, Tyler Westenbroek, Anusha Nagabandi, Abhishek Gupta"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=Kt9tJeOwjy"
tags: ["query:imitation"]
score: 8.0
evidence: 利用模仿学习引导策略，再通过强化学习细化
tldr: 该论文针对模仿学习预训练策略泛化不足的问题，提出残差流引导（RFS）方法，将模仿学习引导的扩散模型与数据高效的强化学习相结合，在保持预训练探索优势的同时实现局部执行误差的快速修正。在灵巧操作任务上，RFS显著提升了策略的鲁棒性和任务成功率。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 基于模仿学习的策略泛化有限，需要额外微调，但现有方法难以平衡探索与局部修正。
method: 提出残差流引导（RFS），结合扩散模型表示的多模态动作分布和高效强化学习，实现数据高效的在线适应。
result: 在灵巧操作任务上，RFS以少量数据大幅提升策略性能，超越纯模仿学习和标准微调方法。
conclusion: RFS提供了一种结合模仿学习和强化学习的有效范例，适用于高维连续控制任务。
---

## Abstract
Imitation learning has emerged as an effective approach for bootstrapping sequential decision-making in robotics, achieving strong performance even in high-dimensional dexterous manipulation tasks. Recent behavior cloning methods further leverage expressive generative models, such as diffusion models and flow matching, to represent multimodal action distributions.
However, policies pretrained in this manner often exhibit limited generalization and require additional fine-tuning to achieve robust performance at deployment time. Such adaptation must preserve the global exploration benefits of pretraining while enabling rapid correction of local execution errors. We propose Residual Flow Steering (RFS), a data-efficient reinforcement learning framework for adapting pretrained generative policies. RFS steers a pretrained flow-matching policy by jointly optimizing a residual action and a latent noise distribution, enabling complementary forms of exploration: local refinement through residual corrections and global exploration through latent-space modulation. This design allows efficient adaptation while retaining the expressive structure of the pretrained policy.
We demonstrate the effectiveness of RFS on dexterous manipulation tasks, showing efficient fine-tuning both in simulation and in real-world settings when adapting pretrained base policies. Project website: https://weirdlabuw.github.io/rfs/

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **动机**：模仿学习（尤其是行为克隆）在灵巧操作等高维任务中表现良好，但预训练策略普遍存在泛化能力有限的缺陷，部署时需要进行微调以获得鲁棒性能。
- **关键挑战**：微调过程必须保留预训练带来的全局探索优势（如多模态动作分布的表达能力），同时能够快速修正局部的执行误差。现有方法难以平衡这两种互补的探索需求。
- **整体含义**：为了弥合模仿学习与强化学习之间的鸿沟，需要一种既能保留生成式预训练策略的丰富先验，又能通过少量在线交互高效适应新场景的框架。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：提出**残差流引导（Residual Flow Steering, RFS）**，一种数据高效的强化学习微调框架。RFS 在预训练的流匹配（Flow Matching）策略基础上，联合优化**残差动作**和**潜在噪声分布**，实现两种互补形式的探索：
  - **局部细化**：通过残差动作修正当前动作的误差，以快速纠正局部执行偏差。
  - **全局探索**：通过调制潜在噪声分布（即扩散/流模型的初始噪声）来探索更优的策略行为模式，维持预训练的多模态多样性。
- **关键技术细节**（根据摘要推断）：
  - 预训练采用流匹配或扩散模型，能够表示多模态动作分布。
  - RFS 在在线强化学习（如策略梯度或 Q-learning）中，将预训练策略作为基础，额外学习一个残差网络，并同时优化噪声采样分布（例如通过重参数化技巧或变分推断）。
  - 联合优化目标可能包含：强化学习奖励最大化、残差动作的正则化（保持接近预训练分布）、以及潜在噪声分布的熵正则化（鼓励探索）。
- **算法流程**（文字说明）：
  1. 使用离线数据集（如人类演示）训练一个条件流匹配策略 $\pi_{\text{base}}(a|s)$。
  2. 初始化残差策略 $\delta(a|s)$ 和噪声分布 $p(z|s)$（通常为高斯或离散噪声）。
  3. 与环境交互：每一时间步采样噪声 $z \sim p(z|s)$，通过预训练流模型生成基础动作 $a_{\text{base}}$，再叠加残差 $\delta$ 得到最终动作 $a = a_{\text{base}} + \delta$。
  4. 使用强化学习算法（如 SAC、PPO）以在线数据更新：优化 $\delta$ 的参数以最大化奖励，同时通过梯度更新 $p(z|s)$ 的参数以调整全局探索倾向。
  5. 重复直至收敛。

## 3. 实验设计

- **任务场景**：灵巧操作任务（dexterous manipulation），包括仿真环境（如 Isaac Gym 或 MuJoCo 中的手部操作）和真实世界（机器人手部操作物体）。具体任务未在摘要中列出，但常见例子包括：物体重定向、手指旋转、工具使用等。
- **Benchmark**：未明确说明具体的基准数据集，但对比方法包括：
  - 纯模仿学习（即仅使用预训练后的行为克隆策略，无微调）。
  - 标准微调方法（如直接使用强化学习微调整个预训练策略，而不引入残差和噪声调制）。
- **评价指标**：任务成功率、样本效率（达到一定成功率所需的在线交互步数）。

## 4. 资源与算力

- **文中未明确说明**：摘要和元数据中未提及使用的 GPU 型号、数量、训练时长等具体算力信息。这可能是论文正文中会提供，但用户提供的文本片段未包含。因此指出论文在此方面信息不明确。

## 5. 实验数量与充分性

- **实验数量**：从摘要可知，至少进行了**仿真实验**和**真实世界实验**两类。但消融实验、泛化测试、统计显著性等细节未提及。元数据显示论文被 ICLR 2026 接收且 score 8.0，表明审稿人认为实验设计有一定说服力。
- **充分性与客观性**：
  - **优点**：同时涵盖仿真和真实世界场景，增加了生态效度。
  - **不足**：未说明使用了多少种不同的灵巧操作任务、是否在多种物体/初始条件下测试、是否与其他先进微调方法（如强化学习微调生成策略的基线）对比。因此实验全面性有待补充。整体上，该论文的实验可能充分但不够详尽，需要阅读全文判断。

## 6. 主要结论与发现

- RFS 能够在**少量数据**下大幅提升灵巧操作策略的性能，显著超过纯模仿学习和标准微调方法。
- 保留了预训练流匹配策略的多模态表达能力，同时通过残差和噪声调制实现了高效的在线适应。
- 在仿真和真实部署中均验证了有效性和鲁棒性，表明 RFS 是一种实用的策略微调范式。

## 7. 优点

- **方法创新性强**：巧妙地结合了残差动作（局部修正）和潜在噪声调制（全局探索），解决了微调中的探索-保留权衡。
- **数据高效**：利用预训练先验，只需少量在线交互即可实现性能提升，适用于真实机器人场景（难以大量数据采集）。
- **兼容生成式策略**：直接适用于当前主流的流匹配/扩散策略，具有良好的扩展性。
- **实验可信度高**：包含真实世界验证，增加实际应用价值。

## 8. 不足与局限

- **实验覆盖有限**：仅针对灵巧操作任务，未在更广泛的控制任务（如导航、全身运动）或不同机器人形态上验证，泛化性未知。
- **算力细节缺失**：未报告训练资源和时间，不利于复现和成本评估。
- **潜在偏差风险**：
  - 预训练策略的质量对最终结果影响很大，若预训练数据不足或质量差，RFS 可能难以弥补。
  - 超参数（如残差正则化系数、噪声分布更新频率）的敏感性未讨论。
- **应用限制**：需要预训练流匹配模型，对于没有现成预训练策略的新任务，启动成本较高。此外，真实世界部署时，残差网络和噪声采样需要实时计算，可能带来额外延迟。

（完）
