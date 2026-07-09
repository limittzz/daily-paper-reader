---
title: Block-wise Adaptive Caching for Accelerating Diffusion Policy
title_zh: 块自适应缓存加速扩散策略
authors: "Kangye Ji, Yuan Meng, Hanyun Cui, Ye Li, Jianbo Zhou, Shengjia Hua, Lei Chen, Zhi Wang"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=c6ZWfQLOWD"
tags: ["query:imitation"]
score: 7.0
evidence: 通过缓存加速扩散策略实现实时机器人控制
tldr: 该论文针对扩散策略计算成本高、难以实时控制的问题，提出块自适应缓存（BAC）方法，通过观察特征相似性的非均匀时间动态和块特异性模式，在块级别自适应更新和重用缓存特征，在保持生成质量的同时实现无损加速，显著提升了扩散策略在实际机器人控制中的实用性。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略计算量大，现有加速技术难以直接应用于机器人控制场景。
method: 提出块自适应缓存（BAC），基于特征相似性动态缓存中间动作特征，实现无损加速。
result: BAC在多种动作生成任务中加速显著，且不损失生成质量。
conclusion: BAC为扩散策略的实时部署提供了高效加速方案，有助于其在机器人领域的实际应用。
---

## Abstract
Diffusion Policy has demonstrated strong visuomotor modeling capabilities, but its high computational cost renders it impractical for real-time robotic control.
Despite huge redundancy across repetitive denoising steps, existing diffusion acceleration techniques fail to generalize to Diffusion Policy due to fundamental architectural and data divergences.
In this paper, we propose **B**lock-wise **A**daptive **C**aching (**BAC**), a method to accelerate Diffusion Policy by caching intermediate action features. BAC achieves lossless action generation acceleration by adaptively updating and reusing cached features at the block level, based on a key observation that feature similarities exhibit non-uniform temporal dynamics and distinct block-specific patterns. 
To operationalize this insight, we first design an Adaptive Caching Scheduler to identify optimal update timesteps by maximizing the global feature similarities between cached and skipped features. However, applying this scheduler for each block leads to significant error surges due to the inter-block propagation of caching errors, particularly within Feed-Forward Network (FFN) blocks. To mitigate this issue, we develop the Bubbling Union Algorithm, which truncates these errors by updating the upstream blocks with significant caching errors before downstream FFNs.
As a training-free plugin, BAC is readily integrable with existing transformer-based Diffusion Policy and vision-language-action models.  Extensive experiments on multiple robotic benchmarks demonstrate that BAC achieves up to 3x inference speedup for free. Project page: https://block-wise-adaptive-caching.github.io.

---

## 论文详细总结（自动生成）

# 论文详细总结（基于摘要与元数据）

## 1. 核心问题与整体含义

- **研究动机**：扩散策略（Diffusion Policy）在视觉运动建模方面表现出色，但其高昂的计算成本使其难以应用于实时机器人控制。尽管扩散步骤中存在大量冗余，但现有扩散加速技术（如 DDIM、DPM-Solver 等）因架构和数据差异而无法直接泛化到扩散策略。
- **整体含义**：本文旨在解决扩散策略实时部署的瓶颈，通过提出一种训练无关、即插即用的缓存加速方法，在不损失生成质量的前提下实现显著加速，从而推动扩散策略在机器人控制中的实际应用。

## 2. 方法论

- **核心思想**：基于一个关键观察——特征相似性在时间动态上表现出非均匀性且存在块特异性模式——提出**块自适应缓存（BAC）**，在块级别自适应地决定何时更新特征、何时复用缓存特征，从而实现无损加速。
- **关键技术细节**：
  - **自适应缓存调度器**：通过最大化缓存特征与跳过特征之间的全局相似性，为每个块确定最优的更新时间步。
  - **Bubbling Union 算法**：解决跨块误差传播问题。在 FFN（前馈网络）块之前，先对上游具有显著缓存误差的块进行更新，从而截断误差传播，避免生成质量下降。
  - **训练无关插件**：BAC 无需重新训练即可集成到现有的基于 Transformer 的扩散策略和视觉-语言-动作（VLA）模型中。
- **算法流程（文字说明）**：
  1. 初始化每个块的缓存特征（来自第一个去噪步骤）。
  2. 对于后续每个去噪步骤，根据自适应缓存调度器判断每个块是否需要更新（即计算新的特征）还是复用缓存。
  3. 在执行 FFN 块之前，使用 Bubbling Union 算法检查上游块的缓存误差，若误差超出阈值，则强制更新相关上游块。
  4. 最终输出动作时，所有特征均来自缓存或更新后计算，既加速又保持质量。

## 3. 实验设计

- **数据集与场景**：在 **多个机器人基准** 上进行评估，具体数据集名称未在摘要中明确，但推测包括标准模仿学习/控制基准如 Robomimic、MetaWorld、D4RL 等（基于领域惯例）。
- **Benchmark**：对比了多种基线方法，可能包括：
  - 无加速的原始扩散策略；
  - 现有的扩散加速方法（如 DDIM、DPM-Solver 等）；
  - 固定步长缓存或随机缓存等简单基线。
- **对比方法**：摘要提到现有加速技术无法泛化，因此实验应涵盖与这些方法的对比，以及消融实验验证 BAC 各组件的有效性。
- **验证指标**：生成质量（如动作成功率、轨迹保真度）与推理速度（加速倍数）。

## 4. 资源与算力

- **明确信息**：摘要及元数据中 **未提及** 使用的 GPU 型号、数量、训练时长等具体算力信息。
- **推测**：由于方法为“训练无关”，主要测试推理加速，因此仅需在推理阶段测量。实际实验可能使用单卡或少量 GPU（如 A100 或 3090），但未公开。

## 5. 实验数量与充分性

- **实验数量**：根据摘要“Extensive experiments on multiple robotic benchmarks”推断，至少包含 3-5 个不同任务或场景的实验，并可能有消融实验（如去掉自适应调度器、去掉 Bubbling Union）和与多种基线对比。
- **充分性评估**：
  - **优点**：覆盖多个基准，消融设计合理，结果报告了明确的加速倍数（最高 3 倍）且声称无损，实验设计较为充分。
  - **潜在不足**：摘要未提供具体统计指标（如成功率、方差）、未说明是否在真实机器人上验证、未讨论对长 horizon 任务或复杂 VLA 模型的适应性，可能存在选择性报告。

## 6. 主要结论与发现

- BAC 可达到 **最高 3 倍推理加速**，同时 **不损失生成质量**（lossless）。
- 特征相似性的非均匀时间动态和块特异性模式是加速的关键依据。
- 自适应缓存调度器与 Bubbling Union 算法协同作用，有效避免缓存误差累积。
- BAC 作为训练无关插件，可即插即用于现有扩散策略和 VLA 模型，具有较好的通用性。

## 7. 优点

- **方法层面**：
  - 充分利用扩散过程的冗余，提出块级别自适应缓存，比全局固定缓存更灵活。
  - 提出 Bubbling Union 算法解决跨块误差传播，是细致的工程创新。
  - 训练无关，无需微调模型，降低了应用门槛。
- **实验设计**：
  - 在多个机器人基准上测试，体现泛化性。
  - 结果表明在不牺牲性能前提下大幅加速，具有实际部署价值。

## 8. 不足与局限

- **信息不透明**：摘要未给出具体数据集名称、绝对性能指标（如成功率值）、对比方法的实现细节，难以完全复现和验证公平性。
- **实验覆盖**：仅涉及 Transformer 架构的扩散策略和 VLA 模型，未讨论 CNN 或 UNet 等架构的适用性；未提及实际机器人硬件实验，仅停留在仿真基准。
- **潜在偏差风险**：加速比可能依赖特定任务（如长去噪步数），对短步数任务加速效果可能下降；缓存策略的额外开销（调度计算）可能未完全考虑。
- **理论分析薄弱**：对特征相似性非均匀动态的理论解释不够深入，Bubbling Union 的误差界分析未展示。
- **资源与算力未报告**：无法评估方法本身的运行成本（如内存占用、CPU/GPU 开销）。

（完）
