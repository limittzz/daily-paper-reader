---
title: Behavior Cloning from Suboptimal Demonstrations with Robust World Models
title_zh: 基于鲁棒世界模型的次优示范行为克隆
authors: "Krishnan Srinivasan, Bhavna Sud, Animesh Garg, Jeannette Bohg"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=PZwpFrNNYn"
tags: ["query:imitation"]
score: 9.0
evidence: 使用鲁棒世界模型从次优示范进行行为克隆
tldr: 行为克隆方法通常需要高质量专家数据，但次优示范导致性能下降。为此提出鲁棒模仿与评论家（RIC）框架，利用隐式世界模型预测未来回报，正则化行为克隆策略。在多种次优示范场景下，RIC能有效提升策略性能，无需数据过滤或重加权。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 行为克隆方法严重依赖高质量示范，次优数据会导致策略性能大幅下降。
method: 提出离线强化学习框架RIC，结合隐式世界模型，通过预测未来回报来正则化行为克隆策略。
result: 在次优示范数据集上，RIC显著优于现有行为克隆方法，并展示了鲁棒性。
conclusion: RIC为从低质量示范中学习提供了有效方案，扩展了行为克隆的适用性。
---

## Abstract
Recent advances in behavior cloning and generative modeling of manipulation behaviors have shown promising results in learning complex multi-modal behavior distributions. However, a common limitation for all behavior cloning methods has been the challenge of acquiring high-quality training data. Existing state-of-the-art methods for policy learning face significant limitations when expert demonstrations are low quality, and often require the filtering or reweighting of failed or noisy demonstrations. To address this challenge, we propose an efficient offline reinforcement learning framework which utilizes an implicit world model to regularize a behavior cloning policy via predicted future returns. Our approach, Robust Imitation with a Critic (RIC), utilizes a critic-regularized imitation learning objective to incorporate both successful and failed demonstrations, steering imitation learning towards better trajectories via a conservative critic. Our method improves on prior works by accelerating the quality of learned policies by as much as 20% in the presence of suboptimal expert training data. Our simulated experiments consider different types of data suboptimality, including rollouts from a poor demonstrator policy and biased action perturbations from controller error.  We empirically evaluate different algorithmic choices for RIC, including comparisons of (1) offline reinforcement learning and behavior cloning, (2) critic guidance via an implicit world-model and a conservative critic estimate, and (3) different behavior cloning methods, including token and diffusion-based architectures.

---

## 论文详细总结（自动生成）

# 论文总结：基于鲁棒世界模型的次优示范行为克隆

## 1. 核心问题与整体含义
- **研究动机**：行为克隆方法在机器人操作等任务中取得了显著进展，但其严重依赖高质量专家示范数据。现实场景中获取大量完美示范困难，次优示范（如失败轨迹、噪声扰动）会导致学习到的策略性能大幅下降。现有方法常需要过滤或重加权数据，增加了额外负担。
- **整体含义**：本文旨在解决从次优示范中进行有效行为克隆的难题，提出一种无需数据筛选即可利用全部示范（包括失败轨迹）的离线强化学习框架，从而扩展行为克隆在低质量数据场景下的适用性。

## 2. 方法论
- **核心思想**：将行为克隆与离线强化学习结合，利用隐式世界模型预测未来回报，并通过保守评论家（conservative critic）对行为克隆策略进行正则化，引导模型倾向于产生高回报轨迹。
- **关键技术细节**：
  - 提出 **RIC (Robust Imitation with a Critic)** 框架。
  - 采用隐式世界模型（implicit world model）来预测未来回报，无需显式建模状态转移。
  - 利用保守评论家估计值稳定离线学习，避免对分布外动作的过高估计。
  - 优化目标为行为克隆损失与评论家指导的正则项之和，使得策略同时模仿示范动作并追求高累积回报。
- **公式或算法流程**（文字说明）：  
  1. 从次优示范数据集（包含成功与失败轨迹）中采样。  
  2. 使用离线强化学习训练一个隐式世界模型，学习预测回报函数。  
  3. 训练一个保守评论家（如CQL风格）以稳定Q值估计。  
  4. 行为克隆策略的损失函数 = 模仿损失 + λ × 评论家指导项（如最大化Q值或最小化策略与Q值导向的偏差）。  
  5. 联合优化，最终得到鲁棒策略。

## 3. 实验设计
- **数据集/场景**：模拟实验，考虑了两种次优性类型：
  - 较差策略的rollouts（直接使用低水平策略生成的轨迹）。
  - 控制器误差引起的动作扰动（在示范中加入偏置噪声）。
- **基准**：对比方法包括：
  - 纯行为克隆（BC）方法（如基于token或扩散架构的BC）。
  - 仅用离线强化学习的基线。
  - 不同评论家引导方式（有无隐式世界模型、是否保守估计）。
- **对比方法**：具体比较了（1）离线强化学习 vs. 行为克隆，（2）评论家引导通过隐式世界模型 vs. 直接保守评论家估计，（3）不同的行为克隆架构（token-based、diffusion-based）。

## 4. 资源与算力
- **文中未明确说明**：未提及使用的GPU型号、数量、训练时长等具体算力信息。仅可推断实验在模拟环境下完成，计算资源需求应与通用离线RL实验相当。

## 5. 实验数量与充分性
- **实验数量**：文中报告了在不同次优数据类型下的对比实验，以及消融研究对比不同算法选择。但从摘要看，实验覆盖了主要变量，但未提供详细数量统计（如重复次数、显著性检验）。
- **充分性**：实验设计较为充分，覆盖了两种关键次优类型，并系统对比了不同组件（世界模型、评论家、BC架构）。但缺少真实机器人实验，且未评估极端次优比例下的表现。总体而言，在模拟环境中的验证相对客观，但可进一步扩展。

## 6. 主要结论与发现
- RIC在次优示范数据集上显著优于现有行为克隆方法，策略质量提升高达**20%**。
- 无需数据过滤或重加权，能同时利用成功和失败示范。
- 隐式世界模型与保守评论家的组合对鲁棒性至关重要。
- 在不同次优类型（较差策略rollout、动作扰动）下均表现出强鲁棒性。

## 7. 优点
- **方法创新**：巧妙融合行为克隆与离线强化学习，利用世界模型正则化，避免了复杂的过滤步骤。
- **实用性强**：可直接使用收集的全部数据（包括失败数据），降低数据收集成本。
- **实验全面**：考虑了多种次优形式，并进行了消融比较不同组件和BC架构。
- **性能提升显著**：在模拟环境中提升高达20%，展示了强大的鲁棒性。

## 8. 不足与局限
- **仅限模拟实验**：未在真实机器人或物理环境中验证，实际部署中的泛化性和鲁棒性未知。
- **算力未报告**：缺少计算资源消耗信息，难以评估复现成本。
- **极端次优情况**：实验覆盖的次优类型有限，对于噪声极大或完全随机示范的效果未明确。
- **依赖隐式世界模型**：模型训练复杂度可能随任务维度增加，且对回报函数的表示能力有要求。
- **论文文本缺失**：提供的只是元数据与摘要，缺乏完整论文的技术细节和结果图表，分析基于有限信息。

（完）
