[← 返回 README](../README.md)

## A.3. Video–Action Interaction Programs

The four programs differ in how predicted video and action trajectories interact within a generation chunk. Each program also receives the observed history and task instruction.



> 💡 **核心定义**: 本节定义了四种视频-动作交互范式，区别在于生成块内预测轨迹的耦合方式。所有程序均共享观测历史与任务指令作为输入基础。

Joint. Video and action trajectories are denoised concurrently. During generation, each stream can attend to the other stream’s evolving prediction, allowing bidirectional interaction before either trajectory is complete.



> 💡 **Joint机制**: 视频与动作轨迹并行去噪，生成过程中两流互相关注对方演化中的预测值，实现双向实时交互。

Video-to-action (VTA). The model first generates the future video trajectory without conditioning on future actions. It then generates the action trajectory conditioned on the generated video trajectory.



> 💡 **VTA流程**: 采用串行生成策略：先独立生成未来视频轨迹（不依赖未来动作），再基于该视频生成对应的动作轨迹。

Action-to-video (ATV). The model first generates the future action trajectory without conditioning on future video. It then generates the video trajectory conditioned on the generated action trajectory.



> 💡 **ATV流程**: 与VTA对称，先独立生成未来动作轨迹，随后以该动作为条件生成视频轨迹，体现动作主导的因果顺序。

Decoupled. Video and action trajectories are generated concurrently, without cross-modal interaction between their future predictions. Both streams can still use the observed history and task instruction.



> 💡 **Decoupled特性**: 视频与动作轨迹并行生成，但未来预测之间没有跨模态交互；两流仍各自使用观测历史与任务指令。它与 Joint 的唯一区别就在于缺少这种互相注意力。

During training, the second stage of VTA and ATV conditions on the ground-truth trajectory of the first modality. At inference time, this ground-truth trajectory is replaced by the trajectory generated in the first stage. Joint and Decoupled jointly denoise both modalities in a single generation stage, whereas VTA and ATV use two sequential generation stages.



> 💡 **训练与推理差异**: VTA 和 ATV 的第二阶段在训练时以第一模态的真值轨迹为条件，推理时改用它第一阶段生成的轨迹（推断：这带来训练与推理条件分布不一致的风险）。Joint 和 Decoupled 只在单一阶段联合去噪。

