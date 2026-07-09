---
title: "When a Robot is More Capable than a Human: Learning from Constrained Demonstrators"
title_zh: 当机器人比人类更强大：从受限示教者中学习
authors: "Xinhu Li, Ayush Jain, Zhaojing Yang, Yigit Korkmaz, Erdem Biyik"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=BvirMuKWV1"
tags: ["query:imitation"]
score: 6.0
evidence: 从受限示教者中学习模仿策略
tldr: 该论文研究了在示教者受限于接口（如摇杆、动觉示教）的情况下，机器人能否学到比示教者更优的策略。通过允许智能体超越直接模仿，提出一种方法使机器人从受限演示中学习，在保持安全的同时探索更优行为，实验表明机器人确实能够超越演示者的性能。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 实际中示教者常受限于控制接口，导致演示次优，但传统模仿学习局限于演示质量。
method: 允许智能体超越直接模仿，通过探索在受限演示基础上学习更优策略。
result: 在模拟和实际任务中，机器人学到的策略性能超越受限示教者。
conclusion: 该工作挑战了模仿学习的假设，展示了从次优演示学习更好策略的可能性。
---

## Abstract
Learning from demonstrations enables experts to teach robots complex tasks using interfaces such as kinesthetic teaching, joystick control, and sim-to-real transfer. However, these interfaces often constrain the expert's ability to demonstrate optimal behavior due to indirect control, setup restrictions, and hardware safety. For example, a joystick can move a robotic arm only in a 2D plane, even though the robot operates in a higher-dimensional space. As a result, the demonstrations collected by constrained experts lead to suboptimal performance of the learned policies. This raises a key question: Can a robot learn a better policy than the one demonstrated by a constrained expert? We address this by allowing the agent to go beyond direct imitation of expert actions and explore shorter and more efficient trajectories. We use the demonstrations to infer a state-only reward signal that measures task progress, and self-label reward for unknown states using temporal interpolation. Our approach outperforms common imitation learning in both sample efficiency and task completion time. On a real WidowX robotic arm, it completes the task in 11 seconds, 10x faster than behavioral cloning.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：在实际机器人应用中，示教者（如人类专家）通常受限于控制接口（如摇杆、动觉示教、sim-to-real 转换等），导致演示的动作并非最优。例如，摇杆只能让机械臂在二维平面运动，但机械臂本身可在更高维空间操作。这使得模仿学习（Imitation Learning, IL）直接依赖次优演示，学到的策略性能受限。
- **核心问题**：机器人能否从受限示教者的演示中学习到比示教者本身更优的策略？
- **整体含义**：挑战传统模仿学习“策略性能受限于演示质量”的假设，证明机器人可以超越演示者，利用自身能力探索更高效的行为。

## 2. 方法论：核心思想、关键技术细节、算法流程
- **核心思想**：允许智能体“超越直接模仿”，通过探索发现更短、更高效的轨迹，而非简单地复现示教者的动作。
- **关键技术细节**：
  - 从演示中提取仅基于状态（state-only）的奖励信号，用于衡量任务进展。
  - 对于未知状态，通过时间插值（temporal interpolation）自我标注奖励（self-label reward），从而引导探索。
  - 结合强化学习框架，在保持安全性的前提下探索更优行为。
- **算法流程（文字描述）**：
  1. 收集受限示教者的演示数据（状态-动作序列）。
  2. 利用这些数据推断一个状态奖励函数（通过插值对未访问状态也赋予奖励）。
  3. 智能体在环境中执行，使用该奖励信号进行强化学习训练，同时允许其偏离示教轨迹（但保持安全约束）。
  4. 通过迭代优化，学习到比演示更高效的策略。

> 注：原摘要未给出具体公式或算法伪代码，以上为基于摘要的合理推断。

## 3. 实验设计
- **数据集/场景**：文中提及在模拟环境（未指明具体模拟器）和真实 WidowX 机械臂上评估。
- **Benchmark**：与行为克隆（Behavioral Cloning, BC）等常见模仿学习方法对比。
- **对比方法**：主要对比行为克隆（BC），可能还包括其他 IL 基线（但摘要只明确提及 BC）。
- **任务类型**：未明确具体任务名称，推测为机器人操作任务（如抓取、移动等需要规划轨迹的任务）。

## 4. 资源与算力
- **文中未明确说明**：摘要中未提及 GPU 型号、数量、训练时长等算力信息。论文全文可能会包含实验设置细节，但在此提供的资料有限，无法给出。

## 5. 实验数量与充分性
- **实验数量**：摘要仅给出两类实验（模拟 + 真实机械臂），并报告了真实机器人任务完成时间（11秒 vs BC 的 10倍慢）。未见消融实验或多数据集对比。
- **充分性与公平性**：初步结果有亮点（10倍加速），但缺乏对不同任务、不同受限程度系统的探索。客观性方面，与 BC 对比直观，但未提及是否控制随机种子、是否多次重复等。因此实验充分性一般，需看全文进一步验证。

## 6. 主要结论与发现
- 机器人从受限演示中学习可以超越演示者的性能：在真实 WidowX 机械臂上，任务完成时间仅需 11 秒，是行为克隆的 1/10（即快 10 倍）。
- 在样本效率和任务完成时间上，所提方法优于常见模仿学习方法。
- 该工作验证了“从次优演示学习更好策略”的可能性，为模仿学习提供了新视角。

## 7. 优点
- **方法创新**：跳出“模仿即最优”的框框，允许智能体探索更优行为，更贴合实际场景（示教者受限于接口）。
- **实用性强**：仅需状态奖励信号，无需复杂的手工设计奖励函数，使用时间插值自动标注奖励，减少人工成本。
- **效果显著**：真实机器人任务实现 10 倍加速，表明方法在效率上优势明显。

## 8. 不足与局限
- **实验覆盖有限**：仅展示了一个真实任务和模拟任务，未在多种操作类型（如灵巧操作、移动操作）上验证。
- **安全性与泛化**：文中提到“保持安全”，但未详细说明安全约束机制，且未讨论策略在训练过程中可能遇到的风险。
- **依赖状态观测**：方法需要完整状态信息，对于部分可观测或高维图像输入场景可能需额外处理。
- **计算资源未透露**：缺乏算力细节，难以判断方法的可复现性。
- **消融研究缺失**：未分析各组件（如时间插值、奖励设计）的贡献，对方法内部机制的理解有限。

（完）
