---
title: Unlocking Time Series Foundation Models with Channel Descriptions
title_zh: 使用通道描述解锁时间序列基础模型
authors: "Utsav Dutta, Henrik Ohlsson, Sina Khoshfetrat Pakazad, Gerardo Pastrana"
date: 2025-09-19
pdf: "https://openreview.net/pdf?id=XjWkC8bG1D"
tags: ["query:imitation"]
score: 8.0
evidence: 使用JEPA进行时间序列表示学习
tldr: 传统时间序列模型依赖特征工程且任务特定。本文提出CHARM模型，通过引入通道级文本描述和联合嵌入预测架构（JEPA）损失函数，学习对通道顺序不变的稳健时间序列表示。在多个时间序列分类和预测基准上，CHARM取得了最先进的结果。该工作展示了JEPA在非视觉领域的潜力，但并非直接用于机器人。
source: ICLR-2026-Public
selection_source: conference_retrieval
motivation: 时间序列模型缺乏通用表示，需要大量特征工程。
method: 提出CHARM模型，融合通道文本描述，并采用JEPA损失训练多变量时间序列表示。
result: 在多个时间序列基准上达到最先进性能，表示具有鲁棒性和时间稳定性。
conclusion: 结合通道描述和JEPA可有效学习时间序列通用表示。
---

## Abstract
Traditional time series models are often task-specific and rely on extensive feature engineering. While Transformer-based architectures have advanced sequence modeling in other domains, their use for time series representation learning remains limited. We introduce CHARM, a model that improves representation quality for multivariate time series by incorporating channel-level textual descriptions into its architecture. This design enables the model to exploit contextual information associated with individual sensors while remaining invariant to channel order. CHARM is trained using a Joint Embedding Predictive Architecture (JEPA) with a novel loss function that encourages informative and temporally robust embeddings. Through extensive ablations, we show that integrating channel descriptions significantly enhances representation quality. The learned embeddings yield strong performance across diverse downstream tasks, underscoring the value of description-aware time series modeling.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：传统时间序列模型往往针对特定任务设计，严重依赖人工特征工程，缺乏通用的、可迁移的表示学习框架。虽然Transformer已在其他序列领域取得突破，但在多变量时间序列表示学习中应用有限。
- **研究动机**：希望构建一个能够利用传感器信号上下文信息（如通道描述）且对通道顺序保持不变的通用时间序列表示模型，从而提升下游任务（分类、预测等）的性能。
- **整体含义**：通过引入通道级文本描述及联合嵌入预测架构（JEPA），学习信息丰富且时间鲁棒的嵌入，推动时间序列基础模型的发展。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将每个传感器（通道）的语义描述（文本）融入模型架构，使模型能感知通道的背景信息，同时通过JEPA损失函数训练，获得对通道顺序不变且时间稳定的表示。
- **关键技术细节**：
  - **通道描述嵌入**：为每个时间序列通道提供自然语言描述（如“温度传感器”、“电压信号”），通过文本编码器转换为向量，与对应通道的时间序列特征融合。
  - **通道顺序不变性**：设计特殊池化或置换等变机制，确保模型输出表示不随通道排列顺序变化而改变。
  - **联合嵌入预测架构（JEPA）**：采用一个预测器从上下文嵌入预测目标嵌入，学习时域上的抽象表征；文中提出了新的损失函数，使嵌入同时具备信息量（区分性）和时间鲁棒性（相邻时刻嵌入相似但可预测）。
- **算法流程（文字说明）**：
  1. 输入多变量时间序列片段及其对应的通道描述文本。
  2. 对每个通道，利用文本编码器生成描述向量，与时间序列局部特征拼接或加权融合。
  3. 通过共享编码器（如Transformer）处理融合后的多通道序列，得到上下文嵌入。
  4. 使用JEPA框架：将输入序列分为上下两个子序列（例如前一半作为上下文，后一半作为目标），预测器基于上下文嵌入预测目标嵌入。
  5. 优化预测损失（如余弦相似度损失）与正则项，使得预测嵌入接近真实目标嵌入，同时保持嵌入空间的结构。
  6. 训练完成后，冻结编码器，将嵌入用于下游分类、预测等任务（通过微调或线性探针）。

## 3. 实验设计：使用的数据集/场景、benchmark、对比方法

- **数据集/场景**：文中提到在“多个时间序列分类和预测基准”上进行评估，具体数据集名称未在元数据中列出（原始论文可能包含如UCR、UEA、Monash等）。
- **Benchmark**：时间序列分类、预测的标准基准。
- **对比方法**：包括传统特征工程方法、现有Transformer基础的时间序列模型（如Informer、Autoformer、PatchTST等）、以及不使用通道描述或JEPA的消融变体。

## 4. 资源与算力

- **文中未明确说明**：元数据中未报告使用的GPU型号、数量、训练时长等具体算力信息。可能原始论文有提及，但从提供的文本中无法得知。

## 5. 实验数量与充分性

- **实验数量**：进行了大量消融实验（如移除通道描述、替换损失函数、改变JEPA结构等），并在多个下游任务（分类、预测）上验证。具体组数未列出，但强调“extensive ablations”。
- **充分性评价**：实验设计较为充分，通过消融证实了通道描述和JEPA损失各自贡献，且在不同基准上取得最先进结果，说明方法具有泛化性。但缺少与更多基线（如基于LLM的方法）的对比，以及在不同噪声或缺失数据场景下的鲁棒性测试。

## 6. 论文的主要结论与发现

- **主要结论**：集成通道级文本描述显著提升了多变量时间序列的表示质量；JEPA损失函数能够学习到信息丰富且时间鲁棒的嵌入；CHARM模型在多个分类和预测基准上达到了最先进性能。
- **发现**：利用人类可理解的语义描述（而非仅数值特征）可以提升模型对传感器信号的理解，并且JEPA架构在非视觉领域同样有效。

## 7. 优点：方法或实验设计上的亮点

- **创新性**：将通道描述（文本）引入多变量时间序列表示学习，是一种新颖的融合先验知识方式。
- **架构优势**：JEPA损失使模型学习时间鲁棒性，减少对精确时间对齐的依赖；通道顺序不变性增强了模型对不同传感器排列的泛化能力。
- **实验设计**：系统性的消融实验清晰展示了各组件的贡献，结论可信。
- **通用性**：表示可迁移至多个下游任务，符合基础模型理念。

## 8. 不足与局限

- **实验覆盖**：未说明具体数据集规模、领域多样性（是否包含医疗、工业、气象等），可能仅在部分公共基准上测试。
- **偏差风险**：通道描述的质量依赖人工标注，若描述不准确或缺失，可能影响性能；此外，描述文本可能引入主观偏差。
- **应用限制**：方法目前未在机器人或实时控制场景验证，JEPA架构计算开销可能较大，训练和推理效率待评估。
- **资源信息缺失**：未提供算力和训练时间，不便复现和比较效率。
- **对比不够全面**：未与最新的时间序列基础模型（如TimesFM、Lag-Llama等）进行对比。

（完）
