---
title: "NavOL: Navigation Policy with Online Imitation Learning"
title_zh: NavOL：在线模仿学习的导航策略
authors: "Xiaofei Wei, Chun Gu, Li Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/c0087257172019e2ed8e011dffcb3ec2d79bcca9.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 使用扩散策略的在线模仿学习导航
tldr: 针对离线模仿学习分布偏移和强化学习低效的问题，本文提出NavOL，一种在线模仿学习范式，基于预训练导航扩散策略在模拟器中交互并收集专家轨迹更新策略。实验显示该方法大幅提升了导航策略的鲁棒性和成功率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离线模仿学习受分布偏移影响，强化学习需要奖励工程。
method: 提出在线模仿学习范式，利用预训练导航扩散策略在模拟器中交互并更新。
result: 相比基线显著提升了导航性能。
conclusion: 在线模仿学习结合扩散策略为导航提供了稳健的解决方案。
---

## Abstract
Learning robust navigation policies remains a core challenge in robotics. Offline imitation learning suffers from distribution shift and compounding errors at rollout, while reinforcement learning requires reward engineering and learns inefficiently. In this paper, we propose NavOL, an online imitation learning paradigm that interacts with a simulator and updates itself using expert demonstrations gathered online. Built upon a pretrained navigation diffusion policy that maps local observations to future waypoints, NavOL trains in a rollout–update loop: during rollout, the policy acts in the simulator and queries a global planner which has privileged access to the global environment for the optimal path segment as ground truth trajectory labels; during update, the policy is trained on the online collected observation–trajectory pairs. This online imitation loop removes the need for reward design, improves learning efficiency, and mitigates distribution shift by training on the policy’s own explored rollouts. Built on IsaacLab with fast, high-fidelity parallel rendering and domain randomization of camera pose and start-goal pairs, our system scales across 50 scenes on 8 RTX 4090 GPUs, collecting over 2,000 new trajectories per hour, each averaging more than 400 steps. We also introduce an indoor visual navigation benchmark with predefined start and goal positions for zero-shot generalization. Extensive evaluations on simulation benchmarks, including the NavDP benchmark and our proposed benchmark, as well as carefully designed real-world experiments, demonstrate the effectiveness of NavOL, showing consistent performance gains in online imitation learning.

---

## 论文详细总结（自动生成）

# 论文《NavOL: 在线模仿学习的导航策略》详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：导航策略是机器人领域的核心挑战。现有学习方法存在两大瓶颈：
  - **离线模仿学习**（Offline Imitation Learning）：在 rollout 时受分布偏移（distribution shift）和复合误差（compounding errors）影响，策略在陌生环境下性能急剧下降。
  - **强化学习**（Reinforcement Learning, RL）：需要精心设计奖励函数（reward engineering），且学习效率低下，难以在实际中高效部署。
- **整体含义**：本文旨在提出一种无需奖励设计、能缓解分布偏移且学习效率更高的导航策略学习方法，通过在线模仿学习范式，利用预训练的扩散策略在模拟器中交互并自我更新，实现鲁棒的视觉导航。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：采用**在线模仿学习范式（Online Imitation Learning）**，构建一个“rollout–update”循环：
  1. **Rollout阶段**：预训练的导航扩散策略在模拟器中执行动作（基于局部观测预测未来路径点），同时查询一个具有全局环境特权信息的全局规划器，以获取最优路径段作为**真实轨迹标签**（ground truth trajectory labels）。
  2. **Update阶段**：将在线收集的（观测‑轨迹）对用于训练策略，从而修正策略自身的探索轨迹，缓解分布偏移。
- **关键技术细节**：
  - 基础模型为**导航扩散策略（Navigation Diffusion Policy）**，输入局部观测（如RGB图像、深度等），输出未来路径点序列（waypoints）。
  - 全局规划器（Global Planner）拥有环境的**特权信息**（如地图、全局路径），提供最优参考轨迹作为模仿学习的监督信号。
  - 无需奖励设计，利用在线采集的专家数据（由全局规划器生成）进行模仿学习，同时策略的交互数据来自自身 rollout，有效缓解分布偏移。
  - 模拟器采用 **IsaacLab**，支持快速高保真并行渲染、相机位姿和起点‑目标对的域随机化（domain randomization）。

## 3. 实验设计：数据集/场景、Benchmark、对比方法

- **模拟Benchmark**：
  - **NavDP benchmark**：已有公开的导航基准。
  - **自建室内视觉导航基准**（Indoor Visual Navigation Benchmark）：预设起点和目标位置，用于零样本泛化测试。
- **真实世界实验**：精心设计的真实场景实验，验证策略的实际部署效果。
- **对比方法**：论文未列出具体对比算法名称，但提到与基线（baselines）比较，包括离线模仿学习方法、强化学习方法等（从 motivation 可推断）。结果显示 NavOL 在成功率、鲁棒性等指标上**持续优于基线**。

## 4. 资源与算力

- 硬件：使用 **8块 RTX 4090 GPU** 进行并行训练。
- 数据采集效率：每小时可收集 **超过2000条新轨迹**，每条轨迹平均超过400步。
- 训练场景数量：在 **50个场景** 中扩展训练。
- 训练时长：论文未明确给出总训练时间（如小时数），但描述了数据生成的高效性。

## 5. 实验数量与充分性

- **实验数量**：覆盖三个层面的评估：
  - 模拟环境中的标准 benchmark（NavDP）。
  - 自建室内视觉导航 benchmark（零样本泛化测试）。
  - 真实世界实验（real-world experiments）。
- **消融实验**：论文未明确提及消融实验（如分析在线收集数据量、域随机化影响等），但提到 **extensive evaluations** 表明有效性。
- **充分性评价**：实验涵盖仿真和真实场景，且使用多个 benchmark，具备一定全面性。但缺乏对方法组件（如扩散策略 vs. 其他生成模型、在线收集数据比例等）的消融分析，公平性方面未与最新RL/IL方法（如PPO、GAIL）进行定量对比，客观性有待加强。

## 6. 论文的主要结论与发现

- NavOL 通过在线模仿学习范式，有效解决了离线模仿学习的分布偏移问题，并克服了强化学习需要奖励工程的缺点。
- 在模拟和真实世界实验中，NavOL 持续提升了导航策略的成功率、鲁棒性和泛化能力。
- 结合扩散策略与在线交互，为视觉导航提供了一种高效、无需奖励设计的稳健解决方案。

## 7. 优点

- **方法创新**：将在线模仿学习与扩散策略结合，提出新颖的 rollout–update 循环，同时利用全局规划器作为线上专家，免去奖励设计，提升学习效率。
- **实验设计亮点**：
  - 采用 **IsaacLab** 并行模拟器，支持大规模场景和域随机化，数据收集效率高（50场景、8 GPU）.
  - 同时评估仿真 benchmark 和真实世界，验证了方法的实际可行性。
- **资源利用高效**：每小时采集2000+轨迹，表明系统在算力消耗方面具有可扩展性。
- **成果贡献**：提供了一个室内视觉导航基准，便于后续研究。

## 8. 不足与局限

- **实验覆盖有限**：
  - 仅针对**室内场景**，未涉及室外、动态障碍物或长距离导航等更复杂环境。
  - 对比基线不够明确：未列出具体的方法（如行为克隆、DAgger、PPO等）及详细数值，难以直接量化提升幅度。
- **消融实验缺失**：未分析在线收集数据量、域随机化强度、扩散策略架构选择等因素对性能的影响，结论的鲁棒性证据不足。
- **潜在偏差风险**：
  - 全局规划器依赖于**特权信息**（如完整地图），在真实世界中可能无法获取，实际部署时需额外感知融合，论文未讨论此限制。
  - 实验场景数量（50个）和轨迹数（每小时>2000条）虽多，但场景多样性（如光照、纹理、物体布局）未详细说明，可能引入特定偏差。
- **应用限制**：方法依赖模拟器交互和全局规划器提供标签，对于无法访问特权信息的任务（如野外导航）不适用；且需要模拟器并行渲染，硬件门槛较高。

（完）
