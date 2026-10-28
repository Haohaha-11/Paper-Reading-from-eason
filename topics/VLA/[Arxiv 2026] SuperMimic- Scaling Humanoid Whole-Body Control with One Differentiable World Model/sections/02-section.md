[← 返回 README](../README.md)

## Contributions.

• Model-based RL (MBRL) for deployable humanoid control. We demonstrate that BPTT through a learned, differentiable world model can train whole-body tracking policies in a setting long dominated by model-free proximal policy optimization (PPO) [4], and that the resulting controller transfers to a physical Unitree G1.



> 💡 **方法定位**: 用可微世界模型加 BPTT（沿时间反向传播）来训练全身跟踪策略，在长期由 model-free PPO 主导的人形控制设定里给出 MBRL 可行的证据，并让控制器迁移到实体 Unitree G1。原文的 claim 是“能训练、能迁移”，并未说已经取代 PPO。

• Compute is no longer the bottleneck to scaling. SuperMimic surpasses the return of SONIC, a cluster-scale PPO pipeline, on a single GPU and reduces GPU-hours by more than 1000× (§4.1). Its training cost grows slowly as the motion library expands, making scaling humanoid wholebody control practical without huge GPU infrastructure.



> 💡 **算力突破**: 在单 GPU 上，SuperMimic 的回报（return）超过集群级 PPO 管线 SONIC，GPU-hours 减少超过 1000×（§4.1）；动作库扩大时训练成本增长缓慢，使规模化人形全身控制不再必须依赖大规模 GPU 基础设施。

• An analysis of the ingredients behind the gains. We identify three world-model properties behind the compute reduction: prediction error that stays stable over a 264× increase in dataset length, rollouts more than 10× faster than the simulator, and analytic gradients, with which the policy reaches the best return of PPO on the world model with about 64× fewer imagined steps.



> 💡 **机制解析**: 作者把算力下降归因于世界模型的三个属性：数据集长度扩大 264× 时预测误差仍稳定、rollout 比模拟器快 10× 以上、以及解析梯度——后者让策略用约 64× 更少的想象步数达到 PPO 在世界模型上的最佳回报。

• RL-trained downstream control beyond distillation. As a preliminary study, the same learned world model trains a downstream goal encoder on the interface’s own task reward. On velocity commands, the policy tracks more accurately with this encoder than with a distillation baseline.



> 💡 **下游应用**: 作为初步研究，同一个学习到的世界模型用接口自身的任务奖励训练下游 goal encoder；在速度指令上，用该 encoder 的策略跟踪精度优于蒸馏基线。原文只报告了这一项初步对比，并未得出“通用性”结论，把它推广到一般 RL 任务属于推断。

