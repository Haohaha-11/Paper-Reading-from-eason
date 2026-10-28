[← 返回 README](../README.md)

## G.1 Training setup

The velocity encoder of §4.5 maps the command $( v _ { x } , v _ { y } , \omega _ { z } )$ and a feature of the last 10 frames of o to the shared latent.



> 💡 **输入定义**: 速度编码器把指令 $( v_x, v_y, \omega_z )$ 与观测 $o$ 最近 10 帧的特征一起映射到共享的 latent 空间。这条说明 §4.5 里“速度接口”的输入到底由什么构成，是理解后面蒸馏损失作用对象的前提。

Each encoder is trained from scratch in its own run, jointly with the motion-tracking interface, for 100,000 iterations. Both runs use a walking and running library filtered from LAFAN1 and BONES-SEED. Half of the 4096 simulator environments and half of each imagined batch use the velocity interface, so $p ( \mathcal { T } )$ is uniform; unlike Algorithm A.1, which samples one interface per update, each imagined batch mixes both.



> 💡 **并行训练设置**: 两个编码器各在自己的 run 中从头训练 100,000 次迭代，并与运动追踪接口联合训练；动作库是 LAFAN1 与 BONES-SEED 过滤出的行走/奔跑片段。4096 个模拟器环境的一半、以及每个想象批次的一半走速度接口，因此 $p(\mathcal{T})$ 均匀；与每次更新只采一种接口的 Algorithm A.1 不同，这里每个批次内部混合两种接口。

Commands. The command $\mathbf { \nabla } \mathbf { g } _ { t } = ( v _ { x } , v _ { y } , \omega _ { z } )$ stacks the target planar root velocity in the heading (yaw-only) frame and the target world-frame yaw rate. A reference command is the centered $2 5 -$ frame mean of the reference root velocity in the reference’s heading frame. For the RL encoder, each environment resamples its command source at every reset and every 250 control steps (5 s): with probability 0.5 it reads the command from the reference clip at every step, and otherwise it holds a command drawn uniformly from $v _ { x } \in [ - 1 . 5 , 2 ] \mathrm { m } / \mathrm { s } , v _ { y } \in [ - 1 . 3 2 , 1 . 3 2 ] \mathrm { m } / \mathrm { s }$ and $\omega _ { z } \in$ $[ - 3 . 3 5 , 3 . 3 5 ] \mathrm { r a d / s } .$ , zeroed (standing) with probability 0.1.



> 💡 **指令采样**: 参考指令是参考片段在自身朝向坐标系下中心化的 25 帧平均根速度；RL 侧每个环境在每次 reset 和每 250 个控制步（5 s）重采样指令来源，0.5 概率逐帧读取参考片段，否则从 $v_x\in[-1.5,2]$、$v_y\in[-1.32,1.32]$ m/s 与 $\omega_z\in[-3.35,3.35]$ rad/s 均匀采样，另有 0.1 概率置零（站立）。这套分布决定了速度编码器见到的指令覆盖范围，复现时不能随意改动。

Encoder. On rollouts with reference commands, a mean-squared loss of weight 0.1 pulls the velocity latent toward the motion encoder’s latent at the same step, with a stop-gradient on the motion latent.



> 💡 **蒸馏损失**: 在带参考指令的 rollout 上，用权重 0.1 的 MSE 损失把速度 latent 拉向同一步运动编码器的 latent，并对运动侧 latent 施加 stop-gradient。这样梯度只更新速度编码器一侧，让两种模态的表示在同一潜空间对齐。

Rollouts, reward, and termination. Each interface’s imagined rollouts start from states logged by its own simulator environments. Velocity rollouts of the RL encoder use the velocity-tracking reward and a fall-only termination (Table A.6); those of the distillation baseline use the motiontracking reward and termination of Appendix A.



> 💡 **奖励与终止条件**: 各接口的想象 rollout 从它自己模拟器记录的状态出发。RL 编码器的速度 rollout 使用速度跟踪奖励和仅跌倒终止（Table A.6）；蒸馏基线则用附录 A 的运动跟踪奖励与终止。两者奖励不同，对比时要注意这是训练目标差异而非单纯结构差异。

