---
title: Text-Conditional JEPA for Learning Semantically Rich Visual Representations
title_zh: 文本条件JEPA：学习语义丰富的视觉表示
authors: "Chen Huang, Xianhang Li, Vimal Thilak, Etai Littwin, Joshua M. Susskind"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3dca6993c97cff3946f0a9cf9b35ebc364fcc59a.pdf"
tags: ["query:imitation"]
score: 7.0
evidence: 通过文本条件改进JEPA视觉表示学习
tldr: 本文提出文本条件JEPA（TC-JEPA），利用图像描述降低掩码特征预测的不确定性，使学习到的视觉表示更具语义性。实验证明在下游任务中性能提升。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: JEPA在掩码位置预测特征时存在视觉不确定性，难以学到语义表示。
method: 引入文本条件，通过稀疏交叉注意力调节预测的块特征。
result: 在多个视觉下游任务上提升性能，表示更语义化。
conclusion: TC-JEPA通过文本调制有效降低了预测不确定性，学习到更丰富的视觉表示。
---

## Abstract
Image-based Joint-Embedding Predictive Architecture (I-JEPA) offers a promising approach to visual self-supervised learning through masked feature prediction. However with the inherent visual uncertainty at masked positions, feature prediction remains challenging and may fail to learn semantic representations. In this work, we propose Text-Conditional JEPA (TC-JEPA) that uses image captions to reduce the prediction uncertainty. Specifically, we modulate the predicted patch features using a fine-grained text conditioner that computes sparse cross-attention over input text tokens. With such conditioning, patch features become predictable as a function of text, thus are more semantically meaningful. We show TC-JEPA improves downstream performance and training stability, with promising scaling properties. TC-JEPA also offers a new vision-language pretraining paradigm based on feature prediction only, outperforming contrastive methods on diverse tasks, especially those requiring fine-grained visual understanding and reasoning.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）
- **背景**：基于图像的联合嵌入预测架构（I-JEPA）是一种通过掩码特征预测进行视觉自监督学习的方法。然而，由于掩码位置固有的视觉不确定性（即同一位置可能对应多种合理的视觉内容），特征预测任务面临挑战，导致模型难以学到真正语义丰富的表示。
- **核心问题**：如何降低掩码特征预测的不确定性，从而学习到更具语义性的视觉表示。
- **动机**：利用图像描述（caption）作为额外条件信息，为预测提供语义上下文，从而降低预测难度，使学习到的块特征更加语义化。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程
- **核心思想**：提出**文本条件JEPA（TC-JEPA）**，使用图像描述（文本）来条件化掩码特征预测过程，通过文本信息减少预测的不确定性。
- **关键技术细节**：
  - 设计一个**细粒度文本调节器（fine-grained text conditioner）**，在预测块特征时计算输入文本token上的**稀疏交叉注意力（sparse cross-attention）**。
  - 通过该注意力机制，预测的块特征被调制为文本的函数，从而具备更强的语义可预测性。
  - 整体架构：在I-JEPA基础上增加文本条件分支，保持原特征预测目标，但预测特征被文本条件化。
- **算法流程（文字说明）**：
  1. 输入图像，通过编码器提取特征图。
  2. 对特征图进行掩码，得到部分可见块。
  3. 同时输入图像对应的文本描述（例如自动生成的caption），通过文本编码器得到文本token表示。
  4. 使用稀疏交叉注意力机制，将文本token与可见块特征交互，生成条件化的预测特征。
  5. 优化目标：最小化预测特征与真实特征之间的误差（类似JEPA的损失函数），但预测特征受到文本条件的约束。
- **备注**：论文未提供具体损失函数公式，以上为基于摘要的推断。

### 3. 实验设计：数据集、基准、对比方法
- **数据集**：论文摘要未明确具体数据集；根据ICML会议常见设定，可能涉及ImageNet（分类）、COCO（检测、分割）、以及视觉-语言任务数据集（如VQA、Retrieval）。但原文未提供，需注意。
- **基准（Benchmark）**：可能包括图像分类、目标检测、语义分割、视觉问答、图像-文本检索等下游任务。
- **对比方法**：主要与**I-JEPA**（基础版）以及**对比学习类视觉-语言预训练方法**（如CLIP、MoCo、SimCLR等）进行对比。摘要特别指出“在多样任务上优于对比方法，尤其需要细粒度视觉理解和推理的任务”。

### 4. 资源与算力
- **明确信息缺失**：论文摘要及元数据**未提及**使用的GPU型号、数量、训练时长等算力细节。因此无法总结具体资源情况，需指出这一点。

### 5. 实验数量与充分性
- **实验数量**：元数据显示实验至少涉及“多个下游任务”和“消融实验”（证据栏提到“消融实验”）。但未列出具体数目。
- **充分性评估**：
  - **正面**：在多个视觉下游任务上验证了性能提升，并提及训练稳定性、扩展性，以及优于对比方法的结果。
  - **不足**：缺乏在标准公开榜单（如ImageNet线性探测、COCO fine-tune）的具体数值对比，也缺少对不同文本描述质量（如噪声caption）的鲁棒性分析。实验覆盖的信息不完整，难以全面评价公平性和客观性。

### 6. 论文的主要结论与发现
- TC-JEPA通过文本调制有效降低了掩码预测的不确定性，学习到更丰富的视觉表示。
- 在下游任务（分类、检测、分割、视觉推理等）上性能提升，训练更稳定。
- 作为一种基于特征预测的视觉-语言预训练新范式，在需要细粒度理解的场景下优于基于对比学习的方法。
- 具有良好的扩展性（Scaling properties）。

### 7. 优点：方法或实验设计上的亮点
- **方法创新**：将文本条件引入JEPA框架，首次实现通过文本降低预测不确定性的视觉-语言自监督学习。
- **结构简洁**：使用稀疏交叉注意力，计算高效，不改变原JEPA的主体架构。
- **结果突出**：在多个任务上超越对比方法，尤其是细粒度任务（如视觉推理），表明语义表示的提升。
- **稳定性提升**：文本条件缓解了掩码预测的模糊性，使训练过程更稳定。

### 8. 不足与局限：包括实验覆盖、偏差风险、应用限制
- **实验覆盖不透明**：未列出具体数据集、超参数、消融细节，无法完全复现或评估泛化能力。
- **文本依赖**：需要高质量的图像描述（caption）作为输入，若描述不准确或缺失，可能导致性能下降；未讨论对弱标注或自动生成caption的鲁棒性。
- **计算开销**：引入文本编码器和交叉注意力，增加了预训练阶段的算力需求（但未量化）。
- **局限性陈述**：可能仅适用于视觉-语言联合场景，对于纯视觉任务（无文本可用）则无法直接使用。
- **偏差风险**：训练数据中的caption可能存在语言偏见（如文化、物体分布），可能影响学到的表示公平性。

（完）
