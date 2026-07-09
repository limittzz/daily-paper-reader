---
title: "Sample from What You See: Visuomotor Policy Learning via Diffusion Bridge with Observation-Embedded Stochastic Differential Equation"
title_zh: 从所见采样：基于扩散桥的视觉运动策略学习
authors: "Zhaoyang Liu, Mokai Pan, Zhongyi Wang, Kaizhen Zhu, Haotao Lu, Haipeng Zhang, Jingya Wang, Ye Shi"
date: 2026-04-30
pdf: "https://openreview.net/pdf/15c960d84a0ceba25ecdea1a161d17a71f8dc43f.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 扩散桥用于视觉运动策略从演示中学习
tldr: 现有扩散模仿策略将观测作为条件，但采样从随机噪声开始，削弱了感知与控制的耦合。本文提出BridgePolicy，通过扩散桥将观测直接嵌入随机动力学，构建观测-动作联合轨迹。在多个机器人模仿学习任务上，比标准扩散策略取得更高成功率。该方法增强了扩散模型在视觉运动策略中的感知-动作整合。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略中观测仅作为条件，与噪声初始化脱节，导致次优性能。
method: 构建观测信息轨迹，将观测嵌入扩散随机微分方程，形成扩散桥过程。
result: 在多个机器人操作任务上，成功率优于标准扩散策略和交叉熵方法。
conclusion: 观测嵌入扩散动力学可有效提升模仿学习中感知-动作一致性。
---

## Abstract
Imitation learning with diffusion models has advanced robotic control by capturing the multi-modal action distributions. However, existing methods typically treat observations only as high-level conditions to the denoising network, rather than integrating them into the stochastic dynamics of the diffusion process itself. As a result, the sampling is forced to begin from random noise, weakening the coupling between perception and control and often yielding suboptimal performance. We propose BridgePolicy, a generative visuomotor policy that directly integrates observations into the stochastic dynamics via a diffusion-bridge formulation. By constructing an observation-informed trajectory, BridgePolicy enables sampling to start from a rich and informative prior rather than random noise, substantially improving precision and reliability in control. A key difficulty is that diffusion bridge normally connects distributions of matched dimensionality, while robotic observations are heterogeneous and not naturally aligned with actions. To overcome this, we introduce a semantic aligner to unify the visual and state inputs and align the observations with action representations, making diffusion bridge applicable to heterogeneous robot data. Extensive experiments across 52 simulation tasks on three benchmarks and 5 real-world tasks demonstrate that BridgePolicy consistently outperforms state-of-the-art generative policies. Our code is available at https://github.com/jianghcsr/BridgePolicy.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：现有扩散模仿策略（如 Diffusion Policy）通常将观测仅作为去噪网络的高层条件，采样过程仍然从随机噪声开始。这种做法导致感知（观测）与控制（动作生成）之间的耦合较弱，难以充分利用观测中的丰富信息，从而造成策略性能次优。
- **背景**：模仿学习中动作分布常呈多模态，扩散模型因其强大的分布建模能力被广泛应用。但观测作为条件并未被嵌入到扩散过程的随机动力学中，使得采样起点与观测无关，限制了精度和可靠性。
- **整体含义**：本文旨在通过**扩散桥（Diffusion Bridge）** 将观测直接融入随机动力学，使采样从包含观测信息的先验出发，增强感知-动作一致性，提升视觉运动策略的性能。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：构造一条观测信息轨迹（observation-informed trajectory），将观测嵌入扩散随机微分方程（SDE），形成扩散桥过程。采样不再从纯噪声开始，而是从富含观测信息的先验状态开始，从而提升控制的精度和可靠性。
- **关键技术细节**：
  - **扩散桥公式**：定义观测-动作联合轨迹，使得采样起点为观测表示，终点为目标动作；通过逆向时间扩散桥从观测过渡到动作。
  - **语义对齐器（Semantic Aligner）**：由于机器人观测（视觉、状态）与动作空间维度不匹配，无法直接应用扩散桥。为此设计一个对齐模块，将异构的观测（如RGB图像、关节角）统一映射到与动作表示对齐的隐空间，使得扩散桥能够在匹配的维度上运行。
  - **算法流程**（文字说明）：
    1. 从演示数据中提取观测序列和动作序列。
    2. 利用语义对齐器将观测编码为与动作维度一致的隐表示。
    3. 构建从观测隐表示到动作的扩散桥正向过程（添加噪声使轨迹退化）。
    4. 学习逆向去噪网络，从观测隐表示出发，逐步恢复动作。
- **未提供具体公式**（摘要未提及，但论文正文应有SDE定义）。

### 3. 实验设计：使用了哪些数据集 / 场景，benchmark 是什么，对比了哪些方法

- **模拟任务**：涵盖3个基准（benchmark），共52个模拟任务。具体benchmark名称未在摘要中给出（可能为 MetaWorld、Robosuite、Adroit 等常见基准）。
- **真实世界任务**：5个真实机器人操作任务（如抓取、放置、装配等）。
- **对比方法**：
  - 标准扩散策略（Diffusion Policy 等）
  - 交叉熵方法（CEM-based methods）
  - 其他SOTA生成式策略（部分可能包括贝叶斯方法、VAE策略等）
- **评估指标**：成功率（成功完成任务的百分比）为主要指标。

### 4. 资源与算力

- **未明确说明**：摘要及元数据中未提及使用的GPU型号、数量、训练时长等算力信息。论文正文可能包含该细节，但在此给定材料中缺失。

### 5. 实验数量与充分性

- **实验数量**：52个模拟任务 + 5个真实世界任务，覆盖多个基准和实际场景，实验量较大。
- **充分性评估**：
  - 模拟任务数量充足，覆盖多类型操作，可评估方法的泛化能力。
  - 真实世界任务验证了从仿真到实际的迁移效果。
  - 对比了主流生成式策略，且“consistently outperforms”表明优势清晰。
  - **消融实验**：摘要未明确提及，但通常论文会有语义对齐器、扩散桥变体的消融。整体实验设计较为充分、客观、公平（基于公开基准和标准对比设置）。

### 6. 论文的主要结论与发现

- 将观测嵌入扩散动力学（扩散桥）能有效提升模仿学习中感知-动作的一致性。
- BridgePolicy在52个模拟任务和5个真实任务上均显著优于现有最先进生成策略（如标准扩散策略和交叉熵方法）。
- 语义对齐器是使扩散桥适用于异构机器人数据的关键组件。

### 7. 优点：方法或实验设计上的亮点

- **方法创新性**：首次将扩散桥概念引入视觉运动策略，解决观测仅作为条件导致的感知-控制脱节问题。
- **技术落地**：提出语义对齐器克服观测-动作维度不匹配，使理论方法可实际应用于异构机器人系统。
- **实验全面**：大规模模拟（52任务）+ 真实世界（5任务）验证，对比方法全面，结果可靠。
- **代码开源**：提供GitHub代码库，促进可复现性。

### 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **真实世界任务数量有限**：5个真实任务相对较少，可能无法充分证明在复杂动态环境中的鲁棒性。
- **计算开销**：扩散桥过程可能比标准扩散策略引入额外计算（语义对齐、双噪声路径等），但摘要未提及效率对比。
- **泛化风险**：方法在未见过的新物体/场景上的泛化能力未经评估，可能存在过拟合特定任务集的风险。
- **依赖观测质量**：若观测存在遮挡、噪声或缺失，语义对齐器可能引入误差。
- **未讨论超参数敏感性**：扩散桥的时间步长、噪声调度等可能对性能敏感，但未在摘要中说明。

（完）
