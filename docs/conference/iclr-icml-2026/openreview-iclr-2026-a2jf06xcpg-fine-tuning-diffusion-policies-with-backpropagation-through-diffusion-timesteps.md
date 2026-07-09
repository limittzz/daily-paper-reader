---
title: Fine-tuning Diffusion Policies with Backpropagation Through Diffusion Timesteps
title_zh: 通过扩散时间步反向传播微调扩散策略
authors: "Ningyuan Yang, Jiaxuan Gao, Feng Gao, Yi Wu, Chao Yu"
date: 2025-09-16
pdf: "https://openreview.net/pdf?id=A2JF06XcPG"
tags: ["query:imitation"]
score: 8.0
evidence: 通过扩散时间步反向传播微调扩散策略以改进决策
tldr: 扩散策略从演示数据学习，但数据覆盖不足时可能产生次优轨迹。现有RL微调方法受限于动作似然估计的困难。本文提出通过扩散时间步进行反向传播的微调方法，直接优化策略目标，避免近似。在机器人、游戏等任务上提升了策略性能，且训练更稳定。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 演示数据有限导致扩散策略生成次优轨迹，现有RL微调方法因似然估计问题效果差。
method: 对扩散过程进行时间步反向传播，直接优化策略价值目标。
result: 在多种决策任务上策略性能显著提升，且训练稳定性优于现有方法。
conclusion: 时间步反向传播为扩散策略的RL微调提供了有效途径。
---

## Abstract
Diffusion policies, widely adopted in decision-making scenarios such as robotics, gaming and autonomous driving, are capable of learning diverse skills from demonstration data due to their high representation power. However, the sub-optimal and limited coverage of demonstration data could lead to diffusion policies that generate sub-optimal trajectories and even catastrophic failures. While reinforcement learning (RL)-based fine-tuning has emerged as a promising solution to address these limitations, existing approaches struggle to effectively adapt Proximal Policy Optimization (PPO) to diffusion models. This challenge stems from the computational intractability of action likelihood estimation during the denoising process, which leads to complicated optimization objectives. In our experiments starting from randomly initialized policies, we find that online tuning of Diffusion Policies demonstrates much lower sample efficiency compared to directly applying PPO on MLP policies (MLP+PPO). To address these challenges, we introduce NCDPO, a novel framework that reformulates Diffusion Policy as a noise-conditioned deterministic policy. By treating each denoising step as a differentiable transformation conditioned on pre-sampled noise, NCDPO enables tractable likelihood evaluation and gradient backpropagation through all diffusion timesteps. This formulation enables direct optimization over the final denoised interactive actions without increasing MDP lengths. Our experiments demonstrate that NCDPO achieves sample efficiency comparable to MLP+PPO when training from scratch, outperforming existing methods in both sample efficiency and final performance across diverse benchmarks, including continuous robot control (with both state and vision inputs) and multi-agent coordination tasks. Furthermore, our experimental results show that our method is robust to the number denoising timesteps.

---

## 论文详细总结（自动生成）

### 论文的中文详细总结

#### 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散策略（Diffusion Policies）凭借其高表达能力，在机器人、游戏、自动驾驶等决策场景中广泛用于从演示数据中学习多样化技能。然而，演示数据通常覆盖不足且包含次优轨迹，导致扩散策略生成次优甚至灾难性失败的轨迹。
- **解决思路**：强化学习（RL）微调有望缓解数据局限性，但现有方法（如将PPO适配到扩散模型）因去噪过程中动作似然估计难以计算而面临优化目标复杂、样本效率低下的问题。作者实验发现，从随机初始化策略开始在线调优扩散策略的样本效率远低于直接将PPO应用于MLP策略（MLP+PPO）。
- **整体含义**：本文提出一种新框架NCDPO，旨在解决扩散策略RL微调中的似然估计难题，实现与MLP+PPO相当的样本效率。

#### 2. 论文提出的方法论
- **核心思想**：将扩散策略重新表述为**噪声条件确定性策略（Noise-Conditioned Deterministic Policy）**，将每个去噪步骤视为基于预采样噪声的可微变换，从而实现可处理的似然评估以及通过所有扩散时间步的梯度反向传播。
- **关键技术细节**：
  - 扩散过程通常包含多个去噪时间步；NCDPO将每个时间步的变换视为确定性的（给定噪声），因此整个去噪链可微。
  - 通过可微去噪链，可以直接对最终去噪后的交互动作进行优化，而无需增加马尔可夫决策过程（MDP）的时间步长度。
  - 这种方法避免了现有方法中因近似动作似然而引入的复杂损失函数，使得直接使用任意RL目标（如策略梯度）成为可能。
- **公式/算法流程说明**（文字描述）：
  - 首先，从噪声分布中采样一个初始噪声，然后通过一系列可微去噪步骤逐步生成动作。
  - 每个去噪步骤由参数化的神经网络执行，其输入包括当前噪声图像、扩散时间步索引以及可选的观测信息。
  - 由于整个过程可微，可以将RL的回报（reward）视为损失函数，通过反向传播梯度更新所有去噪网络参数。
  - 具体实现中，作者可能采用类似确定性扩散模型（如DDIM）的逆过程，以确保可微性。

#### 3. 实验设计
- **使用的数据集/场景**：
  - 连续机器人控制任务（包括状态输入和视觉输入）
  - 多智能体协作任务
- **Benchmark**：论文未明确列出所有基准环境名称，但提及“diverse benchmarks”，包括连续控制（state & vision）以及多智能体协调。
- **对比方法**：
  - 直接将PPO应用于MLP策略（MLP+PPO）
  - 现有的扩散策略RL微调方法（未具体命名）
  - 可能还包括从头在线训练的扩散策略基线

#### 4. 资源与算力
- **文中未明确说明**：提供的文本中未提及GPU型号、数量、训练时长等具体算力信息。仅在示例中提及“在线调优扩散策略”的样本效率比较，但无硬件细节。因此无法总结具体算力开销。

#### 5. 实验数量与充分性
- **实验数量**：涵盖三个大类场景（连续控制状态输入、视觉输入、多智能体），每个场景下应有多个任务（具体数量未给出）。可能包含消融实验（如对去噪步数的鲁棒性）。
- **充分性与客观性**：
  - 实验覆盖了不同输入模态（状态、视觉）和任务类型（单智能体、多智能体），比较全面。
  - 对比了强基线（MLP+PPO）和现有扩散微调方法，结果展示了NCDPO在样本效率和最终性能上的优势。
  - 特别测试了对去噪时间步数的鲁棒性，验证方法稳健。
  - 但未给出具体实验次数、随机种子数等统计细节，无法完全判断结果的可靠性。从论文被ICLR-2026接收（实际为Rejected？元数据标注为Rejected-Public，但score 8.0）看，实验设计应较为扎实。

#### 6. 论文的主要结论与发现
- NCDPO（通过扩散时间步反向传播）能够实现与MLP+PPO相当的样本效率，并且在从头训练时优于现有扩散策略RL微调方法。
- 在连续机器人控制（状态和视觉输入）和多智能体协作任务中，NCDPO在样本效率和最终性能上均优于现有方法。
- 该方法对去噪时间步数具有鲁棒性，即改变步数不会显著影响性能。

#### 7. 优点（方法或实验设计上的亮点）
- **方法上**：巧妙地将扩散策略重构为噪声条件下的确定性策略，使整个去噪链可微，从而绕开动作似然估计的难题，可直接利用标准RL优化器。
- **实验上**：
  - 同时对比了强基线（MLP+PPO）和现有扩散RL方法，突出了样本效率的优势。
  - 覆盖了高维视觉输入和多智能体场景，验证了通用性。
  - 考察了对扩散步数的鲁棒性，增强了方法的实用性。

#### 8. 不足与局限
- **实验覆盖**：未提及真实机器人实验或复杂3D场景（如Habitat、Isaac Gym等），仅限于模拟环境。
- **偏差风险**：论文未披露随机种子数量、方差统计，可能影响结果的可重复性。
- **应用限制**：方法要求扩散过程完全可微，对于使用随机采样（如DDPM的随机噪声注入）的扩散模型可能需要修改；且可微去噪链的梯度计算可能带来额外内存开销（文中未讨论）。
- **算力信息缺失**：不利于他人复现或评估计算成本。
- **对比方法选择**：仅对比了“现有方法”，未给出具体名称和实现细节，难以判断是否包括了最先进的扩散微调技术。

（完）
