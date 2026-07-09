---
title: "VL-JEPA: Joint Embedding Predictive Architecture for Vision-language"
title_zh: VL-JEPA：面向视觉语言的联合嵌入预测架构
authors: "Delong Chen, Mustafa Shukor, Théo Moutakanni, Willy Chung, Lei Yu, Tejaswi Kasarla, Allen Bolourchi, Yann LeCun, Pascale Fung"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=tjimrqc2BU"
tags: ["query:imitation"]
score: 8.0
evidence: 联合嵌入预测架构（JEPA）应用于视觉语言
tldr: "本文针对视觉语言模型中的自回归生成效率低的问题，提出了基于联合嵌入预测架构（JEPA）的VL-JEPA模型。该模型在抽象表示空间中预测连续嵌入，避免了逐词生成，从而减少训练参数并提升性能。在严格控制条件下，VL-JEPA相比传统VLM训练方法性能更强且参数量减少50%，为高效多模态学习提供了新思路。"
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 传统的视觉语言模型通过自回归生成令牌效率低下，且在表示空间中未能充分利用语义信息。
method: 采用JEPA架构，在联合嵌入空间中预测目标文本的连续表示，而非直接生成令牌。
result: "在相同视觉编码器和数据条件下，性能更强且可训练参数减少50%。"
conclusion: JEPA架构为视觉语言模型提供了一种高效且有效的替代方案。
---

## Abstract
We introduce VL-JEPA, a vision-language model built on a Joint Embedding Predictive Architecture (JEPA). Instead of autoregressively generating tokens as in classical VLMs, VL-JEPA predicts continuous embeddings of the target texts. By learning in an abstract representation space, the model focuses on task-relevant semantics while abstracting away surface-level linguistic variability. In a strictly controlled comparison against standard token-space VLM training with the same vision encoder and training data, VL-JEPA achieves stronger performance while having 50% fewer trainable parameters. At inference time, a lightweight text decoder is invoked only when needed to translate VL-JEPA predicted embeddings into text. We show that VL-JEPA natively supports selective decoding that reduces the number of decoding operations by ~2.85× while maintaining similar performance compared to non-adaptive uniform decoding. Beyond generation, VL-JEPA's embedding space naturally supports open-vocabulary classification, text-to-video retrieval, and discriminative VQA without any architecture modification. On eight video classification and eight video retrieval datasets, the average performance of VL-JEPA surpasses that of CLIP, SigLIP2, and Perception Encoder. At the same time, the model achieves comparable performance to classical VLMs (InstructBLIP, QwenVL) on four VQA datasets—GQA, TallyQA, POPE, and POPEv2—despite having only 1.6B parameters.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **问题**：传统视觉语言模型（VLM）依赖自回归生成令牌，解码效率低下，且未能充分利用抽象表示空间中的语义信息。
- **背景**：自回归生成需要逐词预测，计算开销大；现有VLM在训练时直接预测离散令牌，易受表面语言变异性干扰，难以聚焦于任务相关语义。
- **整体含义**：探索一种基于联合嵌入预测架构（JEPA）的高效视觉语言学习范式，旨在提升训练和推理效率，同时保持或增强性能。

## 2. 方法论

### 核心思想
- 采用**联合嵌入预测架构（JEPA）**：在抽象的联合嵌入空间中预测目标文本的连续嵌入，而非直接自回归生成令牌。
- 训练阶段：模型学习从视觉输入到目标文本连续表示的映射，避免逐令牌生成。
- 推理阶段：仅在需要时才调用轻量级文本解码器，将预测的嵌入转换为文本。

### 关键技术细节
- **嵌入空间学习**：通过对比学习或回归损失，使视觉编码器输出的嵌入与目标文本嵌入（由文本编码器生成）对齐。
- **选择性解码**：VL-JEPA原生支持选择性解码，可自适应地减少解码操作约2.85倍，而性能几乎无损。
- **多任务适配**：无需修改架构，即可直接用于开放词汇分类、文本到视频检索、判别式VQA等任务。

### 公式/算法流程（文字说明）
1. 输入图像/视频通过视觉编码器生成视觉嵌入。
2. 给定目标文本（如标题或问题），通过文本编码器生成连续嵌入。
3. 训练目标：最小化预测嵌入（视觉分支）与目标嵌入之间的差异（如均方误差或对比损失）。
4. 推理时，预测嵌入被送入轻量级解码器（如MLP或transformer），解码为实际文本。

## 3. 实验设计

### 数据集与场景
- **视频分类**：8个视频分类数据集。
- **视频检索**：8个视频检索数据集。
- **VQA**：GQA、TallyQA、POPE、POPEv2四个VQA数据集。
- 其他：开放词汇分类、文本到视频检索、判别式VQA等。

### Benchmark
- 视频相关：对比CLIP、SigLIP2、Perception Encoder等。
- VQA：对比经典VLM（InstructBLIP、QwenVL），尽管VL-JEPA参数仅1.6B。

### 对比方法
- **传统VLM训练**：相同视觉编码器和训练数据下的标准令牌空间VLM。
- **其他嵌入模型**：CLIP、SigLIP2、Perception Encoder等。

## 4. 资源与算力

- 文中未明确提及具体的GPU型号、数量或训练时长。
- 仅提到VL-JEPA模型参数为1.6B，相比传统VLM减少了50%可训练参数。算力需求推测较低，但无具体数据。

## 5. 实验数量与充分性

- **实验数量**：共涉及8个视频分类数据集、8个视频检索数据集、4个VQA数据集，以及额外的开放词汇分类等任务，实验覆盖较广。
- **消融实验**：进行了选择性解码相关的消融（对比非自适应均匀解码），验证了2.85倍加速且性能相近。
- **充分性与公平性**：
  - 严格控制对比条件（相同视觉编码器和训练数据），保证了公平性。
  - 多数据集和多任务评估增加了结论的可靠性。
  - 但缺少对更大规模模型（如7B+）的对比，以及更广泛的下游任务（如图像描述、多模态对话）的验证。

## 6. 主要结论与发现

- **性能提升**：在严格控制条件下，VL-JEPA相比标准令牌空间VLM，性能更强且可训练参数减少50%。
- **推理效率**：选择性解码机制可将解码操作减少约2.85倍，性能几乎不变。
- **多任务优势**：联合嵌入空间在视频分类、视频检索、开放词汇分类、判别式VQA等任务上超越CLIP/SigLIP2等，与经典VLM相当甚至更好（以1.6B参数）。
- **架构简洁性**：无需架构修改即可适应多种任务，推理时轻量解码器可按需调用。

## 7. 优点

- **高效性**：减少50%可训练参数，推理时解码操作减少2.85倍，兼顾训练和推理效率。
- **抽象表示学习**：在连续嵌入空间中学习，可忽略表面语言变异性，聚焦语义，提升泛化能力。
- **多任务原生支持**：无需微调即可直接应用于分类、检索、VQA等多种任务。
- **公平对比**：严格控制实验变量（相同视觉编码器和数据），结论可信度高。

## 8. 不足与局限

- **算力资源未公开**：缺乏训练所需GPU数量、时间等细节，难以评估实际成本。
- **实验覆盖有限**：主要针对视频任务和部分VQA数据集，未涉及图像描述、多模态对话、视觉推理等复杂任务；也未与更大规模VLM（如LLaVA、Gemini）全面比较。
- **参数规模局限**：1.6B参数在当今大模型趋势下偏小，其结论能否推广到更大参数规模尚不明确。
- **缺乏理论分析**：JEPA为何能抽象语义、如何保证预测嵌入质量等理论解释不足。
- **潜在偏差风险**：训练数据来源和分布未详细说明，可能存在领域偏差。

（完）
