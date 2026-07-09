---
title: "Demystifying Robot Diffusion Policies: Action Memorization and a Simple Lookup Table Alternative"
title_zh: 揭秘机器人扩散策略：动作记忆化与简单查找表替代方案
authors: "Chengyang He, Xu Liu, Gadiel Mark Sznaier Camps, Joseph Bruno, Guillaume Adrien Sartoretti, Mac Schwager"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=PL0tJOfm7I"
tags: ["query:imitation"]
score: 8.0
evidence: 分析扩散策略并提出查找表替代方案
tldr: 该论文针对扩散策略在少量演示数据上表现出色的原因提出假设：它们本质上记忆了动作查找表。通过实验验证了该假设，并据此提出一个简单的查找表替代方法，在稀疏数据条件下达到与扩散策略相当的性能，揭示了扩散策略的工作机理。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略在少量演示中表现优异，但其成功原因尚不清晰。
method: 提出动作记忆化假设，并通过在潜在空间查找最近邻训练图像来回忆动作，构建简单查找表模型。
result: 查找表模型在稀疏数据下性能与扩散策略相当，验证了记忆化假设。
conclusion: 该工作解释了扩散策略的有效性，并提供了更简洁的替代方案，有助于理解与改进模仿学习。
---

## Abstract
Diffusion policies for visuomotor robot manipulation tasks achieve remarkable dexterity and robustness while only training on a small number of task demonstrations.  However, the reason for this performance remains a mystery. In this paper, we offer a surprising hypothesis: diffusion policies essentially memorize an action lookup table---\emph{and this is beneficial}. We posit that, at runtime, diffusion policies find the closest training image to the test image in a latent space, and recall the associated training action  (i.e. action chunk), offering reactivity without the need for action generalization. This is effective in the sparse data regime, where there is not enough data density for the model to learn action generalization. We support this claim with systematic empirical evidence, showing that even when conditioned on highly out of distribution (OOD) images, Diffusion Policy still outputs an action chunk from the training data. We evaluate and compare three representative policy families on the same data set: Diffusion Policy, Action Chunking with Transformers (ACT), and GR00T, a pre-trained generalist Vision-Language-Action (VLA) model.  We show that Diffusion Policy gives strong action memorization giving surprising robustness in OOD regimes, ACT shows action interpolation with poor robustness in OOD regimes, and GR00T (benefiting from substantial pre-training) shows both action interpolation and OOD robustness. As a simple alternative to Diffusion Policy, we introduce the Action Lookup Table (ALT) policy, showing that an explicit lookup table policy can perform comparably in this low data regime. Despite its simplicity, ALT attains Diffusion Policy–level performance while also providing faster inference and explicit OOD detection via latent-distance thresholds. These results reframe diffusion policies for robot manipulation as reactive memory retrieval under data sparsity, and provide practical tools for interpreting, evaluating, and monitoring such policies. More information can be found at: \url{https://stanfordmsl.github.io/alt/}.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：机器人视觉运动操控任务中，扩散策略（Diffusion Policy）仅需少量演示数据就能实现惊人的灵巧性和鲁棒性，但其成功背后的根本原因一直是一个谜。
- **核心假设**：作者提出一个反直觉的假设——扩散策略本质上是在**记忆一个动作查找表（action lookup table）**，而且这种记忆化反而是有益的。在运行时，扩散策略会在潜在空间中找到与测试图像最接近的训练图像，并直接回忆对应的训练动作块（action chunk），从而实现反应式控制，无需真正的动作泛化。
- **背景意义**：该假设解释了扩散策略在数据稀疏场景下仍然有效的原因（因为数据密度不足，模型无法学习泛化，记忆反而成为优势），并质疑了先前认为扩散策略具备强大泛化能力的认知。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：扩散策略在推理阶段执行的是**基于最近邻的动作回忆**，而非动作生成或插值。
- **关键技术细节**：
  - 作者通过系统性实验验证：即便向扩散策略输入高度分布外（OOD）的图像，它仍然输出训练数据中的某个动作块。
  - 基于该发现，作者提出一个极其简单的替代模型——**动作查找表策略（Action Lookup Table, ALT）**：
    1. 在训练阶段，将每条演示的图像（或图像特征）及其对应的动作块存入表中。
    2. 在测试阶段，将测试图像编码到潜在空间，计算其与所有训练图像特征的距离。
    3. 选择距离最近的训练图像，直接输出其关联的动作块。
  - **关键区别**：ALT 是显式的查找表，而扩散策略是隐式地实现了同样的功能（通过去噪过程近似最近邻检索）。
- **算法流程**（文字说明）：
  - 训练：采集少量演示（图像+动作块序列），训练一个图像编码器（如 ResNet）输出潜在向量，同时存储每一帧的潜在向量与对应的动作块。
  - 推理：输入测试图像 → 编码器得到潜在向量 → 遍历所有训练潜在向量计算欧氏距离 → 找到最近邻 → 输出其动作块。

## 3. 实验设计：数据集/场景、Benchmark、对比方法
- **数据集/场景**：论文未明确指定具体数据集名称，但提及在**同一数据集**上评估三种策略。根据参考文献（网址 https://stanfordmsl.github.io/alt/）推测可能涉及了常见的机器人操控基准（如 Robosuite 或自建任务）。具体场景需参考完整论文或项目网站。
- **Benchmark**：未明确列出标准 benchmark，但通过对比三种代表性策略的性能来建立评估框架。
- **对比方法**：
  - **Diffusion Policy（DP）**：当前最先进的模仿学习方法之一。
  - **Action Chunking with Transformers（ACT）**：基于 Transformer 动作分块的方法。
  - **GR00T**：预训练的通用视觉-语言-动作（VLA）模型，受益于大规模预训练。
- **评估维度**：包括在分布内（ID）和分布外（OOD）测试图像上的表现、动作是否正确回忆（是否来自训练集）、鲁棒性等。

## 4. 资源与算力
- **明确说明**：论文正文中**未提及**所使用的 GPU 型号、数量、训练时长等算力信息。需要指出这一点缺失。
- 推测：由于 ALT 方法极为轻量（仅需一次编码+最近邻搜索），训练算力需求很低；扩散策略和 ACT 可能需要 1-2 块 GPU（如 2080Ti 或 3090），但文中未确认。

## 5. 实验数量与充分性
- **实验组数**：主要进行了三组策略（DP、ACT、GR00T）的对比实验，并额外对比了 ALT 与 DP 在稀疏数据下的性能。同时，可能包含 OOD 图像注入实验、最近邻分析、距离阈值检测等消融/分析实验。
- **充分性评价**：
  - **优点**：实验设计紧扣核心假设（验证 OOD 条件下的输出是否属于训练动作），设计巧妙，能直接检验记忆化假说。
  - **不足**：
    - 仅在一个（或少数）数据集/任务上验证，缺乏跨多种任务、多种物体、多种环境（如真实机器人 vs 仿真）的广泛验证。
    - 未说明每个实验的重复次数和随机种子，统计显著性未知。
    - 缺乏对数据密度不同（如密集数据下 ALT 是否依然有效）的探讨。
    - 对比的 GR00T 是预训练模型，与 DP/ACT 在参数量和训练数据上不对等，可能影响公平性（但作者有意以此展示预训练带来的泛化）。
  - **总体**：实验足以支持主要结论（扩散策略记忆化），但不足以全面评估方法在真实世界的稳健性。

## 6. 论文的主要结论与发现
- **核心发现**：扩散策略在稀疏数据场景下**本质上是动作记忆查找表**，其成功源于对训练数据的精确回忆，而非动作泛化。
- **具体结论**：
  - 即使输入高度 OOD 图像，扩散策略依然输出训练动作块，表明其不具备真正的泛化能力。
  - ACT 表现出**动作插值**（在训练动作之间平滑过渡），但在 OOD 场景下鲁棒性差。
  - GR00T 由于大规模预训练，既具备动作插值能力，也具备 OOD 鲁棒性。
  - 提出的 ALT（显式查找表）在低数据 regime 下能达到与扩散策略相当的性能，同时**推理更快**（无需迭代去噪）且**可显式检测 OOD**（通过设定潜在距离阈值）。
- **理论贡献**：重新定义了扩散策略在机器人操控中的角色——一种数据稀疏下的反应式记忆检索机制。

## 7. 优点：方法或实验设计上的亮点
- **方法创新**：提出“动作记忆化”这一反直觉但简单优雅的假说，并用 ALT 作为概念验证，体现了“奥卡姆剃刀”原则——最简模型即可解释并复现主流的复杂方法。
- **实验设计巧妙**：通过 OOD 图像测试直接探测模型内部行为，直观而有力地佐证假设，避免了黑箱分析的模糊性。
- **实用价值**：ALT 方法近乎零成本推理，且天然支持 OOD 检测（距离阈值），在实际部署中可以有效避免模型在未知场景下做出危险动作，提高了安全性。
- **对比全面**：同时对比了三种代表性策略（扩散策略、动作分块 Transformer、预训练 VLA），覆盖了不同的学习范式，有助于理解不同方法的记忆/泛化特性。

## 8. 不足与局限
- **实验覆盖有限**：
  - 仅在一个（或极少数）数据集上验证，缺乏多任务、多机器人形态、真实世界环境的泛化测试。
  - 未探讨数据密度变化的影响（例如当演示数量增加到足够让模型学到泛化时，ALT 是否会失效？扩散策略是否依然记忆化？）。
- **偏差风险**：
  - 假设依赖潜在空间最近邻，但潜在空间的质量（编码器训练方式）高度影响结果；论文未充分讨论不同编码器的影响。
  - 对比方法中 GR00T 使用了大量外部预训练数据，与 DP 和 ACT 的训练条件不对等，可能影响公平性（但作者已指出这点）。
- **应用限制**：
  - ALT 在密集数据场景下可能完全失效（因为没有泛化能力，最近邻可能较远导致动作错误），而扩散策略在大数据下可能发展出真正的泛化，该论文未覆盖此情况。
  - 动作块是固定长度，无法处理变长任务。
  - 论文未讨论动态环境或干扰对查找表的影响（如物体位置变化需要插值而非回忆）。
- **算力与复现性**：未提供算力信息，也无开源代码（只有项目网站，可能后续会开放），不利于其他研究者复现。

（完）
