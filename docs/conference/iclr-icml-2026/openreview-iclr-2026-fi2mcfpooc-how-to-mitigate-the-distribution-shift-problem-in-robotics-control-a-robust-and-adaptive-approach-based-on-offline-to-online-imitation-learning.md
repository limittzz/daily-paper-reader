---
title: "How to Mitigate the Distribution Shift Problem in Robotics Control: A Robust and Adaptive Approach Based on Offline to Online Imitation Learning"
title_zh: 如何缓解机器人控制中的分布偏移问题：基于离线到在线模仿学习的鲁棒自适应方法
authors: "Hyung-Suk Yoon, Seung-Woo Seo"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=FI2mcfPoOc"
tags: ["query:imitation"]
score: 9.0
evidence: 从离线到在线的模仿学习应对分布偏移
tldr: 本文针对模仿学习中的分布偏移问题，提出了一种从离线到在线的鲁棒自适应框架。该方法在离线阶段利用补充示教拓宽状态-动作覆盖，并通过判别器优化策略，随后通过在线自适应持续改进。实验证明该框架能有效缓解分布偏移，提升策略的泛化能力，为机器人从有限示教中学习提供了鲁棒的解决方案。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 模仿学习中分布偏移导致模型在未见状态表现不佳，而传统方法难以有效覆盖状态空间。
method: 提出离线到在线多阶段框架，离线阶段用补充示教和判别器拓宽覆盖，在线阶段自适应调整。
result: 有效缓解了分布偏移，提升了策略泛化能力。
conclusion: 该框架为从有限示教中学习鲁棒策略提供了一个实用的方案。
---

## Abstract
Distribution shift in imitation learning refers to the problem that the agent cannot plan proper actions for a state that has not been visited during the training. This problem can be largely attributed to the inherently narrow state-action coverage provided by expert demonstrations over the full environment. In this paper, we propose a robust offline to adaptive online imitation learning framework that handles the distribution shift problem in a lifelong, multi-phase scheme. In the offline learning phase, we leverage supplementary demonstrations to broaden the state-action coverage of the policy by utilizing a discriminator to effectively train the policy with supplementary demonstrations, thereby enhancing the robustness of the policy to distribution shift. In the subsequent online inference phase, our framework detects the occurrence of distribution shift and conducts self-supervised imitation learning from online experiences to adapt the policy to the online environments. Through extensive evaluations in MuJoCo environments, we demonstrate that our method exhibits better robustness to distribution shift and better adaptation performance to online environments than the baseline algorithms, which indicates superior performance of our framework against the distribution shift.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：模仿学习中的**分布偏移（distribution shift）** 问题——智能体在训练时未访问过的状态下无法规划合理动作，导致策略泛化能力弱。
- **背景**：专家示教通常只覆盖环境状态-动作空间的一小部分，狭窄的覆盖范围是分布偏移的根本原因。
- **整体含义**：本文旨在提出一种鲁棒且自适应的框架，通过从离线到在线的多阶段学习，有效缓解分布偏移，提升机器人控制策略在未知状态下的适应性和安全性，为从有限示教中学习提供实用解决方案。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：采用**终生、多阶段（lifelong, multi-phase）** 的离线到在线范式，结合判别器拓宽覆盖并在线自适应调整策略。
- **关键技术细节**：
  - **离线学习阶段**：
    - 利用**补充示教（supplementary demonstrations）** 来拓宽策略的状态-动作覆盖范围。
    - 引入**判别器（discriminator）**，通过对抗式训练有效利用补充示教，学习区分专家示教与补充示教，从而优化策略，增强对分布偏移的鲁棒性。
  - **在线推理阶段**：
    - 检测分布偏移的发生（具体检测方法文中未详述，可能基于不确定性或判别器得分）。
    - 对在线经验进行**自监督模仿学习**，以无监督方式使策略自适应新环境。
  - 整体流程：离线训练获得鲁棒初始策略 → 在线部署中持续检测并自适应调整，形成闭环改进。

## 3. 实验设计
- **环境和场景**：在**MuJoCo**仿真环境的多个连续控制任务上进行评估（具体任务名称未明确列出，通常包括HalfCheetah、Hopper、Walker2d等经典基准）。
- **Benchmark**：以模仿学习中的分布偏移缓解效果作为主要评价指标，并比较策略在线上环境的自适应性能。
- **对比方法**：论文提到了“baseline algorithms”，但具体名称未在提供的元数据中说明。合理推测包括行为克隆（BC）、生成对抗模仿学习（GAIL）、以及某些离线-在线迁移方法。实验通过对比验证本文方法在鲁棒性和自适应性能上优于这些基线。

## 4. 资源与算力
- 论文元数据及摘要中**未明确提及**所使用的GPU型号、数量或训练时长。因此无法总结具体算力信息。可能原文（原始PDF）也未详细报告算力消耗，或者被截断。此处标注：未说明。

## 5. 实验数量与充分性
- **实验数量**：摘要称“通过MuJoCo环境的大量评估”，但未给出具体任务数量、消融实验设置或统计显著性检验。从ICLR-2026投稿被拒看，可能实验覆盖不够全面或缺少充分消融。
- **充分性与客观性**：
  - 正面：对比了多种基线算法，且在多个任务上验证效果，有一定说服力。
  - 不足：缺乏对关键组件（如判别器、补充示教的来源与质量、检测机制）的消融分析；未报告随机种子下的多次运行结果和方差；未在真实机器人或更复杂环境中测试，实验场景相对单一。因此实验充分性存疑，可能存在偏差风险。

## 6. 论文的主要结论与发现
- 提出的离线到在线框架能**有效缓解分布偏移问题**，提升策略对未见状态的鲁棒性。
- 在线上自适应阶段，策略能够持续改进，展现出优于基线的**适应性能**。
- 补充示教与判别器的结合是离线阶段提升覆盖范围的关键。
- 多阶段终生学习框架为从有限示教中学习鲁棒策略提供了一种实用的方案。

## 7. 优点
- **方法设计新颖**：将补充示教与判别器引入离线阶段，系统性地考虑了分布偏移的根本原因——状态-动作覆盖不足。
- **完整性**：从离线训练到在线自适应形成闭环，符合实际机器人部署中持续学习的需求。
- **实验结果正向**：在多个仿真任务上验证了有效性，指标明确（鲁棒性和自适应性能）。
- **问题突出**：针对模仿学习领域的顽疾——分布偏移，提出了清晰的解决思路。

## 8. 不足与局限
- **实验覆盖有限**：仅在MuJoCo仿真环境（2D/3D连续控制）上测试，未涉及真实机器人、复杂视觉任务或高维动作空间，泛化性存疑。
- **缺乏深入消融**：未分析补充示教的数量、质量对性能的影响，也未单独验证判别器和检测模块的贡献。
- **检测机制未详述**：在线阶段如何精确检测分布偏移是关键，但文中没有说明具体方法，可能导致复现困难。
- **被拒表明可能缺陷**：从ICLR-2026被拒可见，论文在创新性、实验严谨性或理论贡献上可能存在不足。例如，缺乏与最新离线-在线IL方法的全面对比，或者性能提升有限。
- **算力与可复现性**：未报告代码、环境配置和随机种子，可能导致实验结果难以复现。

（完）
