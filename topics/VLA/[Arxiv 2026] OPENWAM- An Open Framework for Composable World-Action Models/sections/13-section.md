[← 返回 README](../README.md)

## A.5. Real Robot Setup

The real-robot setup, as shown in Figure 5, consists of two Franka FR3 arms with Flexiv Grav grippers. We collect demonstrations through bimanual GELLO teleoperation. Two wrist-mounted RealSense D405 cameras record at 1280 × 720 pixels, and a central Orbbec Femto Mega records at 1920 × 1080 pixels, all at 30 fps. Joint positions, end-effector poses, gripper widths, and control commands are recorded with timestamps at 30 Hz. In the chunk-relative formulation, each arm’s target poses are expressed relative to its observed pose at the start of the chunk, while gripper widths remain absolute. Each action contains 20 values: three position coordinates, six values representing orientation, and one gripper width for each arm.



> 💡 **硬件与动作空间**: 平台为两条 Franka FR3 机械臂，末端为 Flexiv Grav 夹爪，演示通过双手 GELLO 遥操作采集；两路腕部 RealSense D405 为 1280×720、中央 Orbbec Femto Mega 为 1920×1080，均 30 fps，关节位置、末端位姿、夹爪宽度与控制指令以 30 Hz 带时间戳记录。动作共 20 个数值，即每条手臂 3 个位置坐标 + 6 个方向值 + 1 个夹爪宽度；位姿按 chunk-relative 表示为相对于该 chunk 起始观测位姿，夹爪宽度仍为绝对值。推断：这种相对表示可能减弱策略对全局坐标系的依赖，原文只给出定义、未说明动机。

For the real-robot experiments, we collect 200 demonstrations each for Toast Bread and Rubik’s Cube, and 180 demonstrations for Sort Cups. Each demonstration is 30 s long. We evaluate the learned policies over 50 closed-loop episodes for Toast Bread and Rubik’s Cube, and 36 episodes for Sort Cups.



> 💡 **实验规模设置**: 演示数据为 Toast Bread 与 Rubik's Cube 各 200 条、Sort Cups 180 条，每条时长 30 秒；闭环评估分别为 50、50、36 个 episode。原文未说明评估指标，推断：这样的 episode 数通常用于统计真实环境任务成功率。

