---
title: "OGPO: Sample Efficient Full-Finetuning of Generative Control Policies"
title_zh: OGPO：生成控制策略的高样本效率全微调
authors: "Sarvesh Patil, Mitsuhiko Nakamoto, Manan Agarwal, Shashwat Saxena, Jesse Zhang, Giri Anantharaman, Cleah Winston, Chaoyi Pan, Douglas Chen, Nai-Chieh Huang, Zeynep Temel, Oliver Kroemer, Sergey Levine, Abhishek Gupta, Hongkai Dai, Paarth Shah, Max Simchowitz"
date: 2026-04-30
pdf: "https://openreview.net/pdf/b08b261185385a18fb3acec42b4ba2a818aabeaf.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 通过离策略生成策略优化微调行为克隆策略
tldr: 针对生成控制策略（如扩散策略）微调样本效率低的问题，提出离策略生成策略优化（OGPO）算法，利用离策略评论家最大化数据复用，通过修改的PPO目标在全生成过程中传播策略梯度。实验表明OGPO能微调初始化不良的行为克隆策略，在多种操作任务上达到最优，是唯一能实现近乎完全任务成功率的方法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 生成控制策略在机器人学习中有效，但微调过程样本效率低下且难以从次优初始化恢复。
method: 引入离策略评论家，结合修改的PPO目标，在整个生成过程中传播梯度，实现高效的离策略微调。
result: 在多任务、高精度插入和灵巧操作等任务上取得最先进性能，并能有效微调不良初始化的行为克隆策略。
conclusion: OGPO是首个能高效微调生成控制策略并显著提升行为克隆性能的算法。
---

## Abstract
Generative control policies (GCPs), such as diffusion- and flow-based control policies, have proved effective parameterizations for robot learning. This work introduces Off-policy Generative Policy Optimization (**OGPO**), a sample-efficient algorithm for finetuning GCPs that maintains off-policy critics to maximize data reuse and propagate policy gradients through the full generative process of the policy via a modified PPO objective, using critics as the terminal reward. **OGPO** achieves state-of-the-art performance on manipulation tasks spanning multi-task settings, high-precision insertion, and dexterous control. To our knowledge, it is also the only method that can *fine-tune poorly-initialized behavior cloning policies to near full task-success with no expert data in the online replay buffer*, and does so with *few task-specific hyperparameter tuning*. Through extensive investigations, we demonstrate that **OGPO** drastically outperforms alternative methods on policy steering and learning residual corrections, and identify the key mechanisms behind its performance. We further introduce practical stabilization tricks, including success-buffer regularization and two-sided conservative advantages to mitigate critic over-exploitation across state- and pixel-based settings. Beyond proposing **OGPO**, we conduct a systematic empirical study of GCP finetuning, identifying the stabilizing mechanisms and failure modes that govern successful off-policy full-policy improvement.

---

## 论文详细总结（自动生成）

# OGPO：生成控制策略的高样本效率全微调（ICML 2026）

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **问题**：生成控制策略（GCP），如基于扩散或流的控制策略，在机器人学习中被证明有效。然而，其微调过程样本效率低下，且难以从次优初始化（如行为克隆策略）中恢复。
- **动机**：现有微调方法（如PPO、在线强化学习）要么需要大量在线交互，要么无法利用离线数据，导致策略在开局不良时很难提升到高成功率。需要一种能高效复用历史数据、同时在整个生成过程中传播梯度的方法。
- **整体含义**：提出离策略生成策略优化（OGPO），首次实现从次优行为克隆策略微调至近乎完全任务成功，且无需专家数据在重放缓冲区中，大幅提升了GCP微调的样本效率。

## 2. 论文提出的方法论
- **核心思想**：保持离策略评论家（critic）最大化数据复用，并利用修改的PPO目标，通过评论家作为终端奖励，将策略梯度传播到整个生成过程（即策略的完整生成链）。从而实现高效的离策略全策略改进。
- **关键技术细节**：
  - **离策略评论家**：使用重放缓冲区中的历史交互数据训练Q函数，减少在线样本需求。
  - **修改的PPO目标**：标准PPO仅在最终动作上提供奖励，OGPO将评论家的Q值作为每个生成步骤的“奖励信号”，并通过重要性采样和裁剪，确保策略更新稳定。
  - **梯度传播**：策略的生成过程（如扩散模型的去噪步骤）被视为一个动态系统，通过重参数化技巧或直通估计器，将评论家的梯度反向传播到所有生成步骤的参数。
  - **稳定技巧**：
    - **成功缓冲区正则化**：在重放缓冲区中保留成功轨迹，缓解批评家过估计。
    - **两侧保守优势**：同时惩罚高估和低估的优势，避免批评家被过度利用。
  - **适用场景**：支持基于状态和基于像素的输入。
- **算法流程（文字说明）**：
  1. 从重放缓冲区采样批量数据（包括离线行为克隆数据和在线交互数据）。
  2. 使用当前策略生成动作序列（如扩散去噪步骤）。
  3. 利用离策略评论家计算每个生成步骤的Q值近似作为即时奖励。
  4. 计算修改后的PPO目标（含优势函数、裁剪、两侧保守项），更新策略参数。
  5. 定期更新评论家（使用TD误差或蒙特卡洛回归）。
  6. 在线交互：执行策略，收集新数据加入缓冲区，利用成功缓冲区正则化保持平衡。

## 3. 实验设计
- **数据集/场景**：多任务操作、高精度插入、灵巧操作等机器人操控任务。包括状态空间和像素空间的设定。
- **Benchmark**：未明确列出特定benchmark名称，但涵盖标准操作任务（如组装、抓取、插入等）。
- **对比方法**：包括行为克隆（BC）、在线PPO、离策略强化学习（如SAC、CQL）、以及残差学习、策略引导等方法。OGPO与多种基线进行对比，并特别验证了对次优初始化BC策略的微调能力。

## 4. 资源与算力
- **论文中未明确说明**：未提及GPU型号、数量、训练时长等具体算力信息。仅强调OGPO样本效率高，但计算资源细节缺失。

## 5. 实验数量与充分性
- **实验组数**：覆盖多个任务类型，包括多任务、高精度插入、灵巧操作。至少进行了以下类别的实验：
  - 主结果对比（与SOTA方法对比）。
  - 消融实验（成功缓冲区正则化、两侧保守优势、梯度传播设计等）。
  - 初始化鲁棒性实验（从不同质量的行为克隆策略出发）。
  - 扩展实验（像素输入场景）。
- **充分性评估**：实验设计较为全面，涵盖了状态和像素两种观测，比较了多种基线，并进行了消融。但缺少对更多任务（如导航、复杂多步任务）的验证，以及跨机器人平台的泛化性测试。公平性方面，对比方法超参数调优未详述，可能影响公平性。

## 6. 论文的主要结论与发现
- OGPO在多种操控任务上取得最先进性能，是唯一能从次优BC策略微调至近乎完全任务成功的方法。
- OGPO在策略引导和残差学习方面显著优于其他微调方法。
- 关键机制：离策略评论家结合全生成过程梯度传播，以及两侧保守优势是性能提升的核心。
- 样本效率极高，无需专家数据在在线重放缓冲区中，即可从低起点恢复。

## 7. 优点
- **样本效率高**：离策略评论家最大化数据复用，减少在线交互需求。
- **初始化鲁棒**：可从较差的BC策略出发，快速提升至接近100%成功率。
- **最小化超参数依赖**：声称具有很少的任务特定超参数调优。
- **适用性广**：同时支持状态和像素输入，涵盖多种任务类型。
- **实践技巧实用**：成功缓冲区正则化、两侧保守优势等稳定技术有效防止批评家过估计。

## 8. 不足与局限
- **实验覆盖有限**：仅分析了操作任务，缺乏对其他领域（如运动控制、人机交互）的验证。
- **资源信息缺失**：未报告具体的算力需求，难以评估实际部署成本。
- **对比方法选择**：未与最新的生成控制策略微调方法（如Diffusion-QL、IQL等）全面比较，可能遗漏更强基线。
- **理论分析不足**：缺少对收敛性、样本复杂度等理论保证。
- **提示工程缺失**：论文未讨论数据收集的难易程度或是否需要人工工程，可能在实际应用中存在障碍。
- **批评家过利用风险**：尽管引入了两侧保守优势，但高维场景下批评家误差仍可能累积，长期稳定性待验证。

（完）
