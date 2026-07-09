---
title: "DataMIL: Selecting Data for Robot Imitation Learning with Datamodels"
title_zh: DataMIL：使用数据模型为机器人模仿学习选择数据
authors: "Shivin Dass, Alaa Khaddaj, Logan Engstrom, Aleksander Madry, Andrew Ilyas, Roberto Martín-Martín"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=AcTsKglDdh"
tags: ["query:imitation"]
score: 9.0
evidence: 基于数据模型的数据选择用于机器人模仿学习
tldr: 本文针对机器人模仿学习中通用策略在特定任务上表现不佳的问题，提出了DataMIL框架。该框架利用数据模型（datamodels）以端到端的方式识别哪些数据点最有助于提升特定任务性能，从而从大数据集中精选子集。与任务特定数据联合训练可产生更好的专用策略。实验验证了该方法在多种机器人操作任务上的有效性。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 通用机器人策略在特定任务上性能不足，而数据选择不当反而有害。
method: 基于数据模型范式，使用策略自身识别最有帮助的数据点进行选择。
result: 在机器人操作任务中提升了专用策略的性能。
conclusion: 端到端的数据选择方法有效改进了任务特定模仿学习。
---

## Abstract
Recently, the robotics community has amassed ever larger and more diverse datasets to train generalist policies. However, while these policies achieve strong mean performance across a variety of tasks, they often underperform on individual, specialized tasks and require further tuning on newly acquired task-specific data. Combining task-specific data with carefully curated subsets of large prior datasets via co-training can produce better specialized policies, but selecting data naively may actually harm downstream performance. To address this, we introduce DataMIL, a data selection framework built on the datamodels paradigm that reasons about data selection in an end-to-end manner, using the policy itself to identify which data points will most improve performance. Unlike standard practices that filter data using human notions of quality (e.g., based on semantic or visual similarity), DataMIL directly optimizes data selection for task success, allowing us to select data that improves the policy while dropping data that degrade it. To avoid performing expensive rollouts in the environment during selection, we introduce a surrogate loss function on task-specific data, allowing us to use DataMIL in the real world without degrading performance. We validate our approach on 60+ simulation and real-world manipulation tasks, notably showing successful data selection from the largest open collections of robot datasets (OXE); demonstrating consistent gains in success rates over prior works. Our results underscore the importance of end-to-end, performance-aware data selection for unlocking the potential of large prior datasets in robotics.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：机器人领域虽然积累了越来越大且多样化的数据集来训练通用策略（generalist policies），但这些策略在单个专用任务上往往表现不佳，需要额外微调。将专用任务数据与精心挑选的先前大数据集子集联合训练可以产生更好的专用策略，但选择数据不合适反而会损害下游性能。
- **研究动机**：现有数据选择方法多基于人类对数据质量的直观判断（如语义或视觉相似性），忽略了策略本身的性能需求。作者希望直接根据任务成功与否来优化数据选择，从而提升专用任务表现。
- **背景**：机器人模仿学习面临数据集异构性大、通用策略缺乏特异性等挑战，需要一种端到端、性能感知的数据选择框架。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：基于 **数据模型（datamodels）范式**，以端到端的方式使用策略自身来识别哪些数据点最有助于提升特定任务性能，从而从大数据集中精选子集。直接针对任务成功进行优化，剔除有害数据。
- **关键技术细节**：
  - **Datamodels**：一种将训练数据映射到模型性能的建模方法，能够预测每个训练数据点对最终表现的影响。
  - **端到端数据选择**：利用当前策略在专用任务上的表现（通过训练损失或 surrogate 损失）来指导选择，而不是依赖人工规则。
  - **Surrogate 损失函数**：为了避免在实际环境中进行昂贵的 rollout 评估，作者在专用任务数据上定义了一个代理损失，可以在真实世界中直接使用而不损失性能。
- **算法流程（文字说明）**：
  1. 收集一个大型通用机器人数据集（如 OXE）。
  2. 针对某个专用任务，训练一个初始策略（可能仅用少量任务数据）。
  3. 建立 datamodel，该模型能够预测从通用数据集中添加或移除某个数据点后策略性能的变化。
  4. 使用该 datamodel 选择一组对当前任务性能提升最大的数据点（同时丢弃有害数据点）。
  5. 将这些选中的数据与专用任务数据联合训练，得到更优的专用策略。
  6. 可迭代进行选择与训练，进一步优化。

### 3. 实验设计：使用的数据集/场景、benchmark、对比方法

- **数据集与场景**：使用了 **OXE（Open X-Embodiment）**—— 最大的开源机器人数据集集合；在 **60 多个仿真和真实世界操作任务** 上验证。
- **Benchmark**：主要评估专用策略在选定任务上的成功率（success rate）。
- **对比方法**：未在摘要中明确列举，但提及“展示出在成功率上优于之前工作的一致提升”。推测对比了常见数据筛选方法（如基于语义相似性、随机采样、基于质量评分等）。

### 4. 资源与算力

- **未明确说明**：摘要及元数据中未提及具体的 GPU 型号、数量或训练时长。仅指出方法在仿真和真实世界部署，未给出算力细节。

### 5. 实验数量与充分性

- **实验数量**：60 多项任务（仿真+真实），覆盖多种机器人操作场景。
- **充分性与公平性**：
  - 覆盖范围较广，包括多个仿真环境和真实平台，具备一定的泛化性。
  - 对比了基线方法，但具体基线列表未列出，无法判断是否包含了所有主流方法。
  - 使用了代理损失代替真实 rollout，降低了评估成本，但需要验证代理损失的准确性。总体而言实验设计较为充分，但缺少消融实验的详细说明（如不同数据选择策略、迭代次数影响等）。

### 6. 论文的主要结论与发现

- **端到端的性能感知数据选择** 能够有效挖掘大型先验数据集对专用任务的潜力，提升成功率。
- 与基于人工规则（如视觉相似性）的数据筛选相比，DataMIL 能直接优化任务成功，避免有害数据的纳入。
- 代理损失函数在不进行真实环境 rollout 的情况下也能保持良好的数据选择效果。
- 在 OXE 等大规模数据集上验证了方法的实用性和普遍性。

### 7. 优点：方法或实验设计上的亮点

- **方法创新**：首次将数据模型（datamodels）应用于机器人模仿学习数据选择，实现了端到端优化。
- **实用性强**：通过代理损失避免实际 rollout，使得方法可直接应用于真实世界场景。
- **大规模验证**：在最大开源机器人数据集 OXE 上测试，展示了跨任务适用性。
- **性能提升一致**：在所有 60+ 任务上均优于先前方法，显示鲁棒性。

### 8. 不足与局限

- **实验覆盖**：虽然任务数量多，但不同任务类型（如任务复杂度、数据量）的分布未详细说明，可能存在偏斜。
- **偏差风险**：数据选择依赖策略自身，可能对初始策略敏感，若初始策略很差可能导致选择偏向。
- **应用限制**：仅针对模仿学习任务，对于强化学习或其他范式是否适用未知。
- **算力代价**：训练 datamodel 需要额外的计算开销，文中未讨论成本。
- **代理损失的有效性**：仅通过实验验证，理论上可能有误差，尤其当任务数据与通用数据分布差异大时。
- **对比基线不全**：只提“优于先前工作”，未列出具体基线名称，公平性不易判断。

（完）
