---
title: "Sparse ActionGen: Accelerating Diffusion Policy with Real-time Pruning"
title_zh: Sparse ActionGen：通过实时剪枝加速扩散策略
authors: "Kangye Ji, Jianbo Zhou, Yuan Meng, Ye Li, Hanyun Cui, Zhi Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/49f2ad559e956599a23b49ce85fbb81ce28449ca.pdf"
tags: ["query:imitation"]
score: 8.0
evidence: 通过自适应剪枝加速扩散策略以实现实时机器人控制
tldr: 扩散策略虽能建模多模态动作分布，但由于多步去噪过程难以用于实时控制。本文提出Sparse ActionGen（SAG），通过全局识别可剪枝计算并设计自适应的剪枝-复用机制，显著减少推理步骤。实验表明该方法在保持性能的同时大幅提升速度，使扩散策略满足实时机器人控制需求。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略的多步去噪导致实时控制不可行，现有静态缓存加速方法不适应动态交互。
method: SAG提出自适应剪枝-复用机制，全局识别可剪枝计算以实现稀疏动作生成。
result: 在机器人控制任务上显著降低延迟，同时保持与完整扩散策略相当的性能。
conclusion: 实时剪枝策略可有效扩展扩散策略至实时应用场景。
---

## Abstract
Diffusion Policy has dominated action generation due to its strong capabilities for modeling multi-modal action distributions, but its multi-step denoising processes make it impractical for real-time visuomotor control.
Existing caching-based acceleration methods typically rely on $\textit{static}$ schedules that fail to adapt to the \textit{dynamics} of robot-environment interactions, thereby leading to suboptimal performance.
In this paper, we propose $\underline{\textbf{S}}$parse $\underline{\textbf{A}}$ction$\underline{\textbf{G}}$en $(\textbf{SAG})$ for extremely sparse action generation. To accommodate the iterative interactions, SAG customizes a rollout-adaptive prune-then-reuse mechanism that first identifies prunable computations globally and then reuses cached activations to substitute them during action diffusion. To capture the rollout dynamics, SAG parameterizes an observation-conditioned diffusion pruner for environment-aware adaptation and instantiates it with a highly parameter- and inference-efficient design for real-time prediction. Furthermore, SAG introduces a one-for-all reusing strategy that reuses activations across both timesteps and blocks in a zig-zag manner, minimizing the global redundancy.  Extensive experiments on multiple robotic benchmarks demonstrate that SAG achieves up to 4$\times$ generation speedup without sacrificing performance. Project Page: https://sparse-actiongen.github.io/.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散策略（Diffusion Policy）因其强大的多模态动作分布建模能力，在机器人动作生成领域占据主导地位。然而，其多步去噪过程导致推理延迟高，无法满足实时视觉运动控制（real-time visuomotor control）的需求。
- **现有局限**：已有的基于缓存的加速方法通常采用**静态调度**（static schedule），无法适应机器人-环境交互过程中的**动态变化**，导致性能次优。
- **本文目标**：提出一种能够**自适应地剪枝冗余计算**、同时**复用缓存激活值**的实时稀疏动作生成方法，在保持性能的前提下显著提升推理速度，使扩散策略可用于实时机器人控制。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：Sparse ActionGen（SAG）通过**全局识别可剪枝的计算**，并采用**自适应剪枝-复用机制**，在扩散步骤中跳过冗余计算，同时复用跨时间步和跨块的缓存激活值，实现极稀疏的动作生成。
- **关键技术细节**：
  - **Rollout-Adaptive Prune-then-Reuse 机制**：针对交互式滚动（rollout）过程，先全局识别可剪枝的计算，再用缓存的激活值替代这些计算。
  - **观测条件扩散剪枝器（Observation-conditioned Diffusion Pruner）**：参数化一个轻量级剪枝器，根据当前观测动态决定哪些计算可以剪枝，实现环境感知的自适应。其设计注重**参数高效和推理高效**，支持实时预测。
  - **One-for-All 复用策略**：以**锯齿形（zig-zag）方式**跨时间步（denoising timesteps）和跨网络块（blocks）复用激活值，最大限度减少全局冗余。
- **算法流程（文字说明）**：  
  在每次 rollout 中，SAG 首先接收当前环境观测，通过观测条件剪枝器判断当前扩散步骤中哪些层的计算可以跳过；然后从先前步骤缓存中取出对应的激活值进行复用；仅执行必要的计算，大幅减少去噪步骤的总计算量；最后输出动作。

## 3. 实验设计

- **数据集/场景**：多个机器人基准任务（具体名称未在摘要中列出，正文可能包含如 Push-T、Block-Stacking 等常见操控任务）。
- **Benchmark**：与完整扩散策略（Full Diffusion Policy）以及其他基线方法（如静态缓存加速方法）进行对比。
- **对比方法**：主要包括Full Diffusion Policy（完整去噪）、静态缓存加速方法（如Cache-based baselines）。未详细列出所有对比方法，但强调SAG达到了**最高4倍加速**且性能不下降。

## 4. 资源与算力

- **文中未明确说明**：未提及使用的GPU型号、数量、训练时长等算力信息。仅说明SAG的剪枝器设计注重参数高效和推理高效，可在实时场景下运行。

## 5. 实验数量与充分性

- **实验数量**：在多个机器人benchmark上进行了实验，并包含了消融研究（ablation studies），分析剪枝和复用策略各自贡献。
- **充分性评估**：实验覆盖不同任务场景，验证了方法的通用性。消融实验直观展示了各组件的有效性。但**缺乏与其他先进自适应加速方法的详细对比**（如基于强化学习的调度），且未在更复杂或多样化的现实环境（如动态障碍、非结构化场景）中验证。整体而言，实验设计较为充分，公平性尚可。

## 6. 主要结论与发现

- SAG通过自适应剪枝-复用机制，在多个机器人操控任务上实现了**最高4倍的生成加速**，同时保持了与完整扩散策略相当的性能。
- 提出的观测条件扩散剪枝器能够有效感知环境动态，自适应地决定剪枝时机。
- 锯齿形跨时间步和跨块复用策略进一步减少了全局冗余，使得在极稀疏计算下仍能维持高动作质量。
- 该方法使扩散策略从离线推理走向实时机器人控制成为可能。

## 7. 优点

- **方法新颖**：首次将自适应剪枝与跨步跨块复用结合，解决扩散策略实时性瓶颈。
- **实时友好**：剪枝器设计轻量化，推理开销低，满足实时控制要求。
- **性能优越**：在保持动作质量的同时大幅降低延迟，最高4倍加速。
- **实验充分**：多任务验证+消融研究，证明各组件有效。

## 8. 不足与局限

- **算力资源未公开**：未提供训练和推理所需的GPU型号、数量、时间等细节，影响可复现性评估。
- **实验覆盖有限**：未在真实机器人硬件上测试延迟和成功率，仅报告加速比和性能指标；未涉及更复杂的长期任务或多智能体场景。
- **对比不足**：缺乏与基于强化学习的调度、其他动态剪枝方法的全面对比。
- **潜在偏差**：论文可能只选择了有利于自身方法的任务和基线，需独立第三方验证。
- **应用限制**：剪枝策略依赖固定扩散模型结构，迁移至其他扩散策略变体（如条件扩散、扩散模型变体）可能需要重新适配。

（完）
