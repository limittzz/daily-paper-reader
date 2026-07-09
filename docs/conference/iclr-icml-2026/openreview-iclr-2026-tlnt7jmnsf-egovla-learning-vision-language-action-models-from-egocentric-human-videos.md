---
title: "EgoVLA: Learning Vision-Language-Action Models from Egocentric Human Videos"
title_zh: EgoVLA：从自我中心人类视频学习视觉-语言-动作模型
authors: "Ruihan Yang, Qinxi Yu, Yecheng Wu, Rui Yan, Borui li, An-Chieh Cheng, Xueyan Zou, Yunhao Fang, Xuxin Cheng, Ri-Zhao Qiu, Hongxu Yin, Sifei Liu, Song Han, Yao Lu, Xiaolong Wang"
date: 2025-09-08
pdf: "https://openreview.net/pdf?id=TLNT7JmNsf"
tags: ["query:imitation"]
score: 9.0
evidence: 从人类视频学习VLA模型用于机器人模仿
tldr: 机器人模仿学习受限于真实机器人数据采集成本。本文提出EgoVLA，利用大规模第一人称人类视频预训练视觉-语言-动作（VLA）模型，预测人类手腕和手部动作，并通过逆运动学重定向到机器人动作。仅需少量机器人演示微调即可获得机器人策略。在仿真基准上，EgoVLA展现了出色的跨任务泛化能力。该方法有效缓解了机器人数据稀缺问题。
source: ICLR-2026-Rejected-Public
selection_source: conference_retrieval
motivation: 真实机器人数据采集成本高，限制了模仿学习的规模。
method: 利用大规模人类视频预训练VLA模型预测人类动作，再通过逆运动学重定向和少量演示微调获得机器人策略。
result: 在仿真基准上，EgoVLA在多个操作任务上优于基于真实机器人数据训练的方法。
conclusion: 利用人类视频数据可以显著降低机器人模仿学习对硬件数据的依赖。
---

## Abstract
Real robot data collection for imitation learning has led to significant advances in robotic manipulation. 
However, the requirement for robot hardware in the process fundamentally constrains the scale of the data.
In this paper, we explore training Vision-Language-Action (VLA) models using egocentric human videos. The benefit of using human videos is not only for their scale but more importantly for the richness of scenes and tasks. With a VLA trained on human video that predicts human wrist and hand actions, we can perform Inverse Kinematics and retargeting to convert the human actions to robot actions. We fine-tune the model using a few robot manipulation demonstrations to obtain the robot policy, namely EgoVLA. We propose a simulation benchmark called Ego Humanoid Manipulation Benchmark, where we design diverse bimanual manipulation tasks with demonstrations. We fine-tune and evaluate EgoVLA with \benchmarkName and show significant improvements over baselines and ablate the importance of human data

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：真实机器人数据采集成本高昂，硬件依赖导致数据规模受限，从而制约了机器人模仿学习（imitation learning）的扩展性和泛化能力。
- **整体含义**：本文探索利用大规模**第一人称人类视频（egocentric human videos）** 来训练视觉-语言-动作（VLA）模型，以缓解机器人数据稀缺问题。人类视频不仅数量大，而且场景和任务丰富多样，有望使机器人策略获得更强的泛化能力。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：先在大规模人类视频上预训练一个VLA模型，该模型能够预测人类手腕和手部动作；然后通过**逆运动学（Inverse Kinematics, IK）** 和**重定向（retargeting）** 将预测的人类动作转换为机器人动作；最后使用少量机器人演示数据对模型进行微调，得到最终的机器人策略——**EgoVLA**。
- **关键技术细节**：
  - 预训练阶段：使用第一人称视频作为输入，训练VLA模型输出人类手腕位置、手部姿态等动作序列。
  - 动作转换：利用逆运动学将人类手腕/手部运动映射到机器人关节空间。
  - 微调阶段：仅需要**少量真实机器人演示**（文中未给出具体数量，但强调“few”），对预训练模型进行参数更新，使其适配机器人本体和执行具体操作任务。
- **算法流程（文字说明）**：
  1. 收集大规模第一人称人类视频数据。
  2. 在视频数据上训练VLA模型：输入视频帧和语言指令，输出人类动作（手腕/手部）。
  3. 对每个任务，准备少量机器人演示数据，记录机器人关节轨迹。
  4. 将人类动作通过IK重定向为机器人动作，作为监督信号。
  5. 使用机器人演示数据对预训练VLA进行微调，优化策略网络参数。
  6. 部署微调后的EgoVLA模型在机器人上执行任务。

## 3. 实验设计

- **数据集/场景**：
  - 人类视频数据：采用大规模第一人称视频（具体来源未在摘要详述，推测为公开的egocentric数据集，如Ego4D等）。
  - 仿真环境：提出**Ego Humanoid Manipulation Benchmark**，包含多样化的双手操作任务以及对应的演示数据。
- **Benchmark**：自建的仿真基准，设计多种双手操作任务（如装配、递送等）。
- **对比方法**：
  - 与基于真实机器人数据训练的基线（如直接使用机器人演示训练VLA）进行比较。
  - 进行消融实验，验证人类数据预训练的重要性（如去除预训练或减少预训练数据量）。

## 4. 资源与算力

- 论文摘要及元数据中**未明确说明**使用的GPU型号、数量、训练时长等具体算力资源。仅提及在仿真基准上进行训练和评估，未提及硬件配置细节。

## 5. 实验数量与充分性

- **实验数量**：摘要中只提到“significant improvements over baselines and ablate the importance of human data”，未给出具体实验组数量。推测至少包含：
  - 主实验：EgoVLA vs 多种基线（在多个任务上）。
  - 消融实验：至少有一个消融（移除人类数据预训练）。
- **充分性与客观性**：
  - 优点是使用了仿真环境，可以公平控制变量，对比条件清晰。
  - 缺点是缺乏真实机器人实验验证（仅仿真），且未公开具体的数据量、微调样本数等关键参数，使得复现性存疑。
  - 对比的基线方法范围未明说，可能存在选择性比较的风险。

## 6. 论文的主要结论与发现

- 利用大规模人类视频预训练VLA模型，能够显著提升机器人操作任务的跨任务泛化能力。
- 仅需少量机器人演示微调，即可获得高效的机器人策略，相比仅用机器人数据训练的方法有明显改进。
- 人类视频数据中的场景和任务多样性是性能提升的关键因素。

## 7. 优点：方法或实验设计上的亮点

- **数据利用创新**：首次系统探索将第一人称人类视频用于VLA预训练，通过IK重定向桥接人类动作与机器人动作。
- **高效微调**：大幅降低对昂贵机器人数据的依赖，只需少量演示即可适配新本体和新任务。
- **仿真基准设计**：专门针对双手操作任务构建Ego Humanoid Manipulation Benchmark，填补了相关评估工具的空白。

## 8. 不足与局限

- **缺少真实机器人实验**：仅仿真验证，未在真实机器人上部署和测试，真实环境中的噪声、动力学差异等问题未被评估。
- **关键细节缺失**：未公开预训练数据集规模、微调用机器人演示数量、模型架构参数等，难以复现和公平对比。
- **任务范围有限**：只涉及双手操作任务，未验证其他类型任务（如灵巧操作、移动操作）的泛化性。
- **偏差风险**：人类视频中的动作分布可能与机器人可行动作空间存在差距（如人类手掌自由度与机器人夹爪不同），IK重定向可能存在误差，文中未充分讨论该误差对最终策略的影响。

（完）
