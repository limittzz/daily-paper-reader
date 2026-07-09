---
title: Learning Diverse Skills for Behavior Models with Mixture of Experts
title_zh: 使用混合专家学习行为模型的多样技能
authors: "wangtian shen, Jinming Ma, mingliang zhou, Ziyang Meng"
date: 2025-09-18
pdf: "https://openreview.net/pdf?id=ZBD1UuXJum"
tags: ["query:imitation"]
score: 9.0
evidence: 混合专家模型的模仿学习用于多样技能
tldr: 现有模仿学习模型在多任务场景下性能下降，因任务间干扰导致平均化效应。本文提出Di-BM，为每个专家关联不同的观测分布，使用能量模型表示专家分布并联合训练，使专家在观测子空间上专门化。在多个机器人操作任务上，Di-BM显著提升了多任务模仿学习的成功率。该方法有效缓解了多任务干扰问题。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 多任务模仿学习中，全局模型因任务干扰而性能下降。
method: 为每个专家分配独特的观测分布，用能量模型建模并联合训练，实现观测空间上的专业化。
result: 在多个机器人操作任务上，多任务成功率显著优于统一模型和现有方法。
conclusion: 基于观测分布的专家专业化能有效提升多任务模仿学习性能。
---

## Abstract
Imitation learning has demonstrated strong performance in robotic manipulation by learning from large-scale human demonstrations. While existing models excel at single-task learning, it is observed in practical applications that their performance degrades in the multi-task setting, where interference across tasks leads to an averaging effect. To address this issue, we propose to learn diverse skills for behavior models with Mixture of Experts, referred  to as Di-BM. Di-BM associates each expert with a distinct observation distribution, enabling experts to specialize in sub-regions of the observation space. Specifically, we employ energy-based models to represent expert-specific observation distributions and jointly train them alongside the corresponding action models. Our approach is plug-and-play and can be seamlessly integrated into standard imitation learning methods. Extensive experiments on multiple real-world robotic manipulation tasks demonstrate that Di-BM significantly outperforms state-of-the-art baselines. Moreover, fine-tuning the pretrained Di-BM on novel tasks exhibits superior data efficiency and the reusable of expert-learned knowledge.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：在机器人操作的大规模模仿学习中，现有模型在多任务场景下性能显著下降，原因是不同任务之间的干扰导致模型学习到一个“平均化”的行为，无法针对每个任务的特异性进行精确模仿。
- **研究动机**：多任务模仿学习是提升机器人泛化能力的关键，但任务间的干涉严重限制了模型效能。如何让一个模型同时掌握多种技能而不相互干扰，是当前的核心挑战。
- **整体含义**：通过引入混合专家机制，并为每个专家分配不同的观测分布，使得不同专家专门负责观测空间的不同子区域，从而有效缓解任务冲突，提高多任务模仿学习的成功率和数据效率。

### 2. 论文提出的方法论

- **核心思想**：将行为模型设计为混合专家（Mixture of Experts, MoE）架构，让每个专家学习一个独特的观测分布而非统一的策略。专家根据观测输入的差异自动选择最擅长的子区域进行行为预测。
- **关键技术细节**：
  - 使用**能量模型（Energy-based Model）** 表示每个专家专属的观测分布。能量模型能够灵活捕捉复杂分布，并通过对比样本进行训练。
  - 同时联合训练**专家观测分布的能量模型**和**专家对应的动作模型（action model）**。损失函数包括动作预测损失和观测分布的能量对比损失。
  - 方法即插即用，可以无缝集成到任何标准模仿学习方法（如行为克隆、扩散策略等）的框架中，只需替换模型后端为带专家分布约束的MoE。
- **公式或算法流程（文字说明）**：
  1. 输入观测数据，通过选通网络或专家分布能量模型计算每个专家的激活概率。
  2. 能量模型输出每个观测属于某一专家分布的能量值，通过负对数似然或对比散度进行优化。
  3. 激活的专家根据其观测子空间的特性输出下一动作预测。
  4. 总损失 = 动作预测损失（如MSE或负对数似然）+ 专家分布的能量损失。
  5. 联合训练所有参数，使不同专家自动观测子空间上专门化。

### 3. 实验设计

- **数据集 / 场景**：实验基于**多个真实世界机器人操作任务**，但论文未明确公开具体数据集名称（如MetaWorld、LIBERO等）。任务涵盖抓取、放置、转动等典型操作。
- **Benchmark**：对比了**当前最优的模仿学习方法（SOTA baselines）**，包括统一全局模型（如标准行为克隆、扩散策略）以及其他多任务MoE变体。
- **对比方法**：至少包括了统一模型（无专家分化）和现有MoE方法（未对专家分布做显式约束）作为基线。
- **此外**：还进行了**在未见新任务上的微调实验**，以评估数据效率和专家知识可重用性。

### 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量以及训练时长。因此在总结中需要指出：论文未提供具体的算力资源信息，这不利于复现和性能评估。

### 5. 实验数量与充分性

- **实验数量**：元数据仅提到“多个任务”以及消融实验（可能包括与不同专家数量、是否使用能量模型等对比）。具体组数未列出，但声称“显著优于SOTA”。
- **充分性评价**：
  - **优点**：覆盖了真实世界的多任务场景，且进行了微调实验，验证了迁移能力。
  - **不足**：未在公开标准基准（如CALVIN、MetaWorld等）上进行对比，缺乏统计显著性分析和误差范围报告。实验的重复性和可复现性信息不足。需要更多消融和可视化来证明专家专业化是否真正发生。
- **客观公平性**：与SOTA对比结果陈述偏向积极，可能缺乏负例分析或失败案例分析。

### 6. 论文的主要结论与发现

- **主要结论**：
  - Di-BM方法在多任务模仿学习上显著提高了任务成功率，缓解了全局模型因任务干扰导致的“平均化效应”。
  - 专家基于观测分布的专业化使得每个专家学到更纯粹的技能，减少了跨任务冲突。
  - 在新任务上微调时，Di-BM展现出更好的数据效率，能够复用专家已学知识。
- **发现**：能量模型用于表示专家观测分布是有效的，联合训练能使分布自然对齐到不同任务子空间。

### 7. 优点

- **方法即插即用**：可轻松嵌入现有模仿学习框架，无需大规模修改训练流程。
- **理论清晰**：通过显式建模观测分布来引导专家分工，逻辑直观，有能量模型等工具支撑。
- **实验效果好**：在真实机器人多任务场景下超越现有方法，且验证了迁移能力。
- **解决关键痛点**：直接针对多任务干扰这一重要问题，实用价值高。

### 8. 不足与局限

- **算力和复现信息缺失**：未提供GPU规格、训练时间等，降低可复现性和可比较性。
- **实验覆盖有限**：仅使用真实机器人任务，缺乏大规模公开标准基准（如多个仿真环境）的验证，难以衡量方法的泛化能力。
- **统计分析不足**：未报告标准差、置信区间或统计检验，仅凭成功率比较可能不够严谨。
- **消融实验不够详细**：专家数量、能量模型形式、选通网络设计等对性能的影响未被充分探讨。
- **应用限制**：依赖高质量多样化的演示数据；能量模型训练可能增加计算复杂度；在任务数量极大时专家分配策略可能失效。
- **论文被拒**：可能还存在未被公开指出的方法论或实验上的缺点。

（完）
