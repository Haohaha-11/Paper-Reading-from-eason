[← 返回 README](../README.md)

## 3 Method

## 3.1 SuperMimic training loop

SuperMimic trains one goal-conditioned whole-body controller across a multi-skill motion library and supports multiple goal interfaces. It captures the shared humanoid dynamics in a differentiable world model and improves the policy through short imagined rollouts. Training alternates between three phases in one loop (Figure 2). First, the current policy collects simulator transitions into a replay buffer. Second, we fit the differentiable world model to short replay windows. Third, the policy is improved by backpropagating an imagined return through the frozen world model, with each rollout seeded from a replay-buffer state in the Dyna style [23, 24]. The buffer, model, and policy are updated throughout training, so dynamics learning and control improve together rather than in separate stages (Algorithm A.1, Appendix D). Transitions from earlier policies remain valid for fitting the world model, because the dynamics do not depend on the policy that collected them.



> 💡 **机制拆解**: 训练循环包含数据收集、世界模型拟合和基于想象回滚的策略优化三个阶段。关键优势在于利用 Dyna 风格，通过可微分世界模型进行短程想象 rollout 来更新策略，且因动力学独立于策略，旧数据仍有效，实现了动态学习与控制的同步改进。

We denote the goal-conditioned partially observable Markov decision process (POMDP) by M and use compact notation with state $\mathbf { } _ { s _ { t } , }$ , goal $\mathbf { \nabla } _ { \mathbf { { \mathfrak { g } } } _ { t } } ,$ , and action $\mathbf { } \mathbf { a } _ { t }$ . State $\mathbf { \boldsymbol { s } } _ { t }$ gives the proprioceptive observation $\mathbf { } _  \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { } \mathbf \mathbf { }$ and the termination $d _ { t }$ , which is 1 if $\mathbf { \boldsymbol { s } } _ { t }$ is terminal. Action $\mathbf { } \mathbf { a } _ { t }$ in state $\mathbf { \boldsymbol { s } } _ { t }$ yields the reward $r _ { t + 1 }$ and the next state $\mathbf { } s _ { t + 1 }$ The policy is deterministic, $\mathbf { \delta } \mathbf { a } _ { t } = \pi _ { \theta } ( \mathbf { \sigma } _ { t } , \mathbf { \vec { g } } _ { t } )$ , and $\mathbf { } \mathbf { a } _ { t }$ parameterizes a normalized joint setpoint; for motion tracking, $\mathbf { \nabla } _ { \mathbf { \boldsymbol { g } } _ { t } }$ is read from a reference motion. During data collection, the simulator executes the noisy action $\mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf { } \mathbf \Xi \mathbf { } \mathbf { } \mathbf { } \mathbf \Xi \mathbf { } \mathbf { } \mathbf \Xi \mathbf { } \mathbf \Xi \mathbf { } \mathbf \Xi \mathbf { } \mathbf \Xi \mathbf { } \mathbf \Xi \mathbf { } \mathbf \mathbf { } \mathbf \Xi \mathbf \Xi \mathbf { } \mathbf \mathbf \Xi \Xi \mathbf { } \mathbf \mathbf \Xi \mathbf { } \mathbf \mathbf \Xi \mathbf \Xi \mathbf \Xi \Xi \mathbf \Xi \mathbf \Xi \mathbf \Xi \mathbf \Xi \mathbf \Xi \mathbf \Xi \mathbf \Xi \mathbf \mathbf \Xi \mathbf \Xi \mathbf \mathbf \Xi \mathbf \Lambda \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \Lambda \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \Lambda \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf \mathbf $ with action noise $\epsilon _ { t } \sim \rho _ { \epsilon }$ and the replay buffer stores this executed noisy action. The noise widens the range of actions on which the world model is fit; it serves only the model fit and is optional. Returns use a soft survival weight, $c _ { 0 } = 1$ and $c _ { t + 1 } = c _ { t } ( 1 - d _ { t + 1 } )$ , giving $\begin{array} { r } { J ( \theta ) = \mathbb { E } [ \sum _ { t > 0 } \gamma ^ { t } c _ { t } r _ { t + 1 } ] } \end{array}$ with discount $\gamma \in [ 0 , 1 )$ The simulator logs hard terminations, while the learned model predicts a termination probability $\hat { d } _ { t } ~ \in ~ [ 0 , 1 ]$ , which keeps the survival weight differentiable in imagined rollouts. Throughout, a hat marks the world-model counterpart of a simulator quantity. We also factor the observation as $o _ { t } = { \bar { o } } _ { t } + \eta _ { t }$ , with $\bar { \mathbf { o } } _ { t }$ the noise-free observation and known observation noise $\eta _ { t } \sim \rho _ { \eta }$ sampled outside the world model. Appendix A gives the full spaces and reward.



> 💡 **符号与机制**: 这一段定义了 goal-conditioned POMDP：状态包含本体感知与终止标志 $d_t$，确定性策略 $\pi_\theta$ 输出动作，动作被解释为归一化关节设定值，动作跟踪任务的目标取自参考运动。数据收集时执行的动作为加噪动作 $\epsilon_t \sim \rho_\epsilon$，它会被原样存入 replay buffer，作用是拓宽世界模型拟合的动作分布；原文明确该噪声仅服务于模型拟合，并且是可选项。回报用软生存权重 $c_{t+1}=c_t(1-d_{t+1})$ 计算，使模型预测的终止概率 $\hat{d}_t \in [0,1]$ 在想象 rollout 中保持可微。

The default interface is full-reference motion tracking. Other goal interfaces, such as velocity, share the same learned dynamics and train jointly with motion tracking (Appendix B).



> 💡 **任务接口**: 默认接口为全参考动作跟踪。其他目标接口（如速度）共享同一套学习到的动力学模型，并与动作跟踪联合训练。

## 3.2 Learning a differentiable world model

The simulator’s articulated multi-rigid-body dynamics are known, but the simulator need not provide gradients, so SuperMimic learns a differentiable surrogate to obtain policy gradients: it supplies the predictions and Jacobians for the BPTT update in §3.3. The resulting analytic policy gradient is the gradient of a surrogate for the expected return in simulation. Since the simulator’s randomness has known sources, the world model stays deterministic: it conditions on the per-episode sim context ξ (friction, calibration offsets, and torso center-of-mass) and predicts the noise-free observation $\bar { \mathbf { o } } _ { t }$



> 💡 **世界模型动机**: 因模拟器无梯度，作者构建可微分代理模型以支持反向传播。该模型输入包含每回合模拟上下文 $\xi$（如摩擦系数），输出为去噪观测值，从而保持确定性并获取解析策略梯度。

The world model contains a learned dynamics model and reward, observation, and termination heads; each head is either learned or computed analytically,



> 💡 **模块构成**: 世界模型由一个学习得到的动力学模型加三个头组成——奖励、观测、终止，每个头既可以学习，也可以解析计算（解析头不进入训练损失）。并不存在独立的“状态”头，下一状态由动力学模型直接给出。

$$
\begin{array} { r } { \hat { s } _ { t + 1 } = f _ { \omega } \big ( \hat { s } _ { t } , a _ { t } , \xi \big ) , \qquad \hat { r } _ { t + 1 } = r _ { \omega } \big ( \hat { s } _ { t } , a _ { t } , g _ { t } \big ) , \qquad \hat { \sigma } _ { t } = g _ { \omega } \big ( \hat { s } _ { t } , \xi \big ) , \qquad \hat { d } _ { t } = d _ { \omega } \big ( \hat { s } _ { t } , g _ { t } \big ) . } \end{array}
$$



> 💡 **公式 1 批读**: 四个式子分别是动力学 $f_\omega(\hat s_t,a_t,\xi)$ 给出下一状态、奖励头 $r_\omega$、观测头 $g_\omega$ 输出 $\hat\sigma_t$、终止头 $d_\omega$ 给出终止指示。只有动力学和奖励需要动作 $a_t$，观测头只看状态和 $\xi$，奖励与终止头则以任务目标 $g_t$ 为条件。

We fit the network weights ω on k-step autoregressive replay windows $( k { = } 8 )$ , seeded at the logged state $\hat { \pmb { s } } _ { 0 } = { \pmb { s } } _ { 0 }$ and rolled forward on logged actions, by minimizing



> 💡 **训练机制**: 采用 $k=8$ 步自回归回放窗口拟合权重 $\omega$。从记录状态初始化，沿真实动作滚动预测，通过最小化多任务损失实现模型更新。

$$
\mathcal { L } _ { \mathrm { W M } } ( \omega ) = \mathbb { E } _ { \boldsymbol { B } } \sum _ { j = 1 } ^ { k } \Big [ \| \hat { \mathbf { s } } _ { j } - \boldsymbol { s } _ { j } \| ^ { 2 } + ( \hat { \boldsymbol { r } } _ { j } - \boldsymbol { r } _ { j } ) ^ { 2 } + \| \hat { \bar { \mathbf { o } } } _ { j } - \boldsymbol { \bar { o } } _ { j } \| ^ { 2 } + \mathrm { B C E } ( \hat { d } _ { j } , d _ { j } ) \Big ] ,\tag{1}
$$



> 💡 **损失函数**: 总损失 $\mathcal{L}_{WM}$ 包含状态、奖励、观测的 MSE 误差及终止标签的二元交叉熵 (BCE)。解析计算的头部不产生损失项，仅学习型头部参与优化。

where an analytic head has no loss term. Here $r _ { j }$ is the simulator reward, $d _ { j } \in \{ 0 , 1 \}$ is the simulator termination label, and BCE is the binary cross-entropy.



> 💡 **符号定义**: $r_j$ 是模拟器给出的真实奖励，$d_j\in\{0,1\}$ 是终止标签，BCE 为二元交叉熵；解析计算的头在式 1 中没有对应损失项。观测项里的 $\bar o_j$ 是上一节定义的噪声无关观测，此处未再重复定义。

## 3.3 Learning the policy through the world model

During each policy update, we hold the world-model weights $\omega$ fixed and optimize $\pi _ { \theta }$ and $V _ { \psi }$ on imagined rollouts of H=32 steps. The critic $V _ { \psi }$ estimates the return beyond step $H ,$ , so the model must be accurate only within these H steps. This imagination horizon exceeds the k-step replay windows on which we fit the model (§3.2). In practice, the learned model remains stable over indefinitely long rollouts, because the k-step autoregressive fit trains it to correct its own prediction errors.



> 💡 **想象轨迹长度**: 策略更新时固定模型权重，在 $H=32$ 步的想象轨迹上优化。虽然 $H$ 大于训练窗口 $k=8$，但自回归训练使模型能纠正自身误差，保证长程稳定性。

Each imagined rollout starts from a replay-buffer state $s _ { 0 } \sim \rho _ { 0 }$ with its recorded sim context $\xi .$ At step t, the model emits $\hat { \bar { \mathbf { o } } } _ { t }$ , we re-add sensor noise, and the policy acts: $\mathbf { } \mathbf { a } _ { t } = \pi _ { \theta } ( \hat { \bar { \mathbf { o } } } _ { t } + \eta _ { t } , \mathbf { g } _ { t } )$ $\eta _ { t } \sim \rho _ { \eta }$ . The policy objective over imagined rollouts is



> 💡 **数据流细节**: 想象轨迹始于回放缓冲区的状态 $s_0$ 及其上下文 $\xi$。每一步先预测去噪观测，再叠加传感器噪声 $\eta_t$，最后由策略网络根据含噪观测生成动作。

$$
\hat { J } ( \theta ) = \mathbb { E } _ { s _ { 0 } \sim \rho _ { 0 } , \pi _ { \theta } } \Big [ \sum _ { t = 0 } ^ { H - 1 } \gamma ^ { t } \hat { c } _ { t } \hat { r } _ { t + 1 } ~ + ~ \gamma ^ { H } \hat { c } _ { H } V _ { \psi } ( \hat { s } _ { H } , g _ { H } , \pmb { \xi } ) \Big ] .\tag{2}
$$



> 💡 **策略目标（式 2）**: 在 $H=32$ 步想象轨迹上最大化折扣回报，$t=0$ 到 $H-1$ 共累加 $H$ 个即时奖励，每步带存活权重 $\hat c_t$；末项用 $\gamma^H\hat c_H V_\psi$ 估计视界外回报。因此模型只需在 $H$ 步内准确，更远处交给 critic。

For motion tracking, the reward $r _ { t + 1 }$ is the BeyondMimic motion-tracking reward [3] without its self-collision penalty (Appendix A).



> 💡 **奖励设计**: 运动跟踪沿用 BeyondMimic 的跟踪奖励 [3]，但去掉其中的自碰撞惩罚项，细节在 Appendix A。原文没有说明去掉该惩罚项的理由；推断：让奖励专注于姿态匹配，避免接触项干扰模仿信号。

The world model is differentiable, so BPTT computes a first-order gradient $\nabla _ { \boldsymbol { \theta } } \hat { J }$ through the H imagined steps via the world model’s Jacobians with respect to $\hat { \mathbf { } } _ { \pmb { s } _ { t } }$ and $\mathbf { } \mathbf { a } _ { t }$ . In contrast, PPO treats the dynamics as a black box: it weights the score $\nabla _ { \theta }$ log $\pi _ { \theta }$ of a stochastic policy by the advantages of sampled actions, a likelihood-ratio estimate. BPTT instead differentiates the return with respect to each action, so over short horizons its gradient has much lower variance.



> 💡 **梯度方法对比**: 利用世界模型的可微性，通过 BPTT 直接对动作求导获得低方差梯度。相比之下，PPO 基于采样动作的优势函数加权得分，属于高方差的似然比估计。

The critic $V _ { \psi }$ is fit to generalized advantage estimation returns [25] computed backward in time over the imagined transitions only,



> 💡 **Critic 的拟合目标**: $V_\psi$ 只对想象轨迹上的转移做时间反向的 GAE 回归，不使用真实模拟数据，因此与策略更新共享同一批想象样本。

$$
\mathcal { L } _ { V } ( \psi ) = \mathbb { E } \sum _ { t = 0 } ^ { H - 1 } \big ( V _ { t } - \hat { R } _ { t } \big ) ^ { 2 } , \qquad \hat { R } _ { t } = \hat { r } _ { t + 1 } + \gamma \big ( 1 - \hat { d } _ { t + 1 } \big ) \big [ ( 1 - \lambda ) V _ { t + 1 } + \lambda \hat { R } _ { t + 1 } \big ] ,\tag{3}
$$



> 💡 **公式 3 批读**: 定义 GAE 递归式 $\hat{R}_t$。该式结合即时奖励、折扣因子 $\gamma$、终止标志 $(1-\hat{d})$ 及混合参数 $\lambda$，平衡偏差与方差，是标准 Actor-Critic 算法的核心组件。

with $V _ { t } = V _ { \psi } ( \hat { \pmb { s } } _ { t } , { \pmb { g } } _ { t } , { \pmb { \xi } } )$ . The recursion starts from $\hat { R } _ { H } = V _ { H }$ , the critic’s estimate of the return beyond the horizon. The targets $\hat { R } _ { t }$ are computed once per rollout and remain fixed while $\psi$ is fit.



> 💡 **边界条件**: 递归起点设为 $\hat{R}_H = V_H$，即用当前批评家估计填补想象视界外的回报。目标值在每次 rollout 中计算一次并固定，作为回归分析的标签。

The policy and critic updates together form a short-horizon actor–critic in the spirit of SHAC [19], which backpropagates through a differentiable simulator. SuperMimic replaces SHAC’s hard episode termination with the survival weight $\hat { c } _ { t } ^ { \phantom { \dagger } } ,$ , and its critic has no exponential-moving-average target network. Prior motion-tracking controllers trained with analytic gradients often omit a critic and optimize only the H-step reward sum [12, 6]. Without a critic, training can reach a similar nominal tracking return, but this unbootstrapped objective is more myopic. The critic matters after perturbations, where recovery or balance rewards can lie beyond the imagination window (§4.3).



> 💡 **架构创新**: 相比 SHAC，SuperMimic 引入存活权重 $\hat{c}_t$ 处理终止，且移除 EMA 目标网络。批评家的存在至关重要，因为它能捕捉超出短视界的恢复与平衡奖励，克服纯短视界优化的近视性。

