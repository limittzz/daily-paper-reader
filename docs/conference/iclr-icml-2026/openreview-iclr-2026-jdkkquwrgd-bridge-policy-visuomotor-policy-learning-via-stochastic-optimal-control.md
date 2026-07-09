---
title: "Bridge Policy: Visuomotor Policy Learning via Stochastic Optimal Control"
title_zh: Bridge Policy：通过随机最优控制的视觉运动策略学习
authors: "Zhaoyang Liu, Mokai Pan, Zhongyi Wang, Kaizhen Zhu, Haotao Lu, Jingya Wang, Ye Shi"
date: 2025-09-14
pdf: "https://openreview.net/pdf?id=jDkKquWRgd"
tags: ["query:imitation"]
score: 9.0
evidence: 基于模仿学习的视觉运动策略，使用扩散桥公式
tldr: 现有基于扩散的模仿学习方法在前向和反向过程中引入条件，导致流形偏差和估计误差。为此提出BridgePolicy，一种无条件的生成式视觉运动策略，通过随机最优控制的扩散桥公式将观测直接融入前向过程，从观测分布而非随机噪声采样动作。实验证明该方法能更好地捕捉多模态动作分布，提升模仿学习性能。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 现有扩散模仿学习方法在前向和反向过程中施加条件，引入流形偏离和估计误差。
method: 提出BridgePolicy，利用扩散桥公式，从观测分布采样动作，实现无条件的生成式视觉运动策略。
result: 在多个模仿学习基准上验证了有效性，显示出更优的多模态行为建模能力。
conclusion: BridgePolicy通过扩散桥公式提供了一种更稳健的模仿学习方法，无需额外条件化步骤。
---

## Abstract
Imitation learning has been widely used in robotic learning, where policies are derived from expert demonstrations. Recent advances leverage generative models, such as diffusion and flow-based methods, to better capture multi-modal action distributions and temporal dependencies. However, these approaches typically impose conditioning during the forward and reverse process, which inevitably introduces manifold deviation and estimation error. In this work, we propose BridgePolicy, a condition-free generative visuomotor policy that explicitly incorporates observations into the forward process through a diffusion bridge formulation grounded in stochastic optimal control. By sampling actions from observation distributions instead of random noise, BridgePolicy reduces stochasticity and achieves more controllable policy behaviors. However, directly bridging observations to actions poses new challenges, as the action distribution may exhibit mismatched data shape, and the robot observations are inherently multi-modal. In contrast, the diffusion bridge can only connect one-to-one distributions with the same shape.  To address the challenges of aligning distributional endpoints and handling multi-modal robot observations, we design a semantic aligner for distribution shape alignment, and a modality fusion module for unifying robot states and visual inputs. Experiments across 52 tasks on 3 benchmarks and 4 real-world tasks demonstrate that BridgePolicy consistently outperforms state-of-the-art generative policies.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：在机器人学习的模仿学习中，策略需要从专家演示中学习多模态的动作分布和时间依赖关系。近年来，基于扩散模型和流匹配的生成式方法被广泛应用于视觉运动策略，但这些方法在前向过程（加噪）和反向过程（去噪）中均引入了条件（如观测作为条件），这不可避免地导致流形偏差（manifold deviation）和估计误差（estimation error），从而限制了策略的表达能力和鲁棒性。
- **核心问题**：能否设计一种**无条件的生成式视觉运动策略**，直接建立从观测到动作的桥梁，避免因条件化引入的偏差和误差，同时更好地捕捉多模态行为？此外，观测数据具有多模态（视觉、本体感觉等），而扩散桥通常只能连接形状相同的一对一分布，如何解决分布端点形状不匹配以及多模态观测融合的挑战。

## 2. 论文提出的方法论：核心思想、关键技术细节、算法流程（文字说明）

- **核心思想**：提出 **BridgePolicy**，利用**随机最优控制**框架下的**扩散桥公式**（diffusion bridge formulation），将观测显式地融入前向过程，使得动作采样不再从随机噪声出发，而是从**观测分布**开始，经由扩散桥到达动作分布。这样避免了传统方法中在正向和反向过程额外施加条件带来的问题，减少了随机性，实现了更可控的策略行为。
- **关键技术细节**：
  - **扩散桥公式**：借鉴随机最优控制，设计一个从观测分布到动作分布的扩散过程（bridge），使得最终采样得到的动作天然地与观测对齐，无需额外的条件化步骤。
  - **语义对齐器（Semantic Aligner）**：解决观测分布与动作分布形状不匹配的问题。因为动作通常具有明确的维度（如关节角度、末端速度），而观测（尤其图像）具有高维且多模态的特征。语义对齐器将观测特征映射到与动作相同维度的隐空间，使两端分布形状一致，从而使得扩散桥可以连接。
  - **模态融合模块（Modality Fusion Module）**：用于统一机器人状态（如关节角度、力传感器）与视觉输入（如RGB图像）等多模态观测。该模块将不同模态的特征融合为一个统一的表征，作为扩散桥的起点。
- **算法流程（文字说明）**：
  1. 输入：当前多模态观测（图像、机器人状态等）。
  2. 模态融合模块将不同模态特征融合为统一观测表征。
  3. 语义对齐器将融合后的观测表征映射到与动作同构的隐空间，得到观测分布。
  4. 利用扩散桥公式，从观测分布开始，基于随机最优控制进行扩散过程，最终采样得到动作。
  5. 输出：动作（如关节目标位置或末端速度），用于机器人执行。
- 该方法本质上是一种**无条件的生成模型**，因为观测直接作为前向过程的起点，而非作为条件约束。

## 3. 实验设计：数据集/场景、benchmark、对比方法

- **实验场景**：
  - 3个基准测试（benchmarks），共 **52 个任务**。
  - 4个真实世界机器人任务。
- **Benchmark**：文中未明确列举具体基准名称（如MetaWorld、Robosuite、LIBERO等），但根据常见模仿学习基准推测可能包含标准任务集（如DMControl、Adroit、Franka Kitchen等）。
- **对比方法**：与**当前最先进的生成式策略**（state-of-the-art generative policies）进行比较，可能包括基于扩散的策略（如Diffusion Policy）、基于流匹配的方法（如Flow Policy）等。具体对比方法未在摘要中列出。

## 4. 资源与算力

- 文中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅为摘要，详细信息可能在全文中。但可指出缺失。

## 5. 实验数量与充分性

- **实验数量**：共52个模拟任务 + 4个真实世界任务，规模较大。
- **充分性分析**：
  - 覆盖了多个不同难度的任务集，从模拟到真实世界，体现泛化能力。
  - 但缺少消融实验详细描述（如对语义对齐器、模态融合模块的消融），也未提及是否进行统计显著性检验、是否重复多次实验。
  - 总体而言，实验规模足够，但仍需全文验证其客观性和公平性（如是否与所有最新方法在同一设置下比较）。

## 6. 论文的主要结论与发现

- **主要结论**：BridgePolicy 在 52 个模拟任务和 4 个真实世界任务上，**一致优于**当前最先进的生成式策略，证明了所提无条件扩散桥方法的有效性。
- **关键发现**：通过从观测分布而非随机噪声采样动作，能够更准确地捕捉多模态动作分布，减少流形偏差与估计误差，从而提升模仿学习性能。

## 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 首次将**扩散桥公式**应用于视觉运动策略学习，摆脱传统条件化带来的弊端。
  - 设计了**语义对齐器**解决分布形状不匹配问题，使得桥接可行；**模态融合模块**处理多模态观测，增强普适性。
  - 算法简洁，无需额外的逆过程条件化，具有理论上的优雅性。
- **实验亮点**：
  - 覆盖52个模拟任务+4个真实任务，任务数量多，场景多样性高。
  - 在真实机器人上验证，增加了结论的可信度。

## 8. 不足与局限

- **实验覆盖不足**：未说明具体基准名称，且未与其他最新的无条件生成方法（如基于流的直接条件模型）进行详细比较；消融实验未提及，难以判断各模块贡献。
- **偏差风险**：仅依赖摘要信息，无法确认其与基线方法在公平性（如相同种子、相同训练步数、相同超参数）上的比较细节。
- **应用限制**：扩散桥假设观测分布和动作分布可桥接，对于高维复杂观测可能仍需大量数据训练；语义对齐器可能增加模型复杂度；真实世界实验场景较少（仅4个），泛化到更复杂环境仍需验证。
- **算力与资源缺失**：未提供训练成本，难以评估实际部署可行性。

（完）
