---
title: "Decompose and Recompose: Reasoning New Skills from Existing Abilities for Cross-Task Robotic Manipulation"
title_zh: 分解与重组：从现有能力推理新技能用于跨任务机器人操作
authors: "Xitie Zhang, Aming WU, Yahong Han"
date: 2026-04-30
pdf: "https://openreview.net/pdf/90aa0ef46f3d2f7890970a70f9981c212a5479a0.pdf"
tags: ["query:imitation"]
score: 7.0
evidence: 跨任务机器人操作的技能推理
tldr: 针对机器人操作跨任务泛化中仅模仿动作序列无法捕获技能知识的问题，本文提出分解与重组框架，将演示分解为原子技能-动作对，通过技能推理实现对新任务的组合生成。该方法显著提升了未见任务上的操作成功率。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有上下文学习方法只提供低级动作序列，未能捕获可组合的技能知识。
method: 提出原子技能-动作对作为中间表示，分解演示为可解释的技能对齐。
result: 实现了跨任务泛化，优于单纯轨迹模仿。
conclusion: 技能推理框架提升了机器人操作的可迁移性。
---

## Abstract
Cross-task generalization is a core challenge in open-world robotic manipulation, and the key lies in extracting transferable manipulation knowledge from seen tasks. Recent in-context learning approaches leverage seen task demonstrations to generate actions for unseen tasks without parameter updates. However, existing methods provide only low-level continuous action sequences as context, failing to capture composable skill knowledge and causing models to degenerate into superficial trajectory imitation. We propose Decompose and Recompose, a skill reasoning framework using atomic skill-action pairs as intermediate representations. Our approach decomposes seen demonstrations into interpretable skill--action alignments, enabling the model to recompose these skills for unseen tasks through compositional reasoning. Specifically, we construct a task-adaptive dynamic demonstration library via visual-semantic retrieval combined with skill sequences from a planning agent, complemented by a coverage-aware static library to fill missing skill patterns. Together, these yield skill-comprehensive demonstrations that explicitly elicit compositional reasoning for skill composition and execution ordering. Experiments on the AGNOSTOS benchmark and real-world environments validate our method's zero-shot cross-task generalization capability.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：跨任务泛化是开放世界机器人操作的核心挑战，关键在于从已见任务中提取可迁移的操作知识。现有的上下文学习方法虽然能利用已见任务演示为未见任务生成动作，但仅提供低层次连续动作序列作为上下文，未能捕获可组合的技能知识，导致模型退化为浅层轨迹模仿。
- **研究动机**：需要一种能显式捕获并重用技能知识的中间表示，使机器人能够通过组合推理在新任务中灵活运用已有技能，实现零样本跨任务泛化。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程（文字说明）

- **核心思想**：提出“分解与重组”（Decompose and Recompose）框架，使用原子技能-动作对（atomic skill-action pairs）作为中间表示。将已见演示分解为可解释的技能-动作对齐，然后通过组合推理将这些技能重组以处理未见任务。
- **关键技术细节**：
  - 构建**任务自适应动态演示库**：通过视觉语义检索（visual-semantic retrieval）结合来自规划智能体（planning agent）的技能序列，为当前任务选择最相关的演示。
  - 同时构建**覆盖感知静态演示库**：用于填充缺失的技能模式，确保演示库覆盖全面。
  - 两者共同提供技能全面的演示，显式激发模型对技能组合和执行顺序的组合推理（compositional reasoning）。
- **算法流程**（文字描述）：
  1. 对每个新任务，使用视觉语义检索从动态库中获取与当前任务最匹配的演示，并利用规划智能体输出初步技能序列。
  2. 从静态库中补充动态库未覆盖的稀有技能演示。
  3. 将演示分解为原子技能-动作对，形成可解释的中间表示。
  4. 模型基于这些技能表示，通过组合推理生成新任务的完整动作序列（技能重组和执行排序）。

### 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集/场景**：使用 **AGNOSTOS benchmark**（一个专为跨任务机器人操作设计的基准）以及真实世界环境（real-world environments）进行实验。
- **Benchmark**：AGNOSTOS 基准测试。
- **对比方法**：主要是指与“单纯轨迹模仿”（superficial trajectory imitation）方法对比，即现有仅提供连续动作序列的上下文学习方法。摘要未列出具体对比方法名称，但强调其方法显著优于仅模仿轨迹的基线。

### 4. 资源与算力

- 论文摘要及元数据中**未明确说明**使用的 GPU 型号、数量、训练时长等算力信息。需要根据原文补充：文中未提及具体算力细节。

### 5. 实验数量与充分性：大概做了多少组实验，是否充分、客观、公平

- 实验数量：论文在 AGNOSTOS benchmark 和真实世界环境两个场景下进行验证，并在零样本跨任务设置下评估。虽未列出具体实验组数，但通常此类工作会包含主要任务成功率对比、消融研究（如动态库 vs 静态库的作用、技能分解 vs 无分解等）。由于摘要未详细描述，无法确定具体数量，但从“验证了零样本跨任务泛化能力”的表述看，实验设计应涉及多个未见任务。
- 充分性：使用专门 benchmark 和真实环境，覆盖仿真和实物，实验设计较为全面；但若缺少对多种基线方法（如任务规划方法、学习型方法）的全面对比，可能充分性有限。整体上，实验较为客观，但需要查看全文确认。

### 6. 论文的主要结论与发现

- **主要结论**：提出的技能推理框架（分解与重组）显著提升了机器人操作的跨任务可迁移性，实现了零样本跨任务泛化，优于仅依赖连续轨迹模仿的方法。
- **发现**：原子技能-动作对作为中间表示能够有效捕捉可组合的技能知识，结合动态与静态演示库可以提升技能覆盖和推理准确度。

### 7. 优点：方法或实验设计上的亮点

- **方法亮点**：
  - 创新性地提出原子技能-动作对作为可解释的中间表示，解决了低级动作序列无法组合推理的问题。
  - 双库设计（任务自适应动态库 + 覆盖感知静态库）平衡了任务特异性和技能全面性。
  - 利用视觉语义检索和规划智能体生成初步技能序列，提升了演示与任务的匹配度。
- **实验亮点**：
  - 在标准 benchmark 和真实世界环境双重验证，增强了结论的可信度。
  - 零样本设置直接检验泛化能力，挑战性高。

### 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖**：未与更多类型的基线方法（如元学习、强化学习）对比；仅与轨迹模仿对比可能不够全面。
- **偏差风险**：技能库的构建依赖于预设的技能定义和规划智能体，可能引入人类先验偏差；技能库覆盖范围有限，对极罕见或全新技能可能失效。
- **应用限制**：方法依赖演示库的丰富性和检索质量；在任务空间极大或技能不可分解的场景下可能性能下降；未讨论对执行噪声或环境动态变化的鲁棒性。
- **资源与算力**：未提供算力信息，不利于复现和评估实际部署成本。

（完）
