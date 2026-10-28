[← 返回 README](../README.md)

## 1 Introduction

Humanoid robots hold the promise of operating in environments designed for people, but doing so requires whole-body controllers that span diverse human motion, from locomotion and dance to crouching and balance recovery. Physics-based motion imitation addresses this by training RL policies to track reference motion clips [2] drawn from large motion-capture (mocap) datasets [1]. Existing systems scale this idea either by distilling many clip-specific experts into one controller [3] or by training a generalist policy over a large motion library [1]. In both cases, a more diverse motion library produces a more capable controller. But both also become computationally expensive as motion coverage grows: the state-of-the-art tracker SONIC [1], for example, trains for ∼168 h on 128 GPUs, totaling roughly 21,000 GPU-hours. Although reference motions differ, they share the same humanoid hybrid rigid-body dynamics. Model-free RL fails to explicitly model this shared structure, instead repeatedly estimating action consequences from noisy simulator rollouts. Scaling to more motions therefore amounts to scaling up compute.



> 💡 **计算瓶颈**: 人形机器人的全身控制依赖大规模动作库，而参考动作虽然不同，却共享同一套人形刚体动力学；SONIC 需 128 GPU 训练约 168 h（≈21,000 GPU-hours），说明模型无关 RL 没有显式利用这一共享结构，动作覆盖越多，算力开销就越大。

SuperMimic addresses this inefficiency with a single learned, differentiable world model fit from logged simulator transitions. Backpropagation-through-time (BPTT) through short imagined rollouts provides analytic policy gradients through the learned model, allowing one dynamics model to amortize learning across the entire mocap library. Because these gradients come from the learned model rather than from differentiating the simulator, SuperMimic remains compatible with rigid body simulators, including those with non-smooth contacts and non-differentiable implementations.



> 💡 **核心机制**: SuperMimic通过拟合单一可微世界模型替代模拟器梯度，利用BPTT在短想象轨迹中解析计算策略梯度，从而在保持与非平滑接触模拟器兼容的同时实现跨动作库的学习摊销。

On one GPU, SuperMimic surpasses the normalized return of SONIC’s released checkpoint within 3 h and reduces GPU-hours by more than 1000× (§4.1). On a 129,514-motion benchmark, Super-Mimic fails on 25 motions after 3 h on one GPU and on none after 9.8 h on two GPUs, against 179 for SONIC’s released checkpoint (Appendix F). Across a 264× increase in dataset length, the world model’s prediction error stays stable and SuperMimic’s compute to a fixed return grows slowly. The learned policy transfers to a physical Unitree G1. In a preliminary study of downstream interfaces, an RL-trained velocity encoder outperforms a distillation baseline.



> 💡 **效率实证**: 单 GPU 上 3 h 内超过 SONIC 发布检查点的归一化回报，GPU-hours 减少 1000× 以上（§4.1）；在 129,514 个动作的基准上，单 GPU 3 h 失败 25 个动作、双 GPU 9.8 h 零失败，SONIC 发布检查点失败 179 个（Appendix F）。数据集长度扩大 264× 时世界模型预测误差稳定、达到固定回报的算力增长缓慢，策略也已迁移到实体 Unitree G1。

