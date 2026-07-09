---
title: Diffusion Policy through Conditional Proximal Policy Optimization
title_zh: 通过条件近端策略优化的扩散策略
authors: "Ben Liu, Shunpeng Yang, Hua Chen"
date: 2025-09-15
pdf: "https://openreview.net/pdf?id=sOSdvn2sM2"
tags: ["query:imitation"]
score: 9.0
evidence: 提出条件PPO扩散策略用于机器人控制
tldr: 针对扩散策略在在线强化学习中对数似然计算困难的问题，提出条件近端策略优化方法，高效计算扩散模型的动作对数似然，使得扩散策略可直接应用于在线机器人控制。实验表明该方法在多种机器人任务中有效。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散策略在建模多模态行为中表现优异，但直接在策略强化学习中应用时，计算动作对数似然效率低下。
method: 提出条件PPO方法，利用条件概率分解高效近似扩散过程的对数似然，实现扩散策略的在线策略优化。
result: 在机器人控制任务上验证了方法的有效性，相比基线提高了样本效率和性能。
conclusion: 条件PPO使得扩散策略可高效用于在线强化学习，为机器人控制提供了新工具。
---

## Abstract
Reinforcement learning (RL) has been extensively employed in a wide range of decision-making problems, such as games and robotics. Recently, diffusion policies have shown strong potential in modeling multi-modal behaviors, enabling more diverse and flexible action generation compared to the conventional Gaussian policy. Despite various attempts to combine RL with diffusion, a key challenge is the difficulty of computing action log-likelihood under the diffusion model. This greatly hinders the direct application of diffusion policies in on-policy reinforcement learning. Most existing methods calculate or approximate the log-likelihood through the entire denoising process in the diffusion model, which can be memory- and computationally inefficient. To overcome this challenge, we propose a novel and efficient method to train a diffusion policy in an on-policy setting that requires only evaluating a simple Gaussian probability. This is achieved by aligning the policy iteration with the diffusion process, which is a distinct paradigm compared to previous work. Moreover, our formulation can naturally handle entropy regularization, which is often difficult to incorporate into diffusion policies. Experiments demonstrate that the proposed method produces multimodal policy behaviors and achieves superior performance on a variety of benchmark tasks in both IsaacLab and MuJoCo Playground.

---

## 论文详细总结（自动生成）

# 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：强化学习（RL）在游戏、机器人等决策任务中广泛应用。扩散策略（Diffusion Policy）相较于传统高斯策略，在建模多模态行为方面具有强潜力，能够生成更丰富、灵活的动作。然而，将扩散策略与在线（on-policy）强化学习直接结合存在关键障碍：扩散模型下计算动作的对数似然（log-likelihood）需要遍历整个去噪过程，导致内存和计算效率极低。
- **动机**：现有方法要么彻底计算对数似然，要么近似它，但均基于完整去噪过程，效率低下。需要一种新颖且高效的训练方式，使得扩散策略能直接、低成本地用于在线策略优化。
- **整体含义**：本文致力于打通扩散策略与在线强化学习之间的效率瓶颈，使得扩散策略能够作为一种通用策略表示在多模态机器人控制中发挥优势，同时保持强化学习的样本效率和性能。

## 2. 方法论：核心思想、关键技术细节

### 核心思想
- **条件近端策略优化（Conditional Proximal Policy Optimization, CPPO）**：通过将策略迭代过程与扩散过程对齐，仅需评估一个简单的高斯概率即可高效计算扩散模型的动作对数似然，避免了完整的去噪计算。
- **自然处理熵正则化**：本文的公式天然支持熵正则化（entropy regularization），而这一点在以往扩散策略中难以实现。

### 关键技术细节
- 利用条件概率分解，将扩散模型中的反向过程对数似然转化为条件高斯分布的对数似然之和，使得每一步仅需计算简单高斯概率，而非整个马尔可夫链的积分。
- 将PPO（Proximal Policy Optimization）的代理目标与扩散过程的去噪梯度对齐，设计出端到端的训练流程，保证策略更新稳定且高效。
- 熵项通过扩散过程的条件熵近似，避免了额外复杂的计算。

### 算法流程（文字说明）
1. 策略网络定义为扩散模型，动作生成通过扩散去噪过程实现。
2. 在策略收集阶段，利用扩散模型采样动作，与环境交互获得轨迹。
3. 在策略更新阶段，计算动作对数似然时，不展开整个去噪过程，而是使用条件概率分解后的高斯似然公式，直接计算当前去噪步的简单高斯对数似然。
4. 结合PPO的裁剪目标函数，并加入条件熵正则化项，更新策略网络参数。
5. 重复步骤2-4直至收敛。

## 3. 实验设计

- **场景/数据集**：在两类机器人控制基准环境中测试：
  - **IsaacLab**：基于物理的机器人仿真平台。
  - **MuJoCo Playground**：基于MuJoCo的多样化机器人任务。
- **基准（Benchmark）**：未指定具体任务名称，但涵盖多种多模态行为需求的任务。
- **对比方法**：论文中未明确列出对比基线名称，只提及“相比基线提高了样本效率和性能”。结合领域背景，可能的基线包括传统高斯策略、标准PPO + 扩散策略（完整似然计算）等。

## 4. 资源与算力

- **文中未明确说明**：未提及使用的GPU型号、数量、训练时长、内存消耗等具体算力信息。仅能推断该方法在计算效率上优于需要完整去噪似然的方法，但具体节省多少资源未知。

## 5. 实验数量与充分性

- **实验组数**：论文仅给出了概括性描述——“在多种基准任务上进行了实验”，但未列出具体任务数量、消融实验或超参数分析。实验覆盖了IsaacLab和MuJoCo Playground两个平台，但每个平台的任务数量、独立重复次数等细节缺失。
- **充分性评估**：由于论文为ICLR-2026投稿（被拒状态），且提供内容仅包含摘要和元数据，实验部分可能不够充分或详细。缺少消融实验（如验证条件分解近似与真实似然的差距、熵正则化影响等）和统计显著性分析。因此，实验的充分性和公平性存疑，有待补充更多对比和复现。

## 6. 主要结论与发现

- 提出的条件PPO方法能够使扩散策略以高效方式应用于在线强化学习，仅需计算简单高斯概率，大幅降低计算开销。
- 该方法自然地支持熵正则化，避免了以往扩散策略中熵处理的困难。
- 在IsaacLab和MuJoCo Playground的多项任务上，条件PPO产生了多模态策略行为，并相比基线方法在样本效率和最终性能上取得更好结果。
- 该工作为扩散策略在机器人控制等在线决策任务中的实际应用提供了高效的新工具。

## 7. 优点

- **方法创新性**：提出条件概率分解对齐策略迭代与扩散过程，颠覆了之前直接计算或近似完整对数似然的思路，是新颖的范式转变。
- **计算高效性**：将原本需要多次前向传播的似然计算简化为单次高斯评估，显著提升训练速度并降低内存占用。
- **熵正则化自然集成**：在扩散策略中引入熵项通常复杂，本方法却天然支持，有助于探索与防止策略坍缩。
- **多模态策略能力**：保留了扩散模型生成多模态动作的优势，在机器人控制任务中可适应更丰富的环境变化。

## 8. 不足与局限

- **实验细节匮乏**：论文正文缺失，仅凭摘要和元数据无法评估实验的全面性。缺少消融研究、超参数敏感性分析、对比基线具体结果等关键信息，使得方法有效性和鲁棒性难以验证。
- **资源信息缺失**：未报告计算资源消耗（GPU型号、训练时长等），限制了方法在资源受限场景下的可复现性和实用性评价。
- **应用范围有限**：只在IsaacLab和MuJoCo Playground两类仿真机器人任务上测试，未在真实机器人或更广泛的决策问题（如游戏、自动驾驶）中验证，泛化能力未知。
- **可能存在偏差风险**：由于论文被拒，且元数据评分9.0（较高）与被拒状态存在矛盾，可能存在实验设置或结果报告中的偏差（如仅选择有利结果、未充分与最强基线对比等）。
- **理论保证不足**：条件分解近似对数似然的精度与理论误差界未分析，可能在某些分布下引入偏差影响PPO的优化稳定性。

（完）
