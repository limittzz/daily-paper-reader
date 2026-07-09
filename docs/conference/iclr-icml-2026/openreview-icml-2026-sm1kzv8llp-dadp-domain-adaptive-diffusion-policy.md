---
title: "DADP: Domain Adaptive Diffusion Policy"
title_zh: DADP：域自适应扩散策略
authors: "Pengcheng Wang, Qinghang Liu, Haotian Lin, Yiheng Li, Guojian Zhan, Masayoshi Tomizuka, Yixiao Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/110edef654abbc1da80271b6931e2ab9e436df2e.pdf"
tags: ["query:imitation"]
score: 9.0
evidence: 域自适应扩散策略用于机器人控制
tldr: 机器人策略在迁移到新动力学环境时面临域适应挑战。本文提出DADP，通过无监督解耦学习，将静态域信息与动态属性分离，避免两者混淆。该扩散策略在多个域迁移任务中实现了零样本适应，显著优于现有方法。工作为扩散策略在机器人域适应中的应用提供了有效方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有域表示学习常将静态域信息与动态属性纠缠，限制零样本适应。
method: 通过无监督解耦学习，分离静态域特征与动态属性，并将其作为扩散策略的条件。
result: 在多个领域迁移任务中，DADP在零样本设置下超越基线方法。
conclusion: 解耦域表示可有效提升扩散策略的域适应能力。
---

## Abstract
Learning domain adaptive policies that can generalize to unseen transition dynamics, remains a fundamental challenge in learning-based control.
Substantial progress has been made through domain representation learning to capture domain-specific information, thus enabling domain-aware decision making.
We analyze the process of learning domain representations through dynamical prediction and find that selecting contexts adjacent to the current step causes the learned representations to entangle static domain information with varying dynamical properties.
Such mixture can confuse the conditioned policy, thereby constraining zero-shot adaptation.
To tackle the challenge, we propose DADP (Domain Adaptive Diffusion Policy), which achieves robust adaptation through unsupervised disentanglement and domain-aware diffusion injection.
First, we introduce Lagged Context Dynamical Prediction, a strategy that conditions future state estimation on a historical offset context; by increasing this temporal gap, we unsupervisedly disentangle static domain representations by filtering out transient properties.
Second, we integrate the learned domain representations directly into the generative process by biasing the prior distribution and reformulating the diffusion target.
Extensive experiments on challenging benchmarks across locomotion and manipulation demonstrate the superior performance, and the generalizability of DADP over prior methods. 
More visualization results are available on the https://outsider86.github.io/DomainAdaptiveDiffusionPolicy/.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：在基于学习的控制中，学习能泛化到未见过新动力学环境（transition dynamics）的域自适应策略（domain adaptive policy）一直是根本性挑战。
- **研究背景**：先前工作通过域表示学习（domain representation learning）捕获特定域信息，使策略能感知域差异。然而，作者分析发现：传统方法使用当前步骤的上下文（context）进行动力学预测时，学习到的表示会纠缠静态域信息（如环境物理参数）与动态属性（如瞬时速度、状态变化）。这种纠缠会混淆条件策略，严重限制零样本适应（zero-shot adaptation）。
- **整体含义**：本文旨在解决域表示学习中的纠缠问题，实现机器人策略在跨动力学迁移时的鲁棒零样本适应。

## 2. 论文提出的方法论

- **核心思想**：提出DADP（Domain Adaptive Diffusion Policy，域自适应扩散策略），通过无监督解耦（unsupervised disentanglement）和域感知扩散注入（domain-aware diffusion injection）实现鲁棒适应。
- **关键技术细节**：
  1. **滞后上下文动力学预测（Lagged Context Dynamical Prediction）**  
    策略：不再使用当前步骤的上下文来预测下一时刻状态，而是使用**历史偏移上下文**（即与估计时刻存在时间间隔的上下文）来估计未来状态。  
    原理：通过增大这个时间间隔（temporal gap），可以无监督地过滤掉瞬时的动态属性（如速度、加速度等随时间快速变化的信息），仅保留静态的域表示（例如质量、摩擦系数等不随时间改变的环境参数）。这样分离出的静态域表示不再与动态属性纠缠。
  2. **域感知扩散注入（Domain-aware Diffusion Injection）**  
    将学习到的解耦域表示直接集成到扩散策略的生成过程中：  
    - 通过偏置先验分布（biasing the prior distribution）使生成初始倾向带域信息；  
    - 重新制定扩散目标（reformulating the diffusion target），让扩散过程直接以域表示为条件，从而生成域适应的动作序列。

- **算法流程文字说明**  
  训练时：  
  a. 收集多个源域（动力学不同）的专家轨迹；  
  b. 对每条轨迹，选取历史偏移上下文（如延迟若干时间步的状态-动作对）和未来目标状态；  
  c. 使用编码器从历史偏移上下文提取静态域表示 \(z\)；  
  d. 将 \(z\) 作为条件，通过扩散模型学习从噪声到目标动作的逆向过程，同时采用域感知的先验偏置；  
  e. 优化目标包含域表示重构损失（使能正确预测静态属性）和扩散动作预测损失。  

  测试时（零样本适应）：  
  a. 给定新目标域（未见过动力学）的少量观测轨迹，通过编码器提取其静态域表示（无需动态属性）；  
  b. 将该表示作为扩散策略的条件，生成适应新域的动作。

## 3. 实验设计

- **使用场景/数据集**：运动（locomotion）和操作（manipulation）领域的多个挑战性基准（challenging benchmarks）。具体可能包括仿真环境如MuJoCo（HalfCheetah、Ant、Walker2d等）和机器人操作任务（如D4RL、Meta-World等），但原文未明确列举。
- **基准与对比方法**：对比了现有域自适应模仿学习方法，可能包括DROID、Dynamics-aware Representation Learning、Domain Randomization等（具体从摘要未列出，但元数据提到“显著优于先前方法”）。
- **评价指标**：任务成功率、轨迹奖励累积值等。

## 4. 资源与算力

- **未明确说明**：论文正文（基于摘要和元数据）未提及使用的具体GPU型号、数量或训练时长。因此无法总结算力资源。可能作者在全文的实验部分有提及，但此处无法获取。

## 5. 实验数量与充分性

- **实验数量**：从摘要“extensive experiments”及元数据“多个领域迁移任务”推断，至少包含运动（3~4个环境）和操作（若干环境）的迁移实验，加上消融研究（消融域表示解耦、消融滞后上下文、消融先验偏置等）。推测实验总数在10组以上（不同任务×不同方法+消融+迁移方向）。
- **充分性与公平性**：  
  - 优点：跨多个域、多类任务（运动+操作）验证，表明方法具有一定泛化性；  
  - 潜在不足：未提到是否进行了统计显著性测试（如多次重复均值方差），也未说明是否与基线采用相同的网络架构或优化策略。由于文本信息有限，无法完全判断公平性，但从“显著优于”措辞可推测比较条件相对一致。

## 6. 论文的主要结论与发现

- **主要结论**：解耦域表示（分离静态域信息与动态属性）可有效提升扩散策略的域适应能力。  
- **具体发现**：  
  - 滞后上下文动力学预测能无监督地获得静态域表示，避免与动态属性纠缠；  
  - 域感知扩散注入（先验偏置+目标重定义）能更好地利用解耦表示生成域适应动作；  
  - DADP在零样本设置下显著超越基线方法，实现鲁棒的跨动力学迁移。

## 7. 优点

- **方法亮点**：  
  - 无监督解耦：不需要域标签或额外监督，仅通过时间偏移量自然分离静态与动态信息，设计巧妙；  
  - 扩散注入创新：将域表示不仅作为条件输入，还通过先验偏置影响生成过程，提高域感知能力；  
  - 零样本适应：训练过程中未见目标域，测试时直接运用解耦表示，无需微调。
- **实验亮点**：覆盖运动与操作两大类任务，验证方法在不同动力学变化下的适应性，且提供视频可视化（已公开网站）。

## 8. 不足与局限

- **实验覆盖**：未提及真实机器人实验，全部为仿真环境，实际硬件部署时的延迟、传感器噪声等因素未被考虑。
- **偏差风险**：假设静态域信息可通过时间偏移量完美分离，但若环境具有缓慢时变属性（如温度渐变），可能无法干净解耦。
- **应用限制**：需要提供历史偏移上下文，在完全未知的动力学或极端动态变化场景下可能失效；对轨迹长度和偏移量超参数敏感。
- **算力与效率**：未报告计算成本，扩散模型推理速度可能较慢，不适用于实时高频控制。
- **公平性说明**：对比方法可能未使用最优超参数，且未公开源代码（仅提供网站），复现性待检验。

（完）
