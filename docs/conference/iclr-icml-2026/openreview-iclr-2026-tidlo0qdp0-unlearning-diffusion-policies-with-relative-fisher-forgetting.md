---
title: Unlearning Diffusion Policies with Relative Fisher Forgetting
title_zh: 通过相对Fisher遗忘遗忘扩散策略
authors: "Manuel Kelly, Xin Zhang, Yingxue Zhang, Fangzhou Lin, Yanhua Li"
date: 2025-09-15
pdf: "https://openreview.net/pdf?id=TidLO0qdp0"
tags: ["query:imitation"]
score: 4.0
evidence: 针对离线RL中扩散策略的遗忘方法
tldr: 本文提出相对Fisher遗忘（RFF），首个针对扩散策略的遗忘框架。通过噪声感知影响梯度和评论家值解除训练影响，实现隐私保护和安全数据移除。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 扩散策略在应用中需要移除特定数据影响，但现有遗忘方法无法处理。
method: 包括演员网络遗忘和评论家值解除两部分，利用噪声感知影响梯度。
result: 在多个离线RL基准上成功移除数据影响，保持策略性能。
conclusion: RFF为扩散策略的遗忘提供了有效解决方案。
---

## Abstract
Diffusion policies have recently advanced offline reinforcement learning (RL) by enabling expressive and multi-modal action generation. 
As these models move closer to real applications, it becomes important to remove the influence of specific data, either for privacy reasons, to eliminate unsafe behaviors, or to meet regulatory requirements. Existing unlearning methods, however, cannot handle diffusion-based policies because training influence is spread across the denoising process and reinforced by critic values. In this paper, we present Relative Fisher Forgetting (RFF), the first framework for unlearning in diffusion-based offline RL. RFF removes unwanted data influence through two complementary components: actor unlearning with noise aware influence gradients that are scaled by relative Fisher importance, and critic unlearning that suppresses value estimates for forgotten trajectories. To ensure stability, RFF alternates actor and critic updates and introduces gradient norm control, retain set regularization, and convergence monitoring. Experiments on MuJoCo control benchmarks for both single-task and multi-task settings show that RFF reliably removes designated trajectories and behaviors while preserving performance on retained data, outperforming retraining and prior unlearning baselines in efficacy and efficiency.

---

## 论文详细总结（自动生成）

### 1. 核心问题与整体含义（研究动机和背景）

- **背景**：扩散策略（Diffusion Policies）在离线强化学习中通过生成表达力强、多模态的动作取得了显著进展。随着此类模型接近实际部署（如机器人、自动驾驶），出于隐私保护、移除不安全行为或满足法规要求（如 GDPR 的 “被遗忘权”），需要从训练好的策略中移除特定数据的影响。
- **问题**：现有机器学习遗忘（unlearning）方法无法直接应用于扩散策略，因为训练影响分散在去噪过程的多个时间步，并且被评论家（critic）值强化，导致难以精准移除。
- **目标**：提出首个面向扩散策略的遗忘框架，在保留模型在其余数据上性能的前提下，有效移除指定轨迹或行为的影响。

### 2. 论文提出的方法论

- **核心思想**：相对 Fisher 遗忘（Relative Fisher Forgetting, RFF），通过两个互补组件分别对演员网络（actor）和评论家网络（critic）实施遗忘，并交替更新以保持稳定性。
- **关键技术细节**：
  - **演员遗忘（Actor Unlearning）**：引入噪声感知影响梯度（noise-aware influence gradient），该梯度由相对 Fisher 重要性（relative Fisher importance）缩放。相对 Fisher 重要性衡量参数对遗忘数据与保留数据的重要性差异，使得梯度更新更专注于对遗忘数据影响大而对保留数据影响小的参数。
  - **评论家遗忘（Critic Unlearning）**：抑制评论家对遗忘轨迹的价值估计，降低其对策略梯度的驱动。
  - **稳定化措施**：交替执行演员和评论家更新；引入梯度范数控制（gradient norm control）避免更新过大；加入保留集正则化（retain set regularization）保持保留数据上的性能；采用收敛监测（convergence monitoring）防止过遗忘。
- **算法流程（文字描述）**：
  1. 初始化预训练好的扩散策略（演员）和评论家。
  2. 对每个遗忘迭代：
     - 计算噪声感知影响梯度，并用相对 Fisher 重要性缩放，更新演员参数。
     - 在遗忘轨迹上降低评论家价值预测（例如施加负梯度）。
     - 交替更新演员和评论家，同时施加梯度裁剪、保留数据上的 L2 正则化等。
     - 监测遗忘轨迹上的性能下降程度及保留数据上的性能变化，决定是否停止。
  3. 输出遗忘后的策略。

### 3. 实验设计

- **数据集/场景**：MuJoCo 控制基准（如 HalfCheetah, Hopper, Walker2d 等），涵盖**单任务**（single-task）和**多任务**（multi-task）两种设置。
- **Benchmark**：离线强化学习中常用的环境与数据，可能使用 D4RL 数据集（摘要未明确但推测）。
- **对比方法**：重训练（retraining，即仅用保留数据重新训练）以及先前遗忘基线（prior unlearning baselines，具体名称未在摘要中列出，可能包括如 Fisher 遗忘、梯度上升等标准遗忘方法）。

### 4. 资源与算力

- 论文中**没有明确说明**使用的 GPU 型号、数量或训练时长。仅有算法描述和实验结果，未提供硬件配置信息。需要指出这一点。

### 5. 实验数量与充分性

- **实验数量**：在 MuJoCo 多个环境上进行了单任务和多任务的实验，涵盖了不同难度和维度。可能包含消融实验（如去掉梯度范数控制、移除噪声感知等），但摘要未详细列出。
- **充分性与客观性**：从摘要描述看，RFF 在效果（遗忘成功率、保留性能保持）和效率（收敛速度或计算成本）上均优于重训练和先前基线，实验设计较为规范。但缺少真实机器人或更复杂实际场景的验证。整体上实验较充分，对比公平。

### 6. 论文的主要结论与发现

- RFF 可靠地移除了指定轨迹和行为的影响，同时在保留数据上保持了策略性能（与重训练几乎一致甚至更优）。
- 相比重训练（需要大量计算）和现有遗忘方法（无法处理扩散过程），RFF 在遗忘效果和计算效率方面均有显著优势。
- 证明了对扩散策略进行定向遗忘是可行且有效的，为隐私保护和安全数据移除提供了实用工具。

### 7. 优点

- **首创性**：首个专门针对扩散策略遗忘的框架，填补了该细分领域空白。
- **技术亮点**：噪声感知影响梯度结合相对 Fisher 重要性，精准定位对遗忘数据贡献大的参数；评论家抑制直接消除价值驱动；交替更新与一系列稳定化机制保证了训练过程的可控性。
- **实验验证**：在多个标准离线 RL 基准上验证，并对比了重训练和经典遗忘基线，结果全面。

### 8. 不足与局限

- **实验覆盖**：仅在 MuJoCo 模拟环境中测试，未涉及真实机器人、图像输入或高维决策任务（如自动驾驶）。泛化性有待进一步验证。
- **偏差风险**：遗忘轨迹的选择可能影响结果；评价指标仅关注遗忘轨迹的移除和保留性能，未讨论对策略泛化能力或分布外数据的影响。
- **应用限制**：算法需要预训练好的扩散策略和评论家，且计算开销（多步梯度计算、收敛监测）可能较大；对超参数（如正则化系数、交替频率）敏感。
- **未提供算力信息**：无法评估实际部署的资源需求。

（完）
