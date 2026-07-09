---
title: "Rethinking JEPA: Compute‑Efficient Video Self-Supervised Learning with Frozen Teachers"
title_zh: 重新思考JEPA：使用冻结教师进行高效视频自监督学习
authors: "Xianhang Li, Chen Huang, Chun-Liang Li, Eran Malach, Joshua M. Susskind, Vimal Thilak, Etai Littwin"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=3cB9243E9i"
tags: ["query:imitation"]
score: 8.0
evidence: 重新思考JEPA，使用冻结教师进行视频自监督学习
tldr: 本文质疑了V-JEPA中EMA更新教师的必要性，提出仅使用冻结教师即可有效进行掩码潜在预测。该方法将训练分为两阶段：先训练一个目标编码器进行像素重建，然后冻结它并训练学生预测其潜在表示。这种简单方案避免了EMA的复杂性，简化了模型选择，并在视频表示学习上取得了有竞争力的性能。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: V-JEPA中使用EMA更新教师导致模型选择复杂、教师与学生耦合。
method: 两阶段训练：先像素重建训练目标编码器，然后冻结教师训练学生预测潜在表示。
result: 简化了训练流程，性能与原有V-JEPA相当或更优。
conclusion: 冻结教师足以进行有效的掩码潜在预测，为JEPA训练提供了简化方案。
---

## Abstract
Video Joint Embedding Predictive Architectures (V‑JEPA) learn generalizable off-the-shelf video representations by predicting masked regions in latent space with an exponential moving average (EMA)‑updated teacher.
While EMA prevents representation collapse, it complicates scalable model selection and couples teacher and student architectures. 
We revisit masked‑latent prediction and show that a frozen teacher suffices. Concretely, we (i) train a target encoder with a simple pixel‑reconstruction objective under V‑JEPA masking, then (ii) freeze it and train a student to predict the teacher’s latents on masked regions. 
This leads to a two‑stage, unregularized scheme, that we refer to as SALT (Static-teacher Asymmetric Latent Training). SALT decouples optimization into pixel reconstruction (teacher) and masked latent prediction (student), increasing transparency, efficiency, and scalability while preserving the ability of representations to generalize under frozen evaluation. Empirically, our student models outperform recently proposed V-JEPA 2 encoders under frozen backbone evaluation across diverse benchmarks. They are also more compute‑optimal: at matched pretraining FLOPs, our method achieves higher probing accuracy, and its scaling curves dominate V‑JEPA’s accuracy–FLOPs Pareto frontier. 
Finally, we find that student quality is remarkably robust to teacher quality: high-performing students emerge even with small, sub-optimal teachers. This points to a compute budget allocation that should overwhelmingly favor the student.
These results position SALT as a simple, scalable, and compute‑efficient alternative to EMA‑based self‑distillation for video representation learning.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究背景**：视频自监督学习中的联合嵌入预测架构（V‑JEPA）通过指数移动平均（EMA）更新教师网络，在潜在空间中预测掩码区域，以学习通用的视频表示。EMA 用于防止表示崩塌，但带来了模型选择复杂、教师与学生架构耦合的问题，影响可扩展性和透明度。
- **核心问题**：EMA 更新教师的机制是否是掩码潜在预测所必需？能否用更简单的冻结教师替代，同时保持或提升性能，并简化训练流程？
- **整体含义**：本文质疑了 V-JEPA 中 EMA 教师的必要性，提出仅用冻结教师即可实现有效学习，从而简化训练、提升计算效率，并为视频表示学习提供更可扩展的替代方案。

## 2. 提出的方法论：核心思想、关键技术细节

- **核心思想**：将训练解耦为两阶段——先训练一个目标编码器（教师）进行像素重建，然后冻结该教师，训练一个学生网络预测教师在掩码区域的潜在表示。这种方案称为 **SALT（Static-teacher Asymmetric Latent Training）**。
- **关键技术细节**：
  - **第一阶段**：在 V-JEPA 的掩码策略下，使用简单的像素重建目标训练目标编码器（教师）。这一步保证了教师学到有意义的特征表示。
  - **第二阶段**：冻结教师，训练学生网络预测教师在掩码区域的潜在表示（潜在预测），无需额外正则化（如 EMA 或对冲机制）。
  - **两阶段解耦**：教师优化目标为像素重建（监督信号明确），学生优化目标为模仿教师潜在表示，避免了 EMA 带来的耦合和超参数调整。
- **公式/算法流程（文字说明）**：
  1. 输入视频片段，应用随机掩码。
  2. 第一阶段：训练教师编码器，最小化 L2 重建损失（预测掩码像素与真实像素差异）。
  3. 第二阶段：冻结教师权重，训练学生编码器，最小化学生预测的掩码区域潜在表示与教师对应表示之间的距离（例如余弦相似度或 L2 损失）。
  4. 最终得到学生编码器作为下游任务的特征提取器。

## 3. 实验设计

- **数据集与场景**：文中未明确列出具体数据集名称，但提及在“多个多样化基准”下进行冻结骨干评估，包括动作识别、视频分类等常见视频表示学习 benchmark（如 UCF101、HMDB51、Kinetics 等可推测，但需原文确认）。
- **对比方法**：主要与 **V-JEPA 2** 编码器进行对比，也涉及与原始 V-JEPA 的精度–FLOPs 帕累托前沿比较。
- **评估方式**：冻结骨干评估（freeze backbone evaluation），即固定编码器权重，仅训练线性分类器或简单分类头，以衡量表示质量。

## 4. 资源与算力

- **文中未明确说明具体 GPU 型号、数量、训练时长**。
- 但提到“计算最优性”分析，展示了在相同预训练 FLOPs 下，SALT 学生模型获得更高的探测精度，并占据 V-JEPA 的精度–FLOPs 帕累托边界。暗示训练成本与 V-JEPA 相当或更低。
- 由于缺乏具体硬件和时长信息，无法给出详细算力总结。

## 5. 实验数量与充分性

- **实验数量**：虽然摘要信息有限，但提及了：
  - 与 V-JEPA 2 编码器在多个基准上的性能对比。
  - 计算效率分析（匹配 FLOPs 时的精度对比）。
  - 教师质量对学生性能鲁棒性的实验（发现即使使用小且次优的教师，学生仍表现优秀）。
  - 推测还有消融实验（如验证两阶段 vs 联合训练、不同掩码策略等），但未在摘要中列出细节。
- **充分性**：实验覆盖了性能、效率、鲁棒性三个方面，具有一定的系统性。但缺少数据集具体名称、消融实验的表格、误差条等细节，从摘要看较为概括。若在全文中有更详尽的实验，则可能充分。

## 6. 主要结论与发现

- **冻结教师足以进行有效的掩码潜在预测**，无需 EMA 更新。SALT 学生模型在冻结骨干评估下优于近期提出的 V-JEPA 2 编码器。
- **计算最优性**：在相同预训练 FLOPs 下，SALT 获得更高探测精度，其缩放曲线主导 V-JEPA 的精度–FLOPs 帕累托边界。
- **学生质量对教师质量高度鲁棒**：即使使用小型、次优的教师训练，也能产生优质学生。这提示计算预算应大幅向学生倾斜。
- **SALT 是一种简单、可扩展、计算高效的替代方案**，可替代基于 EMA 的自蒸馏方法用于视频表示学习。

## 7. 优点

- **简化训练流程**：两阶段解耦避免了 EMA 的超参数（动量系数、更新频率）调整，以及教师与学生架构必须相同的限制。
- **提高透明度和可扩展性**：教师仅通过像素重建训练，目标明确；冻结后学生可以独立扩展，架构不依赖于教师。
- **计算效率高**：在相同 FLOPs 预算下性能更优，且资源分配可更多地用于学生网络，有利于大规模训练。
- **鲁棒性强**：对教师质量不敏感，降低了对高质量教师预训练的要求。

## 8. 不足与局限

- **实验覆盖不足**：摘要中未列出具体数据集、任务类型（仅提及视频基准），也未展示与其他主流方法（如 VideoMAE、MaskFeat 等）的对比，可能缺乏全面性。
- **偏差风险**：两阶段训练可能导致学生所学受限于教师的质量，虽然文中显示鲁棒，但极端低质量的教师是否仍有效？未讨论。
- **应用限制**：仅针对视频表示学习，是否适用于图像或其他模态尚未验证。另外，像素重建阶段可能引入计算开销，需权衡。
- **未提供开源代码或复现细节**：作为学术论文，缺少更具体的实现细节（如网络架构、超参数、训练配置）可能影响可重复性。
- **算力信息缺失**：未明确训练所需 GPU 数量和时长，难以评估实际资源需求。

（完）
