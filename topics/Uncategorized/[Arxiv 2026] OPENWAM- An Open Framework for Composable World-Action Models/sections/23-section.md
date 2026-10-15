[← 返回 README](../README.md)

## D.1. Effect of Video-Backbone Initialization

We further examine whether the benefit of robot-video pretraining is specific to VTA or persists across interaction programs. Under otherwise matched downstream training, we initialize the video backbone either randomly, from the original Wan2.2 checkpoint, or from our robot-video-pretrained causal checkpoint. Table 12 shows a consistent ordering for both VTA and Joint. Relative to Wan2.2 initialization, robot-video pretraining improves success by 29.4 percentage points for VTA and 34.4 points for Joint, reaching 97.8% and 96.6%, respectively. Random initialization performs substantially worse for both programs. These results indicate that the benefit of the robot-video-pretrained backbone transfers across distinct video–action interaction structures.



> 💡 **消融实验**: 在其余下游训练条件相同的前提下，比较随机、Wan2.2 与机器人视频预训练三种视频骨干初始化。相对 Wan2.2 初始化，机器人视频预训练在 VTA 和 Joint 上分别提升成功率 29.4 与 34.4 个百分点，达到 97.8% 和 96.6%；随机初始化明显更差，作者据此认为该预训练收益可跨不同的视频–动作交互结构迁移。

