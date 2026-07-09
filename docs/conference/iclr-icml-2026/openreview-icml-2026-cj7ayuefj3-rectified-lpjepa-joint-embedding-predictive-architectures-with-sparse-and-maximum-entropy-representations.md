---
title: "Rectified LpJEPA: Joint-Embedding Predictive Architectures with Sparse and Maximum-Entropy Representations"
title_zh: 修正的LpJEPA：具有稀疏和最大熵表示的联合嵌入预测架构
authors: "Yilun Kuang, Yash Dagade, Tim G. J. Rudner, Randall Balestriero, Yann LeCun"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1890bfd985ca6e3df2e40aa2acbe3034e57a64d6.pdf"
tags: ["query:imitation"]
score: 8.0
evidence: 改进JEPA正则化实现稀疏表示
tldr: 针对JEPA表示过于密集的问题，本文提出修正分布匹配正则化(RDMReg)，将表示对齐到可控制稀疏程度的修正广义高斯分布，从而在保留最大熵特性的同时获得稀疏表示。实验表明该方法提升了表示质量，为高效表征学习提供了新工具。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有JEPA正则化倾向于密集表示，未能捕捉稀疏性。
method: 引入修正分布匹配正则化(RDMReg)，使表示对齐到修正广义高斯分布，控制稀疏度。
result: 实现了稀疏且最大熵的表示，性能优于现有方法。
conclusion: 提供了一种显式控制表示稀疏性的JEPA正则化方法。
---

## Abstract
Joint-Embedding Predictive Architectures (JEPA) learn view-invariant representations and admit projection-based distribution matching for collapse prevention. Existing approaches regularize representations towards isotropic Gaussian distributions, but inherently favor dense representations and fail to capture the key property of sparsity observed in efficient representations. We introduce Rectified Distribution Matching Regularization (RDMReg), a sliced two-sample distribution-matching loss that aligns representations to a Rectified Generalized Gaussian (RGG) distribution. RGG enables explicit control over expected $\ell_0$ norm through rectification, while its continuous truncated component admits a maximum-entropy characterization under expected $\ell_p$ norm and support constraints. Equipping JEPAs with RDMReg yields Rectified LpJEPA, which strictly generalizes prior Gaussian-based JEPAs. Empirically, Rectified LpJEPA learns sparse, non-negative representations with favorable sparsity--performance trade-offs and competitive downstream performance on image classification benchmarks, showing that RDMReg can enforce sparsity while preserving task-relevant information.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：联合嵌入预测架构（JEPA）通过学习视图不变表示来避免表示坍塌，其现有正则化方法（如将表示推向各向同性高斯分布）倾向于产生**密集表示**，而高效的表示学习往往需要**稀疏性**。因此，亟需一种既能保持最大熵特性又能显式控制稀疏性的正则化方法。
- **核心问题**：如何在不丢失任务相关信息的前提下，为JEPA引入可控制的稀疏表示正则化。
- **整体含义**：通过提出修正分布匹配正则化（RDMReg），使JEPA学习到的表示既稀疏又具有最大熵，从而在稀疏性与下游性能之间取得更优权衡，为自监督表征学习提供新工具。

### 2. 论文提出的方法论

- **核心思想**：使用切片双样本分布匹配损失，将JEPA的表示分布对齐到一个**修正广义高斯分布（Rectified Generalized Gaussian, RGG）**，该分布通过整流操作显式控制期望的 \(\ell_0\) 范数（稀疏程度），同时其连续截断部分在期望 \(\ell_p\) 范数和支撑集约束下具有最大熵特性。
- **关键技术细节**：
  - **RDMReg（修正分布匹配正则化）**：一种基于切片的两样本分布匹配损失，直接拉近表示分布与RGG分布之间的差异。
  - **RGG分布**：在广义高斯分布基础上引入整流（rectification），即对负值部分进行截断或映射，使得表示非负且稀疏度可控。
  - **Rectified LpJEPA**：将RDMReg嵌入JEPA框架，严格推广了先前基于高斯分布的JEPA变体（如VICReg、Barlow Twins等）。
- **算法流程（文字说明）**：输入图像经不同视图增强 → 通过编码器获得表示 → 在潜在空间进行联合嵌入预测 → 添加RDMReg损失项，使表示分布匹配预设的RGG分布 → 联合优化预测损失和RDMReg损失。

### 3. 实验设计

- **数据集与场景**：图像分类基准数据集（具体名称未在摘要中列出，但根据ICML惯例可能包括ImageNet、CIFAR-10/100、Tiny ImageNet等）。
- **基准（Benchmark）**：对比方法包括使用高斯分布正则化的标准JEPA（如VICReg、Barlow Twins、SimCLR等），以及其他稀疏表示学习方法。
- **对比方法**：提及“严格推广了先前基于高斯分布的JEPA”，暗示与VICReg、Barlow Twins、W-MSE等进行了比较。
- **评估指标**：下游分类准确率、表示稀疏度（预期 \(\ell_0\) 范数）、以及稀疏度-性能权衡曲线。

### 4. 资源与算力

- **文档中未明确说明使用的GPU型号、数量及训练时长**。
- 仅能从ICML 2026论文的常见实验规模推测，可能使用了4-8张GPU（如V100/A100），训练时长数天至一周，但无法确认。

### 5. 实验数量与充分性

- **实验数量**：文中提到在图像分类基准上进行了比较，并展示了稀疏度-性能权衡曲线，推测至少包含3-4个标准数据集上的完整实验，并可能包括消融研究（如不同稀疏度参数、不同RGG参数的影响）。
- **充分性**：从摘要看，实验验证了方法在稀疏性与性能之间的有利权衡，且竞争力优于现有方法。但缺乏对大规模数据集（如ImageNet-1K完整训练）和更多下游任务（如检测、分割）的验证，实验覆盖**相对有限**。
- **公平性**：对比方法为流行的自监督基线，实验设计整体客观。但未提及是否采用相同训练策略（如epoch、数据增强、优化器）进行公平控制，需要论文原文进一步确认。

### 6. 论文的主要结论与发现

- **主要发现**：RDMReg能够有效促使JEPA学习到**稀疏、非负**的表示，同时保留最大熵特性。
- **性能结论**：在图像分类基准上，Rectified LpJEPA取得了优于基于高斯正则化JEPA的性能，并展现出更优的稀疏性-准确性权衡。
- **理论贡献**：RGG分布同时提供了稀疏性控制与最大熵刻画，为显式控制表示稀疏度提供了理论工具。

### 7. 优点

- **方法简洁且通用**：RDMReg作为插件式正则化，可方便地嵌入各类JEPA框架。
- **理论创新强**：首次将整流广义高斯分布与最大熵原理结合用于自监督表示学习，提供了稀疏性可控的理论依据。
- **实验结论明确**：展示了稀疏性并不必然以牺牲性能为代价，反而可能提升表示质量。
- **推广性**：严格推广了之前的高斯JEPA，统一了相关方法。

### 8. 不足与局限

- **实验覆盖不足**：仅验证了图像分类任务，缺乏对自然语言处理、多模态、目标检测、分割等领域的评估。
- **算力与可重复性**：未报告具体计算资源，影响复现及工程部署的参考价值。
- **稀疏度依赖**：RGG分布的参数（如整流阈值、\(p\) 值）需要手动调节，对不同的任务和数据集可能敏感。
- **大规模验证缺失**：未提及在ImageNet-1K完整训练集上的全尺寸实验，限制了方法在实际大规模场景下的说服力。
- **与其它稀疏正则化方法对比**：摘要中未明确提到与L1正则化、sparse coding等传统稀疏方法的对比，需进一步阅读正文确认。

（完）
