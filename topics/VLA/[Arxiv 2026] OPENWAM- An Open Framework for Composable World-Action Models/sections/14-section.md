[← 返回 README](../README.md)

## B. Robot-Video Pretraining

We adapt the Wan2.2-5B video backbone to robot-centric motion and physical interaction before introducing action prediction. The pretraining corpus combines robot demonstrations, human manipulation video, and synthetic robot trajectories across a broad range of embodiments, viewpoints, objects, and interaction patterns. No action labels are used during this stage.



> 💡 **预训练策略**: 在引入动作预测之前，先把 Wan2.2-5B 视频骨干适配到以机器人为中心的运动与物理交互上；语料混合了机器人演示、人类操作视频与合成机器人轨迹三类数据，覆盖多种本体形态、视角、物体和交互模式。本阶段不使用任何动作标签，动作监督被推迟到后续阶段。

