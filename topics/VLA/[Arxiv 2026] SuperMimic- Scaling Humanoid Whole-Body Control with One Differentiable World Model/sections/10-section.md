[← 返回 README](../README.md)

## A Instantiation: observation, action, reward, and sim context

This appendix instantiates the shared spaces of the POMDP of §3.1. The motion-tracking interface follows the BeyondMimic formulation [3]; the changes we make are noted inline.



> 💡 **附录定位**: 本附录把 §3.1 的 POMDP 共享空间落实为具体接口，运动跟踪部分沿用 BeyondMimic 的 formulation [3]，本文的改动在原文中逐处标注。读复现相关内容时，可把这一节当作超参数与信号定义的索引。

Observation. The proprioceptive observation $\mathbf { } _ { o _ { t } }$ comprises the joint positions relative to the default pose ${ \pmb q } - { \pmb q } ^ { 0 }$ , the joint velocities $\dot { \mathbf { q } } ,$ the base angular velocity from the inertial measurement unit (IMU), the projected gravity in the base frame, and the previous action $\mathbf { \delta } _ { a _ { t - 1 } }$ (Table A.1); the policy receives the last 10 observations.
<table><tr><td>Observation Term</td><td>Dim. Noise  $\pmb { \eta } _ { t }$ </td><td></td><td>Unit</td></tr><tr><td>Joint positions  ${ \pmb q } - { \pmb q } ^ { 0 }$ </td><td>29</td><td> $\mathcal { U } [ - 0 . 0 1 , 0 . 0 1 ]$ </td><td>rad</td></tr><tr><td>Joint velocities À</td><td>29</td><td> $\mathcal { U } [ - 0 . 5 , 0 . 5 ]$ </td><td> $\mathrm { r a d } / \mathrm { s }$ </td></tr><tr><td>Base angular velocity (IMU)</td><td>3</td><td> $\mathcal { U } [ - 0 . 2 , 0 . 2 ]$ </td><td> $\mathrm { r a d } / \mathrm { s }$ </td></tr><tr><td>Projected gravity</td><td>3</td><td> $\mathcal { U } [ - 0 . 0 5 , 0 . { \dot { 0 } } 5 ]$ </td><td></td></tr><tr><td>Previous action  $\mathbf { \delta } _ { a _ { t - 1 } }$ </td><td>29 一</td><td></td><td></td></tr></table>



> 💡 **观测构成**: 本体感受观测是四类传感器量与上一动作的拼接：关节位置相对默认位姿 $q-q^0$、关节速度 $\dot q$、IMU 基座角速度、基座系重力投影，再加上 $\delta_{a_{t-1}}$，维度依次为 29、29、3、3、29，共 93 维；策略输入最近 10 个观测。Table A.1 的噪声只覆盖前四项（如关节位置 $\mathcal{U}[-0.01,0.01]$ rad、速度 $\mathcal{U}[-0.5,0.5]$ rad/s），上一动作一栏未给噪声。

Table A.1: Proprioceptive observation $\mathbf { } o _ { t } .$ . The noise column gives the per-dimension sensor noise $\eta _ { t } \sim \rho _ { \eta }$



> 💡 **Table A.1 解读**: 该表逐项给出观测的维度、单位与逐维传感器噪声 $\eta_t \sim \rho_\eta$ 的采样区间，例如重力投影 $\mathcal{U}[-0.05,0.05]$。上一动作一行噪声列为空，即该输入不加噪，这意味着噪声增广不作用于历史动作通道。

Goal. For the motion-tracking interface, the goal $\mathbf { \nabla } _ { \mathbf { \boldsymbol { g } } _ { t } }$ is read from the reference motion. Compared with BeyondMimic [3], we condition the encoder on a $K = 1 0$ future reference sequence rather than a single-frame target. Other goal interfaces replace $\mathbf { \nabla } _ { \mathbf { \boldsymbol { g } } _ { t } }$ with their corresponding command representation, such as target velocity commands (Appendix B).



> 💡 **目标编码差异**: 运动跟踪时目标 $g_t$ 直接读自参考运动；与 BeyondMimic 相比，编码器的条件从单帧目标换成 $K=10$ 的未来参考序列，等于把目标从“当前时刻”扩成“未来一小段轨迹”。其他接口（如目标速度命令，附录 B）只需替换命令表示，说明 $g_t$ 是可插拔的槽位。

Action. The action $\mathbf { } \mathbf { a } _ { t }$ maps to a joint setpoint $\pmb q ^ { \mathrm { s p } } = \pmb { \alpha } \pmb { a } _ { t } + \pmb q ^ { 0 }$ about the default pose $ { \boldsymbol { q } } ^ { 0 }$ . Following BeyondMimic, the low PD gains make this setpoint a proxy for joint torque rather than a desired position.



> 💡 **动作映射机制**: 动作 $a_t$ 经 $q^{\mathrm{sp}} = \alpha a_t + q^0$ 线性映射为关节设定点，即在默认位姿上叠加缩放后的增量。由于 PD 增益取得很低，这个设定点实际是关节力矩的代理而非期望位置，策略因此直接作用于力矩层面，继承自 BeyondMimic。

Reward. We use BeyondMimic’s motion-tracking reward (Table A.2), removing its self-collision penalty. That penalty guards against reward hacking by a stochastic policy. When the reference brings two bodies close without contact, pressing them together suppresses the action noise and raises the velocity-tracking reward. Our policy is deterministic in imagined rollouts (§3.3), so the exploit has no payoff and the term is unnecessary. The joint-limit penalty is lowered from −10 to −2.



> 💡 **奖励函数调整**: 沿用 BeyondMimic 的运动跟踪奖励（Table A.2），但去掉自碰撞惩罚并把关节限位惩罚由 $-10$ 降到 $-2$。作者给出的理由是：该惩罚防的是随机策略“在参考让两个 body 无接触靠近时用力挤压，以抑制动作噪声、抬高速度跟踪奖励”的 reward hacking，而本文在想象 rollout 中策略是确定性的（§3.3），该漏洞无收益，故该项冗余。

Termination. An episode terminates when the root body deviates from the reference beyond a height or tilt threshold $( | e _ { p , z } | > 0 . 2 5$ m or $e _ { \mathrm { t i l t } } > 0 . 8 )$ . The tilt error $e _ { \mathrm { t i l t } }$ is the absolute difference between the z-components of the gravity direction expressed in the reference and robot root (pelvis) frames. For an upright reference, $e _ { \mathrm { t i l t } }$ reaches 0.8 at about $7 8 ^ { \circ }$ of tilt. Unlike BeyondMimic, we do not terminate on end-effector deviation.



> 💡 **终止条件**: 只有根节点高度偏差 $|e_{p,z}| \gt 0.25$ m 或倾斜误差 $e_{\mathrm{tilt}} \gt 0.8$ 才终止；$e_{\mathrm{tilt}}$ 是参考系与骨盆系中重力方向 z 分量之差的绝对值（无量纲，不是弧度），对直立参考 0.8 约合 $78^\circ$ 倾斜。与 BeyondMimic 不同，末端执行器偏差不触发终止，推断这会推迟对局部肢体误差的失败判定，只保留根节点的粗粒度监控。

Sim context and domain randomization. The per-episode sim context ξ collects the domainrandomization parameters fed to the world model (§3.2); Table A.3 lists them. We omit Beyond-Mimic’s root-velocity perturbations: such pushes inject state discontinuities that the deterministic world model cannot capture, and we find randomization over the physical parameters alone suffices for transfer to hardware.



> 💡 **域随机化策略**: 逐 episode 的 sim context $\xi$ 收集送入世界模型（§3.2）的域随机化参数，具体项见 Table A.3。作者省略了 BeyondMimic 的根速度扰动，理由是这类推力会注入状态不连续、确定性世界模型无法刻画，并称仅对物理参数随机化已足以迁移到真机；这是世界模型可预测性与扰动多样性之间的取舍。

