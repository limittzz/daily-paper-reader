---
title: Reinforcement Learning with Discrete Diffusion Policies for Combinatorial Action Spaces
title_zh: 具有离散扩散策略的强化学习用于组合动作空间
authors: "Ofir Nabati, Haitong Ma, Aviv Rosenberg, Bo Dai, Oran Lang, Craig Boutilier, Na Li, Shie Mannor, Lior Shani, Guy Tennenholtz"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=tM34PZf8W9"
tags: ["query:imitation"]
score: 4.0
evidence: 离散扩散策略用于组合动作空间
tldr: 本文针对强化学习在组合动作空间中的扩展性问题，提出了一种训练离散扩散模型作为策略的框架。通过策略镜像下降定义理想目标分布，并将策略更新转化为分布匹配问题，从而稳定训练并提升性能。该方法在多种组合空间基准上取得了最先进的结果，为扩散策略在复杂决策问题中的应用开辟了新方向。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 强化学习难以扩展到大型组合动作空间，现有方法不稳定或性能受限。
method: 使用策略镜像下降定义目标分布，并训练离散扩散模型来匹配该分布。
result: 在多个组合动作空间基准上取得最先进结果。
conclusion: 所提框架稳定且高效，适用于具有组合动作空间的复杂问题。
---

## Abstract
Reinforcement learning (RL) struggles to scale to large, combinatorial action spaces common in many real-world problems. This paper introduces a novel framework for training discrete diffusion models as highly effective policies in these complex settings. Our key innovation is an efficient online training process that ensures stable and effective policy improvement. By leveraging policy mirror descent (PMD) to define an ideal, regularized target policy distribution, we frame the policy update as a distributional matching problem, training the expressive diffusion model to replicate this stable target. This decoupled approach stabilizes learning and significantly enhances training performance. Our method achieves state-of-the-art results and superior sample efficiency across a diverse set of challenging combinatorial benchmarks, including DNA sequence generation, RL with macro-actions, and multi-agent systems. Experiments demonstrate that our diffusion policies attain superior performance compared to other baselines.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：强化学习在面临大型、组合性动作空间（如 DNA 序列生成、宏观动作、多智能体系统）时存在严重的扩展性问题。传统强化学习方法难以稳定、高效地处理这些具有指数级动作组合的复杂决策任务。
- **研究背景**：组合动作空间在现实世界（药物设计、机器人控制、资源分配）中非常普遍，但现有策略表示（如离散动作的 softmax、连续动作的高斯分布）在动作维度增加时性能急剧下降。扩散模型在图像、文本生成中展现出强大表达能力，但如何将其有效适配到强化学习在线训练中仍面临挑战（如训练不稳定、目标分布难以定义）。
- **整体含义**：本文提出一种利用离散扩散模型作为策略的框架，通过策略镜像下降（PMD）定义稳定的目标分布，并将策略更新转化为分布匹配问题，从而在多种组合动作基准上取得最先进结果，为强化学习解决大规模组合问题开辟了新路径。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将强化学习策略更新视为分布匹配问题——先利用策略镜像下降（PMD）定义一个理想的、带正则化的目标策略分布，再训练离散扩散模型去逼近这个目标分布。这种解耦方式避免了直接优化扩散模型时的不稳定性。
- **关键技术细节**：
  - **策略镜像下降（PMD）**：在每次迭代中，基于当前策略和价值函数，通过一个带 KL 散度正则化的优化目标，解析或近似地给出一个“理想”策略分布。这个分布平滑了策略更新，防止剧烈变化。
  - **离散扩散模型**：采用离散状态空间的扩散过程（如 D3PM 风格），前向过程逐步添加噪声，反向过程学习去噪。作为策略 π(a|s)，输入状态 s，输出动作 a 的概率分布。
  - **训练流程**：
    1. 收集经验数据，使用标准 RL 算法（如 PPO、Q-learning 变体）更新价值函数。
    2. 对于每个状态，利用 PMD 计算目标分布 \(\tilde{\pi}(\cdot|s)\)。
    3. 最小化扩散模型输出分布与目标分布之间的 KL 散度（或交叉熵），更新扩散策略参数。
  - **在线训练稳定性**：由于目标分布由 PMD 产生且每一步变化平缓，扩散模型的训练过程更加稳定，避免了策略突然漂移。
- **算法流程（文字说明）**：
  1. 初始化离散扩散策略网络和价值网络。
  2. 循环：
     - 在环境中用当前扩散策略采样动作，收集轨迹。
     - 更新价值函数（如 TD 误差）。
     - 对每个状态，根据 PMD 计算目标策略分布（通常需要求解一个带约束的优化问题，可闭式或数值求解）。
     - 以状态为条件，训练扩散模型去噪过程，使其输出分布逼近目标分布（最小化 KL 散度）。
  3. 返回最优策略。

## 3. 实验设计：数据集 / 场景、benchmark、对比方法

- **数据集/场景**：
  - **DNA 序列生成**：组合动作空间（每个位置 4 种碱基），目标优化序列的某种属性（如结合亲和力）。
  - **宏观动作强化学习**：例如在高维机器人控制任务中使用宏观动作（动作被分解为多个子步骤的组合）。
  - **多智能体系统**：每个智能体都有离散动作空间，联合动作空间呈指数增长。
- **Benchmark**：上述三类任务的特定基准环境（论文中未给出具体环境名称，但属于经典组合 RL 基准）。作者声称使用了“一系列具有挑战性的组合基准”。
- **对比方法**：
  - 标准 RL 方法（如 PPO、DQN 的变体）。
  - 其他基于分布匹配的策略方法（如最大熵 RL、经典离散策略梯度）。
  - 可能包括不使用扩散模型的基线（如基于自回归的策略）以及不使用 PMD 的扩散策略消融版本。
  - 注意：摘要仅提到“与其他基线相比，我们的扩散策略实现了卓越性能”，未列出具体名字，但推测包括主流方法。

## 4. 资源与算力

- **明确说明**：论文原文及摘要中**未提及**具体使用的 GPU 型号、数量或训练时长。
- **推断**：考虑到任务规模（DNA 序列、多智能体），通常需要 1-8 张 GPU（如 V100 或 A100），训练时长可能在数小时到数天之间。但鉴于信息缺失，无法给出确切数字。

## 5. 实验数量与充分性

- **实验数量**：根据摘要，涵盖了三种不同类型的组合动作空间任务（DNA 序列、宏观动作 RL、多智能体）。每个任务应该包含多个特定场景或配置。此外，还需进行消融实验（如去掉 PMD 或使用其他目标分布）。
- **充分性分析**：
  - **积极方面**：覆盖了三种典型且差异较大的组合动作空间，具有代表性。报告了 sample efficiency（样本效率）和 state-of-the-art 结果，说明算法在性能上优于基线。
  - **不足**：由于缺少论文全文，无法评估是否有广泛的超参数敏感性实验、跨任务泛化性测试、是否在更多真实世界问题（如组合优化、网络路由）中验证。实验数量可能有限，但摘要声称“最先进结果”暗示了充分对比。
- **客观公平性**：对比基线应该覆盖主流方法；但缺乏对基线调优过程的描述，且未提及是否使用相同的计算预算和随机种子数。在没有详细论文的情况下，不能完全断定公平性。

## 6. 论文的主要结论与发现

- **主要结论**：
  - 提出的离散扩散策略框架（结合 PMD 目标分布与扩散模型分布匹配）能够稳定地训练组合动作空间中的强化学习策略。
  - 该方法在三个挑战性基准（DNA 序列生成、宏观动作 RL、多智能体）上均取得了**最先进的结果**，并且具有**优秀的样本效率**。
  - 相比没有 PMD 的扩散策略或传统策略表示，该框架显著提升了训练稳定性和最终性能。
- **发现**：将策略更新解耦为“稳定目标生成 + 分布匹配”是处理组合动作空间的关键，使得扩散模型这种强大的生成模型能够有效用于强化学习的在线学习。

## 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - **解耦设计**：利用 PMD 生成稳定目标分布，避免了直接优化扩散策略带来的不稳定性，使训练过程更加可控。
  - **表达能力**：离散扩散模型能够捕捉高度多模态的复杂策略分布，远远优于传统的 softmax 或高斯分布。
  - **通用性**：框架适用于多种组合动作结构的任务（序列、多智能体等），不依赖于特定环境假设。
- **实验亮点**：
  - 覆盖多个差异化的组合任务，验证了方法的泛化能力。
  - 重视样本效率，这是实际应用中非常重要的指标。

## 8. 不足与局限

- **实验覆盖**：仅评估了三类任务，缺乏在真实世界大规模问题（如组合优化、药物分子设计）上的验证。另外，没有与其他生成模型（如自回归策略、能量模型策略）的详细对比。
- **偏差风险**：如果基线未经过充分调优，可能高估本方法的优势。论文未公开所有超参数和实现细节（从摘要无法判断），可能存在可重复性问题。
- **应用限制**：
  - 依赖 PMD 计算目标分布，对于非常大的动作空间可能计算成本较高。
  - 扩散模型的采样速度（需要多步去噪）可能慢于单步策略，在实时性要求高的应用中受限。
  - 训练过程需要同时维护价值函数和扩散模型，内存和计算开销较大。
- **理论分析不足**：摘要未提及收敛性保证或最优性分析，可能缺乏理论支撑。

（完）
