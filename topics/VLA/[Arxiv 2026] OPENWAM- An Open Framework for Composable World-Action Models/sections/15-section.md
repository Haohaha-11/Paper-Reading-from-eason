[← 返回 README](../README.md)

## B.1. Pretraining Corpus

Our robot-video pretraining corpus combines large-scale real-robot manipulation, human interaction video, and synthetic robot trajectories. Table 8 summarizes the source data before training-window sampling. The UMI-family entry aggregates UMI, DexUMI [86], UMI on Legs [23], and MV-UMI.



> 💡 **语料构成与口径**: 语料由真实机器人操作、人类交互视频和合成机器人轨迹三类数据组成，UMI-family 这一条实际聚合了 UMI、DexUMI、UMI on Legs 和 MV-UMI 四个来源。Table 8 统计的是训练窗口采样（training-window sampling）之前的规模，表中数字不等于实际训练用到的数据量。

Table 8. Data used for causal robot-video pretraining.
<table><tr><td>Dataset</td><td>Trajectories / takes</td><td>Hours</td><td>Source type</td></tr><tr><td>Open X-Embodiment (49 manipulation datasets)</td><td>1,300,749</td><td>1,911.29</td><td>Multi-embodiment robot data</td></tr><tr><td>AgiBot World Beta</td><td>1,003,672</td><td>2,976.4</td><td>Real robot manipulation</td></tr><tr><td>Ego-Exo4D v2</td><td>5,035</td><td>221.26</td><td>Human interaction</td></tr><tr><td>InternData-A1</td><td>637,498</td><td>7,433.91</td><td>Synthetic robot manipulation</td></tr><tr><td>RoboCOIN</td><td>183,157</td><td>1,306.83</td><td>Real bimanual manipulation</td></tr><tr><td>RoboMIND</td><td>107,877</td><td>305.5</td><td>Multi-embodiment robot data</td></tr><tr><td>FastUMI-100K</td><td>92,823</td><td>461.77</td><td>Human-guided manipulation</td></tr><tr><td>UMI family</td><td>5,430</td><td>24.60</td><td>Human-guided manipulation</td></tr></table>



> 💡 **Table 8 解读**: 轨迹数最多的是 Open X-Embodiment（1,300,749 条，覆盖 49 个操作数据集），时长最长的是 InternData-A1（7,433.91 小时，属合成数据），真实数据中时长最大的是 AgiBot World Beta（2,976.4 小时）。八项相加为 3,336,241 条、14,641.56 小时，与正文 3.34M / 14.64k 的说法吻合；注意 AgiBot World Beta 的 Source type 是真实机器人操作，RoboCOIN 才是真实双臂操作。

The corpus contains approximately 3.34 million trajectory or take units and 14.64k nominal hours, spanning heterogeneous robot embodiments, collection interfaces, viewpoints, human-object interactions, and synthetic manipulation at scale, providing the visual and interaction prior used by all downstream OPENWAM models.



> 💡 **规模与用途**: 约 3.34M 条轨迹/拍摄单元、14.64k 名义小时，覆盖多形态机器人、采集接口、视角与人-物交互。作者称这份视觉与交互先验被所有下游 OPENWAM 模型使用；原文并未把规模或异构性直接归因于下游泛化能力的提升，这一步需要下游实验支撑（推断）。

