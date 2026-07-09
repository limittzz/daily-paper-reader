---
title: "Var-JEPA: A Variational Formulation of the Joint-Embedding Predictive Architecture – Bridging Predictive and Generative Self-Supervised Learning"
title_zh: Var-JEPA：联合嵌入预测架构的变分公式——连接预测式与生成式自监督学习
authors: "Moritz Gögl, Christopher Yau"
date: 2026-04-30
pdf: "https://openreview.net/pdf/32941e8a8b7689a76ff00144d97db2163713348f.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 联合嵌入预测架构（JEPA）的变分公式化
tldr: JEPA通常被视为非生成式自监督学习，强调表征空间预测而非观测重建。本文论证了标准JEPA设计（成对编码器与上下文到目标预测器）本质上对应于一类耦合潜变量模型的变分后验与条件先验，从而将JEPA纳入概率生成模型框架。该理论统一了预测式与生成式自监督学习，并为改进JEPA提供了理论指导。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: JEPA被认为非生成式，但其与概率生成模型的关系尚未明确。
method: 从变分推断视角重新解释JEPA，将其视为确定性变分特例。
result: 理论上揭示了JEPA的生成式本质，为扩展JEPA模型提供基础。
conclusion: JEPA与生成式自监督学习可以统一在变分框架下。
---

## Abstract
The Joint-Embedding Predictive Architecture (JEPA) is often seen as a non-generative alternative to likelihood-based self-supervised learning, emphasizing prediction in representation space rather than reconstruction in observation space. We argue that the resulting separation from probabilistic generative modeling is largely rhetorical rather than structural: the canonical JEPA design–coupled encoders with a context-to-target predictor–mirrors the variational posteriors and learned conditional priors obtained when variational inference is applied to a particular class of coupled latent-variable models, and standard JEPA can be viewed as a deterministic specialization in which regularization is imposed via architectural and training heuristics rather than an explicit likelihood. Building on this view, we derive the *Variational JEPA* (Var-JEPA), which makes the latent generative structure explicit by optimizing a single Evidence Lower Bound (ELBO). This yields meaningful representations without ad-hoc anti-collapse regularizers and allows principled uncertainty quantification in the latent space. We instantiate the framework for tabular data (Var-T-JEPA) and achieve strong representation learning and downstream performance, improving over T-JEPA across real-world tabular benchmarks while remaining competitive with strong raw-feature baselines.

---

## 论文详细总结（自动生成）

## 论文详细中文总结

### 1. 核心问题与整体含义（研究动机和背景）
- **核心问题**：联合嵌入预测架构（JEPA）通常被视为非生成式的自监督学习方法，因为它强调在表征空间中进行预测而非在观测空间中重建。本文质疑这种区分是否只是修辞上的而非结构性的，并试图揭示JEPA与概率生成模型之间的内在联系。
- **研究动机**：标准JEPA虽然依赖编码器-预测器结构并避免了显式似然建模，但其设计（成对编码器与上下文到目标预测器）实际上与变分推断中获得的变分后验和条件先验高度相似。作者希望将JEPA纳入概率生成模型框架，从而统一预测式与生成式自监督学习，并为改进JEPA提供理论指导。
- **背景意义**：该工作填补了JEPA与生成式自监督学习之间的理论空白，为理解JEPA的成功提供了新的视角，并有助于设计更鲁棒、更可解释的表征学习算法。

### 2. 方法论：核心思想、关键技术细节
- **核心思想**：将标准JEPA视为一种确定性变分特例，其中通过架构和训练启发式（如非对抗性防坍塌正则化）施加约束，而非显式似然。作者提出**Var-JEPA**（变分联合嵌入预测架构），将潜在生成结构显式化，通过优化单一的证据下界（ELBO）来学习表征。
- **关键技术细节**：
  - 将JEPA中的成对编码器重新解释为变分后验网络，将上下文到目标的预测器解释为条件先验。
  - 推导出适用于JEPA的ELBO，使模型能够显式地建模不确定性，并在潜在空间中提供原理性不确定性量化。
  - 具体实例化：针对表格数据提出**Var-T-JEPA**，这是T-JEPA的变分版本。
- **公式/算法流程**（用文字描述）：输入数据被分为上下文块和目标块；上下文块经由编码器生成表征，目标块经过另一编码器（或共享权重）生成表征；预测器尝试从上下文表征预测目标表征；同时，变分后验估计目标表征的分布，ELBO包含重建项（在表征空间中的预测损失）和KL散度项（后验与条件先验之间的正则化）。训练时最大化ELBO，无需额外的防坍塌正则化项。

### 3. 实验设计
- **数据集/场景**：目前仅在表格数据上进行实验（基于T-JEPA的基准）。具体数据集名称未在摘要中列出，但提到“真实世界的表格基准”（real-world tabular benchmarks）。
- **Benchmark**：主要与原始T-JEPA对比，同时与强原始特征基线（strong raw-feature baselines）进行比较。
- **对比方法**：T-JEPA（标准非生成式版本）、以及其他可能的基线方法（未具体说明）。

### 4. 资源与算力
- 文中未明确说明使用的GPU型号、数量、训练时长等算力信息。仅提及“实例化框架”，需要额外信息才能评估计算成本。

### 5. 实验数量与充分性
- **实验数量**：从摘要看，主要报告了在表格基准上的下游性能提升，但未给出具体实验组数（如不同数据集个数、消融实验等）。可能包含多组数据集，但信息不详。
- **充分性评估**：由于缺乏详细的实验设置、数据集列表、超参数调整、重复次数、统计显著性等细节，难以充分判断实验的全面性和客观性。论文声称“强于T-JEPA”，但需要更完整的实验报告来验证。

### 6. 主要结论与发现
- JEPA本质上对应一类耦合潜变量模型的变分后验与条件先验，标准JEPA可视为确定性变分特例。
- 提出的Var-JEPA通过优化ELBO可以产生有意义的表征，无需人工防坍塌正则化，并允许原理性不确定性量化。
- 在表格数据上的实例Var-T-JEPA实现了强表征学习性能和下游性能，超越了T-JEPA，并与强原始特征基线保持竞争力。

### 7. 优点
- **理论贡献**：首次严格论证了JEPA与生成式自监督学习在变分框架下的统一，具有重要的理论指导意义。
- **方法创新**：Var-JEPA将隐性正则化显式化为ELBO中的KL项，简化了训练过程（无需防坍塌正则器）。
- **实用性**：在表格数据上验证了有效性，性能优于前代方法，且保留了生成式模型的不确定性量化能力。

### 8. 不足与局限
- **实验覆盖不足**：仅针对表格数据，未涉及图像、文本、视频等JEPA常用领域，泛化能力未知。
- **基线比较有限**：只与T-JEPA和原始特征基线对比，缺乏与当前主流自监督方法（如SimCLR, MAE, iBOT等）的全面比较。
- **细节缺失**：未提供数据集具体信息、超参数设置、计算资源、消融实验、统计显著性测试等，可重复性和公平性难以保证。
- **潜在偏差**：理论推导可能依赖于特定假设（如潜变量模型的耦合结构），实际应用中可能受限。

（完）
