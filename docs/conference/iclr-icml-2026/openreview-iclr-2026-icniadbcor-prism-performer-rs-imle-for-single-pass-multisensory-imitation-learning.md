---
title: "PRISM: Performer RS-IMLE for Single-pass Multisensory Imitation Learning"
title_zh: PRISM：基于Performer RS-IMLE的单遍多感官模仿学习
authors: "Amisha Bhaskar, Pratap Tokekar, Stefano Di Cairano, Alexander Schperberg"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=icnIadBCoR"
tags: ["query:imitation"]
score: 9.0
evidence: 单遍多感官机器人模仿学习
tldr: 本文提出PRISM，基于批全局拒绝采样IMLE的单遍策略。结合时序多感官编码器和线性注意力生成器，在MetaWorld、CALVIN等基准上同时达到实时性和多模态分布建模，满足实际机器人需求。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有生成方法无法同时满足实时性、多模态和多感官要求。
method: 采用IMLE的变体，结合时序多感官编码器和Performer架构。
result: 在多个机器人基准上实现实时控制和高精度模仿。
conclusion: PRISM成功平衡了多模态、实时性和多感官融合，适用于真实机器人。
---

## Abstract
Robotic imitation learning typically requires models that capture multimodal action distributions while operating in real-time control rates and accommodating multiple sensing modalities. Although recent generative approaches such as diffusion models, flow matching, and Implicit Maximum Likelihood Estimation (IMLE) have achieved promising results in this domain, they satisfy only a subset of these requirements. To satisfy these requirements, we introduce PRISM, based on a batch-global rejection-sampling variant of IMLE. PRISM is a single-pass policy that couples a temporal multisensory encoder (e.g, RGB, Depth, tactile, audio, proprioception) with a linear-attention generator using a Performer architecture. We validate on MetaWorld, CALVIN, Robomimic, and a real hardware suite using a Unitree Go2 with a 7-DoF arm, wrist and shoulder RGB, tactile, audio, and proprioception sensors. PRISM matches or outperforms diffusion, flow-matching, and prior IMLE policies in terms of task success rates, robustness, and sample efficiency. In CALVIN with 10\% of the data, PRISM improves the success rate by $\sim$ 10\% over IMLE, $\sim$ 20\% over flow matching, and $\sim$ 25\% over diffusion, while reducing the jerk by about $20\times$. On MetaWorld, PRISM is 5-12\% on Hard/Very-Hard splits over diffusion and flow baselines. Real-world loco-manipulation shows 10--25\% higher success and maintains faster inference diffusion policy. These results position PRISM as a fast, accurate, and multisensory imitation policy that retains multimodal action coverage without iterative sampling.

---

## 论文详细总结（自动生成）

# 论文详细中文总结：PRISM: Performer RS-IMLE for Single-pass Multisensory Imitation Learning

## 1. 论文的核心问题与整体含义

- **研究动机**：机器人模仿学习需要模型同时满足三个关键要求：1) 捕获多模态动作分布（处理多峰行为）；2) 实时控制率（低延迟推理）；3) 融合多种传感模态（RGB、深度、触觉、音频、本体感受）。现有的生成方法（扩散模型、流匹配、隐式最大似然估计IMLE）只能满足其中部分要求。
- **核心问题**：如何在单次前向传播（单遍）中同时实现多模态动作建模、实时推理和多感官融合，使策略可部署于真实机器人平台。现有方法要么迭代采样慢（扩散/流匹配），要么无法有效处理多模态（传统IMLE），要么缺乏多感官编码。
- **整体含义**：提出PRISM，首次在单遍策略中同时满足上述三个需求，在多个模拟和真实基准上显著提升任务成功率、鲁棒性和样本效率，为实际机器人实时多感官模仿学习提供了可行方案。

## 2. 论文提出的方法论

- **核心思想**：基于一种批全局拒绝采样变体（batch-global rejection-sampling variant）的IMLE，结合时序多感官编码器和线性注意力生成器（Performer架构），形成单遍（single-pass）策略。
- **关键技术细节**：
  - **IMLE变体**：传统IMLE通过从先验中采样候选动作，再拒绝不匹配的样本。PRISM采用批全局拒绝采样，允许单次前向传播生成高质量多模态动作，避免迭代。
  - **时序多感官编码器**：融合RGB、深度、触觉、音频、本体感受等不同传感器的时间序列特征，使用Transformer或类似结构处理多模态时序信息。
  - **Performer生成器**：使用线性注意力机制（FAVOR+）替换标准点积注意力，将计算复杂度从O(N²)降至O(N)，确保实时性。Performer作为动作生成器，在单次前向中直接输出动作分布。
  - **单遍策略**：整个网络从感官输入到动作输出只需一次前向传播，无需迭代采样，实现低延迟推理。
- **公式/算法流程（文字说明）**：
  1. 输入：多感官时序数据（RGB帧序列、深度、触觉、音频、本体感受等）。
  2. 时序多感官编码器：将各模态序列分别编码，并通过跨模态注意力融合为统一特征表示。
  3. 批全局拒绝采样（训练阶段）：在训练时，从先验分布中采样一批候选动作，用IMLE目标（最小化最近邻距离）选择与专家动作一致的动作；采用拒绝采样机制高效训练。
  4. Performer生成器：在推理时，利用线性注意力从编码特征直接生成动作分布（均值或具体动作），无需采样迭代。
  5. 输出：连续动作（例如机器人关节位置或末端速度控制信号），以高控制频率（>30Hz）驱动机器人。

## 3. 实验设计

- **使用的数据集/场景**：
  - **MetaWorld**：多种机器人任务（如推动、拾取等），分Hard/Very-Hard难度。
  - **CALVIN**：长 horizon 桌面操作任务，重点测试样本效率（使用10%数据）。
  - **Robomimic**：标准模仿学习基准（包含多个操作数据集）。
  - **真实硬件套件**：使用Unitree Go2机器人配7自由度机械臂、腕部/肩部RGB、触觉、音频、本体感受传感器，进行真实移动操作任务。
- **Benchmark**：在以上所有场景中评估任务成功率、鲁棒性（如对抗扰动）、样本效率（数据量）、以及动作平滑度（jerk，即加加速度）。
- **对比方法**：
  - 扩散策略（diffusion policies）
  - 流匹配（flow matching）
  - 先前IMLE策略（原始IMLE）
  - 可能还包括其他基线（摘要未列举，但暗示对比了这些）
- **评价指标**：任务成功率（%）、jerk（动作平滑度）、推理速度（毫秒级）、样本效率（不同数据比例下的性能）。

## 4. 资源与算力

- **文中未明确说明使用的GPU型号、数量、训练时长**。仅从论文摘要和元数据看，没有提及具体算力配置。
- **可推测**：由于涉及多感官编码和Performer线性注意力，训练开销适中，可能使用单张或几张高性能GPU（如NVIDIA A100或RTX 4090），但无确凿证据。需注意这是信息缺失，并非论文承诺。

## 5. 实验数量与充分性

- **实验组数**：
  - 在MetaWorld上报告了Hard/Very-Hard分割下的成功率对比（PRISM比扩散和流匹配高5-12%）。
  - 在CALVIN上使用10%数据时，PRISM比IMLE高~10%，比流匹配高~20%，比扩散高~25%；同时jerk降低约20倍。
  - 在 Robomimic 上有结果（具体数值未列出但提到匹配或超越）。
  - 真实硬件显示成功率提高10-25%，且推理速度更快（维持扩散策略的快速推理特性）。
  - 可能还有消融实验（例如移除某种感官模态、改变采样策略等），摘要未详述但合理推测有消融。
- **充分性评价**：
  - 覆盖了3个模拟基准（MultiWorld、CALVIN、Robomimic）和1个真实机器人平台，场景多样。
  - 对比了三种主流生成方法（扩散、流匹配、IMLE），基线全面。
  - 同时报告了成功率、样本效率、动作平滑度多个指标，评估相对全面。
  - **不足**：未见统计显著性检验（如标准差、多次实验的误差线），也未报告每个任务的具体细分结果（如CALVIN长序列完成率）。消融实验细节缺失。实验数量对于发表级别可能足够，但某些结论（如jerk降低20倍）若能给出置信区间更具说服力。

## 6. 论文的主要结论与发现

- PRISM在几乎所有基准上匹配或超越扩散、流匹配和先前IMLE策略，尤其在样本效率上表现突出（10%数据下CALVIN提升显著）。
- 推理速度满足实时控制要求（单遍，线性注意力），而扩散/流匹配需要多次迭代导致延迟较高。
- 多感官融合（RGB+触觉+音频+本体）对真实机器人任务（如移动操作）至关重要，PRISM可有效处理多模态输入。
- 通过批全局拒绝采样变体，IMLE训练稳定且能保留多模态动作覆盖，避免了扩散模型中的模式坍塌问题。

## 7. 优点

- **方法创新**：结合IMLE的批全局拒绝采样与Performer线性注意力，首次实现单遍多感官模仿学习，平衡了多模态、实时性和多感官融合。
- **实验全面**：覆盖模拟和真实场景，对比多个主流基线，结果一致占优或持平。
- **实用性强**：在真实机器人（Unitree Go2）上验证，显示实际部署潜力；推理速度快，满足控制频率。
- **样本效率**：在小数据场景下（10% CALVIN）提升巨大，对机器人数据采集困难场景有实用价值。

## 8. 不足与局限

- **实验细节缺失**：未报告训练与推理的精确延迟、GPU资源使用情况、超参数设置；缺少消融实验（如不同感官组合的重要性、Performer vs 标准Transformer的对比）。
- **统计严谨性不足**：未提供多次实验的标准差或置信区间，无法判断结果的稳定性。
- **多模态输入量的限制**：实验涉及RGB、Depth、触觉、音频、本体，但未分析各模态的贡献度，也未讨论不同模态缺失时的鲁棒性。
- **应用局限性**：主要验证了移动操作（loco-manipulation）任务，对于精细操作（如手术、装配）效果未知；IMLE的拒绝采样在动作空间高维时可能面临采样效率问题（本文通过批全局策略缓解，但未经验证在超大规模动作空间的表现）。
- **对比基线选择**：未对比最新基于流匹配的高效变体（如Rectified Flow、Consistency Models）或隐式扩散模型（如iDDPM），可能低估了其他方法的改进。
- **可复现性**：论文未提供代码开源或详细算法伪代码，依赖公开的IMLE和Performer实现，但PRISM的具体改进（批全局拒绝采样实现细节）未完全公开。

（完）
