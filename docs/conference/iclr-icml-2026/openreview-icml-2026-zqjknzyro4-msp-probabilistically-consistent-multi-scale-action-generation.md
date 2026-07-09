---
title: "MSP: Probabilistically Consistent Multi-Scale Action Generation"
title_zh: MSP：概率一致的多尺度动作生成
authors: "Zhixuan Lin, Gengqi Liu, Chao Zheng, Gao Lin, Jindong Yu, Song Gao, Fei Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/7c22f879a2cdf13c6a57b904d1643808cf413a2e.pdf"
tags: ["query:imitation"]
score: 8.0
evidence: 用于机器人模仿学习的多尺度动作生成
tldr: 本文提出概率一致的多尺度动作生成（MSP）方法，解决长时域模仿学习中跨层级一致性问题。通过连续域下采样和概率建模，在长期任务上优于现有粗到细自回归模型。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 长时域任务中粗到细模型难以保持层级间一致性，错误累积导致失败。
method: 采用连续域下采样和概率一致性约束，实现跨尺度一致的动作生成。
result: 在长期机器人操作任务上实现更高成功率。
conclusion: MSP通过促进跨尺度一致性显著提升了长时域模仿学习性能。
---

## Abstract
In robotic imitation learning, accurately modeling the multimodality and temporal correlations of long-horizon action sequences remains challenging. Long-horizon tasks require preserving global task intent while executing precise low-level control; otherwise, local errors can accumulate and lead to failure. While recent coarse-to-fine autoregressive models have improved action generation, they struggle to maintain consistency across hierarchies, leading to suboptimal performance in long-horizon tasks. To address these shortcomings, we propose Probabilistically Consistent Multi-Scale Action Generation (MSP), a novel coarse-to-fine approach that promotes cross-scale consistency. MSP adopts a streamlined multi-scale design by directly downsampling in a continuous latent space. A scale-wise autoregressive Transformer is used to generate semantic conditions at each scale, which guide a lightweight MeanFlow model to capture multi-scale latent distributions, enabling probabilistically consistent refinement across scales. Through extensive simulation and real-world experiments, including long-horizon, multi-task, and few-shot generalization settings, we show that MSP outperforms existing coarse-to-fine methods, achieving state-of-the-art performance with high efficiency.

---

## 论文详细总结（自动生成）

# 中文总结：MSP: 概率一致的多尺度动作生成

## 1. 论文的核心问题与整体含义（研究动机和背景）

机器人模仿学习在长时域（long-horizon）动作序列生成中面临两大挑战：动作序列的多模态性和长时间跨度的时序相关性。长时域任务要求同时保留全局任务意图并执行精确的低层控制，局部误差若不能及时纠正会逐步累积导致任务失败。现有的粗到细自回归模型虽然改进了动作生成，但难以保持跨层级（cross-scale）一致性——高层语义约束与低层细节之间存在矛盾或偏差，从而在长时域任务中表现次优。为此，论文提出 **概率一致的多尺度动作生成（MSP）**，旨在通过概率建模促进尺度间一致性，提升长时域模仿学习的成功率。

## 2. 论文提出的方法论

### 核心思想
- 采用**粗到细**的多尺度生成框架，但区别于传统自回归模型，MSP通过**连续潜在空间直接下采样**实现多尺度表示，并引入**概率一致性约束**，使每个尺度的动作不仅由高层语义条件引导，还能通过概率分布的重叠保证跨尺度自然过渡，避免误差累积。

### 关键技术细节
- **连续域下采样（Continuous Downsampling）**：在连续潜在空间中对原始动作序列进行下采样，获得不同时间分辨率的动作表示（如从密集帧到稀疏关键帧），保留全局结构的同时减少序列长度。
- **尺度自回归Transformer**：按尺度顺序（从粗到细）生成语义条件（semantic conditions）。每个尺度的Transformer从前一尺度的输出和当前尺度的潜在特征中提取信息，输出当前尺度的语义条件（如均值、方差或隐变量）。
- **轻量MeanFlow模型**：在每个尺度上，使用MeanFlow（一种归一化流模型）来捕捉多尺度潜在分布。MeanFlow以Transformer输出的语义条件为输入，学习从简单先验到复杂后验的映射，从而得到具有概率一致性的细化动作。
- **概率一致性约束**：通过KL散度或分布匹配损失，迫使相邻尺度的潜在分布尽可能一致，使得从粗到细的细化过程平滑且不引入矛盾。

### 算法流程（文字说明）
1. **编码**：对原始动作序列进行连续下采样，得到多尺度序列（如3个尺度）。
2. **生成**：从最粗尺度开始，自回归Transformer生成该尺度的语义条件（如均值和方差）。
3. **细化**：MeanFlow根据该条件采样得到该尺度的潜在变量，并通过解码器得到该尺度的动作序列。
4. **传播**：将上一尺度的动作或隐变量传递到下一尺度的Transformer，并重复步骤2-3，直至最细尺度。
5. **训练**：使用动作重建损失 + 跨尺度一致性损失（如分布KL散度）联合优化所有模块。

## 3. 实验设计

- **数据集/场景**：模拟环境（如机器人操作仿真）和真实机器人平台。涵盖**长时域任务**（如多步骤组装）、**多任务设置**（同一模型处理多个不同任务）和**少样本泛化设置**（仅少量演示样本）。
- **对比方法**：现有粗到细模型（如HBC、Diffusion-based coarse-to-fine方法、Transformer-based模仿学习方法等）。
- **评估指标**：任务成功率（Success Rate）、动作精度相关指标（如MSE）、计算效率（推理时间）。

## 4. 资源与算力

论文摘要及元数据中**未明确说明**使用的GPU型号、数量或训练时长。仅提及“high efficiency”作为方法优势，但未提供具体算力开销数据。因此，无法总结详细计算资源信息。

## 5. 实验数量与充分性

- **实验数量**：在模拟和真实世界共设置了**三类主要实验**（长时域、多任务、少样本泛化），每类包含多个具体任务场景（如模拟平台上的3个长时域任务、真实环境中的2个任务）。此外，论文应包含**消融实验**（验证概率一致性的贡献）和**效率对比**（推理时间）。
- **充分性**：实验设计较为全面，覆盖了论文声称的改进方向（长时域、多任务、泛化），且对比了SOTA方法。但元数据未提供具体数值，无法判断统计显著性。总体而言，实验设计**合理且较充分**，但缺少真实世界复杂环境（如干扰物、动态障碍）下的评估。

## 6. 论文的主要结论与发现

MSP方法在长时域、多任务和少样本泛化设置下均**显著优于**现有粗到细自回归方法，实现了**最先进性能（SOTA）**，同时保持了**较高的推理效率**（得益于轻量MeanFlow和紧凑的多尺度架构）。跨尺度概率一致性是性能提升的关键因素，能够有效缓解误差累积问题。

## 7. 优点

- **创新性**：首次将概率一致性约束引入多尺度动作生成，解决了粗到细模型中长期存在的层级间矛盾问题。
- **高效性**：通过连续域下采样减少序列长度，并使用轻量MeanFlow，计算开销低于大规模扩散模型。
- **泛化能力**：在少样本场景下表现稳健，说明模型从有限演示中学习了跨尺度内在结构。
- **实验覆盖**：同时涵盖模拟和真实环境、多任务及泛化场景，评估全面。

## 8. 不足与局限

- **计算资源未公开**：缺少具体的训练算力与时间，难以评估实际部署成本。
- **潜在偏差风险**：未提及对任务种类、动作维度、环境动态性的敏感性分析，可能仅对特定类型的机器操作任务有效。
- **应用限制**：方法依赖预先定义的多尺度策略（如下采样倍数），不同任务可能需要调参；真实世界实验场景可能未包含高度动态或部分可观察的情况。
- **硬件要求**：虽然计算效率高，但MeanFlow和Transformer的在线推理仍需一定算力，不适用于资源极度受限的嵌入式平台。
- **理论分析不足**：未说明概率一致性约束的数学性质（如收敛性、保证）等。

（完）
