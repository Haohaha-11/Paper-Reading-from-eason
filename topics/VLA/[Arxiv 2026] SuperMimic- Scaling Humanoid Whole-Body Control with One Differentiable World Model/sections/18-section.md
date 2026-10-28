[← 返回 README](../README.md)

## F.1 Benchmark metrics

We evaluate SONIC and SuperMimic on the same filtered BONES-SEED training-set benchmark, containing 129,514 evaluated motions. For SONIC we evaluate its released checkpoint and its v1.1 checkpoint [1]. Each motion plays once from its first frame, with the same simulation for every policy: foot friction 1.0, no domain randomization or observation noise, and deterministic physics. Each policy uses its own actuator gains and action scale, and receives its observations as its training environment builds them. A motion is marked as failed if the rollout triggers SuperMimic’s training termination (Appendix A). SONIC’s own success criterion fails a motion on root and end-effector height deviations above 0.25 m, without an orientation term [1].



> 💡 **评测设置**: 在 BONES-SEED 的 129,514 条动作上对比 SONIC 与 SuperMimic，每条动作从首帧播放一次。物理环境严格确定（摩擦 1.0，无域随机化、无观测噪声），但各策略保留自身执行器增益、动作缩放和观测构建方式。失败判定标准不同：SuperMimic 沿用训练终止条件，SONIC 仅基于根节点与末端执行器高度偏差超过 0.25 m，且不含朝向项。

