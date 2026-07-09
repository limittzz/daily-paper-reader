<div class="dpr-home-notice-card">
  <h3 class="dpr-home-notice-title">🚀 Start Here</h3>
  <ul class="dpr-home-notice-list">
    <li><a href="#/tutorial/README">使用教程</a></li>
  </ul>
</div>

## 每次日报
- 最新运行日期：2026-07-09
- 运行时间：2026-07-09 21:52:32 UTC
- 运行状态：成功
- 本次总论文数：19
- 精读区：8
- 速读区：11

### 今日简报（AI）
今日聚焦 Vision-Language-Action 模型，精读两篇高分论文：Q-Guided 流匹配策略与任务无关的 VLA 预训练方法。最值得关注的方向是提升 VLA 策略的推理效率与泛化能力。建议优先精读两篇 9.0 分论文，再配合速读中的加速技巧（Action Caching）与低维世界模型（KAM-WM）扩展理解。
- 详情：[/202607/09/README](/202607/09/README)

### 精读区论文标签
1. [Guided Action Flow: Q-Guided Inference for Flow-Matching Vision-Language-Action Policies](/202607/09/2607.02092v2-guided-action-flow-q-guided-inference-for-flow-matching-vision-language-action-policies)  
   标签：评分：9.0/10、query:imitation
   evidence：流匹配视觉-语言-动作策略结合Q引导推理；直接针对机器人控制的扩散策略
2. [Learning to Move Before Learning to Do: Task-Agnostic pretraining for VLAs](/202607/09/2607.02466v1-learning-to-move-before-learning-to-do-task-agnostic-pretraining-for-vlas)  
   标签：评分：9.0/10、query:imitation
   evidence：提出面向VLA模型的任务无关预训练，利用模仿数据学习运动先验
3. [ObjRetarget: An Object-Aware Motion Retargeting Framework with Anthropomorphic Arm Constraints and Polyhedral Hand Modeling](/202607/09/2607.03828v1-objretarget-an-object-aware-motion-retargeting-framework-with-anthropomorphic-arm-constraints-and-polyhedral-hand-modeling)  
   标签：评分：9.0/10、query:imitation
   evidence：将人类操作重定向到机器人动作，实现从人类视频的模仿学习
4. [Spatial Attention: Adapting Execution Horizons for Diffusion Policies via Observation Sensitivity](/202607/09/2607.04739v1-spatial-attention-adapting-execution-horizons-for-diffusion-policies-via-observation-sensitivity)  
   标签：评分：9.0/10、query:imitation
   evidence：针对机器人示教学习中的扩散策略自适应调整执行视野
5. [Optimal Transport Q-Learning for Flow Policy Steering and Acceleration](/202607/09/2607.06262v1-optimal-transport-q-learning-for-flow-policy-steering-and-acceleration)  
   标签：评分：9.0/10、query:imitation
   evidence：微调流策略用于机器人控制；与扩散策略和从演示学习直接相关
6. [LAMP: Latent Motion Prior-Guided Real-World Learning for Dexterous Hand Manipulation](/202607/09/2607.06323v1-lamp-latent-motion-prior-guided-real-world-learning-for-dexterous-hand-manipulation)  
   标签：评分：9.0/10、query:imitation
   evidence：模仿学习与强化学习结合用于灵巧手操作
7. [SPECTRA: Context-Conditioned Spectral Movement Primitives for Robot Skill Generalization](/202607/09/2607.06978v1-spectra-context-conditioned-spectral-movement-primitives-for-robot-skill-generalization)  
   标签：评分：9.0/10、query:imitation
   evidence：面向机器人操作模仿学习的谱运动基元
8. [Compositional Motion Generation from Demonstration with Object-Centric Neural Fields](/202607/09/2607.07129v1-compositional-motion-generation-from-demonstration-with-object-centric-neural-fields)  
   标签：评分：9.0/10、query:imitation
   evidence：生成式学习从示范中，通过组合建模用于操作

### 速读区论文标签
1. [High-Fidelity One-Step Generative Visuomotor Policy via Recursive Correction, Frequency Consistency, and Contrastive Flow Matching](/202607/09/2607.03865v1-high-fidelity-one-step-generative-visuomotor-policy-via-recursive-correction-frequency-consistency-and-contrastive-flow-matching)  
   标签：评分：8.0/10、query:imitation
   evidence：高保真一步生成式视觉运动策略，结合扩散与流匹配用于机器人控制
2. [KAM-WM: Kinematic Affordance Maps from Latent World Models for Robot Manipulation](/202607/09/2607.04652v1-kam-wm-kinematic-affordance-maps-from-latent-world-models-for-robot-manipulation)  
   标签：评分：8.0/10、query:imitation
   evidence：基于世界模型提取的运动学affordance图条件扩散策略用于少样本操作
3. [Training-Free Acceleration for Vision-Language-Action Models with Action Caching and Refinement](/202607/09/2607.06370v1-training-free-acceleration-for-vision-language-action-models-with-action-caching-and-refinement)  
   标签：评分：8.0/10、query:imitation
   evidence：基于流匹配的VLA模型机器人控制，动作缓存加速
4. [WAM-TTT: Steering World-Action Models by Watching Human Play at Test Time](/202607/09/2607.06988v1-wam-ttt-steering-world-action-models-by-watching-human-play-at-test-time)  
   标签：评分：8.0/10、query:imitation
   evidence：从人类玩耍视频适应世界动作模型，一种模仿学习形式
5. [FAR: Failure-Aware Retry for Test-Time Recovery and Continual Policy Improvement](/202607/09/2607.01111v1-far-failure-aware-retry-for-test-time-recovery-and-continual-policy-improvement)  
   标签：评分：7.0/10、query:imitation
   evidence：利用失败数据进行机器人策略自适应
6. [WorldSample: Closed-loop Real-robot RL with World Modelling](/202607/09/2607.02431v1-worldsample-closed-loop-real-robot-rl-with-world-modelling)  
   标签：评分：7.0/10、query:imitation
   evidence：利用世界模型克服模仿学习限制；与机器人操作模仿学习的最新进展相关
7. [Function-Space Diffusion for Motion Planning](/202607/09/2607.02977v1-function-space-diffusion-for-motion-planning)  
   标签：评分：7.0/10、query:imitation
   evidence：函数空间扩散用于运动规划，可应用于机器人控制
8. [CoRE-VLA: Towards Scalable and Robust Vision-Language-Action Modeling via Conditional Routing of Experts](/202607/09/2607.03693v1-core-vla-towards-scalable-and-robust-vision-language-action-modeling-via-conditional-routing-of-experts)  
   标签：评分：7.0/10、query:imitation
   evidence：面向机器人操作的VLA模型，基于模仿学习范式
9. [Human-Centric Transferable Tactile Pre-Training for Dexterous Robotic Manipulation](/202607/09/2607.01067v1-human-centric-transferable-tactile-pre-training-for-dexterous-robotic-manipulation)  
   标签：评分：6.0/10、query:imitation
   evidence：触觉预训练用于灵巧操作，与模仿学习相关
10. [TACO: TActile World Model as a Self-COrrector forScalable VLA Post-Training](/202607/09/2607.02840v1-taco-tactile-world-model-as-a-self-corrector-forscalable-vla-post-training)  
   标签：评分：6.0/10、query:imitation
   evidence：触觉世界模型用于VLA策略后训练；改善接触丰富操作中的模仿学习
11. [HALO-WA: Hybrid-Attention Latent-Guided Online Reinforcement Learning for World-Action Models](/202607/09/2607.04265v1-halo-wa-hybrid-attention-latent-guided-online-reinforcement-learning-for-world-action-models)  
   标签：评分：6.0/10、query:imitation
   evidence：用于机器人操作的世界动作模型的在线强化学习自适应


<div class="dpr-home-promo-card">
  <h3 class="dpr-home-promo-title">💬 社区与支持</h3>
  <ul class="dpr-home-promo-list">
    <li>欢迎 Star / Fork / Issue / PR</li>
    <li>QQ群：583867967（欢迎交流，已有：1151人）</li>
  </ul>
</div>
