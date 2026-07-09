---
title: "LLM-JEPA: Large Language Models Meet Joint Embedding Predictive Architectures"
title_zh: LLM-JEPA：语言模型遇见联合嵌入预测架构
authors: "Hai Huang, Yann LeCun, Randall Balestriero"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=GbXKPo9QfH"
tags: ["query:imitation"]
score: 7.0
evidence: 将JEPA应用于语言模型
tldr: 语言模型依赖输入空间重建，而视觉JEPA在嵌入空间训练更优。本文迈出第一步，提出LLM-JEPA，将JEPA风格目标应用于语言模型微调和预训练，尝试弥合两种模态训练方法的差距，初步结果显示其在语言任务中的潜力。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 语言模型训练目标与视觉JEPA目标不匹配，缺乏JEPA风格LLM。
method: 提出LLM-JEPA，将JEPA训练目标应用于语言模型的微调与预训练。
result: 初步验证了JEPA在语言领域的可行性。
conclusion: 打通了语言与视觉训练方法的桥梁。
---

## Abstract
Large Language Model (LLM) pretraining, finetuning, and evaluation rely on input-space reconstruction and generative capabilities. Yet, it has been observed in vision that embedding-space training objectives, e.g., with Joint Embedding Predictive Architectures (JEPAs), are far superior to their input-space counterpart. That mismatch in how training is achieved between language and vision opens up a natural question: {\em can language training methods learn a few tricks from the vision ones?} The lack of JEPA-style LLM is a testimony of the challenge in designing such objectives for language. In this work, we propose a first step in that direction where we develop LLM-JEPA, a JEPA based solution for LLMs applicable both to finetuning and pretraining. Thus far, LLM-JEPA is able to outperform the standard LLM training objectives by a significant margin across models, all while being robust to overfiting. Those findings are observed across numerous datasets (NL-RX, GSM8K, Spider, RottenTomatoes) and various models from the Llama3, OpenELM, Gemma2 and Olmo families. Code: \url{https://github.com/galilai-group/llm-jepa}.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：当前大型语言模型（LLM）的预训练、微调和评估几乎完全依赖于**输入空间的重建与生成**（如自回归next-token prediction）。而在视觉领域，基于**嵌入空间**的训练目标（如联合嵌入预测架构 JEPA）被证明远优于输入空间目标。这种语言与视觉训练范式之间的鸿沟引发了一个自然问题：语言模型的训练能否借鉴视觉模型的“技巧”？然而，目前尚缺乏JEPA风格的LLM训练框架。
- **整体含义**：论文试图弥合两种模态训练目标的不匹配，首次将JEPA理念引入语言模型，提出 **LLM-JEPA**，并初步验证其在语言任务中的可行性，为语言模型训练开辟新方向。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：将视觉领域的JEPA范式迁移到语言模型：不再要求模型在输入空间中精确重建被掩码的token，而是**在嵌入空间预测被掩码token的表示**，从而避免对低层次细节的过度拟合。
- **关键技术细节**：
  - 模型结构：基于现有LLM（如Llama3、OpenELM等）架构，修改训练目标。
  - 训练流程（文字说明）：
    1. 对输入文本进行部分掩码（类似自编码器或掩码语言模型）。
    2. 通过上下文化路径得到可见token的嵌入表示。
    3. 预测器从可见token的嵌入中预测被掩码位置的嵌入表示。
    4. 损失函数在嵌入空间计算预测嵌入与目标嵌入之间的差异（如余弦相似度或均方误差），而非在词汇表上的交叉熵。
  - 支持微调和预训练两种场景。
  - 代码已开源（GitHub链接）。

### 3. 实验设计

- **数据集/场景**：NL-RX（推理）、GSM8K（数学推理）、Spider（文本到SQL）、RottenTomatoes（情感分析）——覆盖推理、数学、代码、分类等多种任务。
- **基准（Benchmark）**：对比标准LLM训练目标（即传统的输入空间重建/生成目标，如交叉熵损失）。
- **对比方法**：在多个模型族上评估，包括Llama3、OpenELM、Gemma2、Olmo各系列模型，与各自的标准微调/预训练方法比较。

### 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量或训练时长。仅指出实验跨多个模型族，但未提供具体的算力消耗细节。

### 5. 实验数量与充分性

- **实验数量**：涵盖4个数据集（NL-RX, GSM8K, Spider, RottenTomatoes），并使用了4个不同模型族（Llama3, OpenELM, Gemma2, Olmo），每组估计包含标准训练与LLM-JEPA的对比，以及可能的多重配置实验。
- **充分性评估**：数据集多样性较好（推理、数学、代码、情感），模型族覆盖不同规模；但未详述消融实验（如不同掩码策略、嵌入空间损失函数变体），也未报告在更大规模预训练场景下的完整结果。实验设计相对客观（公平对比标准训练目标），但充分性方面略显不足（缺少对架构超参数的敏感性分析、对训练稳定性的详细评估等）。

### 6. 论文的主要结论与发现

- **LLM-JEPA在多个模型和数据集上显著优于标准LLM训练目标**，且具备更强的抗过拟合能力（robust to overfitting）。
- 初步证明了JEPA风格目标在语言领域的可行性，打通了语言与视觉训练方法之间的桥梁。

### 7. 优点

- **创新性**：首次将JEPA框架应用于LLM，填补了该方向理论空白。
- **跨模态借鉴**：有效引入了视觉领域已被证明优越的嵌入空间预测思想。
- **广泛验证**：在多个任务和模型族上对比，且结果一致正向。
- **抗过拟合**：体现了JEPA目标比输入空间重建更鲁棒。
- **开源**：提供代码库，利于复现和后续研究。

### 8. 不足与局限

- **算力与细节缺失**：未报告任何训练资源信息，难以评估实际部署成本。
- **消融实验不够详尽**：缺少对预测器设计、掩码比例、嵌入空间损失函数选择等的深入分析。
- **任务范围有限**：仅覆盖4个数据集，未涉及超大模型（如70B+）或开放域对话/长文本生成，泛化性需更多验证。
- **预训练验证薄弱**：虽提及适用于预训练，但实验可能主要基于微调，预训练效果证据不足。
- **风险偏差**：仅在文中列出的公开数据集上表现良好，在其他类型任务（如多模态、知识密集任务）上尚未测试。
- **应用限制**：当前结果尚处于“第一步”，离大规模替代标准LLM预训练方法仍有距离，需更多工程优化。

（完）
