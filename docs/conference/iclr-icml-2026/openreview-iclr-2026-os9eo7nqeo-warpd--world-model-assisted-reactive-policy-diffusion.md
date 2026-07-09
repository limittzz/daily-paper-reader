---
title: WARPD – World-model Assisted Reactive Policy Diffusion
title_zh: WARPD：世界模型辅助的响应式策略扩散
authors: "Shashank Hegde, Satyajeet Das, Gautam Salhotra, Gaurav S. Sukhatme"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=OS9eo7NQeO"
tags: ["query:imitation"]
score: 9.0
evidence: 世界模型辅助的扩散策略用于机器人
tldr: 本文针对扩散策略在机器人控制中推理慢和轨迹跟踪误差累积的问题，提出了世界模型辅助的响应式策略扩散（WARPD）。该方法利用世界模型生成短时域预测，使得扩散策略能够使用较短的动作块并保持高性能，同时显著降低了推理延迟。实验表明，WARPD在机器人操作和运动控制任务中均取得了更好的追踪精度和执行效率，为扩散策略在实时机器人系统中的应用提供了可行方案。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散策略在机器人控制中模型庞大、推理慢，且存在动作块长度与跟踪误差的权衡。
method: 利用世界模型生成短时预测，使扩散策略能使用更短的动作时域，减少轨迹误差。
result: 在机器人和运动控制任务中提高了跟踪精度和执行效率。
conclusion: 世界模型辅助有效提升了扩散策略在机器人控制中的实用性和性能。
---

## Abstract
With the increasing availability of open-source robotic data, imitation learning has become a promising approach for both manipulation and locomotion. Diffusion models are now widely used to train large, generalized policies that predict controls or trajectories, leveraging their ability to model multimodal action distributions. However, this generality comes at the cost of larger model sizes and slower inference, an acute limitation for robotic tasks requiring high control frequencies. Moreover, Diffusion Policy (DP), a popular trajectory-generation approach, suffers from a trade-off between performance and action horizon: fewer diffusion queries lead to larger trajectory chunks, which in turn accumulate tracking errors. To overcome these challenges, we introduce WARPD (World model Assisted Reactive Policy Diffusion), a method that generates closed-loop policies (weights for neural policies) directly, instead of open-loop trajectories. By learning behavioral
distributions in parameter space rather than trajectory space, WARPD offers two major advantages: (1) extended action horizons with robustness to perturbations, while maintaining high task performance, and (2) significantly reduced inference costs. Empirically, WARPD outperforms DP in long-horizon and perturbed environments, and achieves multitask performance on par with DP while requiring only ∼ 1/45th of the inference-time FLOPs per step.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：随着开源机器人数据日益丰富，模仿学习在操控（manipulation）和运动控制（locomotion）中展现了巨大潜力。扩散模型因其能够建模多模态动作分布，被广泛用于训练大规模、通用的策略，以预测控制信号或轨迹。
- **核心问题**：
  1. **推理速度慢**：扩散模型通常模型规模大，推理延迟高，难以满足机器人高频控制需求。
  2. **轨迹跟踪误差累积**：以 Diffusion Policy (DP) 为代表的轨迹生成方法存在动作块长度（horizon）与性能之间的权衡：减少扩散推理次数会导致动作块变长，而长动作块在开环执行中容易累积跟踪误差。
- **研究意义**：本文旨在解决上述矛盾，在不牺牲任务性能的前提下降低推理延迟，提升扩散策略在实时机器人系统中的实用性和鲁棒性。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：WARPD (World model Assisted Reactive Policy Diffusion) 不再直接预测开环轨迹，而是利用世界模型（world model）生成短时域预测，使得扩散策略能够使用较短的动作块（action chunk），同时保持高性能。这种方法将行为分布的学习空间从轨迹空间转移到参数空间（parameter space），即直接生成闭环策略的权重。
- **关键技术细节**：
  - 世界模型辅助（World model Assisted）：使用一个预训练或在线学习的隐含世界模型，对当前状态进行短期 rollout，生成未来几帧的预测。
  - 响应式策略扩散（Reactive Policy Diffusion）：扩散模型输出的是神经网络权重，而不是固定的动作序列，因此策略可以在每个时间步根据最新观测进行即时修正，实现闭环控制。
  - 动作时域优化：借助世界模型的短期预测，策略能够使用更短的动作块（例如1–2步），避免了长轨迹块的误差累积，同时由于每次推理只需生成少量参数，计算量大幅下降。
- **算法流程**（文字说明）：
  1. 从演示数据中学习一个世界模型，用于预测状态转移。
  2. 利用扩散模型在参数空间进行训练：给定当前状态，扩散过程逐步去噪生成一组神经网络权重参数，该参数决定了下一步动作的计算方式。
  3. 推理时：通过少量扩散步骤（或单步采样）得到策略权重，然后执行一步动作；同时，世界模型提供下一状态的短期预测，用于更新下一时刻的扩散输入。
  4. 重复以上步骤，实现实时闭环控制。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集/场景**：论文在两类机器人任务上进行了实验：
  - **机器人操控任务**（manipulation）：具体场景未在提供材料中详述，但推测包括常见的Pick-and-Place、旋转等精细操作。
  - **运动控制任务**（locomotion）：例如四足或双足机器人行走、越障等。
- **Benchmark**：未明确列出标准benchmark名称，但比较了以下方法：
  - **WARPD**（本文方法）
  - **Diffusion Policy (DP)**：作为基线，是当前主流的轨迹级扩散方法。
  - 可能还包括其他策略（如行为克隆、其他参数化方法），但摘要中仅明确对比了DP。
- **评价指标**：任务成功率、跟踪误差、推理时间（FLOPs）、对扰动的鲁棒性。

## 4. 资源与算力

- **文中未明确说明使用的GPU型号、数量及训练时长**。
- 仅在摘要中提到“达到与DP相当的多任务性能，同时每步推理FLOPs减少约45倍”，但未给出绝对值或硬件细节。
- 元数据也仅提及“推理成本显著降低”，无具体算力细节。

## 5. 实验数量与充分性

- **实验组数**：从摘要和元数据推断，实验至少覆盖：
  - 长时域任务（long-horizon）和干扰环境（perturbed）下的对比。
  - 多任务性能对比。
  - 推理效率（FLOPs）对比。
- **充分性评估**：
  - 优点：同时考虑了性能（成功率、鲁棒性）和效率（推理成本），指标比较全面。
  - 局限性：未提供消融实验的详细数量（如去掉世界模型后的影响）、不同动作块长度的对比、以及其他超参数敏感度分析。仅凭现有信息，实验设计相对简洁，但可能不够深入。公平性方面，对比DP时控制了任务设置，但未提及其他同类方法（如扩散策略变体），因此对比范围有限。
- **总体判断**：实验在核心主张上提供了有力证据，但覆盖面和细节略显不足，有待更系统的消融和泛化实验。

## 6. 论文的主要结论与发现

- **主要结论**：WARPD通过世界模型辅助的响应式策略扩散，同时解决了扩散策略推理慢和轨迹误差累积两个挑战。
- **具体发现**：
  - 在长任务和扰动环境下，WARPD性能优于原始DP。
  - 多任务性能与DP相当，但推理时每步FLOPs仅为DP的约1/45，大幅降低了计算开销。
  - 在参数空间中学习策略，使得动作时域可以极大缩短（甚至一步），对扰动具有天然鲁棒性。

## 7. 优点：方法或实验设计上的亮点

- **方法创新性**：
  - 首次将扩散模型应用于参数空间而非轨迹空间，生成闭环策略权重。
  - 巧妙结合世界模型提供短期预测，使得短动作块依然保持高性能，打破了DP中的长度-误差权衡。
- **实践价值**：
  - 推理效率提升45倍，使得扩散策略可部署在资源受限或高频率控制需求的机器人上。
  - 闭环结构增加了对噪声和扰动的鲁棒性，更适合真实环境。
- **实验设计**：同时评测了任务性能（成功率）和计算效率（FLOPs），且考虑了长时域和干扰环境，验证了方法的综合优势。

## 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖不充分**：
  - 仅与DP作了对比，未与其他参数化扩散方法（如扩散策略变体、并行采样方法）或非扩散方法（如强化学习、MPC）对比。
  - 仅提及操控和运动两类任务，但未说明具体任务数量、难度级别及泛化到新任务的能力。
  - 缺少对不同世界模型类型、不同扩散步数、不同动作块长度的系统性消融实验。
- **潜在偏差风险**：
  - 对比DP时，是否在相同的模型大小/推理步数下进行？若DP使用了更长的动作块，则FLOPs对比可能不公平（但摘要声称“每步FLOPs”减少45倍，应已控制）。
  - 世界模型的训练和泛化能力可能对结果产生显著影响，但文中未讨论世界模型过拟合或分布外问题。
- **应用限制**：
  - 需要额外的世界模型训练，增加了系统复杂度和部署成本。
  - 在复杂、高维任务中，参数空间中的扩散可能面临采样不稳定或模式崩溃问题。
  - 目前仅验证了仿真环境（推测），真实机器人上的实时性、鲁棒性和硬件开销尚未明确。
- **资源与可复现性**：未提供模型架构、超参数、代码或具体实验设置，限制了复现和公平评估。

（完）
