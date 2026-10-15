[← 返回 README](../README.md)

## A.1. Model Architecture and Representation

Dual-stream backbone. We use a 30-layer dual-stream Mixture-of-Transformers (MoT) architecture with modalityspecific video and action experts. The video expert has a hidden dimension of 3072, 24 attention heads with a head dimension of 128, and an FFN dimension of 14336. The action expert has a hidden dimension of 2048 and an FFN dimension of 8192, while retaining the same attention configuration of 24 heads with a head dimension of 128. Consequently, its query, key, and value projections each map the 2048-dimensional hidden state to 3072 dimensions, and the attention output is projected back to 2048 dimensions. Cross-modal information exchange is controlled by the future cross-modal attention pattern associated with each interaction program. Task text conditions both streams through a separate cross-attention layer.



> 💡 **MoT 双流骨干**: 30 层，视频专家 hidden 3072 / FFN 14336，动作专家 hidden 2048 / FFN 8192，两者均为 24 头、头维 128。动作专家 QKV 把 2048 维投影到 3072 维再投回 2048 维，只是维度对齐；跨模态信息交换实际由每个 interaction program 关联的 future cross-modal attention pattern 控制，任务文本经单独 cross-attention 层注入两流。

Visual observations. Each observation contains an agentview image and a wrist-camera image, both at $1 2 8 \times 1 2 8$ resolution. The two views are encoded independently into $8 \times 8 \times 4 8$ VAE latents and concatenated along the spatial width dimension to form an $8 \times 1 6 \times 4 8$ latent canvas. Using a $1 \times 2 \times 2$ temporal-spatial patch size, each latent frame contains 32 video tokens. The video VAE temporally compresses every four environment steps into one latent video frame. Each generation chunk spans four future latent frames, corresponding to 16 environment steps.



> 💡 **视觉编码流程**: 双视角图像经VAE压缩为8x16x48潜空间张量，按1x2x2分块生成每帧32个token。时间上每4步环境数据映射为1个潜帧，单次生成覆盖16步未来状态。

Actions and proprioception. The policy predicts a 16- step action chunk aligned with the same temporal horizon, with four action steps corresponding to each latent video frame. Each action has seven dimensions: three for endeffector position, three for axis-angle rotation, and one for the gripper command. Each chunk is conditioned on an eightdimensional proprioceptive state comprising the 3D endeffector position, a 3D axis-angle orientation converted from the simulator quaternion, and two gripper joint positions. The proprioceptive state is projected to the corresponding hidden dimension of each stream and added to its hidden representation rather than appended as a sequence token.



> 💡 **动作与本体感知**: 策略输出 16 步动作 chunk，与 4 个潜在视频帧对齐（每潜帧 4 步）；每个动作 7 维（3 末端位置 + 3 轴角 + 1 夹爪），并条件于 8 维本体状态（3 位置 + 3 轴角 + 2 夹爪关节）。本体状态经投影后加到各流隐藏表示上而非作为序列 token 拼接，原文未说明这一设计的原因。

