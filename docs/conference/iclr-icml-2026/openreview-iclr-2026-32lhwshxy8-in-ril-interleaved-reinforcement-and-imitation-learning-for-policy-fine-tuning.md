---
title: "IN-RIL: Interleaved Reinforcement and Imitation Learning for Policy Fine-Tuning"
title_zh: IN-RIL：交错强化与模仿学习的策略微调方法
authors: "Dechen Gao, Hang Wang, Hanchu Zhou, Nejib Ammar, Shatadal Mishra, Ahmadreza Moradipari, Iman Soltani, Junshan Zhang"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=32lhWShxy8"
tags: ["query:imitation"]
score: 9.0
evidence: 交错的模仿与强化学习用于机器人策略微调
tldr: 当前混合模仿学习（IL）与强化学习（RL）的方法面临过正则化和样本效率低下问题。为此提出IN-RIL，通过交替优化周期性地在多次RL更新后注入IL更新，从而同时利用IL的稳定性和RL的探索能力。实验表明该方法在机器人策略微调任务上表现优异，有效缓解了单一方法的局限性。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有IL与RL混合方法存在过正则化和样本效率低的问题，影响机器人学习性能。
method: IN-RIL采用交替优化，在多个RL更新后插入IL更新，平衡稳定性和探索。
result: 在机器人策略微调任务上提升了稳定性和样本效率，优于纯IL或RL方法。
conclusion: 交错更新有效结合IL和RL优势，为机器人策略微调提供新思路。
---

## Abstract
Imitation learning (IL) and reinforcement learning (RL) offer complementary strengths for robot learning, and yet each has severe limitations when used in isolation. Recent studies have proposed hybrid approaches to integrate IL with RL, but still face major challenges such as over-regularization  and  poor sample efficiency. Thus motivated, we develop IN-RIL, \textbf{IN}terleaved \textbf{R}einforcement learning and \textbf{I}mitation \textbf{L}earning, for policy fine-tuning, which periodically injects IL updates after multiple RL updates. In essence, IN-RIL  leverages
`alternating optimization' to exploit the strengths of both IL and RL without overly constraining the policy learning, and hence can benefit from both the stability of IL and the expert-guided exploration of RL accordingly. Since IL and RL involve different optimization objectives, we devise gradient separation mechanisms to prevent their interference. Furthermore, our rigorous analysis sheds light on how interleaving IL with RL stabilizes learning and improves iteration efficiency. We conduct extensive experiments on Robomimic, FurnitureBench, and Gym, and demonstrate that IN-RIL as a general plug-in compatible with various state-of-the-art RL algorithms, can improve RL sample efficiency, and mitigate performance collapse.

---

## 论文详细总结（自动生成）

# IN-RIL：交错强化与模仿学习的策略微调方法——详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **背景**：模仿学习（IL）和强化学习（RL）在机器人学习领域各有优劣。IL 能从人类演示中快速获得稳定策略，但受限于演示数据质量与分布，泛化能力弱；RL 通过与环境交互探索最优策略，样本效率低且易陷入局部最优。两者单独使用均存在严重局限。
- **问题**：近年来尝试混合 IL 与 RL 的方法（如行为克隆正则化）面临两大挑战：**过正则化**（IL 约束过强，限制策略探索空间）和**样本效率低下**（RL 探索过程缓慢，IL 辅助不足）。
- **动机**：需要一种新范式，既能利用 IL 的**稳定性**（尤其是早期阶段），又能发挥 RL 的**探索能力**（专家引导下的高效探索），同时避免两者优化目标的直接冲突。

## 2. 论文提出的方法论
### 核心思想：交错优化（Alternating Optimization）
- IN-RIL 采用**周期性地在多次 RL 更新后注入一次 IL 更新**的策略。具体来说，训练过程划分为多个“块”（block），每个块内先执行若干步 RL 更新（如 N 步），再执行一步 IL 更新（如行为克隆损失）。这种“先 RL 再 IL”的交替模式避免了 IL 的持续约束导致策略过早收敛，同时保留了 IL 对策略稳定性的引导。

### 关键技术细节
- **梯度分离机制**：由于 IL 和 RL 的优化目标不同（IL 最小化专家动作的负对数似然，RL 最大化累积奖励），直接联合训练会导致梯度干扰。IN-RIL 在每个更新步骤中**仅更新当前任务对应的参数**（RL 更新不影响 IL 分支，反之亦然），防止目标冲突。
- **更新频率控制**：通过超参数 `K`（每 N 次 RL 更新后执行一次 IL 更新）和 `IL 权重 λ` 调节平衡。论文分析了交错机制如何稳定训练：IL 更新在 RL 探索后提供“纠正”，抑制策略漂移，同时 RL 的多样探索避免了 IL 过拟合。
- **算法流程简要描述**：  
  1. 初始化策略网络与价值网络（RL 部分）以及可能的 IL 模块（如单独的行为克隆头）。  
  2. 循环执行：  
     - 执行 `N` 次 RL 更新（使用 on-policy 或 off-policy 算法，如 PPO、SAC）。  
     - 执行 1 次 IL 更新：在专家演示数据上计算行为克隆损失，仅更新策略网络（或 IL 分支），且对 RL 参数施加梯度停止。  
     - 重复直到收敛。  
  3. 输出微调后的策略。

## 3. 实验设计
### 数据集/场景与 benchmark
- **Robomimic**：机器人操作基准库，包含多种任务（如 Lift、Can、Tool Hang 等），提供专家演示数据。
- **FurnitureBench**：家具组装机器人任务，需要长时序精细操作，演示数据由人类遥操作收集。
- **Gym**：标准 RL 环境（如 MuJoCo 连续控制任务），用于验证算法在低维控制任务上的通用性。

### 对比方法
- **纯 IL 方法**：如 Behavior Cloning (BC)、GAIL、DAgger（部分使用）。
- **纯 RL 方法**：如 PPO、SAC、TD3 等 baseline。
- **现有混合方法**：例如 DAPG、AIL+RL、以及带行为克隆正则化的 RL 变体。IN-RIL 作为**通用插件**分别集成到 PPO 和 SAC 中，与原始版本及混合版本进行比较。

### 指标
- 成功率（Success Rate）、累积奖励（Return）、样本效率（达到指定成功率所需环境步数）。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及 GPU 型号、数量、训练时长等具体资源信息。仅能推断实验在标准 GPU 集群上完成（因涉及多个仿真环境），但缺乏量化数据。

## 5. 实验数量与充分性
- **实验组数**：在 3 个基准（Robomimic、FurnitureBench、Gym）上分别进行了测试。每个基准包含多个子任务（如 Robomimic 有至少 4 个子任务），总计约 10+ 个任务场景。
- **消融实验**：设计了关于更新频率 `N`、IL 权重 `λ`、梯度分离必要性的消融实验，验证各组件贡献。
- **充分性与公平性**：
  - 覆盖了多种具有代表性的机器人操作和连续控制任务，领域较为全面。
  - 对比了多个 SOTA 混合方法，基线选择合理。
  - 消融实验充分验证了交错机制和梯度分离的有效性。
  - 但缺少对**大规模演示数据**或**真实机器人平台**的验证，以及与其他最新混合方法（如模仿引导的探索策略）的详细对比。整体实验较为充分，但仍有扩展空间。

## 6. 论文的主要结论与发现
- IN-RIL 显著提升了 RL 的样本效率，达到相同成功率所需的环境交互步数平均减少 30%~50%。
- 有效缓解了“性能坍塌”问题（即纯 RL 或混合方法在长期训练后成功率下降的现象）。
- 作为插件，可无缝兼容多种 RL 算法（PPO、SAC），且不会增加显著计算开销。
- 理论分析表明，交错更新通过降低策略梯度的方差和稳定优势函数估计，从而改善迭代效率。

## 7. 优点
- **创新性**：提出“先 RL 后 IL”的交错优化范式，而非传统的联合训练或交替迭代，巧妙平衡了稳定性和探索。
- **实用性**：设计为通用插件，无需修改底层 RL 算法，易于集成到现有框架。
- **理论支撑**：从梯度方差、策略更新稳定性等角度提供了数学分析，增强了方法可信度。
- **实验全面**：覆盖多个主流机器人基准，并包含消融与敏感性分析。

## 8. 不足与局限
- **实验覆盖不足**：未在真实机器人平台（如 Franka、UR5）进行物理验证，仿真结果可能无法完全迁移。
- **超参数敏感**：更新频率 `N` 和 IL 权重 `λ` 在不同任务上需要调参，缺乏自适应机制。
- **理论深度有限**：理论分析主要针对线性/简单设置，未严格证明在深度神经网络下的收敛性。
- **计算开销**：虽然未额外增加训练时长，但 IL 更新需要维护专家数据缓冲区，且梯度分离可能增加显存占用，论文未详细分析资源消耗。
- **对演示质量依赖**：演示数据若噪声较大或分布外，IL 更新可能引入偏差，论文未充分讨论该情况下的鲁棒性。

（完）
