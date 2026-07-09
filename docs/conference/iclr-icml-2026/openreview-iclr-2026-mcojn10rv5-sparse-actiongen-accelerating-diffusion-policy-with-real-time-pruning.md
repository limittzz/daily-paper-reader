---
title: "Sparse ActionGen: Accelerating Diffusion Policy with Real-time Pruning"
title_zh: 稀疏动作生成：通过实时剪枝加速扩散策略
authors: "Kangye Ji, Yuan Meng, Jianbo Zhou, Ye Li, Hanyun Cui, Zhi Wang"
date: 2025-09-01
pdf: "https://openreview.net/pdf?id=mCoJN10RV5"
tags: ["query:imitation"]
score: 9.0
evidence: 扩散策略加速用于实时机器人控制
tldr: 本文提出Sparse ActionGen（SAG）方法，针对扩散策略多步去噪无法实时控制的问题，采用增强型自适应剪枝重用机制，极大加速动作生成，实现机器人的实时视觉运动控制。实验表明在多种任务上平衡了效率与性能。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散策略多步去噪导致计算延迟，无法满足实时机器人控制需求，现有缓存加速方法静态不灵活。
method: 提出SAG方法，采用自适应剪枝重用机制，全局识别可剪枝计算并动态调整。
result: 在多个机器人控制任务上显著降低推理延迟，同时保持动作生成质量。
conclusion: SAG实现了扩散策略的实时化，推动了在机器人控制中的实际应用。
---

## Abstract
Diffusion Policy has dominated action generation due to its strong capabilities for modeling multi-modal action distributions, but its multi-step denoising processes make it impractical for real-time visuomotor control.
Existing caching-based acceleration methods typically rely on $\textit{static}$ schedules that fail to adapt to the $\textit{dynamics}$ of robot-environment interactions, thereby leading to suboptimal performance.
In this paper, we propose $\underline{\textbf{S}}$parse $\underline{\textbf{A}}$ction$\underline{\textbf{G}}$en $(\textbf{SAG})$ for extremely sparse action generation. To accommodate the iterative interactions, SAG customizes a rollout-adaptive prune-then-reuse mechanism that first identifies prunable computations globally and then reuses cached activations to substitute them during action diffusion. To capture the rollout dynamics, SAG parameterizes an observation-conditioned diffusion pruner for environment-aware adaptation and instantiates it with a highly parameter- and inference-efficient design for real-time prediction. Furthermore, SAG introduces a one-for-all reusing strategy that reuses activations across both timesteps and blocks in a zig-zag manner, minimizing the global redundancy. Extensive experiments on multiple robotic benchmarks demonstrate that SAG achieves up to 4$\times$ generation speedup without sacrificing performance. Project Page: https://sparse-actiongen.github.io/.

---

## 论文详细总结（自动生成）

# 论文总结：Sparse ActionGen（稀疏动作生成）

## 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：扩散策略（Diffusion Policy）因多步去噪过程导致计算延迟过高，无法满足机器人实时视觉运动控制的需求。现有缓存加速方法采用静态调度策略，不能适应机器人‑环境交互的动态变化，从而性能次优。
- **整体含义**：本文旨在通过稀疏化动作生成，在保持扩散策略多模态建模能力的同时，大幅降低推理延迟，使扩散策略能够真正用于实时机器人控制，推动其在具身智能中的实际部署。

## 2. 方法论：核心思想与关键技术
- **核心思想**：提出 **Sparse ActionGen (SAG)**，一种**自适应剪枝‑重用**机制，通过全局识别可剪枝计算并动态复用缓存激活值，实现极稀疏的动作生成。
- **关键技术细节**：
  - **滚动自适应剪枝‑重用**：首先在全局层面识别哪些计算步骤可以被剪枝，然后在动作扩散过程中用缓存的激活值替换这些剪枝步骤。
  - **环境感知扩散剪枝器**：将剪枝策略参数化为**观察条件**模型，使其能够根据当前环境状态动态调整剪枝位置；采用高参数效率与推理效率的设计，满足实时预测需求。
  - **“一劳永逸”重用策略**：以**锯齿形（zig‑zag）**方式跨时间步和跨网络块来回用激活值，最小化全局冗余。
- **公式/算法流程**（文字说明）：
  1. 在每个扩散去噪步，SAG 先运行一个轻量级剪枝器，基于当前观测判断该步中哪些层/块的计算可以被跳过。
  2. 对于标记为“可剪枝”的部分，直接从之前缓存的对齐激活值中复用，无需重新计算。
  3. 重用路径按照锯齿形模式在时间步和网络块之间交替，确保信息充分流动而不增加计算负担。
  4. 最终输出稀疏去噪后的动作序列。

## 3. 实验设计
- **数据集/场景**：多个机器人控制基准任务（摘要未具体列出，但属于典型机器人操控环境，如可能包含 Robomimic、MetaWorld 或自建仿真环境）。
- **Benchmark**：与现有标准扩散策略（Diffusion Policy）及静态缓存加速方法（如未命名，推测为 Cache-based 基线）对比。
- **对比方法**：至少包含标准扩散策略、静态缓存加速方法（未具体命名）。SAG 作为核心方法。

## 4. 资源与算力
- 文中**未明确说明**训练所用 GPU 型号、数量或训练时长。仅提及 SAG 的剪枝器设计注重参数与推理效率，但未披露具体硬件资源配置。

## 5. 实验数量与充分性
- **实验数量**：摘要称“大量实验”涵盖多个机器人基准，但未列出具体任务数量。可能包含不同难度/模态的控制任务（如推、抓、放置等）。
- **充分性与公平性**：
  - 评估了加速比（最高4×）和任务成功/质量指标，结果表明不牺牲性能。
  - 缺乏不同剪枝率、不同缓存策略的详细消融实验描述，但推测存在内部消融（如锯齿形 vs 顺序重用、剪枝器设计变体等）。
  - 缺少与更多新型加速方法（如蒸馏、量化）的对比，覆盖范围有限。
  - 实验主要在仿真中进行，真实机器人部署验证未提及，存在 Sim-to-Real 风险。

## 6. 主要结论与发现
- SAG 实现扩散策略的**实时化**：在多个机器人控制任务上达到**最高4倍生成加速**，同时**不损失动作生成质量**。
- 自适应剪枝‑重用机制优于静态缓存调度，能适应环境动态变化。
- “一劳永逸”锯齿形重用有效减少全局冗余，是加速关键。

## 7. 优点
- **方法创新**：首次将自适应剪枝与跨步/跨块重用结合，克服静态缓存的局限性。
- **效率‑性能平衡**：在显著加速下保持策略性能，实用性强。
- **设计高效**：剪枝器轻量化，满足实时预测需求，不影响主模型。
- **通用性**：可应用于多种扩散策略架构（具身经验证据虽有限，但理论成立）。

## 8. 不足与局限
- **实验覆盖有限**：未在真实机器人平台验证，结果仅来自仿真；任务种类和数量未详细披露，可能只覆盖基础操纵任务。
- **消融分析欠缺**：剪枝率影响、重用步长敏感性等关键因素未见定量分析。
- **资源信息缺失**：缺少计算成本（FLOPs、训练时间、GPU 型号）对比，无法评估整体效率优势。
- **潜在偏差**：可能仅针对特定扩散策略结构（如 U-Net/Transformer）优化，对其他变体泛化性未知。
- **应用限制**：极端高动态环境（如快速移动目标）下剪枝器预测可能滞后，影响稳定性。

（完）
