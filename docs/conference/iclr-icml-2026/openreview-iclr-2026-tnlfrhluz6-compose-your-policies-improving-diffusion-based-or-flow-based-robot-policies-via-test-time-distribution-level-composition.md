---
title: "Compose Your Policies! Improving Diffusion-based or Flow-based Robot Policies via Test-time Distribution-level Composition"
title_zh: 组合你的策略！通过测试时分布级组合改进基于扩散或流的机器人策略
authors: "Jiahang Cao, Yize Huang, Hanzhong Guo, Qiang Zhang, Rui Zhang, Weijian Mai, Mu Nan, Jiaxu Wang, Hao Cheng, Jingkai SUN, Gang Han, Wen Zhao, Yijie Guo, Qihao Zheng, Xiao Li, Chunfeng Song, Ping Luo, Andrew Luo"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=TnLFRhLuZ6"
tags: ["query:imitation"]
score: 9.0
evidence: 扩散策略组合用于机器人控制
tldr: 针对数据获取成本高限制扩散策略性能的问题，本文提出一种测试时策略组合范式，通过凸组合多个扩散模型的分值分布，在不训练新模型的情况下提升策略性能。实验结果表明组合策略能超越父策略，为机器人策略优化提供了高效新途径。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 基于扩散的机器人策略性能受限于大规模交互数据的获取成本。
method: 提出无需额外模型训练的测试时策略组合方法，通过凸组合多个扩散模型的分值分布实现。
result: 实验表明组合策略可超越任一父策略的性能，实现性能提升。
conclusion: 提供了一种无需额外训练即可提升机器人策略性能的新范式。
---

## Abstract
Diffusion-based models for robotic control, including vision-language-action (VLA) and vision-action (VA) policies, have demonstrated significant capabilities. Yet their advancement is constrained by the high cost of acquiring large-scale interaction datasets. This work introduces an alternative paradigm for enhancing policy performance ***without additional model training***. Perhaps surprisingly, we demonstrate that the composed policies can exceed the performance of either parent policy. Our contribution is threefold. First, we establish a theoretical foundation showing that the convex composition of distributional scores from multiple diffusion models can yield a superior one-step functional objective compared to any individual score. A Grönwall-type bound is then used to show that this single-step improvement propagates through entire generation trajectories, leading to systemic performance gains. Second, motivated by these results, we propose General Policy Composition (GPC), a training-free method that enhances performance by combining the distributional scores of multiple pre-trained policies via a convex combination and test-time search. GPC is versatile, allowing for the plug-and-play composition of heterogeneous policies, including VA and VLA models, as well as those based on diffusion or flow-matching, irrespective of their input visual modalities. Third, we provide extensive empirical validation. Experiments on Robomimic, PushT, and RoboTwin benchmarks, alongside real-world robotic evaluations, confirm that GPC consistently improves performance and adaptability across a diverse set of tasks. Further analysis of alternative composition operators and weighting strategies offers insights into the mechanisms underlying the success of GPC. These results establish GPC as a simple yet effective method for improving control performance by leveraging existing policies.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：基于扩散模型（Diffusion-based models）的机器人控制策略（包括视觉-语言-动作 VLA 和视觉-动作 VA 策略）展现出了强大的能力，但其性能提升受限于大规模交互数据集的获取成本。在实际机器人应用中，收集大量高质量演示数据代价高昂，这阻碍了扩散策略的进一步优化。
- **核心问题**：如何在**不进行额外模型训练**的前提下，利用已有的多个预训练策略来突破单一策略的性能瓶颈，实现显着的性能提升。
- **整体含义**：论文提出了一种**测试时策略组合**的新范式，通过凸组合多个扩散模型的分值分布（distributional scores），可以在无需任何训练的情况下获得比任意单个父策略更优的控制性能。这一发现为解决数据稀缺问题提供了高效、实用的替代路径。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将多个预训练的扩散/流匹配策略（policy）在**分布层面**进行凸组合，使得组合后的评分函数（score function）在单步目标函数上优于任何单个评分。通过 Grönwall 型界（Grönwall-type bound）证明，这种单步优势可以沿着整个生成轨迹传递，最终实现系统性的性能增益。
- **关键技术细节**：
  - **General Policy Composition (GPC)**：一种无需训练（training-free）的测试时组合方法。具体步骤：
    1. 给定多个预训练策略（可异构，如 VA、VLA、扩散模型、流匹配模型），每个策略对应一个分布分数。
    2. 在测试时，对这些分布分数进行**凸组合**（convex combination），组合系数可以是固定的或通过测试时搜索（test-time search）自适应调整。
    3. 使用组合后的分数指导动作生成过程（去噪或流匹配迭代）。
    4. 无需修改任何父策略的权重，属于“即插即用”式组合。
  - **理论基础**：证明凸组合后的分数对应的单步优化目标（one-step functional objective）优于任何单个分数；进一步利用 Grönwall 型不等式证明该优势在多次迭代后依然保持，从而保证整个轨迹的改进。
- **算法流程（文字说明）**：
  1. 输入：K 个预训练策略模型（如扩散策略或流匹配策略），以及对应的测试任务状态。
  2. 对于每个时间步 t（或去噪步骤），计算每个策略的评分函数（score function）或向量场。
  3. 对 K 个评分函数进行加权平均（凸组合），权重 λ_i 满足 Σλ_i = 1，λ_i ≥ 0。
  4. 使用组合后的评分执行一步更新（如去噪或流匹配中的 ODE/SDE 步）。
  5. 重复步骤 2-4 直到所有生成步完成，输出最终动作。
  6. （可选）通过测试时搜索（如交叉验证）优化权重 λ 以适应当前任务。

## 3. 实验设计：数据集/场景、Benchmark、对比方法

- **数据集与场景**：
  - **Robomimic**：包含多种机器人操作任务的基准（如 lifting, can, square 等）。
  - **PushT**：用于评估接触推动任务的仿真环境。
  - **RoboTwin**：包含复杂机器人操作任务的基准。
  - **真实世界机器人评估**：在真实机器人平台上进行任务测试（未说明具体任务类型）。
- **Benchmark**：以上三个标准机器人控制基准以及真实世界实验。
- **对比方法**：
  - 主要对比各个单独的父策略（parent policies），即未组合的原始策略。
  - 可能还对比了不同组合算子（如线性组合、乘积组合等）以及不同的权重选择方式（固定权重 vs 搜索权重）。
  - 论文指出 GPC 可以与多种异构模型组合，包括 VA 和 VLA 模型，以及基于扩散或流匹配的模型。

## 4. 资源与算力

- 论文摘要和元数据中**未明确说明**所使用的 GPU 型号、数量、训练时长等具体算力信息。
- 由于 GPC 是训练无关（training-free）方法，其计算开销主要在于测试时需要进行多个父策略的多次前向传播（每步需计算每个策略的评分），以及可能的权重搜索。但具体算力成本未量化报告。

## 5. 实验数量与充分性

- **实验数量**：
  - 在三个仿真基准（Robomimic, PushT, RoboTwin）上进行了实验，并补充了真实世界机器人实验，覆盖了不同难度和类型的机器人任务。
  - 进行了消融研究：分析了不同组合算子（如凸组合 vs. 其他组合方式）以及不同权重策略（固定 vs. 搜索）的影响。
- **充分性评估**：
  - **正面**：任务覆盖面较广（仿真 + 真实），且包含了异构策略组合的验证（VA、VLA、扩散、流匹配），实验设计较为全面。
  - **可能不足**：未提及与现有的其他组合方法（如模型集成、蒸馏）的对比，仅比较了未组合的父策略；未报告统计显著性检验或多次重复实验下的方差；真实世界实验细节不足（如任务数量、机器人平台型号）。

## 6. 论文的主要结论与发现

- **核心结论**：测试时通过凸组合多个预训练策略的分布分数可以**一致地提升机器人控制性能**，且组合后的策略性能能够**超过任意一个父策略**。
- **理论发现**：单步目标函数的优势可以由 Grönwall 型界传递到整个生成轨迹，从数学上保证了组合策略的全局收益。
- **实践发现**：GPC 方法简单易用、无需额外训练，支持异构模型的即插即用，对不同视觉模态和网络架构都具有较好的适应性。

## 7. 优点：方法或实验设计上的亮点

1. **极低部署成本**：无需任何模型训练或微调，仅需在测试时执行多个前向传播和加权组合，大幅节省了计算和数据开销。
2. **通用性与灵活性**：可以组合 VA 和 VLA 模型、扩散和流匹配模型，甚至不同输入模态（如图像、语言）的策略，具有高度的即插即用特性。
3. **理论支撑扎实**：从单步目标改进出发，利用 Grönwall 型不等式推导出整体轨迹的收益，为组合策略的有效性提供了严谨的理论证明。
4. **丰富的实验验证**：在多个标准基准和真实机器人上验证，并进行了组合算子和权重的消融研究，表明方法的稳健性。

## 8. 不足与局限

1. **实验覆盖范围有限**：仅使用了三个仿真基准和一个真实世界场景，缺乏大规模、更多样化的机器人任务（如移动操作、双臂协作等）验证。
2. **未与现有组合方法对比**：论文仅比较了未组合的父策略，未与模型集成、知识蒸馏、加权投票等其他策略增强方法进行性能对比，难以判断 GPC 的相对优势。
3. **计算成本分析缺失**：虽然免训练，但测试时需同时运行多个父策略，实际推理时间开销和内存消耗并未量化，可能对实时控制产生不利影响。
4. **权重选择依赖搜索**：虽然提出自适应搜索，但搜索过程本身可能需要额外计算或假设任务可交互，对不可交互的离线场景可能不适用。
5. **应用限制**：方法依赖于已有预训练策略的质量和数据多样性，如果父策略均采集于相似分布，组合增益可能有限；此外，凸组合假设所有父策略具有一致的动作空间，异构策略可能需对齐。

（完）
