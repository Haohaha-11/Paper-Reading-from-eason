[← 返回 README](../README.md)

## B.2. Causal Adaptation

Pretraining starts from the Wan2.2-5B checkpoint. We retain the pretrained video representation and optimize the model on the corpus above using the chunk-causal prediction objective described in Sec. 3. Future chunks may attend to the current observation, preceding observations, and previously generated chunks, but not to later chunks. Tokens within each chunk are denoised jointly.



> 💡 **机制拆解**: 基于Wan2.2-5B初始化，采用chunk-causal目标。未来块可看当前/过去观测及生成块，但不可看后续块；块内token联合去噪。

This stage uses video alone. Robot actions, proprioception, and task-specific policy targets are not supplied to the model. The resulting checkpoint serves both as a standalone causal video predictor and as the common visual initialization for the downstream OPENWAM interaction programs.



> 💡 **输入限制**: 此阶段仅用视频，机器人动作、本体感知和任务特定策略目标都不输入模型。产出的 checkpoint 既是独立的因果视频预测器，也是下游 OPENWAM 交互程序的统一视觉初始化。

