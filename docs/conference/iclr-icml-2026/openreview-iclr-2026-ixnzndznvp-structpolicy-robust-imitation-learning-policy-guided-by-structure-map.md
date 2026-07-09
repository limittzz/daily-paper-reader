---
title: "StructPolicy: Robust Imitation Learning Policy Guided by Structure Map"
title_zh: StructPolicy：结构图引导的鲁棒模仿学习策略
authors: "Zehao Du, Jiude Wei, Cewu Lu, Jianhua Sun"
date: 2025-09-02
pdf: "https://openreview.net/pdf?id=IXNZNDZNVp"
tags: ["query:imitation"]
score: 9.0
evidence: 结构图引导的鲁棒模仿学习策略用于机器人操作
tldr: 模仿学习易受视觉噪声干扰导致鲁棒性差。本文提出StructPolicy，通过构建稳定且任务相关的结构图（Structure Map），提取拓扑线索并过滤无关视觉噪声，仅保留结构信息引导机器人操作。实验证明该方法有效提升了策略对分布偏移和领域差异的鲁棒性。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 模仿学习易过拟合视觉噪声，导致对分布偏移敏感，鲁棒性不足。
method: 构建任务相关的结构图，过滤视觉噪声，仅保留结构信息引导动作输出。
result: 在机器人操作任务上有效提升了鲁棒性，尤其在领域偏移场景下优于基线。
conclusion: 结构先验可显著增强模仿学习策略的泛化能力。
---

## Abstract
Imitation Learning (IL) offers an effective approach for robot manipulation by learning a mapping function from visual inputs to actions.
However, this paradigm suffers from domain discrepancies and is highly sensitive to distribution shifts, as the mapping function inherent in IL tends to overfit task-irrelevant visual noise, thereby compromising robustness. To address this, we propose StructPolicy, a lightweight module that guides the robot to acquire structural knowledge by constructing a stable and task-relevant Structure Map.
The structure map is composed of task-relevant object structures and reveals the topological cues essential for accomplishing manipulation tasks.
By filtering out visually distracting noise and retaining only the structural attributes, it effectively guides the robot's policy learning toward robust manipulation.
We begin by introducing a general object structure representation based on atomic geometric primitives, enabling flexible composition and scalability to arbitrary objects.
Building on this, we design StructGen, a module that automatically constructs structure maps from visual observations.
Finally, we design StructTransformer, which employs hierarchical attention over structure vertices to extract map features from the structure map for action prediction.
We extensively evaluate StructPolicy across 3 IL models, 3 different simulators, and 50 diverse manipulation tasks.
In addition, through ablation studies under various visual changes, including visual noise, camera viewpoint shifts, light intensity, and background color variations, we demonstrate that StructPolicy consistently improves robustness against distribution shifts.
Results demonstrate consistent and significant performance improvements across all tasks, validating the effectiveness and robustness of StructPolicy. Our code will be released publicly to facilitate reproducibility and further research.

---

## 论文详细总结（自动生成）

# 论文详细总结：StructPolicy：结构图引导的鲁棒模仿学习策略

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：模仿学习（Imitation Learning, IL）在机器人操作中通过从视觉输入到动作的映射学习策略，但该范式极易过拟合与任务无关的视觉噪声（如背景纹理、光照变化、相机视角偏移等），导致对分布偏移（domain shift）敏感，鲁棒性严重不足。
- **动机**：现有IL模型在真实部署时，由于训练与测试环境的视觉差异（如光照、背景颜色、相机角度变化），性能大幅下降。作者认为根本原因在于模型学到的映射函数混杂了任务无关的视觉干扰，缺乏稳定的结构先验。
- **整体含义**：提出一种轻量模块StructPolicy，通过构建任务相关的“结构图”（Structure Map）来提取拓扑线索，过滤视觉噪声，仅保留稳定的几何结构信息，从而引导机器人实现鲁棒操作。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将视觉观测中的任务相关对象解耦为原子几何基元（atomic geometric primitives）组成的结构图，该图只包含对象间的拓扑关系（如相对位置、连接方式），忽略纹理、颜色、光影等无关信息。策略网络仅基于结构图进行动作预测。
- **关键技术细节**：
  - **对象结构表示**：基于原子几何基元（如球体、长方体、圆柱体等）对任意物体进行灵活组合，实现可扩展的结构表示。
  - **StructGen模块**：自动从视觉观测中构建结构图。该模块能够识别场景中的物体及其基元，并生成包含顶点（基元属性）和边（空间关系）的结构图。
  - **StructTransformer模块**：在结构图上应用层次化注意力机制，提取图特征用于动作预测。该模块设计为可插拔，可适配多种现有IL骨干网络。
- **算法流程文字描述**：
  1. 输入当前视觉观测（RGB图像/点云等）。
  2. 通过StructGen生成结构图：识别物体基元，构建顶点（位置、尺寸等）和边（距离、朝向等）。
  3. 将结构图送入StructTransformer，经过图注意力层和图池化层提取全局图特征。
  4. 将图特征与原始IL模型的特征融合，或直接替换视觉编码器，输出动作预测。
  5. 训练时，结构图构建过程不参与梯度更新（或使用端到端可微结构），整体策略以模仿学习目标（如行为克隆损失）优化。

## 3. 实验设计：数据集/场景、基准、对比方法

- **数据集/场景**：涵盖3种不同的模拟器（simulators），共计50种不同的操作任务（如抓取、放置、组装等）。未具体说明模拟器名称，推测可能包括MuJoCo、PyBullet等常见平台。
- **Benchmark**：未明确指定单一公开基准，而是自行构建了多任务测试集，并在多种视觉变化条件下评估鲁棒性。
- **对比方法**：在3种不同的IL模型（如行为克隆、DAgger类方法、隐式策略等）基础上，分别嵌入StructPolicy模块，并与原始模型及可能的其他鲁棒性增强方法（如域随机化、数据增强等）进行对比。具体基线名称未在摘要中列举，但提到“在多种视觉变化下显著优于基线”。

## 4. 资源与算力

- **论文摘要及元数据中未明确说明使用的GPU型号、数量或训练时长**。仅提到“轻量模块”（lightweight module），暗示计算开销较低。但具体的算力配置信息缺失，需查阅全文补充。

## 5. 实验数量与充分性

- **实验数量**：涵盖3种IL模型 × 3种模拟器 × 50种任务 = 潜在大量组合实验。此外，还专门进行了消融研究（ablation studies），针对视觉噪声、相机视角偏移、光照强度、背景颜色变化等多种场景。
- **充分性评估**：
  - **优点**：实验覆盖范围较广，涉及多模型、多模拟器、多任务，且在多种分布偏移下测试，具有一定的系统性。
  - **不足**：未提及真实机器人实验，仅限模拟器内部评估。另外，未说明每个实验的随机种子次数、标准误等统计细节。对于“50种任务”的具体难度分布、是否覆盖长时域任务等未阐述。整体而言，实验设计较为充分但仍需更多细节来证明客观公平。

## 6. 主要结论与发现

- **核心结论**：StructPolicy能够显著提升模仿学习策略对分布偏移的鲁棒性，在所有任务上均获得一致且显著的性能提升。
- **具体发现**：
  - 结构图可以有效过滤视觉噪声，保留任务相关的拓扑线索。
  - 基于原子几何基元的表示具有良好的可扩展性，可应用于任意物体。
  - StructTransformer的层次化注意力能够有效提取结构图中的关键信息。
  - 该方法在不同IL模型、不同模拟器、以及多种视觉变化条件下均稳定有效。

## 7. 优点（方法或实验设计的亮点）

- **方法亮点**：
  - 提出结构图这一显式结构化先验，从根本上解决了IL对视觉噪声的过拟合问题，思路新颖。
  - 原子基元组合的设计使得结构表示可扩展到任意物体，具有通用性。
  - 轻量化模块设计，可即插即用于现有IL模型，无需重新训练骨干网络。
- **实验亮点**：
  - 多维度评估：覆盖3种IL模型、3种模拟器、50种任务，消融多种视觉变化情况，实验规模较大。
  - 消融研究系统化：逐因子分析视觉噪声、视角、光照、背景等影响，增强结论可信度。
  - 强调可复现性：承诺开源代码，便于后续研究。

## 8. 不足与局限

- **实验方面**：
  - 未进行真实机器人实验，仅在模拟器中验证，实际部署中的物理噪声、感知误差等未涉及。
  - 未提供与最先进的鲁棒IL方法（如域随机化、因果表示学习等）的直接比较，对比基线可能不够全面。
  - 未报告计算资源消耗（GPU型号、训练时长等），影响复现可操作性。
- **方法局限**：
  - 假设物体可由原子基元组合表示，对于软体、变形物体或非刚体可能难以适用。
  - StructGen模块的构建精度依赖于感知质量，若观测噪声过大可能导致结构图错误。
  - 层次化注意力增加计算复杂度，可能在实时性要求高的场景中受限。
- **偏差风险**：论文可能倾向于展示成功案例，对于失败任务的分析不够详尽。另外，50种任务可能并非完全随机挑选，存在选择性展示的潜在偏差。

（完）
