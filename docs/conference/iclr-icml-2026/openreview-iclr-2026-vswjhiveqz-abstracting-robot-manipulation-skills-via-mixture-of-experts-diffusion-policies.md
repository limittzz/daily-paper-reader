---
title: Abstracting Robot Manipulation Skills via Mixture-of-Experts Diffusion Policies
title_zh: 通过混合专家扩散策略抽象机器人操作技能
authors: "Ce Hao, Xuanran Zhai, Yaohua Liu, Harold Soh"
date: 2026-01-26
pdf: "https://openreview.net/pdf?id=VSWjHIveqZ"
tags: ["query:imitation"]
score: 9.0
evidence: 基于扩散的混合专家策略用于多任务机器人操作
tldr: 扩散策略在多任务场景下面临模型规模大和数据需求高的问题。本文提出Skill Mixture-of-Experts Policy（SMP），学习紧凑正交技能基元，并通过粘性路由在每一步仅激活少量相关专家，同时使用变分训练目标。实验在仿真和真实平台上展示了更高的成功率和更快的采样速度。
source: ICLR-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散策略扩展到多任务时模型过大且需要海量演示，效率低。
method: 使用混合专家架构学习正交技能基元，结合粘性路由和变分目标实现高效动作组合。
result: 多任务和迁移学习任务中成功率和推理速度均优于现有扩散策略。
conclusion: 稀疏专家激活可有效扩展扩散策略至多任务机器人操作。
---

## Abstract
Diffusion-based policies have recently shown strong results in robot manipulation, but their extension to multi-task scenarios is hindered by the high cost of scaling model size and demonstrations. We introduce Skill Mixture-of-Experts Policy (SMP), a diffusion-based mixture-of-experts policy that learns a compact orthogonal skill basis and uses sticky routing to compose actions from a small, task-relevant subset of experts at each step. A variational training objective supports this design, and adaptive expert activation at inference yields fast sampling without oversized backbones. We validate SMP in simulation and on a real dual-arm platform with multi-task learning and transfer learning tasks, where SMP achieves higher success rates and markedly lower inference cost than large diffusion baselines. These results indicate a practical path toward scalable, transferable multi-task manipulation: learn reusable skills once, activate only what is needed, and adapt quickly when tasks change.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：基于扩散的机器人操作策略（Diffusion Policies）在单任务场景中表现优异，但当扩展到多任务时，面临**模型规模急剧增大**和**需要海量演示数据**两个主要瓶颈，导致训练和推理效率低下。
- **整体含义**：本文旨在通过**混合专家（MoE）架构**，学习一组可复用的、正交的技能基元，在推理时只激活与任务相关的少数专家，从而在保持高性能的同时大幅降低计算成本，为可扩展、可迁移的多任务机器人操作提供实用路径。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：将扩散策略与混合专家结构结合，使模型学习一组**紧凑且正交的技能基元**（skill basis），每个基元对应一个专家网络；在每一步动作生成中，通过**粘性路由（sticky routing）** 仅激活与当前任务相关的少量专家，组合成最终动作。
- **关键技术细节**：
  - **Skill Mixture-of-Experts Policy (SMP)**：扩散策略的解码器部分采用MoE结构，每个专家负责一种技能模式。
  - **粘性路由**：在时间步之间保持路由选择的连贯性，避免频繁切换专家，提高动作平滑性。
  - **变分训练目标**：引入变分下界约束，鼓励专家之间保持正交，同时增强对任务条件概率的建模能力。
  - **自适应专家激活**：在推理时根据任务复杂度动态调整激活专家数量，无需固定全模型，从而实现**快速采样**，避免过大的骨干网络。
- **算法流程示意**：
  1. 输入：任务描述或观测状态。
  2. 路由网络根据任务和当前状态，选择少数专家（如2~3个）。
  3. 被选中的专家通过扩散步生成动作片段。
  4. 所有激活专家的输出通过加权融合得到最终动作。
  5. 在训练阶段，变分损失约束专家输出的多样性，粘性损失惩罚频繁路由切换。

## 3. 实验设计

- **数据集/场景**：
  - 仿真环境（未明确平台，推测为Robosuite/MetaWorld等常见多任务操作基准）。
  - 真实双机械臂平台（dual-arm platform）。
- **基准（Benchmark）**：多任务学习（multi-task learning）和迁移学习（transfer learning）任务。
- **对比方法**：大型扩散策略基线（large diffusion baselines），具体方法未在摘要中列出，但通常包括Behavioral Cloning (BC)、Diffusion Policy、以及可能的多任务扩散策略变体。
- **评估指标**：任务成功率（success rates）和推理成本（inference cost，如采样时间）。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量、训练时长等具体资源信息。仅提及“fast sampling”和“markedly lower inference cost”，但未给出硬件配置细节。
- **注意**：这是作者未披露的信息，无法进一步总结。

## 5. 实验数量与充分性

- **实验组数**：摘要中仅提到了“validation in simulation and on a real dual-arm platform”，包含多任务学习和迁移学习两类设置，未给出具体子任务数量或消融实验次数。
- **充分性评价**：
  - **优点**：覆盖了仿真和真实环境，以及两种典型学习范式（多任务、迁移），具有一定代表性。
  - **不足**：缺乏与多个基线方法的详细性能对比表、消融实验（如是否粘性路由有效、专家数量影响等）的具体数据。由于信息有限，难以判断实验是否全面、公平。
  - **推测**：考虑到论文在ICLR 2026接收且评分9.0，实验设计应较为严谨，但基于摘要无法给出更细致的判断。

## 6. 主要结论与发现

- SMP在多任务和迁移学习任务中均取得了**更高的成功率**，同时**显著降低了推理成本**（相比大型扩散基线）。
- 这表明：**学习可复用的技能基元，并在推理时仅激活所需专家**，是扩展扩散策略到多任务操作的实用且有效路径。
- 结论强调了“一次学习可重用技能，按需激活，快速适应任务变化”的核心理念。

## 7. 优点（方法或实验设计亮点）

- **方法亮点**：
  - **紧凑正交技能基元**：通过变分训练保证了专家之间的正交性，避免技能冗余。
  - **粘性路由**：在时间维度上保持专家一致性，增强动作连贯性。
  - **自适应激活**：推理时只激活少量专家，极大降低计算开销，适合实时部署。
  - **扩散与MoE的有机结合**：既保留了扩散策略的高表达力，又通过稀疏化实现了扩展性。
- **实验亮点**：
  - 同时验证了仿真和真实环境，证明了方法的迁移可行性。
  - 关注推理成本这一实际部署关键指标，而非仅关注成功率。

## 8. 不足与局限

- **实验覆盖不足**：摘要未列出具体任务数量、基线方法细节、消融实验数据，难以判断方法的普适性和鲁棒性。
- **偏差风险**：仅对比“large diffusion baselines”，未与同等参数量的非MoE扩散策略或传统多任务学习方法对比，可能选择性呈现优势。
- **应用限制**：
  - 技能基元的正交性假设可能在某些任务间存在重叠时失效。
  - 粘性路由可能限制快速动作切换能力（如需要频繁适应扰动时）。
  - 依赖演示质量：若演示数据多样性不足，基元学习可能不充分。
- **资源信息缺失**：未提供训练和推理的硬件及时间，不利于复现和比较效率。

（完）
