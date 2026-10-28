[← 返回 README](../README.md)

## E Motion data filtering

We train on the publicly available G1-retargeted BONES-SEED motion set [27]. The raw release contains 142,220 clips, but many interact with terrain or objects the robot cannot use (stairs, boxes, ladders, chairs) or carry retargeting artifacts that make them infeasible for a free-standing humanoid on flat ground. We remove these in two passes and keep 129,514 clips (Table A.7).



> 💡 **数据筛选动机**: 原始 G1-retargeted BONES-SEED 数据集包含大量机器人无法执行的动作（如爬楼梯、搬箱子）或重定向伪影。作者通过两轮过滤剔除这些不可行数据，最终保留 129,514 个适合平地自由站立人形机器人的片段。

Name filter. Each clip is named by its action. We drop any clip whose name contains a term denoting contact with terrain or an object the robot cannot use—e.g. stair, ladder, box, chair,



> 💡 **名称过滤规则**: 采用关键词匹配策略，直接根据动作命名剔除涉及地形或物体交互的片段（如 stair, box, chair）。这是第一轮快速筛选，旨在移除显性不可行动作。

<table><tr><td>Hyperparameter</td><td>Symbol</td><td>Value</td></tr><tr><td>World model</td><td></td><td></td></tr><tr><td>Window length</td><td>k</td><td>8</td></tr><tr><td>Optimizer</td><td></td><td>Adam</td></tr><tr><td>Learning rate</td><td> $\alpha _ { \omega }$ </td><td> $1 _ { \mathrm { \scriptsize ~ G P U : ~ 2 \times 1 0 ^ { - 4 } ; 2 _ { \mathrm { \scriptsize ~ G P U s : ~ \sqrt { 2 } } \times 2 \times 1 0 ^ { - 4 } } } }$ </td></tr><tr><td>Batch size Gradient clipping</td><td></td><td>10 batches of 2048 windows per update global norm 1.0</td></tr><tr><td></td><td></td><td></td></tr><tr><td>Imagination and policy Imagination horizon</td><td>H</td><td>32</td></tr><tr><td>Optimizer</td><td></td><td>Adam,  $\beta = ( 0 . 9 , 0 . 9 5 )$ </td></tr><tr><td>Learning rate</td><td> $\alpha \theta$ </td><td>same as  $\alpha _ { \omega }$ </td></tr><tr><td>Batch size</td><td></td><td>4096 imagined rollouts per update</td></tr><tr><td>Gradient clipping</td><td></td><td>global norm 1.0, then element-wise 0.1</td></tr><tr><td>Discount</td><td>γ</td><td>0.99</td></tr><tr><td>Sensor noise</td><td></td><td>Table A.1</td></tr><tr><td></td><td> $\eta _ { t } \sim \rho _ { \eta }$ </td><td></td></tr><tr><td>Critic</td><td></td><td></td></tr><tr><td>GAE parameter</td><td>λ</td><td>0.95</td></tr><tr><td>Optimizer</td><td></td><td>Adam</td></tr><tr><td>Learning rate</td><td></td><td>same as  $\alpha _ { \omega }$ </td></tr><tr><td>Gradient clipping</td><td></td><td>global norm 1.0</td></tr><tr><td>Critic epochs per update</td><td></td><td>5, each over 4 shuffled minibatches</td></tr><tr><td>Data collection</td><td></td><td></td></tr><tr><td>Simulator environments per GPU</td><td></td><td>4096</td></tr><tr><td>Action noise</td><td> $\epsilon _ { t } \sim \rho _ { \epsilon }$ </td><td> $\mathcal { N } ( 0 , 0 . 1 ^ { 2 } )$  per action dimension</td></tr><tr><td>Replay-buffer size</td><td></td><td>1000 steps per environment</td></tr><tr><td>Schedule</td><td></td><td></td></tr><tr><td>Update-to-data ratio</td><td></td><td>0.4</td></tr><tr><td>Policy updates per world-model update</td><td></td><td>1</td></tr><tr><td>Learning-rate schedule</td><td></td><td>cosine decay to 0.03× over 250,000</td></tr><tr><td></td><td></td><td>iterations, then constant</td></tr><tr><td>Training length</td><td></td><td>320,000 iterations</td></tr><tr><td>Multi-GPU</td><td></td><td>data parallel; gradients averaged across GPUs</td></tr></table>



> 💡 **超参数配置表**: 列出 SuperMimic 模型训练的关键超参数，包括世界模型窗口长度 $k=8$、想象视界 $H=32$、折扣因子 $γ=0.99$ 及学习率调度策略。注意不同 GPU 数量下的学习率缩放规则。

Table A.5: SuperMimic hyperparameters.



> 💡 **表格标题说明**: 该标题表明上方表格为 SuperMimic 方法的超参数设置，供复现时参考具体数值。

<table><tr><td>Reward Term</td><td>Equation</td><td>Weight</td></tr><tr><td>Task (velocity tracking)</td><td></td><td></td></tr><tr><td>Linear velocity</td><td> $\exp \Big ( - \| \mathbf { v } _ { x y } - \mathbf { v } _ { x y } ^ { \mathrm { c m d } } \| ^ { 2 } / 0 . 5 ^ { 2 } \Big )$ </td><td>2.0</td></tr><tr><td>Yaw rate</td><td> $\exp \Big ( - ( \omega _ { z } - \omega _ { z } ^ { \mathrm { c m d } } ) ^ { 2 } / 0 . 5 \Big )$ </td><td>2.0</td></tr><tr><td>Regularization</td><td></td><td></td></tr><tr><td>Action smoothness</td><td> $\| \mathbf { a } _ { t } - \mathbf { a } _ { t - 1 } \| ^ { 2 }$ </td><td>-0.3</td></tr><tr><td>Joint limit</td><td> $\begin{array} { r } { \sum _ { j } \left[ \operatorname* { m a x } ( 0 , q _ { j } - q _ { j } ^ { \operatorname* { m a x } } ) + \operatorname* { m a x } ( 0 , q _ { j } ^ { \operatorname* { m i n } } - q _ { j } ) \right] } \end{array}$ </td><td>-2</td></tr><tr><td>Torso sideways lean</td><td> $\sin ^ { 2 } \phi _ { \mathrm { t o r s o } }$ </td><td>-4</td></tr><tr><td>Torso roll and pitch rate</td><td> $\omega _ { \mathrm { r o l l } } ^ { 2 } + \omega _ { \mathrm { p i t c h } } ^ { 2 }$ </td><td>-0.05</td></tr><tr><td>Termination (fall)</td><td></td><td></td></tr></table>



> 💡 **奖励函数构成**: 定义 RL 编码器的速度跟踪奖励项，包括线速度和偏航角速度的高斯核奖励（权重 2.0），以及针对动作平滑、关节限位和躯干倾斜的惩罚项（负权重）。

Table A.6: Velocity-tracking reward and termination used to train the RL encoder. $\mathbf { v } _ { x y }$ is the planar root velocity in the heading frame, $\omega _ { z }$ the world yaw rate, and $( \mathbf { v } _ { x y } ^ { \mathrm { c m d } } , \omega _ { z } ^ { \mathrm { c m d } } )$ the command. $\phi _ { \mathrm { t o r s o } }$ is the sideways lean of the torso z-axis (forward lean is not penalized) and $\omega _ { \mathrm { r o l l } } , \omega _ { \mathrm { p i t c h } }$ the torso roll and pitch rates, all in the heading frame. $h _ { \mathrm { r o o t } }$ is the root height and $u _ { z }$ the z-component of the unit gravity direction in the root frame, so $1 + u _ { z }$ is 0 upright and 2 upside down, and the tilt condition triggers at about $7 3 ^ { \circ }$ . Every term, here and in Table A.2, is multiplied by the control period $\Delta t = 0 . 0 2 \mathrm { s }$



> 💡 **奖励符号与终止条件**: 解释公式中变量含义：$φ_{\text{torso}}$ 为侧倾角，$h_{\text{root}}$ 为根高度。特别指出 $1+u_z$ 用于检测跌倒，约 $73^°$ 倾斜触发终止，且所有奖励项需乘以控制周期 $Δt=0.02s$。

<table><tr><td>Stage</td><td>Clips</td></tr><tr><td>Raw BONES-SEED (G1 retarget)</td><td>142,220</td></tr><tr><td>After name filter After feasibility filter</td><td>129,667 129,514</td></tr></table>



> 💡 **过滤结果统计**: 展示各阶段剩余片段数：原始 142,220 → 名称过滤后 129,667 → 可行性过滤后 129,514。可见名称过滤移除了大部分不可行数据，可行性过滤进一步精修。

Table A.7: Number of BONES-SEED clips before filtering and after each filter.



> 💡 **统计表标题**: 明确上方数字对应 BONES-SEED 数据集在每步过滤后的片段保留量。

sit, climb, jump off 50cm, look over fence. This pass (adapted from the keyword list in SONIC’s released data-processing code [1], plus a few terms it misses) removes 12,553 clips.



> 💡 **名称过滤细节与效果**: 补充了更多被剔除的关键词（如 sit, climb），并说明该步骤基于 SONIC 代码库改进，共移除 12,553 个片段，占原始数据的约 8.8%。

Feasibility filter. The name filter misses clips whose names do not reveal the problem (e.g. a routine that walks onto a box). We run each motion through MuJoCo forward kinematics and flag it from the resulting body-link and foot-geometry trajectories, writing $z _ { \mathrm { m i n } } ( t ) = \mathrm { m i n } _ { b } z _ { b } ( t )$ for the height of the lowest body link at time t with the floor at z = 0:



> 💡 **可行性过滤机制**: 针对名称无法识别的复杂场景（如走上箱子），使用 MuJoCo 正向运动学模拟。核心指标为最低身体链接高度 $z_{\text{min}}(t)$，以此判断动作是否在物理上可执行。

• Elevated support. $z _ { \mathrm { m i n } }$ holds above 0.20 m for at least 0.6 s (standing or climbing on a box).



> 💡 **高架支撑判定**: 定义一种不可行动作：若 $z_{\text{min}}$ 持续高于 0.20m 超过 0.6s，视为站在或爬上物体，予以剔除。

• Ground penetration. $z _ { \mathrm { m i n } }$ falls below −0.12 m, or below −0.08 m for over 80% of the clip (legs driven through the floor by a faulty retarget).



> 💡 **地面穿透判定**: 定义另一种不可行动作：若 $z_{\text{min}}$ 低于 -0.12m，或低于 -0.08m 的时间占比超过 80%，视为腿部穿透地板的重定向错误，予以剔除。

• Floating feet. For at least 0.5 s both feet stay more than 0.02 m above the floor—measured as MuJoCo’s exact geom-to-plane distance (mj geomDistance) from the foot collision geometry, not a link-origin proxy—while the pelvis is upright and at standing height and the body is nearly stationary. A free-standing humanoid cannot hold both feet off the floor while standing still, so this is infeasible on flat ground whatever the cause (a low platform, a retarget float, or a slow rise; a ballistic jump is excluded by the stationary gate). The clean ground stance sits at $- 0 . 0 0 4 \pm 0 . 0 0 4 \mathrm { m }$ , so the 0.02 m gate is several σ clear of the floor.



> 💡 **悬浮脚检测逻辑**: 判据是脚部碰撞几何体到地面的 MuJoCo 精确几何距离（mj geomDistance），而不是连杆原点（link-origin）代理量；当骨盆直立、处于站立高度且身体近乎静止时，该距离连续至少 0.5 s 超过 0.02 m 就判为不可行。依据是自由站立的人形机器人无法双脚离地、在平地上静止不动，成因（低平台、retarget 浮空、缓慢起身）不影响判定，弹道式跳跃由“近乎静止”条件排除。干净站立时该距离为 $-0.004\pm0.004$ m，0.02 m 阈值高出地面数个 $\sigma$。

This pass removes a further 153 clips. All thresholds are tuned for the G1 retarget at 50 Hz.



> 💡 **过滤效果与参数**: 这条规则又额外剔除了 153 个片段。所有阈值都按 50 Hz 的 G1 retarget 数据调优；推断：换机器人或换采样率时，这些数值不能直接照搬。

