[← 返回 README](../README.md)

## A.2. Downstream Training

The four interaction programs start from the same robotvideo-pretrained causal backbone. The action expert is initialized from width-adapted copies of the corresponding pretrained video-expert layers, while action-specific input/output and conditioning layers are initialized separately. The video and action experts are then jointly optimized during downstream training. All programs use the same data, optimization settings, and flow-matching objective; they differ only in generation order and future cross-modal attention.



> 💡 **初始化与对照条件**: 动作专家由对应的预训练视频专家层经宽度适配复制而来，动作专用的输入/输出与条件层单独初始化，随后两个专家联合训练。四个交互程序只在生成顺序和 future cross-modal attention 上不同，数据、优化设置与流匹配目标完全一致，因此差异可归因于这两点。

For the downstream runs examined here, training uses AdamW with $\beta _ { 1 } = 0 . 9 , \beta _ { 2 } = 0 . 9 5$ , weight decay 0.1, a learning rate of $1 0 ^ { - 5 }$ , with 10 warmup steps followed by a constant learning-rate schedule. We use a per-device batch size of 1 and accumulate gradients over 10 steps, yielding an effective batch size of 10. Gradient norms are clipped at 2.0, and training uses bf16 mixed precision. We train all downstream models for 10,000 optimization steps.



> 💡 **优化设置**: 采用 AdamW、$10^{-5}$ 学习率及 bf16 精度。有效 batch size 为 10（单卡 bs=1，梯度累积 10 步），共训练 10k 步。

Flow-matching objective. Both streams are trained with the rectified-flow objective in Eq. (4). For implementation, we parameterize the noise level as $\sigma = 1 - \tau ,$ , such that $\sigma = 0$ denotes clean data and $\sigma = 1$ denotes pure noise. We uniformly sample from a 1,000-level base noise grid and apply the modality-specific shift



> 💡 **噪声参数化**: 定义 $\sigma = 1 - \tau$，其中 $\sigma=0$ 为干净数据，$\sigma=1$ 为纯噪声。噪声等级在 1000 级网格上均匀采样。

$$
S _ { s } ( \sigma ) = \frac { s \sigma } { 1 + ( s - 1 ) \sigma } , \qquad s _ { V } = 5 . 0 , \quad s _ { A } = 1 . 0 ,\tag{11}
$$



> 💡 **公式 11 批读**: $S_s(\sigma)$ 是模态相关的噪声水平重映射，视频取 $s_V=5.0$、动作取 $s_A=1.0$。$s=1$ 时该式为恒等映射，$s\gt 1$ 会把采样到的 $\sigma$ 整体推高，因此实际上只有视频侧被施加了噪声偏移。

for video and action, respectively.



> 💡 **系数归属**: 这一行把 Eq. (11) 的两个移位系数与模态对应起来——$s_V=5.0$ 用于视频、$s_A=1.0$ 用于动作，是读该公式时容易忽略的对应关系。

We additionally apply timestep-dependent loss weighting,



> 💡 **损失加权引入**: 除了模态特定的噪声偏移外，还引入了随时间步变化的损失权重机制，以调节不同噪声水平的贡献。

$$
w _ { P } ( \sigma ) \propto \exp \Bigl [ - 2 \bigl ( \sigma - \textstyle { \frac { 1 } { 2 } } \bigr ) ^ { 2 } \Bigr ] - e ^ { - 1 / 2 } ,\tag{12}
$$



> 💡 **公式 12 批读**: 权重是中心在 $\sigma=0.5$ 的高斯形状，再减去常数 $e^{-1/2}$；这一项使 $\sigma=0$ 和 $\sigma=1$ 两端的取值恰好为 0，只保留中间噪声区间的监督信号。

where the weights are normalized to have unit mean over the corresponding timestep grid. This emphasizes intermediate noise levels while assigning zero weight to the pure-noise endpoint.



> 💡 **加权效果**: 权重归一化后均值为 1。该设计强调中等噪声水平的训练信号，并将纯噪声端点的权重设为零。

The flow-matching loss is applied only to predicted future video latents and valid action entries. Video losses are normalized over the supervised latent elements, while action losses are averaged over the action horizon after masking invalid action dimensions. The two modality losses are combined with equal weight,



> 💡 **损失计算细节**: 仅对预测的未来视频潜变量和有效动作计算损失。视频按元素归一化，动作掩码无效维度后取平均，两者等权相加。

$$
\mathcal { L } = \mathcal { L } _ { V } + \mathcal { L } _ { A } .\tag{13}
$$



> 💡 **总损失公式**: 最终目标函数 $\mathcal{L}$ 是视频损失 $\mathcal{L}_V$ 与动作损失 $\mathcal{L}_A$ 的直接求和，体现多任务联合优化。

