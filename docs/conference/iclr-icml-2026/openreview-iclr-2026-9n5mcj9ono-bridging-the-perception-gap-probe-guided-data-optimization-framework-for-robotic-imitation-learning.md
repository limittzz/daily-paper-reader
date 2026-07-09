---
title: "Bridging the Perception Gap: Probe-Guided Data Optimization Framework for Robotic Imitation Learning"
title_zh: 弥合感知差距：探针引导的机器人模仿学习数据优化框架
authors: "Xueqi Li, Zheng Wang, Han Xue, Xiaotong Liu, Zhaofengnian Wang, Guo-Ping Liu, Cewu Lu, Nanyang Ye"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=9n5mcj9onO"
tags: ["query:imitation"]
score: 9.0
evidence: 机器人模仿学习数据优化
tldr: 针对人机感知差距导致模仿学习失败的问题，本文提出策略意图探针(PIP)量化模型感知，划分鲁棒操作区，标准化非鲁棒区轨迹。该方法在不增加模型复杂度的情况下优化数据分布，显著提升机器人模仿学习在真实场景中的表现。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 传统数据收集未考虑人机感知差距，导致模仿学习性能不佳。
method: 提出策略意图探针(PIP)，量化模型感知能力，划分鲁棒操作区与欠鲁棒区，标准化轨迹。
result: 有效提升了模仿学习的实际执行效果。
conclusion: 通过数据优化弥补感知差距，提高了模仿策略的鲁棒性。
---

## Abstract
Imitation learning allows robots to acquire complex manipulation skills from human demonstrations. However, traditional data collection methods often haven't account for the "perceptual gap" between humans and robots, which leads to models that don't perform as expected. To solve this, we introduce Policy-Intent Probe (PIP). This method trains a proxy model with a small amount of demonstration data, then quantifies the model's perceptual capabilities based on its policy distribution. Based on the model's feedback, we divide the task's operational space into a Robust Operation Zone (ROZ) and a Non-Robust Operation Zone (NROZ). By standardizing the trajectories in the NROZ and waiting until the robot enters the ROZ to perform precise operations, we have optimized the data collection trajectories. Furthermore, aided by PIP, we can correctly carry out subtask segmentation. We can supplement data collection based on subtask complexity, which enhances the model's generalization and robustness. By cleaning only the subtask data containing anomalous trajectories or failure, we minimize data loss. Based on an empirical evaluation on three real-world tasks, we proved that perceptual capabilities can affect a task's success rate and that arbitrary subtask decomposition can lead to negative consequences. Our model-in-the-loop data optimization framework can significantly boost the success rate of long-horizon precision manipulation tasks, enhance model robustness, and increase data collection efficiency.

---

## 论文详细总结（自动生成）

好的，以下是对该论文的详细中文总结。

---

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：传统机器人模仿学习的数据收集方法忽略了人类演示者与机器人之间的“感知差距”（Perceptual Gap）。人类依赖丰富的感官和上下文理解执行任务，而机器人仅基于有限的传感器输入和特征空间进行决策，导致从人类轨迹中习得的策略在机器人实际执行时性能不佳。
- **研究动机**：现有工作多聚焦于模型架构或算法改进，而较少关注数据收集阶段本身引入的感知不匹配。本文试图从数据优化层面弥合该差距，提升模仿学习的鲁棒性和成功率。
- **整体含义**：提出一种模型在环的数据优化框架，通过量化机器人对任务状态的“感知程度”，动态调整演示轨迹，从而在不增加模型复杂度的情况下，仅通过优化数据分布来提升策略性能。

---

### 2. 论文提出的方法论

- **核心思想**：利用一个轻量的代理模型（Proxy Model）——策略意图探针（Policy-Intent Probe, PIP）来近似机器人模型的感知能力。基于PIP的策略分布，将任务操作空间划分为**鲁棒操作区（Robust Operation Zone, ROZ）**和**非鲁棒操作区（Non-Robust Operation Zone, NROZ）**。随后对原始演示数据进行标准化处理：将NROZ内的轨迹部分进行归一化或等待，直到机器人进入ROZ后再执行精确操作，从而规避感知模糊区域。
- **关键技术细节**：
  - **策略意图探针（PIP）训练**：使用少量演示数据训练一个简单代理模型，使其能够输出给定状态下的策略分布（如动作的概率分布）。
  - **区域划分**：根据PIP在状态空间中的预测置信度或熵值，将高置信度、低不确定性的区域标记为ROZ，反之为NROZ。
  - **轨迹标准化**：对于原始演示中穿越NROZ的部分，进行轨迹平滑、速度调整或插入等待动作，确保机器人执行时仅在有把握的区域（ROZ）进行精细操作。
  - **子任务分割与数据补充**：借助PIP的反馈，自动将长时程任务分解为子任务，并根据各子任务的复杂度（NROZ占比）有选择地补充演示数据，同时仅清理包含异常轨迹或失败的子任务数据，最小化数据损失。
- **算法流程（文字说明）**：
  1. 收集少量人类演示轨迹。
  2. 利用轨迹训练PIP代理模型。
  3. 在每个状态下运行PIP，计算策略分布的不确定性，划分ROZ/NROZ。
  4. 对原始轨迹进行后处理：在NROZ内执行标准化（如等待、减速），在ROZ内保留精确动作。
  5. 根据PIP进行子任务自动切分，对高复杂度的子任务补充额外演示。
  6. 使用优化后的数据集训练最终模仿学习策略。

---

### 3. 实验设计

- **数据集/场景**：在**三个真实世界任务**（real-world tasks）上进行评估，具体任务名称未在摘要中列出，但属于“长时程精密操作任务”（long-horizon precision manipulation tasks）。
- **Benchmark**：论文未明确指出与哪些公开基准（如RoboTurk, RLBench）进行对比。实验设计主要是**消融研究**和**自身对比**：对比有无PIP数据优化、有无子任务分割、原始数据 vs. 优化数据。
- **对比方法**：摘要中未提及与现有模仿学习或数据增强方法（如DAgger、行为克隆、逆强化学习）的直接比较，主要验证框架的有效性。

---

### 4. 资源与算力

- **未明确说明**：论文摘要及提供的文本中未提及所使用的GPU型号、数量、训练时长等算力信息。这一缺失限制了可重复性分析。

---

### 5. 实验数量与充分性

- **实验数量**：仅提及“三个真实世界任务”，未列出每个任务的具体实验轮次、成功率标准差等统计细节。可能包含消融实验（如是否使用PIP、是否进行子任务分割），但数量有限。
- **充分性与公平性**：
  - **优势**：在真实机器人上验证，具有一定说服力。
  - **不足**：
    - 缺乏与现有SOTA方法的定量对比，难以评估相对提升幅度。
    - 实验场景数量少（3个），且任务类型可能集中于特定操作（如拾取、放置、拧螺丝等），泛化性未充分验证。
    - 未提供统计显著性检验或多次重复实验的详细结果。

---

### 6. 论文的主要结论与发现

- 感知能力（PIP量化的不确定性）与任务成功率之间存在直接关联：NROZ占比高的子任务成功率显著低于ROZ。
- 任意子任务分解（未考虑感知差异）可能导致负面效果，反而降低整体性能。
- 所提出的模型在环数据优化框架（PIP + 轨迹标准化 + 子任务补充）能够显著提升长时程精密操作任务的成功率、增强模型鲁棒性，并提高数据收集效率（仅清洁异常子任务数据，减少数据浪费）。

---

### 7. 优点

- **方法新颖性**：首次将“感知差距”概念引入数据收集阶段，并通过轻量代理模型量化机器人的感知不确定性，思路简洁且可解释性强。
- **模型无关性**：框架不要求修改最终策略模型，仅优化数据分布，易于集成到现有模仿学习流程。
- **数据高效**：通过子任务级别的数据补充与清理，避免了全数据集重收集，提升了数据利用效率。
- **实验验证真实**：在真实机器人平台上进行测试，贴近实际应用场景。

---

### 8. 不足与局限

- **实验覆盖不足**：
  - 仅三个真实任务，缺乏公开数据集（如RLBench、MetaWorld）上的定量比较，难以客观评估通用性。
  - 未与现有数据增强方法（如DAgger、噪声注入、随机化）进行对比，壁垒性不明确。
- **偏差风险**：PIP代理模型本身可能引入偏差，若其划分的ROZ/NROZ不准确，反而会降低策略性能。论文未讨论代理模型错误传播的影响。
- **应用限制**：方法依赖手动或半自动的子任务定义，对于非结构化任务（如自由探索）可能不适用；等待/标准化操作可能改变任务时间特性，在某些实时性要求高的场景不可行。
- **资源与复现**：未提供算力信息，未开源代码或数据集（据摘要未提及），复现困难。
- **理论分析缺失**：缺乏对感知差距的数学定义或收敛性保证，经验分析为主。

---

（完）
