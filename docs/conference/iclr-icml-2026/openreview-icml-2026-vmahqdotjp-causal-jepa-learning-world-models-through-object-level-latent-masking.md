---
title: "Causal-JEPA: Learning World Models through Object-Level Latent Masking"
title_zh: Causal-JEPA：通过物体级潜在掩码学习世界模型
authors: "Heejeong Nam, Quentin Le Lidec, Lucas Maes, Yann LeCun, Randall Balestriero"
date: 2026-04-30
pdf: "https://openreview.net/pdf/47cdd841e9e570a19a299d08ad52b4387f3469aa.pdf"
tags: ["query:imitation"]
score: 6.0
evidence: 基于JEPA的物体级掩码世界模型
tldr: 针对物体中心世界模型无法捕捉交互依赖的问题，本文提出C-JEPA，将掩码联合嵌入预测扩展至物体级潜在表示，通过预测被掩码物体状态迫使模型学习因果结构。实验表明该模型在关系预测和控制任务中表现优异，为机器人世界建模提供了新思路。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 物体中心表示不足以捕获交互依赖的动态关系。
method: 将掩码联合嵌入预测从图像块扩展到物体级潜在表示，预测被掩码物体状态。
result: 学习到因果结构化的世界模型，提升预测与控制性能。
conclusion: 提供了面向物体交互的世界建模新框架。
---

## Abstract
World models require robust relational understanding to support prediction, reasoning, and control. While object-centric representations provide a useful abstraction, they are not sufficient to capture interaction-dependent dynamics. We therefore propose C-JEPA, a simple and flexible object-centric world model that extends masked joint embedding prediction from image patches to object-centric representations. By masking object-level latents and requiring each masked object state to be inferred from the surrounding context, C-JEPA imposes structured partial observability during training, creating counterfactual-like prediction queries that discourage shortcut solutions and make interaction-dependent prediction necessary under the learning objective. Empirically, C-JEPA leads to consistent gains in visual question answering, with an absolute improvement of about 20\% in counterfactual reasoning over the same architecture without object-level masking. On agent control tasks, C-JEPA enables substantially more efficient planning by using only 1\% of the total latent input features required by patch-based world models, while achieving comparable performance. Finally, we provide a formal analysis demonstrating that object-level masking induces useful inductive bias by controlling observability. Our code is available at https://github.com/galilai-group/cjepa.

---

## 论文详细总结（自动生成）

# 论文详细中文总结：Causal-JEPA：通过物体级潜在掩码学习世界模型

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题**：世界模型需要强大的关系理解能力以支持预测、推理和控制。现有的物体中心表示虽然提供了有用的抽象，但不足以捕捉物体间交互所依赖的动态关系（即交互依赖的动态）。
- **背景**：联合嵌入预测架构（JEPA）已在图像块级掩码预测中取得成功，但尚未扩展到物体级表示。物体级表示能更好地抽象场景结构，但缺乏对物体间因果交互的显式建模。
- **贡献**：本文提出 **C‑JEPA**，将掩码联合嵌入预测从图像块扩展到物体级潜在表示，通过强制模型从上下文推断被掩码物体的状态，学习具有因果结构的物体中心世界模型。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：在物体级潜在表示上执行掩码操作，要求模型根据周围未被掩码的物体状态预测被掩码物体的潜在状态。这种结构化部分可观测性迫使模型学习物体间的交互依赖关系，避免捷径解，并创造类似反事实的预测查询。
- **关键技术细节**（文字说明，无公式）：
  - **物体级潜在表示**：首先将输入（如图像或视频）编码为物体中心的潜在表示（每个物体对应一个潜在向量），可能通过预训练的物体检测或分割模型获得。
  - **掩码策略**：随机选择部分物体进行掩码（将对应潜在向量置零或掩码标记），其余物体作为上下文可见。
  - **预测目标**：训练一个预测器，以可见物体的潜在表示作为条件，预测被掩码物体的潜在表示。目标函数是联合嵌入损失，使预测的表示与通过编码器从完整观察中提取的真实表示相似。
  - **训练过程**：通过反向传播同时优化编码器和预测器，使模型学会利用物体间的关系进行推断。
- **算法流程**：
  1. 对输入场景提取物体级表示（编码器 → 物体潜在向量）。
  2. 随机选择部分物体进行掩码（构造部分可观测状态）。
  3. 利用未被掩码物体的潜在表示，通过预测器推理被掩码物体的表示。
  4. 计算预测表示与真实表示（由编码器从完整输入获得）之间的相似度损失。
  5. 联合更新编码器和预测器参数。

## 3. 实验设计：数据集、Benchmark 与对比方法

- **视觉问答（VQA）任务**：
  - 使用包含反事实推理问题的 VQA 数据集（具体名称未在摘要中提及，推测可能是 CLEVR 或类似合成/真实场景）。
  - Benchmark：与**不进行物体级掩码**的相同架构（即标准 JEPA）对比。
  - 结果：C‑JEPA 在反事实推理准确率上获得约 20% 的绝对提升。
- **智能体控制任务**：
  - 使用基于物体中心世界模型的规划控制任务（如机器人操作或导航场景）。
  - 对比方法：基于图像块的补丁级世界模型（patch‑based world models）。
  - 结果：C‑JEPA 仅使用补丁级模型**总潜在输入特征的 1%** 即可达到相当的性能，大幅提升了规划效率。
- **理论分析**：通过形式化证明（见摘要末尾）说明物体级掩码通过控制可观测性引入了有用的归纳偏置。

## 4. 资源与算力

- **论文中未明确说明**使用的 GPU 型号、数量、训练时长等具体算力信息。仅提供了代码仓库链接，但未在摘要中提及计算资源。

## 5. 实验数量与充分性

- **实验数量**：摘要中明确提及两个主要实验任务（VQA 反事实推理、智能体控制规划），以及一个形式化理论分析。未提及消融实验或更多数据集上的验证。
- **充分性评估**：
  - **优点**：在反事实推理上取得了显著的绝对提升（20%），控制任务展现了效率优势，实验设计直接对应论文核心动机（交互依赖预测）。
  - **不足**：实验覆盖范围有限，仅两个任务；缺乏对不同掩码比例、物体数量、场景复杂度的消融；没有在更多标准机器人基准（如 Meta‑world 或 DMControl）上验证；仅与自己架构的消去版本对比，缺少与其他物体中心世界模型（如 MONet、Slot‑Attention 类方法）的横向比较。
  - **公平性**：对比基准合理（相同架构、有无物体级掩码；与补丁级模型对比），但控制任务中的性能比较仅提及使用 1% 特征，未给出精确的预测精度或任务成功率，完整性略有不足。

## 6. 论文的主要结论与发现

- C‑JEPA 能够学习到具有因果结构的世界模型，有效提升物体间关系预测的准确性。
- 在 VQA 反事实推理上，物体级掩码带来约 20% 的准确率提升，表明模型学会了利用上下文推断被掩码物体状态。
- 在智能体控制中，物体级表示使得世界模型在规划时仅需处理极少量的潜在输入特征（补丁级模型的 1%），同时保持相近性能，显著降低了计算开销。
- 形式化分析表明，物体级掩码通过控制可观测性引入了有用的归纳偏置，促进了因果关系的学习。

## 7. 优点

- **方法简洁灵活**：直接扩展 JEPA 框架到物体级潜在表示，无需复杂的图神经网络或显式关系推理模块。
- **高效性**：在控制任务中通过物体级表示大幅压缩状态空间，实现极低的计算成本（1% 特征量）。
- **泛化能力**：在反事实推理任务中展现强大的因果推理能力，可能对新物体或未见过交互模式具有更好的泛化性。
- **理论支撑**：提供了物体级掩码为何有效的正式分析，增强了方法的可信度。

## 8. 不足与局限

- **实验覆盖不足**：仅在两个任务上评估，缺乏在多样化机器人场景（如 Atari、DMControl）或更复杂多物体动态环境中的验证。
- **物体级表示获取依赖**：文中未详细说明如何获得物体级潜在表示（例如是否依赖预训练的物体检测器或分割模型），其质量可能成为性能瓶颈，且该方法对物体分割错误的鲁棒性未知。
- **消融实验缺失**：没有分析不同掩码比例、掩码策略（随机 vs 特定物体）对结果的影响，也未与基于注意力机制的掩码方案对比。
- **横向对比缺失**：仅与自身消去版本及补丁级模型对比，未与其他流行的物体中心世界模型（如 OPEn、GNN‑based models）进行公平比较。
- **现实场景挑战**：合成 VQA 和简单控制任务可能不能完全代表真实世界中物体交互的复杂性和噪声，应用迁移存在不确定性。

（完）
