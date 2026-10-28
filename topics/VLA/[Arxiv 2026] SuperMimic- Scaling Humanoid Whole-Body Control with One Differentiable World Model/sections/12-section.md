[← 返回 README](../README.md)

## C Network architectures

Table A.4 lists the world-model dynamics network of §3.2 and the policy and critic networks of Appendix B.



> 💡 **表格定位**: Table A.4 汇总了§3.2的世界模型动力学网络及附录B的策略与Critic网络架构，是复现模型结构的关键索引。

<table><tr><td>Network</td><td>Input</td><td></td><td>Output Architecture</td></tr><tr><td>Dynamics  $f _ { \omega }$ </td><td> $\hat { \textbf { \textit { s } } } _ { t } , \textbf { \em a } _ { t } , \xi$ </td><td> $\hat { \pmb { s } } _ { t + 1 }$ </td><td>MLP (2048, 2048, 2048)</td></tr><tr><td>Motion encoder encθ,motion</td><td> $K { = } 1 0$  future reference frames z</td><td></td><td>MLP (2048, 1024, 512, 256)</td></tr><tr><td>Velocity encoder encθ,vel (Appendix G)</td><td>normalized command  $\left( { { v _ { x } } , { v _ { y } } , { \omega _ { z } } } \right)$  , feature of the last 10 frames of o</td><td>Z</td><td>MLP (256, 256)</td></tr><tr><td>Shared decoder decθ Critic  $V _ { \psi _ { i } }$ </td><td>z, last 10 frames of o  $\hat { \textbf { \textit { s } } } _ { t } , \textbf { \textit { g } } _ { t } , \textbf { \textit { \xi } }$ </td><td> $\mathbf { \pmb { a } } _ { t }$  scalar</td><td>MLP (2048, 1024, 512, 256) MLP (256, 128); one critic per</td></tr></table>



> 💡 **架构拆解**: 表中各模块均为 MLP：Dynamics $f_\omega$ 输入 $(\hat{s}_t, a_t, \xi)$ 输出 $\hat{s}_{t+1}$；Motion encoder 接收 $K=10$ 个未来参考帧，Velocity encoder 接收归一化指令与最近 10 帧观测特征并产出 32 维 $z$；每个接口各有一个独立 Critic $V_{\psi_i}$。原文未说明 $\xi$ 的具体性质，也未评述这些设计各自的收益。

Table A.4: World-model, policy, and critic networks. MLP: multilayer perceptron, listed by hidden widths. The latent z is 32-dimensional and bounded by tanh.



> 💡 **隐空间约束**: 潜变量 $z$ 为 32 维并经 tanh 有界，取值落在 $(-1,1)$；原文未量化该约束的作用。推断：有界表示能限制解码器与策略的输入尺度，但文中没有对应消融。

```latex
Algorithm A.1 SuperMimic training. Both ω and θ update every iteration; ω is held fixed (stop
gradient) only inside the policy’s backward pass. For a downstream interface, ${ \hat { J } } _ { i }$ updates only its
encoder $\mathrm { e n c } _ { \theta , i }$
Require: world model $f _ { \omega } ;$ policy $\pi \theta$ shared across interfaces $\{ \mathcal { M } _ { i } \} _ { i \in \mathbb { Z } } ;$ replay buffer B
1: for each iteration do
2: ▷ Collect. Roll out $\pi _ { \theta }$ with action noise in simulation; push transitions to $B ,$ replacing the oldest
3: Draw reset start points by return-based sampling ▷ Appendix D.1
4: ▷ Fit world model. Sample a logged k-step window from $B ,$ roll $f _ { \omega }$ out autoregressively on the logged
actions, and step ω $ \omega - \bar { \alpha } _ { \omega } \nabla _ { \omega } \mathcal { L } _ { \mathrm { W M } }$ ▷ Eq. 1
5: ▷ Improve policy. Sample an interface $i \sim p ( \mathcal { T } )$ and a start state from B
6: Imagine H steps on-policy through the fixed $\begin{array} { r } { \dot { f } _ { \omega } \colon \hat { s } _ { t + 1 } = f _ { \omega } \big ( \hat { s } _ { t } , \pi _ { i } \big ( \hat { \bar { \boldsymbol { o } } } _ { t } + \eta _ { t } , { \pmb { g } } _ { t } \big ) , { \pmb \xi } \big ) } \end{array}$
7: Step $\theta \gets \theta + \alpha _ { \theta } \nabla _ { \theta } \hat { J } _ { i }$ ▷ Eq. 2; analytic gradients through $f _ { \omega } ;$ critic $V _ { \psi _ { i } }$
8: Fit the critic $V _ { \psi _ { i } }$ to the GAE targets of the imagined rollout ▷ Eq. 3
9: end for
```



> 💡 **训练机制**: 每次迭代 $\omega$ 与 $\theta$ 都更新，只在策略反向传播时对 $f_\omega$ 停梯度；世界模型在缓冲区采样的已记录 k 步窗口上按 Eq.1 自回归拟合，策略在固定的 $f_\omega$ 上想象 H 步、按 Eq.2 用解析梯度更新，Critic 拟合想象轨迹的 GAE 目标（Eq.3）。另有一条易被忽略的约束：下游接口 $i$ 的目标 $\hat{J}_i$ 只更新它自己的 encoder $\mathrm{enc}_{\theta,i}$。

