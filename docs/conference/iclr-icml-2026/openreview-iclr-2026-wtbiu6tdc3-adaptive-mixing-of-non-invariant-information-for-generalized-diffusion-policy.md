---
title: Adaptive Mixing of Non-Invariant Information for Generalized Diffusion Policy
title_zh: 自适应非不变信息混合用于泛化扩散策略
authors: "Pingrui Zhang, Yu Zhang, Pengyuan Wu, Zhaxizhuoma, Zhigang Wang, Kehai Chen, Dong Wang, Min Zhang, Bin Zhao, Xuelong Li"
date: 2025-09-20
pdf: "https://openreview.net/pdf?id=WtbIU6tDc3"
tags: ["query:imitation"]
score: 7.0
evidence: 解决扩散策略在观测变化下的泛化问题
tldr: 该论文针对扩散策略在观测微小变化（如光照、相机姿态）下性能严重退化的问题，引入细粒度基准分析，发现相机姿态是导致退化的主因。提出自适应非不变信息混合方法，增强扩散策略对视角变化的零样本泛化能力，在多个基准上取得显著提升。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散策略对观测变化脆弱，特别是相机姿态变化导致性能大幅下降。
method: 提出因子化基准分析影响因素，并设计自适应混合非不变信息的方法增强泛化。
result: 在多种扰动下，该方法有效提升了扩散策略的零样本泛化性能。
conclusion: 该工作揭示了扩散策略泛化瓶颈并提供了有效改进，推动其在实际场景中的鲁棒应用。
---

## Abstract
Diffusion policies (DP) have emerged as a leading paradigm for learning-based robotic manipulation, offering temporally coherent action synthesis from high-dimensional observations. 
However, despite their centrality to downstream tasks, DPs exhibit fragile generalization capabilities. Minor variations in observations, such as changes in lighting, appearance, or camera pose, can lead to significant performance degradation, even when operating on familiar trajectories.
To address the issue, we introduce a factorized, fine-grained benchmark that isolates the impact of individual perturbation factors on zero-shot generalization.
Based on it, we reveal camera pose as a dominant driver of performance degradation, explaining the pronounced drops observed at higher levels of domain randomization. 
In this case, we propose $A$daptive $M$ixing of non-$I$nvariant (AMI) information, a model-agnostic training strategy that requires no additional data and reinforces invariant correlations while suppressing spurious ones.
Across simulated evaluations, AMI consistently and significantly outperforms strong baselines, mitigating DP's sensitivity to observation shifts and yielding robust zero-shot generalization over diverse perturbation factors. 
We further validate these improvements in real-world experiments by zero-shot deploying the policies in natural settings, demonstrating their robustness to observation variations.

---

## 论文详细总结（自动生成）

# 中文总结：Adaptive Mixing of Non-Invariant Information for Generalized Diffusion Policy

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：扩散策略（Diffusion Policy, DP）已成为基于学习的机器人操作领域的主流范式，能够从高维观测中生成时间上连贯的动作。  
- **核心问题**：DP 在零样本泛化方面表现出脆弱性——观测中的微小变化（如光照、外观、相机姿态等）会导致性能严重退化，即使是在熟悉的轨迹上执行。  
- **研究动机**：现有研究对 DP 泛化瓶颈的因子化分析不够细致，缺乏针对特定扰动因素的归因，也缺少有效的通用增强方法。  
- **整体含义**：该工作旨在揭示 DP 对观测变化（尤其是相机姿态）敏感的根本原因，并提出一种模型无关的训练策略，在不增加额外数据的前提下提升其零样本鲁棒性。

## 2. 提出的方法论
- **核心思想**：引入**因子化细粒度基准**，隔离并量化不同扰动因素（光照、外观、相机姿态等）对零样本泛化的影响；基于此发现相机姿态是性能退化的主导因素。  
- **关键技术**：提出**自适应非不变信息混合（Adaptive Mixing of non-Invariant information，AMI）** 策略。  
  - 模型无关：不依赖特定架构，可直接用于现有 DP 训练。  
  - 增强**不变相关性**（invariant correlations）并抑制**虚假相关性**（spurious correlations）。  
  - 无需额外数据：在原有训练数据上通过自适应混合非不变信息来实现。  
- **算法流程（文字说明）**：  
  1. 利用因子化基准分析，识别对泛化影响最大的扰动因素（相机姿态）。  
  2. 在训练过程中，将非不变信息（如视角相关特征）以自适应方式混合到观测中，迫使模型关注真正对任务不变的表示。  
  3. 通过损失函数或数据增强机制，使策略学习鲁棒的动作映射，降低对变化因素的依赖。  
- **公式**：论文中未提供具体公式，但核心在于自适应混合权重的设计。

## 3. 实验设计
- **数据集/场景**：模拟评估（simulated evaluations）和多扰动因素下的零样本测试；实际机器人实验（real-world experiments）在自然环境中零样本部署策略。  
- **Benchmark**：自建的**因子化细粒度基准**，独立隔离光照、外观、相机姿态等扰动因素。  
- **对比方法**：与多个强基线（strong baselines）对比，包括标准扩散策略及域随机化等方法。  
- **评价指标**：零样本泛化性能（成功率或任务完成率）在不同扰动水平下的表现。

## 4. 资源与算力
- 原文未明确说明所使用的 GPU 型号、数量或训练时长。  
- 从实验规模推断，可能使用了常用深度强化学习/模仿学习平台的标准配置（如单卡或少量 GPU），但具体信息缺失。

## 5. 实验数量与充分性
- **实验数量**：包含模拟环境中的多组评估（涵盖多种扰动因素及不同扰动强度），以及真实环境下的零样本部署验证。  
- **充分性**：  
  - 因子化基准能够系统评估每个因素的影响，设计合理。  
  - 与强基线对比，结果显示 AMI 持续且显著优于基线。  
  - 有真实世界实验，增强了说服力。  
- **客观与公平**：基准是自我构建的，但方法比较应属公平；缺失跨不同标准机器人基准（如 RLBench、MimicGen）的验证，可能偏向于特定设置。

## 6. 主要结论与发现
- **发现1**：相机姿态是导致扩散策略零样本泛化性能退化的主导因素，光照和外观变化影响相对较小。  
- **发现2**：现有域随机化方法在相机姿态扰动下的表现仍不理想，较高程度的域随机化反而加剧退化。  
- **结论**：AMI 方法可有效缓解 DP 对观测变化的敏感性，在多种扰动因素上实现了稳健的零样本泛化，并在真实环境中验证了有效性。

## 7. 优点（方法与实验亮点）
- **方法论亮点**：  
  - 提出因子化分析框架，精准定位瓶颈因素。  
  - AMI 训练策略简单、模型无关、无需额外数据，具有实用价值。  
- **实验亮点**：  
  - 同时包含模拟和真实世界验证。  
  - 对比多个强基线，结果显著。  
  - 零样本测试直接反映泛化能力，与微调后测试不同，更具挑战性。

## 8. 不足与局限
- **实验覆盖**：仅采用自建的细粒度基准，未在广泛认可的公共机器人操作基准（如 MetaWorld、CALVIN、RLBench）上评估，可能限制结论的通用性。  
- **偏差风险**：因子化扰动可能无法完全模拟真实世界中耦合的变化；真实实验场景数量有限。  
- **应用限制**：AMI 需要预知识别哪些信息是非不变的（如视角），对于复杂耦合场景可能需要额外设计。  
- **算力与超参数**：未提供训练成本细节，不利于复现和公平比较。  
- **消融实验**：元数据未提及其他消融实验（如 AMI 中不同混合策略的对比），可能不够全面。

（完）
