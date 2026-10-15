[← 返回 README](../README.md)

## A.4.3 TRAINING FLOPS

Compact form. Approximating backward cost as twice the corresponding forward cost,



> 💡 **问题动机**: 这一句设定训练 FLOPs 的紧凑近似前提：反向成本按对应前向成本的两倍计，因此 Eq. 21 把总训练成本写成约 $3 \times$ 带梯度前向加标签计算。它是后续与 baseline 比较的基础。注意此处只是给出近似口径，原文并未在本句直接声明“算法层面效率优势”，该定位依据是 block 3 的“same training implementation … algorithmic differences”，属于推断性定位。（AI 批注）

$$
\mathrm { F L O P s } _ { \mathrm { t r a i n } } \approx 3 \mathrm { F L O P s } _ { \mathrm { f o r w a r d } } + \mathrm { F L O P s } _ { \mathrm { l a b e l } } ,\tag{21}
$$



> 💡 **公式批读 (Eq. 21)**: 定义训练总 FLOPs 的紧凑形式。由前一句“反向成本约为前向两倍”得到 $\mathrm{FLOPs}_{\mathrm{train}} \approx 3\,\mathrm{FLOPs}_{\mathrm{forward}} + \mathrm{FLOPs}_{\mathrm{label}}$。其中 $3\,\mathrm{FLOPs}_{\mathrm{forward}}$ 表示一次前向加两次等价反向；$\mathrm{FLOPs}_{\mathrm{forward}}$ 指带梯度的前向，不包含后面单独列出的无梯度标签计算。（AI 批注）

where $\mathrm { F L O P s } _ { \mathrm { f o r w a r d } }$ includes all gradient-tracked forward computation, including the LM head, updater and decider, and $\mathrm { F L O P s } _ { \mathrm { l a b e l } }$ includes all additional no-gradient computation for online labels, including context reconstruction and auxiliary-module calls. All models share the same training implementation, so implementation-level choices such as activation checkpointing are not counted and the comparison reflects only algorithmic differences.



> 💡 **机制拆解**: 界定两项边界。原文列举 $\mathrm{FLOPs}_{\mathrm{forward}}$ 含所有带梯度的前向计算，包括 LM head、updater 和 decider；backbone 由 Eq. 22 的 $\mathrm{FLOPs}_{\mathrm{bb}}$ 计入，属于由公式支持的补充。$\mathrm{FLOPs}_{\mathrm{label}}$ 含在线标签所需的额外无梯度计算，包括 context reconstruction 和 auxiliary-module calls。原文还说明所有模型共用同一训练实现，因此 activation checkpointing 等实现层选择不计入，比较只反映算法差异。（AI 批注）

Per-depth counts. Consider a training sequence of $T$ tokens. Let $N _ { m } = | \{ t : m _ { t } \geq m \} |$ be the number of tokens that execute iteration $m ,$ , with $N _ { 1 } = T$ , and let $\begin{array} { r } { A _ { m } = \sum _ { t : m _ { t } \geq m } S _ { t } ^ { ( m ) } } \end{array}$ be the number of query–key pairs at depth m. Here $S _ { t } ^ { ( m ) }$ follows Equation 15 without the prompt term, because training attends over the whole sequence.



> 💡 **变量定义**: $T$ 为训练序列 token 数；$N_m = |\{t : m_t \ge m\}|$ 是执行第 $m$ 次迭代的 token 数，且 $N_1 = T$；$A_m = \sum_{t : m_t \ge m} S_t^{(m)}$ 是深度 $m$ 的 query–key 对数。$S_t^{(m)}$ 沿用 Eq. 15 但去掉 prompt 项，原因原文写明是训练注意力覆盖整个序列。把“去掉 prompt 项”称为与推理形式的关键差异，是由原文该句支持的推断，而非原文直接比较句。（AI 批注）

Gradient-tracked forward. Every executed iteration runs the backbone and LM head with gradients, the updater prepares iterations 2 to M, and the decider runs after iterations 1 to $M - 1 \colon$



> 💡 **梯度跟踪范围**: 每次被执行的迭代都带梯度运行 backbone 和 LM head；updater 用于准备第 2 到第 $M$ 次迭代；decider 在第 1 到第 $M-1$ 次迭代之后运行。Eq. 22 据此把三类模块的调用次数分别展开。原文未在本句直接说这解释 baseline 随 $M$ 线性增长；结合 Table 10 可作此推断，已标为推断。（AI 批注）

$$
\begin{array} { r } { \mathrm { F L O P s } _ { \mathrm { f o r w a r d } } = \displaystyle \sum _ { m = 1 } ^ { M } \Big [ N _ { m } \big ( \mathrm { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } \big ) + 4 L d A _ { m } \Big ] } \\ { + \displaystyle \sum _ { m = 2 } ^ { M } N _ { m } \mathrm { F L O P s } _ { \mathrm { u p d } } + \sum _ { m = 1 } ^ { M - 1 } N _ { m } \mathrm { F L O P s } _ { \mathrm { d e c i d e r } } . } \end{array}\tag{22}
$$



> 💡 **公式批读 (Eq. 22)**: 展开 $\mathrm{FLOPs}_{\mathrm{forward}}$。第一项 $\sum_{m=1}^{M}[N_m(\mathrm{FLOPs}_{\mathrm{bb}}+\mathrm{FLOPs}_{\mathrm{head}})+4LdA_m]$ 覆盖每次迭代的 backbone、LM head 和注意力开销 $4LdA_m$；第二项 $\sum_{m=2}^{M}N_m\mathrm{FLOPs}_{\mathrm{upd}}$ 是 updater 开销；第三项 $\sum_{m=1}^{M-1}N_m\mathrm{FLOPs}_{\mathrm{decider}}$ 是 decider 开销。求和上下限不同，对应 updater 从第 2 次迭代开始、decider 到第 $M-1$ 次迭代为止的执行时机。（AI 批注）

The decider loss reuses the decider outputs of this pass and adds no further calls.



> 💡 **补充说明**: decider loss 复用本次 pass 的 decider 输出，不再增加额外调用，因此 Eq. 22 中不需要为 decider loss 单独计一项。这条限定避免把 loss 的监督信号误算成一次额外的 decider 前向。（AI 批注）

Label computation. Online labels require the next-iteration loss of every token that stops. After iteration $m < M$ , a no-gradient lookahead therefore applies the updater, backbone and LM head at depth $m + 1$ . Under extended duo-causal attention, a stopped token’s lookahead query must also see the depth- $( m + 1 )$ KV of earlier tokens that continue. The lookahead reconstructs this context by re-running all $N _ { m }$ tokens that executed iteration m, rather than only the $N _ { m } - N _ { m + 1 }$ tokens that stop:



> 💡 **标签计算机制**: 在线标签需要每个停止 token 的下一迭代 loss。对 $m \lt M$，无梯度 lookahead 要在深度 $m+1$ 应用 updater、backbone 和 LM head。由于 extended duo-causal attention，停止 token 的 lookahead query 还必须看到继续执行的更早 token 在深度 $m+1$ 的 KV；因此 lookahead 重建上下文时重跑所有在第 $m$ 次迭代执行的 $N_m$ 个 token，而不是只跑停止的 $N_m-N_{m+1}$ 个 token。这是标签计算昂贵的原因，属于原文直接给出的机制说明。（AI 批注）

$$
\mathrm { F L O P s } _ { \mathrm { l a b e l } } = \sum _ { m = 1 } ^ { M - 1 } \Big [ N _ { m } \big ( \mathrm { F L O P s } _ { \mathrm { u p d } } + \mathrm { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } \big ) + 4 L d \tilde { A } _ { m + 1 } \Big ] ,\tag{23}
$$



> 💡 **公式批读 (Eq. 23)**: 量化 $\mathrm{FLOPs}_{\mathrm{label}}$。求和从 $m=1$ 到 $M-1$，因为最后一次迭代之后不需要 lookahead；每项包含 $N_m(\mathrm{FLOPs}_{\mathrm{upd}}+\mathrm{FLOPs}_{\mathrm{bb}}+\mathrm{FLOPs}_{\mathrm{head}})$ 和 lookahead 注意力项 $4Ld\tilde{A}_{m+1}$。与 Eq. 22 的 forward 项结构相似，但这里是无梯度计算。（AI 批注）

where $\tilde { A } _ { m + 1 }$ counts each lookahead query as if its token continued.



> 💡 **符号解释**: $\tilde{A}_{m+1}$ 把每个 lookahead query 都按“其 token 继续执行”来计数，用于估算重建深度 $m+1$ 上下文时的 query–key 对数。它不等同于 $A_m$，因为后者只统计实际执行第 $m+1$ 次迭代的 token。（AI 批注）

Baselines. Baselines compute no labels, so $\mathrm { F L O P s } _ { \mathrm { l a b e l } } = 0$ , and every token executes all iterations, so $N _ { m } = T$ . Standard has one backbone pass and one LM head per token. Ouro applies the LM head at every iteration because its objective supervises each exit, Huginn applies it once after the coda, and TaH2-fixed applies it at every iteration together with M − 1 updater calls. Their attention pairs follow the fixed-depth key counts of Section A.4.2 with $P = 0$



> 💡 **Baseline 对比逻辑**: baseline 不计算在线标签，故 $\mathrm{FLOPs}_{\mathrm{label}}=0$；且每个 token 都执行所有迭代，故 $N_m=T$。Standard 每个 token 一次 backbone pass 和一次 LM head。Ouro 每次迭代都应用 LM head（其目标监督每个出口）；Huginn 只在 coda 之后应用一次；TaH2-fixed 每次迭代都应用 LM head，并有 $M-1$ 次 updater 调用。它们的注意力对按 Section A.4.2 的固定深度 key 计数且 $P=0$。注意原文用词是“每次迭代”而非“每层”。由这些调用频率差异加 $N_m=T$，可推断固定深度方法在相同 ceiling 下通常比 TaH2 更贵，这是推断而非本句直述。（AI 批注）

Training cost of the 1.7B runs. Table 10 evaluates these expressions for all 1.7B models, each trained for 6,402 steps of 128 sequences (three epochs). For TaH2, we use the average iteration count per token logged during training. The label lookahead accounts for 20–24% of the training FLOPs of TaH2. At M = 2, TaH2 costs 1.87× Standard, below TaH2-fixed (2.01×) and Ouro (2.00×) at the same ceiling.



> 💡 **实验/表格解读**: Table 10 用这些表达式评估所有 1.7B 模型；每个模型训练 6,402 步、每步 128 条序列（三个 epoch）。对 TaH2 使用训练中记录的平均迭代次数/token。原文陈述标签 lookahead 占 TaH2 训练 FLOPs 的 20–24%；按表中 Label/Total 反算，$M=2$ 约 19.5%、$M=4$ 约 23.4%、$M=8$ 约 24.1%，与原文区间基本吻合，$M=2$ 处于下界附近（口径或四舍五入所致，属核对备注）。在 $M=2$ 时，TaH2 成本为 Standard 的 1.87 倍，低于同一 ceiling 下的 TaH2-fixed（2.01 倍）和 Ouro（2.00 倍）。原文是“evaluates these expressions”的公式评估，不是 wall-clock 实测；把优势归因于“自适应深度带来的节省”是合理推断，原文该句未直接给出因果。（AI 批注）

Table 10: Training FLOPs $( 1 0 ^ { 1 8 } )$ of the 1.7B post-training runs, split into the terms of Equation 21. Fwd. + bwd. reports 3 FLOPs<sub>forward</sub>.
<table><tr><td>Model</td><td>M</td><td>Fwd. + bwd.</td><td>Label</td><td>Total</td><td>vs. Standard</td></tr><tr><td>Standard</td><td>1</td><td>41.8</td><td>一</td><td>41.8</td><td>1.00×</td></tr><tr><td rowspan="2">Huginn</td><td>3</td><td>77.7</td><td>一</td><td>77.7</td><td>1.86×</td></tr><tr><td>7</td><td>149.3</td><td>一</td><td>149.3</td><td>3.57×</td></tr><tr><td rowspan="2">Ouro</td><td>2</td><td>83.6</td><td>一</td><td>83.6</td><td>2.00×</td></tr><tr><td>4</td><td>167.1</td><td>一</td><td>167.1</td><td>4.00×</td></tr><tr><td rowspan="2">TaH2-fixed</td><td>2</td><td>84.0</td><td>一</td><td>84.0</td><td>2.01×</td></tr><tr><td>4</td><td>168.4</td><td>一</td><td>168.4</td><td>4.03×</td></tr><tr><td rowspan="3">TaH2</td><td>2</td><td>62.8</td><td>15.2</td><td>78.1</td><td>1.87×</td></tr><tr><td>4</td><td>87.6</td><td>26.7</td><td>114.3</td><td>2.73×</td></tr><tr><td>8</td><td>164.2</td><td>52.0</td><td>216.2</td><td>5.17×</td></tr></table>



> 💡 **Table 10 批读**: 表格把各模型在不同最大深度 $M$ 下的训练 FLOPs 拆成 Eq. 21 的项，单位为 $10^{18}$；Fwd. + bwd. 列报告 $3\,\mathrm{FLOPs}_{\mathrm{forward}}$。逐项核对：Standard $M=1$ 为 41.8（1.00 倍）；Huginn $M=3/7$ 为 77.7/149.3（1.86 倍/3.57 倍）；Ouro $M=2/4$ 为 83.6/167.1（2.00 倍/4.00 倍）；TaH2-fixed $M=2/4$ 为 84.0/168.4（2.01 倍/4.03 倍）；TaH2 $M=2/4/8$ 为 78.1/114.3/216.2（1.87 倍/2.73 倍/5.17 倍）。Label 列仅 TaH2 有值（15.2/26.7/52.0），对应其在线标签机制；Fwd.+bwd.+Label 与 Total 逐行自洽（如 87.6+26.7=114.3）。随 $M$ 增大 TaH2 相对 TaH2-fixed 的倍数优势扩大（$M=4$: 2.73 倍 vs 4.03 倍），这是表格数据直接显示的趋势；同样要注明这是公式评估结果，不是 wall-clock 实测。此外 TaH2 的 $M=4$ 与 $M=8$ 总成本高于 Ouro/TaH2-fixed 同 ceiling，原文并未声称在所有 $M$ 上都更便宜。（AI 批注）

