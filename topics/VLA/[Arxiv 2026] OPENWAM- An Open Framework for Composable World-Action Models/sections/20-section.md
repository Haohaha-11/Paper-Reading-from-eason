[← 返回 README](../README.md)

## C.2. State and Interaction Coverage

Within the dataset, 75% of segments begin from demonstration-prefix states. The remaining 25% begin after an additional executed perturbation, extending the startingstate distribution beyond the demonstrated trajectories.



> 💡 **起始状态分布**: 75% 的片段从演示前缀状态开始，其余 25% 在执行一次额外扰动之后开始，把起始分布扩展到演示轨迹之外。推断：这样做是为了让策略接触到演示数据未覆盖的状态，但原文没有说明这一设计动机。

Table 10. Intervention composition of LIBERO-Long-CF. Categories denote generation recipes and may induce overlapping physical effects.
<table><tr><td>Intervention family</td><td>Fraction (%)</td></tr><tr><td>Stop / rescale arm motion</td><td>9.4</td></tr><tr><td>Reverse / redirect translation</td><td>6.3</td></tr><tr><td>Axis biases and pulses</td><td>12.5</td></tr><tr><td>Dedicated yaw perturbation</td><td>3.1</td></tr><tr><td>Noise / randomized arm controls</td><td>12.5</td></tr><tr><td>Dedicated gripper interventions</td><td>31.3</td></tr><tr><td>Random-duration arm / gripper interventions</td><td>25.0</td></tr></table>



> 💡 **Table 10 解读**: 各干预族占比（合计约 100%）：专用夹爪干预 31.3% 最高，随机时长手臂/夹爪干预 25.0% 次之，轴偏置与脉冲、噪声/随机化手臂控制各 12.5%，停止/缩放手臂运动 9.4%，反向/改向平移 6.3%，专用 yaw 扰动 3.1%。caption 说明这些类别是生成配方，可能诱导重叠的物理效应，不应当作互斥事件来读。

Table 11. Physical interaction statistics for LIBERO-Long-CF. Categories overlap.
<table><tr><td>Measured interaction</td><td>Rate (%)</td></tr><tr><td>Gripper-object / fixture contact</td><td>90.4</td></tr><tr><td>Detected grasp</td><td>50.0</td></tr><tr><td>Object-configuration effect</td><td>72.3</td></tr></table>



> 💡 **Table 11 解读**: 三类物理交互的实测比例：夹爪与物体或夹具接触 90.4%，检测到抓取 50.0%，物体配置相对同起点参考续接发生变化 72.3%。caption 指出类别之间存在重叠，所以这三个比例不能相加，每一项各自衡量一个不同层面的交互。

Among characterized perturbed starts, the end-effector state is on average 3.34 cm from the nearest point on the corresponding demonstrated path (median 1.90 cm). Under thresholds of 1 cm translation, 5<sup>◦</sup> rotation, or 5% articulatedjoint travel, 61.6% also contain object configurations outside the corresponding demonstrated configurations.



> 💡 **偏离程度分析**: 受扰起始状态的末端执行器与最近演示路径点平均相距 3.34 cm、中位数 1.90 cm；在 1 cm 平移、5° 旋转或 5% 关节行程的判定阈值下，61.6% 的样本同时含有超出对应演示配置的物体配置。推断：中位位姿偏离接近 2 cm、幅度有限，真正扩大状态覆盖的更多是那 61.6% 的物体配置变化。

The corpus contains substantial physical interaction. As shown in Table 11, 90.4% of characterized segments contain gripper contact with an object or fixture, 50.0% contain a detected grasp, and 72.3% alter object configuration relative to the same-start reference continuation.



> 💡 **交互实质**: 这里用文字重申 Table 11 的数字，但值得注意参照基准的差别：72.3% 是与“同起点参考续接”相比的物体配置变化率，即同一初始状态下扰动执行与不扰动执行的差异，比单纯的接触率更能说明扰动确实改变了任务进程。原文未给出未扰动条件下该变化率的对照值。

