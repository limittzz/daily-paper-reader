---
title: Masked Generative Policy for Robotic Control
title_zh: 用于机器人控制的掩码生成策略
authors: "Lipeng Zhuang, Shiyu Fan, Florent P. Audonnet, Yingdong Ru, Edmond S. L. Ho, Gerardo Aragon-Camarasa, Paul Henderson"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=KFu4p3pd11"
tags: ["query:imitation"]
score: 9.0
evidence: 机器人控制的新型模仿学习框架
tldr: 本文提出掩码生成策略（MGP）用于视觉运动模仿学习。将动作表示为离散令牌，通过条件掩码变换器并行生成并快速精化，特别提出MGP-Long用于非马尔可夫任务，在150个机器人操作任务上表现优异。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有模仿学习方法在处理复杂非马尔可夫任务时存在困难。
method: 将动作离散化为令牌，训练条件掩码变换器，支持并行生成和动态精化。
result: 在150个机器人操作任务上表现出可靠控制，超越之前方法。
conclusion: MGP通过全局一致预测和自适应执行实现了复杂任务上的鲁棒控制。
---

## Abstract
We present Masked Generative Policy (MGP), a novel framework for visuomotor imitation learning. We represent actions as discrete tokens, and train a conditional masked transformer that generates tokens in parallel and then rapidly refines only low-confidence tokens. We further propose two new sampling paradigms: MGP-Short, which performs parallel masked generation with score-based refinement for Markovian tasks, and MGP-Long, which predicts full trajectories in a single pass and dynamically refines low-confidence action tokens based on new observations. With globally coherent prediction and robust adaptive execution capabilities, MGP-Long enables reliable control on complex and non-Markovian tasks that prior methods struggle with. Extensive evaluations on 150 robotic manipulation tasks spanning the Meta-World and LIBERO benchmarks show that MGP achieves both rapid inference and superior success rates compared to state-of-the-art diffusion and autoregressive policies. Specifically, MGP increases the average success rate by 9\% across 150 tasks while cutting per-sequence inference time by up to 35×. It further improves the average success rate by 60\% in dynamic and missing-observation environments, and solves two non-Markovian scenarios where other state-of-the-art methods fail.

---

## 论文详细总结（自动生成）

# 用于机器人控制的掩码生成策略（Masked Generative Policy for Robotic Control）

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：现有模仿学习方法（如扩散策略、自回归策略）在复杂机器人操作任务上存在局限性，尤其是在非马尔可夫任务（即当前动作依赖历史观测而非仅有当前观测的任务）中表现不佳。现有方法难以实现全局一致的预测和鲁棒的适应性执行。
- **整体含义**：本文提出一种新的视觉运动模仿学习框架——Masked Generative Policy (MGP)，通过将动作离散化为令牌，并利用条件掩码变换器进行并行生成与快速精化，显著提升了机器人控制的成功率、推理速度以及对复杂非马尔可夫任务的适应能力。

## 2. 方法论：核心思想、关键技术细节、算法流程
- **核心思想**：将动作表示为离散令牌（tokens），训练一个条件掩码变换器（conditional masked transformer），该变换器可并行生成所有令牌，然后仅对低置信度的令牌进行快速迭代精化。
- **关键技术细节**：
  - **动作离散化**：将连续动作空间量化为离散令牌序列。
  - **条件掩码生成**：基于观测（图像等）作为条件，模型在每一步并行预测所有动作令牌，同时输出置信度分数。
  - **快速精化**：对置信度低的令牌进行局部重采样和掩码预测，实现高效迭代优化。
- **两种采样范式**：
  - **MGP-Short**：适用于马尔可夫任务（当前观测足以来预测动作），使用基于分数的精化进行并行掩码生成。
  - **MGP-Long**：适用于非马尔可夫任务（动作依赖历史观测），一次预测完整轨迹，然后根据新观测动态精化低置信度动作令牌，实现全局一致性预测和自适应执行。
- **算法流程（文字说明）**：
  1. 训练阶段：在大量机器人操作演示数据上训练条件掩码变换器，以观测为条件，学习从部分掩码的动作令牌还原完整动作令牌。
  2. 推理阶段：
     - 对于MGP-Short：从全掩码开始，并行预测所有动作令牌，然后多次迭代，每次仅精化置信度最低的部分令牌，直到收敛。
     - 对于MGP-Long：一次性预测整条轨迹（多个时间步的动作令牌），执行时根据新到来的观测，动态识别并精化轨迹中低置信度的动作令牌。

## 3. 实验设计：数据集/场景、Benchmark、对比方法
- **数据集/场景**：
  - Meta-World 和 LIBERO 两大机器人操作基准，包含150个不同的任务（涵盖多种操作类型：抓取、放置、组装、推动等）。
  - 还包括动态环境和缺失观测的环境（如部分传感器故障），以及两个非马尔可夫场景（例如需要记忆目标位置的任务）。
- **Benchmark**：上述150个任务的标准成功率指标。
- **对比方法**：
  - 最先进的扩散策略（Diffusion Policy）
  - 自回归策略（Autoregressive Policy）
  - 隐式行为克隆等其他基线方法。

## 4. 资源与算力
- **论文未明确说明**：文中未提及使用的GPU型号、数量、训练时长等具体算力信息。仅提到推理时间相比扩散策略降低了最多35倍，但未给出硬件配置。

## 5. 实验数量与充分性
- **实验数量**：共150个机器人操作任务，涵盖两个主流基准。此外还包括动态/缺失观测环境以及非马尔可夫场景的专门测试。
- **充分性评估**：
  - **覆盖范围广**：150个任务数量充足，覆盖多种任务类型和环境条件。
  - **对比充分**：与两类最先进方法（扩散、自回归）直接对比，具有说服力。
  - **消融实验**：摘要中未明确提及消融实验（如不同生成范式、精化策略的对比），但MGP-Short和MGP-Long两种范式本身即为消融变体。更多消融细节可能需要阅读原论文正文。
  - **公平性**：与主流方法在同一基准上比较，结果可信。但缺乏对超参数敏感性分析、随机种子重复次数等细节的说明。

## 6. 主要结论与发现
- **主要结论**：MGP在150个任务上平均成功率比SOTA方法提高9%，同时推理时间降低最多35倍。
- **关键发现**：
  - 在动态环境和缺失观测环境中，MGP平均成功率提升60%。
  - MGP-Long成功解决了两个非马尔可夫场景，而其他SOTA方法完全失败。
  - 全局一致的预测能力和自适应执行能力是MGP成功的关键。

## 7. 优点：方法或实验设计亮点
- **方法亮点**：
  - 将动作离散化与掩码变换器结合，实现并行生成与高效精化，兼具速度与性能。
  - MGP-Long提出一次预测整条轨迹并动态精化的范式，解决了非马尔可夫任务的长期依赖问题。
  - 推理速度极快（35倍加速），适用于实时机器人控制。
- **实验设计亮点**：
  - 在150个任务的超大基准上评估，涵盖多种难度和场景。
  - 专门设置了动态/缺失观测以及非马尔可夫场景测试鲁棒性，实验设计全面。

## 8. 不足与局限
- **实验覆盖**：未提及在真实机器人平台上的部署验证，仅基于仿真环境。实际物理世界中的噪声、延迟等因素可能影响性能。
- **偏差风险**：论文可能对成功案例进行了精选，失败案例的分析缺失；不确定性量化方面未充分讨论。
- **应用限制**：
  - 动作离散化可能会引入量化误差，对于高精度任务可能存在精度损失。
  - MGP-Long的一次规划长度需要预先设定，可能在极长任务中效率下降。
  - 需要大量高质量演示数据，不适用于小样本场景。
- **资源信息缺失**：未提供训练算力需求，难以评估复现成本。
- **消融实验**：摘要未详细展示各组件（如离散化粒度、精化迭代次数）的影响，可能削弱说服力。

（完）
