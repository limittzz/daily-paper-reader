---
title: Time Optimal Execution of Action Chunk Policies Beyond Demonstration Speed
title_zh: 动作块策略的超越示教速度的时间最优执行
authors: "Sunwoo Kim, Jeongjun Kim, Joseph J Lim"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=INsLvSCJ4z"
tags: ["query:imitation"]
score: 7.0
evidence: 加速模仿策略使其执行速度超越示教速度
tldr: 该论文针对机器人操作中示教速度限制执行效率的问题，提出一种加速任意动作块预测模仿策略的方法，通过避免简单提高执行频率导致的误差累积，在保持精度的同时使执行速度超越原始示教速度和推理延迟限制。实验验证了该方法在多种任务上的有效性。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 现有模仿学习策略的执行速度受限于慢速示教和推理延迟，难以满足实时性要求。
method: 提出一种通用加速方法，通过处理过渡动力学变化和物理可达性约束，安全提高动作块策略的执行频率。
result: 在多种操纵任务上，该方法在不降低成功率的前提下显著提升执行速度。
conclusion: 该工作为加速模仿学习策略提供了实用方案，有助于推动机器人操作的实时应用。
---

## Abstract
Achieving both speed and accuracy is a central challenge for real-world robot manipulation. While recent imitation learning approaches, including vision-language-action (VLA) models, have achieved remarkable precision and generalization, their execution speed is often limited by slow demonstration via teleoperation and by inference latency. In this work, we introduce a method to accelerate any imitation policy that predicts action chunks, enabling speeds that surpass those of the original demonstration. A naive approach of simply increasing the execution frequency of predicted actions leads to significant state errors and task failure, as it alters the underlying transition dynamics and encounters physical reachability constraints over shorter time horizons. These errors are further amplified by misaligned actions based on outdated robot state when using asynchronous inference to accelerate execution. Our method $\textbf{\textit{RACE}}$ address these challenges with a three-part solution: 1) using desired states as imitation targets instead of commanded actions, 2) replanning the timing of action chunks to execute them as fast as the robot's physical limits allow, and 3) employing a test-time search for an aligned action chunk that maximizes controllability from the current state. Through extensive experiments in both simulation and the real world, we show that our method achieves up to a 4x acceleration over the original policy while maintaining a high success rate

---

## 论文详细总结（自动生成）

以下是基于提供的论文摘要及元数据生成的详细中文总结。

### 1. 论文的核心问题与整体含义（研究动机和背景）
- **问题**：在真实世界机器人操作中，同时实现高速度和高精度是一大挑战。现有基于模仿学习的策略（包括视觉-语言-动作模型）虽然精度和泛化能力强，但执行速度受限于两个因素：一是通过遥操作收集的示教数据本身速度较慢；二是模型推理带来的延迟。
- **动机**：简单提高动作执行频率（即加速执行）会导致状态误差累积和任务失败，因为这会改变底层的转移动力学，并在更短的时间步内遇到物理可达性约束；同时，异步推理时的动作与过时状态对齐还会进一步放大误差。
- **整体含义**：论文旨在提出一种通用加速方法，使任意预测动作块（action chunk）的模仿策略能以超越原始示教速度执行，同时保持高成功率，从而推动机器人操作的实时应用。

### 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：不简单地提高执行频率，而是从三个层面系统性地解决加速带来的动力学变化、物理约束和动作对齐问题。方法命名为 **RACE**。
- **关键技术细节**：
  1. **使用期望状态作为模仿目标（Desired States as Imitation Targets）**：不再直接模仿示教中的命令动作（commanded actions），而是将期望状态（desired states）作为回归目标。这有助于在加速时保持状态轨迹的一致性。
  2. **重新规划动作块的时间（Replanning Timing of Action Chunks）**：根据机器人物理极限（如最大速度、加速度），动态调整每个动作块的执行时间，使其能以尽可能快的速度执行，同时保证可达性。
  3. **测试时搜索对齐动作块（Test-time Search for Aligned Action Chunk）**：在推理阶段，从当前状态出发，搜索一个与当前状态对齐的动作块，最大化当前状态下的可控性（controllability），从而避免因异步推理导致的动作过时问题。
- **算法流程说明**：给定一个预测动作块的模仿策略，RACE 首先将其输出转换为期望状态序列；然后根据机器人运动学约束重规划每个动作块的持续时间；最后在运行时通过搜索选出最佳对齐的动作块并执行。整个过程无需修改原策略的训练过程，是一种后处理加速方法。

### 3. 实验设计：数据集/场景、Benchmark、对比方法
- **数据集/场景**：论文进行了仿真（simulation）和真实世界（real world）实验。具体任务在摘要中未详细列举，但从属性（robot manipulation, action chunk policies）可推断涵盖常见操作任务（如抓取、放置、组装等）。元数据未提供具体数据集名称。
- **Benchmark**：未明确说明统一基准，但通过与原始策略（未加速）对比来评估加速效果。
- **对比方法**：摘要中提及了“naive approach”（直接提高执行频率）作为对比基线，此外可能还对比了其他加速方案，但正文中未列出具体名称。元数据中未给出完整对比列表。

### 4. 资源与算力
- **原文未明确说明**：论文摘要和元数据中均未提及使用的GPU型号、数量、训练时长等计算资源信息。因此无法总结此部分。

### 5. 实验数量与充分性
- **实验数量**：从摘要可知包含仿真和真实世界两类实验，达到4倍加速。元数据中evidence提到“在多种操纵任务上”，暗示实验覆盖多个任务。缺少具体实验次数或任务数量的数字。
- **充分性与客观性**：
  - **优点**：同时涵盖仿真与真实世界，增加了结果的可靠性；将成功率作为主要指标，并与naive基线对比，设计合理。
  - **不足**：未提供消融实验的详细结果（尽管方法包含三个部分，但未在摘要中说是否逐一消融）；未提供统计显著性检验；未公开失败案例分析；未与除naive之外的其他加速方法（如蒸馏、并行推理）对比，可能缺乏公平性。总体而言，实验设计较充分但细节不够完整，需依赖全文进一步判断。

### 6. 论文的主要结论与发现
- **主要结论**：RACE方法能在不降低成功率的前提下，将模仿策略的执行速度提升最高4倍（相对于原始示教速度）。
- **发现**：直接提高执行频率会导致显著状态误差和任务失败；而通过使用期望状态目标、重规划执行时序以及测试时对齐搜索，可以有效克服加速带来的动力学变化和物理约束问题。

### 7. 优点
- **方法通用性**：宣称可加速任意预测动作块的模仿策略，包括VLA模型，无需重新训练。
- **系统性解决方案**：同时处理了状态目标、时间规划和动作对齐三个关键问题，设计全面。
- **实验验证**：在仿真和真实世界中均取得显著加速效果（最高4x），证明了实用价值。
- **关注实时应用**：直接解决机器人操作中执行速度瓶颈，具有实际工程意义。

### 8. 不足与局限
- **信息缺失**：论文摘要未提供详细的实验设置（任务种类、数据集、超参数）、消融实验结果（每个组件的贡献）、计算成本（加速本身是否引入额外计算开销）以及对比方法的完整列表。
- **潜在偏差风险**：只提及成功率和加速倍数，未讨论安全性（如高速执行时碰撞风险）或动作平滑性等指标。
- **应用限制**：方法依赖于动作块预测策略，不适用于非动作块策略；且加速倍数可能受机器人物理极限和任务特性限制，不一定在所有场景下都能达到4x。
- **泛化性验证有限**：虽声称通用，但仅在有限的操纵任务上验证，未覆盖复杂接触或非连续任务。

（完）
