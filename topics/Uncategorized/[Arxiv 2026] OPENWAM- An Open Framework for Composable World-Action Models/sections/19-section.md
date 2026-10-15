[← 返回 README](../README.md)

## C.1. Scale and Intervention Coverage

LIBERO-Long-CF contains 4.10 million control records, compared with 138,090 controls in the 500 source demonstrations, increasing local transition supervision by approximately 29.7×. At 20 Hz, these correspond to 56.9 and 1.92 control-equivalent hours, respectively.



> 💡 **数据规模**: LIBERO-Long-CF 含 4.10M 控制记录，较源演示的 138k 增加约 29.7 倍监督信号。按 20Hz 计算，相当于 56.9 小时控制数据（源仅 1.92 小时），显著提升局部过渡覆盖度。

The branch generator modifies motion magnitude, direction, temporal structure, individual action axes, stochastic arm motion, and gripper behavior. Table 10 summarizes the intervention distribution.



> 💡 **干预维度**: 分支生成器通过修改运动幅度、方向、时序结构、单轴动作、随机臂动及夹爪行为来构造多样性。Table 10 提供了这些干预类型的具体分布统计。

Interventions include stopped and rescaled motion, reversed or redirected translation, translational and rotational pulses, structured control noise, piecewise-random commands, and changes to gripper state and timing. All commands are within LIBERO’s normalized controller range.



> 💡 **干预细节**: 具体操作包括停止/缩放运动、反转/重定向平移、脉冲式平移与旋转、结构化噪声、分段随机指令及夹爪状态/时序变更。所有指令都限制在 LIBERO 标准化控制器的取值范围内，这是原文给出的约束；推断：该限制使生成的动作落在控制器可接受的区间内。

