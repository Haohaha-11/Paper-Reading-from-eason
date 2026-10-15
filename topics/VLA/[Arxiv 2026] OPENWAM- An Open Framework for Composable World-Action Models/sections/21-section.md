[← 返回 README](../README.md)

## C.3. Representation for Local Dynamics Learning

Each segment contains two synchronized 128 × 128 RGB streams, 128 seven-dimensional delta-OSC controls, and 129 proprioceptive states. Actions contain three translation channels, three rotation channels, and one gripper command.



> 💡 **数据维度定义**: 明确局部动力学学习的数据结构：每段包含双路128x128 RGB视频、128个7维delta-OSC控制信号及129个本体感受状态。动作空间具体化为3平移+3旋转+1夹爪共7维，为后续模型输入提供精确的张量形状基准。

Both camera streams are encoded with the OPENWAM video VAE. The encoded sequence contains one boundary observation and 32 future latent frames, with four controls aligned to each future latent frame.



> 💡 **编码时序对齐**: 使用OPENWAM VAE将双路视频压缩为1个边界观测和32个未来潜帧。关键设计是时间对齐机制：每个未来潜帧对应4个控制动作（$32 \times 4 = 128$），确保视觉表征与控制序列在时间步上的严格同步。

Local-context IDM conditions on the boundary observation, proprioception, and future visual trajectory to predict the action sequence. Local-context FDM conditions on the boundary observation, proprioception, and action sequence to predict the future visual trajectory. Both objectives exclude task language and pre-start history.



> 💡 **双向预测机制**: 阐述IDM与FDM的对偶关系：IDM由视觉轨迹反推动作，FDM由动作预测视觉轨迹。两者均仅依赖当前边界观测和本体感受，刻意排除语言指令与历史上下文，旨在隔离并强化纯粹的局部动态建模能力。

Table 12. Effect of video-backbone initialization on closed-loop LIBERO-Long success. Downstream architecture and training are held fixed within each interaction program.
<table><tr><td>Video initialization</td><td>VTA (%)</td><td>Joint (%)</td></tr><tr><td>Random initialization</td><td>20.0</td><td>27.2</td></tr><tr><td>Original Wan2.2</td><td>68.4</td><td>62.2</td></tr><tr><td>Robot-video pretrained</td><td>97.8</td><td>96.6</td></tr></table>



> 💡 **初始化消融实验**: Table 12 在固定下游架构与训练的条件下比较三种视频骨干初始化：随机初始化仅 20.0%/27.2%（VTA/Joint），通用视频模型 Original Wan2.2 为 68.4%/62.2%，机器人视频预训练达 97.8%/96.6%。推断：通用视频预训练已带来大幅提升，而机器人视频初始化的增益更大，说明该任务对视觉表征与操作域的匹配度相当敏感。

Table 13. Effect of the Mixture-of-Transformers architecture on closed-loop LIBERO-Long success. All variants use robot-videopretrained initialization.
<table><tr><td>Architecture</td><td>VTA (%)</td><td>Joint (%)</td></tr><tr><td>Non-MoT</td><td>92.8</td><td>93.6</td></tr><tr><td>MoT</td><td>97.8</td><td>96.6</td></tr></table>



> 💡 **架构增益分析**: Table 13 的三个变体都用机器人视频预训练初始化，MoT 相对 Non-MoT 把 VTA 从 92.8% 提到 97.8%、Joint 从 93.6% 提到 96.6%。推断：MoT 的增益约 3–5 个百分点，即初始化仍是主要贡献来源，架构设计是次级但稳定的补充；这仅是单一 LIBERO-Long 闭环基准上的结果，不宜外推为普遍鲁棒性优势。

