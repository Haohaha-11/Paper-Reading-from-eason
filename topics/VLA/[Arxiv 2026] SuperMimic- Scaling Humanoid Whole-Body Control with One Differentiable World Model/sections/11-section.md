[← 返回 README](../README.md)

## B Joint training across goal interfaces

In §3, the goal $\mathbf { \sigma } _ { \mathbf { \sigma } _ { \mathbf { \sigma } _ { \mathbf { \lambda } } } } \mathbf { \sigma } _ { \mathbf { \sigma } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } } \mathbf { \sigma } _ { \mathbf { \lambda } _ { \mathbf { \lambda } } }$ is read from a full reference motion. Downstream goal interfaces specify the motion only in part or in another form. The same dynamics model can train several goal interfaces because the body physics is shared. We instantiate one POMDP $\mathcal { M } _ { i }$ per goal interface (for example, a full-reference motion target, a velocity command, end-effector poses, or an SMPL body-pose target), each with its own goal space $\mathcal { G } _ { i } .$ reward $r _ { i } ,$ and termination $d _ { i } ,$ and train each with its own critic $V _ { \psi _ { i } }$ . A single policy network uses per-interface goal encoders $\mathrm { e n c } _ { \theta , i }$ and a shared decoder dec<sub>θ</sub>,



> 💡 **共享与独立的边界**: 每个目标接口 $i$ 实例化一个 POMDP，各自带目标空间 $\mathcal{G}_i$、奖励 $r_i$、终止条件 $d_i$ 和 critic $V_{\psi_i}$；策略网络则用接口专属编码器 $\mathrm{enc}_{\theta,i}$ 加共享解码器 $\mathrm{dec}_\theta$。作者给出的复用依据是各接口面对同一套身体物理，因此同一个动力学模型可服务于多种接口。

<table><tr><td>Reward Term</td><td>Equation</td><td>Weight</td></tr><tr><td>Task (tracking)</td><td></td><td></td></tr><tr><td>Body Position</td><td> $\exp \Big ( - \big ( \frac { 1 } { | \mathcal { T } | } \sum _ { b c \mathcal { T } } \| \mathbf { p } _ { b } ^ { \mathrm { d e s } } - \mathbf { p } _ { b } \| ^ { 2 } \big ) / 0 . 3 ^ { 2 } \Big )$ </td><td>1.0</td></tr><tr><td>Body Orientation</td><td> $\mathrm { e x p } \Big ( - \big ( { \textstyle \frac { 1 } { | { \cal T } | } } \sum _ { h \in { \cal T } } ^ { \sim } \| \log ( R _ { b } ^ { \mathrm { d e s } } R _ { b } ^ { \top } ) \| ^ { 2 } \big ) / 0 . 4 ^ { 2 } \Big )$ </td><td>1.0</td></tr><tr><td>Body Linear velocity</td><td> $\mathrm { e x p } \Big ( - \big ( { \frac { 1 } { | \mathcal { T } | } } \sum _ { b \in \mathcal { T } } ^ { \sim } \| \mathbf { v } _ { b } ^ { \mathrm { d e s } } - \mathbf { v } _ { b } \| ^ { 2 } \big ) / 1 . 0 ^ { 2 } \Big )$ </td><td>1.0</td></tr><tr><td>Body Angular velocity</td><td> $\mathrm { e x p } \Big ( - \big ( { \textstyle \frac { 1 } { | T | } } \sum _ { b \in T } ^ { - } \| \omega _ { b } ^ { \mathrm { d e s } } - \omega _ { b } \| ^ { 2 } \big ) / 3 . 1 4 ^ { 2 } \Big )$ </td><td>1.0</td></tr><tr><td>Root Position</td><td> $\exp \Big ( - \mathrm { \| \mathbf { p } _ { r o o t } ^ { d e s } - \mathbf { p } _ { r o o t } \| ^ { 2 } / 0 . 3 ^ { 2 } } \Big )$ </td><td>0.5</td></tr><tr><td>Root Orientation</td><td> $\exp \Big ( - \mathrm { \lVert ~ l o g } ( R _ { \mathrm { r o o t } } ^ { \mathrm { d e s } } R _ { \mathrm { r o o t } } ^ { \top } ) \rVert ^ { 2 } / 0 . 4 ^ { 2 } \Big )$ </td><td>0.5</td></tr><tr><td colspan="3">Regularization</td></tr><tr><td>Action smoothness</td><td> $\| \mathbf { a } _ { t } - \mathbf { a } _ { t - 1 } \| ^ { 2 }$ </td><td>-0.1</td></tr><tr><td>Joint limit</td><td> $\begin{array} { r } { \sum _ { j } \big [ \operatorname* { m a x } ( 0 , q _ { j } - q _ { j } ^ { \operatorname* { m a x } } ) + \operatorname* { m a x } ( 0 , q _ { j } ^ { \operatorname* { m i n } } - q _ { j } ) \big ] } \end{array}$ </td><td>-2</td></tr></table>



> 💡 **奖励函数构成**: 表格定义了运动追踪任务的密集奖励项，包括身体位置、朝向及速度的高斯核相似度计算。值得注意的是正则化项中关节限位惩罚权重设为 -2，且未包含自碰撞惩罚，这直接影响策略对极端姿态的容忍度。

Table A.2: Motion-tracking interface reward, following BeyondMimic [3] with the self-collision penalty removed and the joint-limit weight lowered from −10 to −2 (§3.3). $\tau$ is the set of tracked bodies.



> 💡 **基线差异说明**: 明确指出该奖励设计基于 BeyondMimic，但移除了自碰撞惩罚并降低了关节限位权重（−10 → −2）。原文只说明改了哪几项，没有解释动机；推断：降低限位惩罚是给策略更多姿态余量，代价是关节可能更贴近或越过限位。

<table><tr><td>Domain Randomization</td><td>Sampling Distribution</td></tr><tr><td>Friction coefficients Joint calibration offsets [rad] Torso&#x27;s center-of-mass offset [m]</td><td> $\mu \sim \mathcal { U } [ 0 . 3 , 1 . 2 ]$   $\Delta q _ { i } ^ { 0 } \sim \mathcal { \dot { U } } [ - 0 . 0 \dot { 1 } , 0 . 0 1 ]$ </td></tr></table>



> 💡 **域随机化参数**: 表中给出摩擦系数 $\mu\sim\mathcal{U}[0.3,1.2]$ 与关节校准偏移 $\Delta q_i^0\sim\mathcal{U}[-0.01,0.01]$ rad；躯干质心偏移这一行在表格里没有对应分布，复现时需回查原文。推断：这些扰动让策略在训练中见到不同接触条件与运动学偏差。

Table A.3: Domain-randomization parameters that make up the sim context $\xi ,$ following BeyondMimic [3] with the root-velocity perturbations removed. $( \mathcal { U } [ \cdot ] ;$ uniform distribution.)



> 💡 **Table A.3 说明**: caption 说明这些参数构成仿真上下文 $\xi$，取自 BeyondMimic 但移除了根速度扰动，具体取值见表内几行。推断：去掉根速度随机化与需要干净的根速度信号的速度指令接口有关，原文未展开原因。

$$
z = \mathrm { e n c } _ { \theta , i } ( \pmb { g } ) \in \mathcal { Z } , \qquad \pmb { a } = \mathrm { d e c } _ { \theta } ( z , \pmb { o } ) , \qquad \pmb { g } \in \mathcal { G } _ { i } ,\tag{A.1}
$$



> 💡 **公式 A.1 批读**: 定义策略结构——接口目标 $g$ 经该接口的编码器 $\mathrm{enc}_{\theta,i}$ 映射为潜在变量 $z$，再与观测 $o$ 一起输入共享解码器 $\mathrm{dec}_\theta$ 得到动作 $a$。各接口只换编码器，解码器与潜在空间 $\mathcal{Z}$ 共用。

and training optimizes the lifted objective max<sub>θ</sub> $\mathbb { E } _ { i \sim p ( \mathcal { T } ) } [ \hat { J } _ { i } ( \theta ) ]$ , where $p ( \mathcal { T } )$ is a distribution over the set I of goal interfaces. Each interface i thus has its own policy $\begin{array} { r } { \pmb { a } = \pi _ { i } ( \pmb { o } , \pmb { g } ) : = \mathrm { d e c } _ { \pmb { \theta } } ( \mathrm { e n c } _ { \pmb { \theta } , i } ( \pmb { g } ) , \pmb { o } ) ; } \end{array}$ the velocity encoder also receives a feature of the observation history (Table A.4). Each ${ \hat { J } } _ { i }$ is the objective of Eq. 2 with $\pi _ { i }$ in place of $\pi _ { \theta }$ , the reward and termination of $\mathcal { M } _ { i }$ , and the critic $V _ { \psi _ { i } }$ Every interface trains through the same dynamics model $f _ { \omega } ,$ , which is not retrained per interface.



> 💡 **联合训练目标**: 训练最大化 $\mathbb{E}_{i\sim p(\mathcal{T})}[\hat{J}_i(\theta)]$，各接口用自己的 $\mathcal{M}_i$ 奖励、终止和 critic $V_{\psi_i}$，但都通过同一个动力学模型 $f_\omega$ 反传，$f_\omega$ 不按接口重训。结构上唯一的例外是速度接口的编码器额外接收观测历史特征。

A downstream interface is trained by distillation or by RL, depending on its reward. Distillation uses the motion-tracking reward (§3.3), with the command read from the reference motion. The reward gradient then trains that interface to reproduce the reference motion from this command. RL optimizes the interface’s own task reward, such as velocity tracking, directly through the world model. On a downstream interface’s rollouts, the return gradient passes through the shared decoder to that interface’s encoder, with a stop-gradient on the decoder’s weights; the decoder is still trained by the motion-tracking objective. The RL-trained velocity encoder of §4.5 also has a latent-alignment los toward the motion encoder’s latent on rollouts with reference commands (Appendix G).



> 💡 **两类下游训练**: 蒸馏用 §3.3 的运动追踪奖励、命令取自参考运动；RL 直接优化接口自身的任务奖励。在所有下游接口的 rollout 上，回报梯度都经共享解码器回传到该接口编码器，同时解码器权重加 stop-gradient、仍只由运动追踪目标更新；§4.5 的速度编码器另有向运动编码器 latent 对齐的损失（附录 G）。

