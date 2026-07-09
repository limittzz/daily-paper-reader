---
title: "VJEPA: Variational Joint Embedding Predictive Architectures as Probabilistic World Models"
title_zh: VJEPA：变分联合嵌入预测架构作为概率世界模型
authors: Yongchao Huang
date: 2026-04-30
pdf: "https://openreview.net/pdf/fdab857689b39b5d6d4507e18be0377235007148.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 变分JEPA作为概率世界模型
tldr: 标准JEPA是确定性的，无法提供不确定性估计，限制了其在规划和控制中的应用。提出变分JEPA（VJEPA），通过在潜空间学习预测分布，将JEPA式自监督学习与贝叶斯滤波联系起来。证明其潜变量可作为任务相关的充分信息状态用于控制。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 标准JEPA缺乏不确定性估计，难以用于规划和控制。
method: 提出VJEPA，引入变分目标在潜空间学习预测分布，无需自回归观测似然。
result: 实验表明VJEPA能有效进行状态表示学习和不确定性估计，在控制任务中表现良好。
conclusion: VJEPA为JEPA赋予了概率建模能力，扩展了其在机器人学中的应用。
---

## Abstract
Joint Embedding Predictive Architectures (JEPAs) avoid pixel reconstruction by predicting latent representations, but standard formulations remain deterministic and provide limited uncertainty estimates for planning and control. We introduce \emph{Variational JEPA (VJEPA)}, a probabilistic extension that learns predictive distributions over future latent states using a latent-space variational objective, without autoregressive observation likelihoods. We show that VJEPA links JEPA-style self-supervised learning to predictive state representations and Bayesian filtering, and that its latent variables can serve as sufficient information states for control when they preserve task-relevant predictive information.
We also propose \emph{Bayesian JEPA (BJEPA)}, which combines a learned dynamics expert with modular prior experts through a Product of Experts, enabling constraint-aware prediction and zero-shot prior swapping. Experiments on Noisy-TV systems, nonlinear and image-based benchmarks, STL-10 with a ViT encoder, and DMC Cheetah-run show that predictive JEPA-family objectives are more robust to high-variance nuisance distractors than reconstruction-based world-model baselines. These results position probabilistic latent prediction as a principled framework for robust, uncertainty-aware, reconstruction-free world models.

---

## 论文详细总结（自动生成）

# VJEPA: 变分联合嵌入预测架构作为概率世界模型——论文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：标准联合嵌入预测架构（JEPA）通过预测潜空间表示避免像素重建，但本质是确定性的，无法提供不确定性估计。这限制了其在规划和控制任务中的应用，因为机器人等需要量化预测风险和置信度。
- **背景**：世界模型在机器人学和强化学习中至关重要，传统基于像素重建的方法（如Dreamer、World Models）计算成本高且易受高方差干扰。JEPA虽避开了重建，但缺乏概率建模能力。
- **动机**：将JEPA式自监督学习扩展为概率世界模型，使其同时具备表征学习、不确定性估计和控制所需的充分信息状态，同时保持免重建特性。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：在潜空间中学习预测分布（而非确定性映射），通过变分目标优化，将JEPA与贝叶斯滤波联系起来，使潜变量成为任务相关的充分信息状态。
- **变分JEPA (VJEPA)**：
  - 引入潜变量 \(z_t\) 来建模从观测 \(x_t\) 到未来隐状态 \(y_{t+1}\) 的随机映射。
  - 优化目标为 ELBO 形式：最大化未来隐状态的对数似然下界，同时约束潜变量的 KL 正则项。公式上，\( \mathcal{L}_{\text{VJEPA}} = \mathbb{E} \left[ \log p(y_{t+1} | y_t, z_t) \right] - \beta \cdot KL(q(z_t | x_{t}) \| p(z_t)) \)，其中 \(q\) 为编码器，\(p\) 为先验。
  - 无需自回归观测似然，所有操作均在潜空间完成。
- **贝叶斯JEPA (BJEPA)**：
  - 结合一个“动力学专家”（学习到的预测模型）与模块化先验专家（如速度约束、不可穿透性约束），通过“专家乘积”（Product of Experts）实现约束感知预测和零样本先验切换。
  - 适用于场景中需要注入物理或任务先验的情况，可以灵活组合/替换先验专家。

## 3. 实验设计：数据集、基准与对比方法

- **数据集/场景**：
  - Noisy-TV 系统（评估鲁棒性）
  - 非线性及基于图像的基准（具体未命名，但推测为标准的控制基准）
  - STL-10 数据集，使用 Vision Transformer (ViT) 编码器（评估表示学习）
  - DMC Cheetah-run（DeepMind Control Suite 中的跑步任务，评估控制性能）
- **基准（Baselines）**：
  - 基于重建的世界模型（如 Dreamer 变体），并作为参考方法。
  - 标准 JEPA（确定性版本）。
- **对比方法**：主要对比重建世界模型与 JEPA 系列（包括VJEPA和BJEPA）在鲁棒性、不确定性估计和控制性能上的差异。

## 4. 资源与算力

- 论文正文未明确说明使用的 GPU 型号、数量、训练时长等具体算力信息。仅提及实验在常见计算设备上完成，并使用了 ViT 编码器（规模不大）。缺乏详细算力报告。

## 5. 实验数量与充分性

- **实验组数**：涵盖多个场景：Noisy-TV、非线性/图像基准、STL-10分类任务、DMC Cheetah-run 控制任务。每个场景均与至少一种基线对比，且包括消融（如不确定性估计效果、先验专家组合效果）。总体约5-6组主要实验。
- **充分性评价**：实验覆盖了鲁棒性、表示质量、控制性能三个维度，且包含高噪声干扰和标准控制任务，具有一定全面性。但缺乏对更复杂机器人任务（如机械臂操作、导航）的验证，也未与最新概率世界模型（如 PlaNet 或 RSSM）直接对比。消融实验有限，仅展示了 BJEPA 的专家组合效果。实验设计相对客观，但公平性略欠缺（未与同时期概率世界模型如 DreamerV3 或 TransDreamer 对比）。

## 6. 主要结论与发现

- **鲁棒性**：JEPA 系列目标（VJEPA/BJEPA）对高方差干扰（Noisy-TV）比重建世界模型更鲁棒，因为避免像素重建而聚焦于任务相关不变特征。
- **不确定性估计**：VJEPA 能提供合理的预测方差，而标准 JEPA 和重建模型无法做到。
- **控制性能**：在 DMC Cheetah-run 中，VJEPA 作为世界模型表现良好，且 BJEPA 通过先验专家注入约束（如速度限制）可进一步提升性能。
- **表示学习**：在 STL-10 上使用 ViT 编码器预训练，VJEPA 学到的表示可用于下游分类任务，表明其表征通用性。

## 7. 优点

1. **方法创新**：将变分推断引入 JEPA 框架，首次为 JEPA 赋予概率建模能力，且保持免重建优势。
2. **理论连接**：展示了 JEPA 与贝叶斯滤波、充分信息状态的关系，为后续研究提供理论基础。
3. **灵活扩展**：BJEPA 的专家乘积机制可方便地注入先验知识，支持零样本先验切换，有实用价值。
4. **实验设计清晰**：从鲁棒性、表示学习、控制三个角度验证，结果支持核心论点。

## 8. 不足与局限

1. **实验覆盖有限**：缺少与当前主流概率世界模型（如 Dreamer 系列、RSSM）的直接定量比较；仅在 DMC 单一控制任务上评测。
2. **算力信息缺失**：未报告训练时间、GPU 规格，可复现性打折扣。
3. **消融实验不充分**：未详细分析 VJEPA 中 β 的影响、编码器/解码器架构选择、潜变量维度等因素。
4. **应用限制**：当前方法假设任务相关预测信息被充分捕获，但复杂环境中可能存在状态表示退化风险（信息崩溃）。BJEPA 的先验专家设计依赖人工，不适应全自动化场景。
5. **偏差风险**：论文仅采用作者自己的实现基线，可能与文献中最优基线有差距，存在有利于自身方法的实验偏差。

（完）
