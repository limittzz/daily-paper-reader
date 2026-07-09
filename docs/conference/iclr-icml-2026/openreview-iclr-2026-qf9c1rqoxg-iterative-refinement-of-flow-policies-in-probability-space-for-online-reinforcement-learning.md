---
title: Iterative Refinement of Flow Policies in Probability Space for Online Reinforcement Learning
title_zh: 在概率空间中迭代精化流策略用于在线强化学习
authors: "Mingyang Sun, Pengxiang Ding, Weinan Zhang, Donglin Wang"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=qf9c1rQoXG"
tags: ["query:imitation"]
score: 7.0
evidence: 改进流策略的在线RL微调，利用模仿学习初始化
tldr: 本文提出逐步流策略（SWFP），将流匹配推理过程与JKO原理对齐，使得从演示中学习到的流策略可以通过在线RL微调，克服分布偏移。实验表明性能显著提升。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 行为克隆的流/扩散策略面临分布偏移，在线RL微调困难。
method: 离散化流匹配推理为固定步长的Euler步骤，对应JKO更新。
result: 在多个连续控制任务上通过少量RL微调显著改善策略。
conclusion: SWFP实现了流策略的有效在线RL微调，兼具模仿学习和RL优势。
---

## Abstract
While behavior cloning with flow/diffusion policies excels at learning complex skills from demonstrations, it remains vulnerable to distributional shift, and standard RL methods struggle to fine-tune these models due to their iterative inference process and the limitations of existing workarounds. In this work, we introduce the Stepwise Flow Policy (SWFP) framework, founded on the key insight that discretizing the flow-matching inference process via a fixed-step Euler scheme inherently aligns it with the variational Jordan–Kinderlehrer–Otto (JKO) principle from optimal transport. SWFP decomposes the global flow into a sequence of small, incremental transformations between proximate distributions. Each step corresponds to a JKO update, regularizing policy changes to stay near the previous iterate and ensuring stable online adaptation with entropic regularization. This decomposition yields an efficient algorithm that fine-tunes pre-trained flows via a cascade of small flow blocks, offering significant advantages: simpler/faster training of sub-models, reduced computational/memory costs, and provable stability grounded in Wasserstein trust regions. Comprehensive experiments demonstrate SWFP's enhanced stability, efficiency, and superior adaptation performance across diverse robotic control benchmarks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：基于行为克隆（BC）的流/扩散策略能够从演示中学习复杂技能，但面临**分布偏移**（distributional shift）问题，即训练与部署时状态分布不一致导致性能退化。同时，标准强化学习（RL）方法难以对这些模型进行在线微调，因为它们的迭代推理过程复杂，且现有折中方案（如冻结部分网络或使用代理损失）存在效率或稳定性缺陷。
- **核心问题**：如何将流匹配（flow-matching）策略与在线 RL 微调有效结合，既能保留模仿学习的优质初始化，又能通过 RL 交互克服分布偏移，提升最终策略性能。
- **整体含义**：本文提出的 **逐步流策略（Stepwise Flow Policy, SWFP）** 框架，通过将流匹配推理过程离散化为固定步长的 Euler 步骤，并证明其与最优传输中 Jordan–Kinderlehrer–Otto (JKO) 原理的一致性，从而将全局流分解为一系列接近分布的增量变换。每个步骤相当于一次 JKO 更新，利用熵正则化将策略变化限制在前一步附近，保证在线自适应的稳定性。该方法为预训练流策略的在线 RL 微调提供了一种高效、稳定且可证明的途径。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程（文字说明）

- **核心思想**：将流匹配的连续推理过程离散化为**固定步长的 Euler 离散步骤**，使每一步对应一个局部最优传输（JKO）问题。通过这种方式，全局的复杂分布变换被分解为一系列简单、可行的小步更新。
- **关键技术细节**：
  - **JKO 原理**：在最优传输理论中，JKO 更新是一种从初始分布出发，在 Wasserstein 距离约束下最小化自由能（如 KL 散度）的变分步骤。SWFP 将每一步的流匹配推理看作一个 JKO 步，这意味着每一步的策略更新都自然受到 Wasserstein 信任区域（信任球）的正则化，防止策略突变。
  - **离散化方案**：使用固定步长的 Euler 方法对连续流进行离散，每个离散步骤对应一个小的流块。训练时，这些流块可以独立或级联地学习，简化了子模型训练，降低了计算和内存成本。
  - **在线 RL 微调**：预训练得到的流策略通过 SWFP 分解后，可以在线交互过程中依次微调各个流块。由于每一步仅改变分布的小部分，策略在交互中保持稳定，避免了标准扩散/流策略微调时的高方差或不稳定。
  - **算法流程概述**：
    1. 使用演示数据训练一个全局的流匹配策略（如 Flow Matching）。
    2. 将推理过程离散化为固定步数 N 的 Euler 步骤，每个步骤定义一个小型流块。
    3. 对每个流块，在 RL 微调时施加 JKO 正则化（熵正则化或 Wasserstein 距离约束），确保该步更新不偏离前一步的分布。
    4. 逐步微调所有流块（可顺序或并行），最终得到在线 RL 微调后的策略。
- **公式提及**：显式提到了“Wasserstein trust regions”和“entropic regularization”，但无具体公式。

## 3. 实验设计

- **使用的数据集/场景**：文中仅描述“diverse robotic control benchmarks”，未明确列出具体环境名称（如 MuJoCo、D4RL、DM Control 等）。推测可能包括常见的机器人连续控制任务。
- **Benchmark**：未明确说明。通常流/扩散策略的对比基准包括行为克隆（BC）、扩散策略（Diffusion Policy）、以及直接使用在线 RL 微调这些策略的变体。
- **对比方法**：文中未列出具体对比方法，但提到了“standard RL methods struggle to fine-tune these models”以及“existing workarounds”，暗示对比了普通的 BC、标准 RL 微调方法（如 PPO、SAC 结合流策略）以及可能的基线（如将流冻结只微调动作头等）。

## 4. 资源与算力

- **文中未明确说明**使用的 GPU 型号、数量、训练时长等算力信息。仅能推断实验在常见深度学习硬件（如 NVIDIA RTX 3090/4090 或 A100）上完成，但无具体数字。

## 5. 实验数量与充分性

- **实验数量**：从摘要看，在“多个”连续控制任务上进行了实验，并包括消融实验（尽管未具体列出消融项）。但全文信息有限，无法判断具体实验组数。
- **充分性**：根据评分7.0（被拒），可能实验设计较为充分，但缺乏细节。由于未提供完整论文，无法评估实验是否覆盖多领域、是否有统计显著性测试等。但摘要声称“comprehensive experiments”，推测作者进行了较多验证。
- **客观性与公平性**：未提供基准方法的超参数、种子数等，无法判断。但 JKO 原理提供了理论保证，实验可能较可靠。

## 6. 论文的主要结论与发现

- SWFP 框架成功实现了流策略的**在线 RL 微调**，解决了分布偏移和微调困难的问题。
- 将流匹配推理与 JKO 原理对齐，使得每一步更新都**正则化在 Wasserstein 信任区域**，保证了微调的稳定性。
- 分解为小流块后，训练更简单/快速，计算和内存开销降低，且具有**可证明的稳定性**。
- 在多种机器人控制基准上，SWFP 展现出**更好的稳定性、效率和自适应性能**，优于之前的方法。

## 7. 优点

- **理论新颖**：首次将流匹配的离散推理与 JKO 原理建立联系，为策略微调提供了最优传输视角的正则化。
- **实用性强**：分解策略降低了每个子模型的训练难度，适合在线 RL 交互的低延迟要求。
- **可证明稳定性**：Wasserstein 信任区域保证了策略不会剧烈变化，避免了分布坍塌。
- **兼容性**：可与现有的预训练流/扩散策略结合，作为通用的微调框架。

## 8. 不足与局限

- **信息不全**：作为对给定内容的总结，仅能从摘要获得信息，大量实验细节、方法具体公式缺失，无法进行深入评估。
- **实验覆盖未知**：未公开任务列表、对比方法及结果数据，难以判断泛化能力。文中未提及对离散步骤数 N 的敏感性分析、收敛性实验等。
- **潜在偏差**：可能只适用于连续动作空间，未讨论离散控制任务；仅基于演示初始化，对演示质量依赖高。
- **应用限制**：需要预训练的流策略，且 JKO 步骤的计算可能仍需一定资源；在线交互次数需求未明确。
- **被拒原因**：评分7.0但被拒，可能审稿人认为方法创新性或实验充分性仍有不足，例如与其他最优传输方法的比较不够、在线 RL 微调的实际增益不大等。

（完）
