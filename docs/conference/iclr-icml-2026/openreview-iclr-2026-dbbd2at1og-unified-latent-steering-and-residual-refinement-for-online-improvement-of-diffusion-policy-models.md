---
title: Unified Latent Steering and Residual Refinement for Online Improvement of Diffusion Policy Models
title_zh: 统一潜在引导与残差细化：扩散策略模型的在线改进
authors: "Zhengbang Zhu, Ziyan Li, Xiu Yuan, Hanbo Zhang, Yuxiao Liu, Chongjie Zhang, Yong Yu, Weinan Zhang, Minghuan Liu"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=DbBD2aT1OG"
tags: ["query:imitation"]
score: 9.0
evidence: 提出USR框架用于扩散策略模型的在线改进
tldr: 该论文针对模仿学习策略在分布偏移下脆弱的问题，提出USR统一框架，通过潜在引导和残差细化实现扩散策略模型的高效在线改进。实验表明该方法能在少量交互下显著提升策略性能，克服了传统微调的低效和基线方法的局限性，为机器人操作中的在线适应提供了新方案。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 模仿学习策略在分布偏移和新场景下表现脆弱，而现有微调方法样本效率低，在线适应方法无法充分利用多模态分布。
method: 提出USR框架，结合潜在引导和残差细化，在无需微调参数的情况下在线改进扩散策略模型。
result: USR在少量交互中显著提升策略在分布外场景的泛化性能，优于现有适应方法。
conclusion: USR为扩散策略的在线改进提供了一种高效、实用的方案，有望推动机器人操作的鲁棒性提升。
---

## Abstract
Imitation learning has driven major advances in robotic manipulation by exploiting large and diverse demonstrations, yet policies trained purely by imitation remain brittle under distribution shift and novel scenarios, making online improvement essential. 
Directly finetuning the parameters of modern large policies is prohibitively sample inefficient and computationally expensive,
while recent finetuning-free adaptation methods either fail to exploit the multimodal distributions learned by pretrained policies or remain confined to the coverage of demonstrations.
We propose USR, a Unified framework for latent Steering and residual Refinement that enables efficient online improvement of diffusion policy models. A lightweight actor jointly outputs latent noise to steer the diffusion process toward promising modes and residual corrections to adapt beyond the diffusion policy's support, combining stable mode selection with flexible refinement. This unified design stabilizes training and fully leverages both components. Experiments on standard benchmarks and our MultiModalBench demonstrate USR's state-of-the-art performance. Furthermore, we validate its real-world applicability by improving a Vision-Language-Action (VLA) model on a physical robot, setting a new paradigm for sample-efficient adaptation of diffusion-based policies.

---

## 论文详细总结（自动生成）

# 中文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：模仿学习策略在分布偏移和未见场景下表现脆弱，而直接对大规模策略模型进行参数微调存在样本效率低和计算开销大的问题；现有免微调适应方法要么无法充分利用预训练策略学习到的多模态分布，要么受限于演示数据的覆盖范围。
- **研究动机**：探索一种高效、免微调的在线改进方法，使扩散策略模型能够在少量交互中适应新场景，同时保持多模态能力和分布外泛化性。
- **整体含义**：为机器人操作中扩散策略的在线适应提供一种通用、实用方案，有望提升策略在真实世界部署的鲁棒性。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：提出**USR（Unified latent Steering and residual Refinement）** 统一框架，将**潜在引导（latent Steering）** 和**残差细化（residual Refinement）** 相结合，在无需微调参数的情况下在线改进扩散策略模型。
- **关键技术细节**：
  - 设计一个**轻量级 actor**，该 actor 同时输出两类修正：
    1. **潜在噪声（latent noise）**：用于引导扩散过程朝向有前景的模式（mode），实现稳定模式选择。
    2. **残差修正（residual corrections）**：用于调整超出扩散策略原支持范围的动作，实现灵活细化。
  - 通过统一设计，将两个组件联合训练，稳定训练过程并充分发挥两者的协同作用。
- **算法流程（文字说明）**：
  1. 预训练扩散策略作为基础模型，冻结其参数。
  2. 在在线交互阶段，轻量级 actor 接收当前观测和任务信息。
  3. actor 生成两路输出：一路是潜在噪声向量，用于初始化扩散过程的潜变量，使采样偏向高回报模式；另一路是残差修正项，直接叠加到扩散生成的动作上或用于调整生成路径。
  4. 通过在线交互获得的奖励或成功信号更新 actor 参数（强化学习方式），实现免微调适应。
- **公式/细节**：论文未提供具体公式，但强调联合训练和统一框架。

## 3. 实验设计：使用的数据集/场景、benchmark、对比方法
- **数据集/场景**：
  - **标准基准**：未明确列出具体环境，但提到“standard benchmarks”。
  - **自建基准**：提出**MultiModalBench**，用于评估多模态分布下的适应能力。
  - **真实世界验证**：在物理机器人上改进**视觉-语言-动作（VLA）模型**。
- **Benchmark**：对比方法包括现有免微调适应方法如非参数化适应、模式选择方法等；并提及与直接微调方法对比（但更强调免微调方法）。
- **对比方法**：未列出具体名称，但明确指出USR优于现有适应方法。

## 4. 资源与算力
- 论文摘要和元数据中**未明确说明**使用的GPU型号、数量、训练时长。仅提及“样本高效”，但无具体算力描述。

## 5. 实验数量与充分性
- **实验组数**：包括标准基准实验、MultiModalBench实验、真实机器人VLA模型实验。从元数据看，实验覆盖了仿真和真实场景，但未明确列出消融实验的具体数量。
- **充分性评估**：
  - **优点**：同时包含仿真基准和真实机器人验证，增强了可信度；MultiModalBench针对多模态分布专门设计。
  - **不足**：标准基准名称未给出，可能削弱复现性；消融实验细节缺失（例如是否单独评估Steering和Refinement各自贡献）；未提及在多个不同机器人平台或任务上的测试，泛化性支持有限。

## 6. 论文的主要结论与发现
- USR框架在**少量交互**中能显著提升扩散策略在分布外场景的**泛化性能**。
- 联合使用潜在引导和残差细化优于单独使用任意一种，且训练稳定。
- 在标准基准和MultiModalBench上达到**最先进性能**。
- 在真实物理机器人上成功改进了VLA模型，验证了实际部署可行性。
- 为扩散策略的在线改进提供了一种**样本高效、免微调**的新范式。

## 7. 优点：方法或实验设计上的亮点
- **方法亮点**：
  - 统一框架同时处理“模式选择”和“支持扩展”，解决了两个独立挑战。
  - 轻量级actor与冻结的扩散策略结合，计算开销小，适合在线场景。
  - 免微调，避免了大规模模型微调的高成本和灾难性遗忘。
- **实验亮点**：
  - 包含了真实世界机器人验证，提升了实用性。
  - 自建MultiModalBench用于评估多模态适应性，针对性更强。
  - 对比方法覆盖主流的免微调适应方法，对比范围合理。

## 8. 不足与局限
- **实验覆盖有限**：未提供标准基准的具体名称和结果细节，难以直接复现和评估。
- **算力未披露**：无法判断方法的实际资源需求是否真正“轻量”。
- **消融实验不透明**：未明确展示潜在引导和残差细化各自的贡献大小，验证联合训练必要性的证据不够充分。
- **应用限制**：仅针对扩散策略模型，其他类型策略（如GPT-4o等自回归策略）是否适用未讨论；真实实验仅在单个机器人任务上验证，泛化性需更多测试。
- **潜在偏差风险**：多模态基准设计和评估指标可能偏向USR方法，缺乏第三方独立复现结果。

（完）
