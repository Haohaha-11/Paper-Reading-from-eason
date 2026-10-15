[← 返回 README](../README.md)

## 4 TAH2: POST-TRAINING ADAPTIVE LOOPED MODELS

Building on Section 3, TaH2 learns token-dependent depth through joint post-training of the backbone and decider. We describe its architecture and training scheme below.



> 💡 **Section 概览**：TaH2 在 Section 3 的基础上，通过 backbone 与 decider 的联合 post-training 学习 token-dependent depth，本节随后给出架构与训练方案。需要界定的一点：原文此处只说明联合后训练了 backbone 和 decider；updater 也被联合训练是在 block 14 才出现的。至于“把深度选择从推理时策略转为训练时优化”，正文要到 block 12 才通过与 Ouro/TaH 的对比间接支撑，本句本身并未如此表述，属于对比性推断。

## 4.1 ARCHITECTURE

TaH2 comprises a shared Transformer backbone, a learned input-injection updater, and a token-level iteration decider. The updater and decider add fewer than $3 \%$ parameters at every scale we study (Table 7). Each iteration executes all L backbone layers using extended duo-causal attention.



> 💡 **架构组成与开销**：TaH2 由共享 Transformer backbone、learned input-injection updater、token 级 iteration decider 三部分组成。数字核对：新增参数少于 3% 的主语是“updater 与 decider 合计”，且在每个测试规模上都成立（Table 7）；每次迭代执行全部 $L$ 层 backbone，并使用 extended duo-causal attention。批注：原文只给出参数量这一效率证据，未提供 FLOPs 或延迟数据，因此“轻量高效”目前仅有参数占比支撑，不宜外推到实际推理速度。

Extended duo-causal attention. We extend TaH’s duo-causal attention (Fu et al., 2025b) to support training-time lookahead. At depth $m ,$ , token t attends to executed KV states at positions $s \leq t$ and depths $j \ \leq \ m$ . During training, stopped tokens take an additional no-gradient iteration for supervision. Lookahead queries cannot attend to other tokens’ lookahead states; all other attention follows the duo-causal rule (Appendix A.2.1).



> 💡 **Extended duo-causal attention 批读**：把 TaH 的 duo-causal attention 扩展以支持 training-time lookahead。规则是：在深度 $m$，token $t$ 只关注“已执行”的 KV 状态（executed KV states），位置满足 $s \leq t$、深度满足 $j \leq m$；训练中已停止的 token 额外做一次 no-gradient 迭代以提供监督；lookahead query 不能关注其他 token 的 lookahead 状态，其余注意力遵循 duo-causal 规则（Appendix A.2.1）。原文只说明 lookahead 用于监督、且不跨 token 泄露；草稿中“确保训练时的视野与推理一致”并没有原文依据，应视为推断。

Learned state update. The first iteration takes the token embedding $\mathbf { e } _ { t }$ as input. Subsequent iterations use a learned input-injection updater (Geiping et al., 2025a), modifying Equation 1 to



> 💡 **状态更新的输入约定**：第一轮迭代以 token embedding $\mathbf{e}_t$ 为输入；后续轮改用 learned input-injection updater（Geiping et al., 2025a），把 Equation 1 改为 Eq. 2。这里要纠正一个易误读处：后续轮并非“不再复用上一轮输出”，updater 的输入同时包含 $\mathbf{e}_t$ 与上一轮最终层状态 $\mathbf{h}_t^{(m)}$，其作用是把该状态映射回 backbone 输入空间并重新注入输入 embedding。原文没有提到“梯度消失”或“特征漂移”的动机，这类说法属于推断，不应写成论文主张。

$$
\mathbf { h } _ { t } ^ { ( m + 1 ) } = \mathcal { F } _ { \boldsymbol { \theta } } \left( \mathcal { U } _ { \psi } \left( \mathbf { e } _ { t } , \mathbf { h } _ { t } ^ { ( m ) } \right) \right) .\tag{2}
$$



> 💡 **公式批读（Eq. 2）**：$\mathbf{h}_t^{(m+1)} = \mathcal{F}_{\theta}(\mathcal{U}_{\psi}(\mathbf{e}_t, \mathbf{h}_t^{(m)}))$。$\mathcal{U}_{\psi}$ 是小型归一化 MLP，作用是把上一轮 final-layer state 映射回 backbone 输入空间，同时重新注入输入 embedding $\mathbf{e}_t$，因此每一轮都能重新接触到原始输入。原文只说明 $\mathcal{U}_{\psi}$ 的结构与输入输出（Appendix A.2）；“防止随深度增加而偏离太远”是合理推断，但需标为推断而非原文结论。

Here $\mathcal { U } _ { \psi }$ is a small normalised MLP that reinjects the input embedding while mapping the previous final-layer state back to the backbone input space (Appendix A.2).



> 💡 **Updater 细节**：$\mathcal{U}_{\psi}$ 被描述为 small normalised MLP，承担两个功能——reinject 输入 embedding、把 previous final-layer state 映射回 backbone 输入空间（Appendix A.2）。批注：“轻量级”与“低参数开销”方向一致，但参数证据是 Table 7 中 updater 与 decider 合计少于 3%，不能单独归因于 updater。

Iteration decider and output. After iteration $m < M$ , a lightweight decider predicts a conditional continue probability



> 💡 **Decider 与输出接口**：在 $m \lt M$ 时，轻量 decider 预测一个条件继续概率 $g_t^{(m)}$；输出混合则在停止或到达 $M$ 后构造。原文只把 decider 定义为“预测 continue probability 的轻量模块”，并未说明其网络形态（是否二分类头）或“软门控”的具体实现，后者属于解释性表述。

$$
g _ { t } ^ { ( m ) } = \mathcal { D } _ { \phi } \Big ( \mathbf { e } _ { t } , \mathbf { h } _ { t } ^ { ( m ) } , \mathrm { T o p K } ( \mathbf { q } _ { t } ^ { ( m ) } ) \Big ) \in ( 0 , 1 ) .\tag{3}
$$



> 💡 **公式批读（Eq. 3）**：$g_t^{(m)} = \mathcal{D}_{\phi}(\mathbf{e}_t, \mathbf{h}_t^{(m)}, \mathrm{TopK}(\mathbf{q}_t^{(m)})) \in (0,1)$。decider 的输入包含初始 embedding、当前最终层状态以及当前预测的 TopK 结果。原文未解释 TopK 的语义，也未说明它是否代表“置信度”；因此“据此判断是否需要更多计算来修正错误”属于推断，需要 Appendix 或后续实验作为证据。

With exit threshold $\tau _ { \mathrm { e x i t } }$ , token t stops at the first depth where $g _ { t } ^ { ( m ) } < \tau _ { \mathrm { e x i t } }$ and otherwise runs to M. The continue probabilities induce stopping weights over the executed depths (Graves, 2016; Zeng et al., 2026), with the final executed iteration absorbing the remaining mass:



> 💡 **停止规则与输出混合**：给定退出阈值 $\tau_{\mathrm{exit}}$，token 在首个满足 $g_t^{(m)} \lt \tau_{\mathrm{exit}}$ 的深度处停止，否则一直执行到 $M$。继续概率按 Graves (2016)、Zeng et al. (2026) 的方式诱导出各执行深度上的停止权重，最后一个实际执行的迭代吸收剩余概率质量。批注：这一设计把离散的深度选择转成可微的加权聚合，是 Eq. 4 成立的前提。

$$
\omega _ { t } ^ { ( m ) } = \left\{ \begin{array} { l l } { ( 1 - g _ { t } ^ { ( m ) } ) \prod _ { j < m } g _ { t } ^ { ( j ) } , } & { m < m _ { t } , } \\ { \prod _ { j < m _ { t } } g _ { t } ^ { ( j ) } , } & { m = m _ { t } , } \end{array} \right. \quad \quad \mathbf { q } _ { t } = \sum _ { m = 1 } ^ { m _ { t } } \omega _ { t } ^ { ( m ) } \mathbf { q } _ { t } ^ { ( m ) } .\tag{4}
$$



> 💡 **公式批读（Eq. 4）**：对 $m \lt m_t$，$\omega_t^{(m)} = (1-g_t^{(m)}) \prod_{j \lt m} g_t^{(j)}$；对 $m = m_t$，$\omega_t^{(m)} = \prod_{j \lt m_t} g_t^{(j)}$，即最后执行的深度吸收剩余质量；最终 $\mathbf{q}_t = \sum_{m=1}^{m_t} \omega_t^{(m)} \mathbf{q}_t^{(m)}$。可以自行验证各权重之和为 1（截断几何分布形式），因此这是对已执行深度的凸组合；但原文没有展开说明归一性或梯度性质，这两点属于数学/实现层面的推断。

Training is on-policy with respect to depth selection: tokens follow the current decider’s decisions under the same rule used at inference. In contrast, Ouro trains at full depth before applying early exit at inference (Zhu et al., 2025), while TaH trains under an oracle policy and uses learned decisions at inference (Fu et al., 2025b).



> 💡 **On-policy 深度选择与对比**：TaH2 在深度选择上是 on-policy 的——训练时 token 遵循当前 decider 的决策，且规则与推理时相同。对照关系：Ouro 在 full depth 下训练、推理时再做 early exit（Zhu et al., 2025）；TaH 在 oracle policy 下训练、推理时使用学习到的决策（Fu et al., 2025b）。据此推断 TaH2 意在减少 train–inference mismatch，这是合理推断；原文本身只给出三者训练/推理策略的差异描述。

## 4.2 TRAINING WITH LOOKAHEAD DEPTH SUPERVISION

TaH2 jointly trains the backbone, updater and decider, with the decider learning online how many iterations each token should execute. At each iteration, lookahead depth supervision uses the measured change in prediction loss to supervise whether the token should continue (Figure 2, right).



> 💡 **训练信号来源**：TaH2 联合训练 backbone、updater 与 decider，decider 在线学习每个 token 应执行多少轮；每次迭代用预测 loss 的变化量做 lookahead depth supervision，监督 token 是否继续（Figure 2 右侧）。原文未说“不存在 ground-truth 最佳深度”，这是对动机的推断；另外标签并非简单按“loss 显著下降”生成，而是经过 block 18–21 的排序 + 覆盖率阈值流程，草稿此处过度简化。

Online continuation labels. For each supervised token t, let $a _ { t } ^ { ( m ) } = \mathbf { 1 } [ m _ { t } \geq m ]$ indicate whether it actually executes iteration m. For tokens with $a _ { t } ^ { ( m ) } = 1$ and $m < M$ , we measure iteration gains using the per-iteration predictions $\mathbf { q } _ { t } ^ { ( m ) }$ . For target token $y _ { t } ^ { * }$ , the corresponding prediction loss and gain from another iteration are



> 💡 **在线继续标签的定义**：$a_t^{(m)} = \mathbf{1}[m_t \geq m]$ 表示该 token 是否真的执行了第 $m$ 轮；对满足 $a_t^{(m)}=1$ 且 $m \lt M$ 的 token，用逐轮预测 $\mathbf{q}_t^{(m)}$ 度量迭代增益，目标 token 为 $y_t^{*}$。批注：增益度量依赖 per-iteration prediction，而非最终混合输出；“loss 下降即代表更准确/更自信”是隐含假设，原文未直接论证。

$$
\ell _ { t } ^ { ( m ) } = - \log \mathbf { q } _ { t } ^ { ( m ) } [ y _ { t } ^ { \ast } ] , \qquad \delta _ { t } ^ { ( m ) } = \ell _ { t } ^ { ( m ) } - \ell _ { t } ^ { ( m + 1 ) } .\tag{5}
$$



> 💡 **公式批读（Eq. 5）**：$\ell_t^{(m)} = -\log \mathbf{q}_t^{(m)}[y_t^{*}]$ 是该轮对目标 token 的交叉熵；$\delta_t^{(m)} = \ell_t^{(m)} - \ell_t^{(m+1)}$ 是多执行一轮带来的增益。$\delta \gt 0$ 表示下一轮 loss 更低（block 17 明确说正增益倾向继续、负增益倾向停止），$\delta \lt 0$ 表示继续反而变差。

Positive gains favour continuing; negative gains favour stopping. For tokens that stop before $M$ , a no-gradient lookahead supplies the next-iteration loss for supervision.



> 💡 **No-gradient lookahead 的作用**：对在 $M$ 之前停止的 token，用一次 no-gradient lookahead 提供下一轮的 loss 用于监督。批注：这一步让停止的 token 也能算出 $\delta$，同时梯度只从主路径回传；原文只说该 lookahead 用于 supervision 且不带梯度，“确保训练视野与推理一致”属于推断。

Let $c _ { t } ^ { ( m ) } \in \{ 0 , 1 \}$ be the target continue label after iteration $m ,$ with $c _ { t } ^ { ( 0 ) } = 1$ for all supervised tokens. At iteration $m ,$ we rank positive gains among tokens with $a _ { t } ^ { ( m ) } = 1$ and $c _ { t } ^ { ( m - 1 ) } = 1$ and



> 💡 **标签筛选的排序规则**：$c_t^{(m)} \in \{0,1\}$ 是第 $m$ 轮后的目标继续标签，所有被监督 token 满足 $c_t^{(0)}=1$。第 $m$ 轮在满足 $a_t^{(m)}=1$ 且 $c_t^{(m-1)}=1$ 的 token 中给正增益排序，选取最大的增益。注意修正：覆盖率 $\rho$ 约束的是这些增益占总增益的比例，而不是 token 数量的前 $\rho$ 比例，且排序有两个前置条件，草稿遗漏了它们。

select the largest gains until they account for a fraction $\rho$ of the total. The smallest selected gain defines the computed cutoff $\delta _ { \mathrm { c u t } } ^ { ( m ) }$ , giving



> 💡 **cutoff 的定义**：持续选取最大增益，直到其累计量达到总量的 $\rho$ 比例；被选集合中最小的增益定义为 $\delta_{\mathrm{cut}}^{(m)}$。此后只有 $\delta_t^{(m)} \geq \delta_{\mathrm{cut}}^{(m)}$ 才被当作有效继续。批注：$\rho$ 只作用于正增益总量，被排除的是 marginal、可能带噪声的改进（见 block 21）。

$$
c _ { t } ^ { ( m ) } = a _ { t } ^ { ( m ) } c _ { t } ^ { ( m - 1 ) } { \bf 1 } \Big [ \delta _ { t } ^ { ( m ) } \geq \delta _ { \mathrm { c u t } } ^ { ( m ) } \Big ] .\tag{6}
$$



> 💡 **公式批读（Eq. 6）**：$c_t^{(m)} = a_t^{(m)} \cdot c_t^{(m-1)} \cdot \mathbf{1}[\delta_t^{(m)} \geq \delta_{\mathrm{cut}}^{(m)}]$。三个乘性条件分别是：该轮确实执行（$a=1$）、此前未被判定停止（$c^{(m-1)}=1$）、增益不低于当轮动态阈值。由于链式结构，一旦某轮 $c$ 变为 0，后续标签都会保持 0（block 21 明确说明）。

Once a token receives a stop label, its later labels remain zero. These labels supervise the decider, whose decisions determine $a _ { t } ^ { ( m + 1 ) }$ . Coverage $\rho$ retains most of the positive gain while excluding marginal improvements that can produce noisy labels.



> 💡 **标签传播与覆盖率含义**：token 一旦收到 stop label，之后各轮标签恒为 0；这些标签监督 decider，而 decider 的决策又决定下一轮的 $a_t^{(m+1)}$。覆盖率 $\rho$ 保留大部分正增益、排除可能产生噪声标签的 marginal 改进；$\rho=0.99$ 的具体取值在 block 24 给出（此处为提前引用）。

Joint objective. We combine next-token prediction loss with cost-sensitive supervision of the decider. The joint objective is



> 💡 **联合目标构成**：总损失 = next-token prediction loss + 对 decider 的 cost-sensitive 监督项。批注：第一项优化语言建模，第二项只作用于 decider 的继续/停止判断；两项通过 $\alpha_D$ 平衡。

$$
\mathcal { L } _ { \mathrm { S F T } } = \underbrace { \frac { 1 } { N } \sum _ { t } - \log \mathbf { q } _ { t } [ y _ { t } ^ { * } ] } _ { \mathrm { n e x t : t o k e n ~ p r e d i c t i o n ~ l o s s } } + \alpha _ { D } \underbrace { \frac { 1 } { N } \sum _ { m = 1 } ^ { M - 1 } \sum _ { t : a _ { t } ^ { ( m ) } = 1 } w _ { t } ^ { ( m ) } \mathrm { B C E } \Big ( g _ { t } ^ { ( m ) } , c _ { t } ^ { ( m ) } \Big ) } _ { \mathrm { c o s t - s e n s i t i v e ~ d e c i d e r ~ l o s s } } ,\tag{7}
$$



> 💡 **公式批读（Eq. 7）**：$\mathcal{L}_{\mathrm{SFT}} = \frac{1}{N}\sum_t -\log \mathbf{q}_t[y_t^{*}] + \alpha_D \frac{1}{N} \sum_{m=1}^{M-1} \sum_{t: a_t^{(m)}=1} w_t^{(m)} \mathrm{BCE}(g_t^{(m)}, c_t^{(m)})$。第二项只在 $m=1,\dots,M-1$ 且该轮实际执行的 token 上计算。权重 $w_t^{(m)}$ 是 cost-sensitive 的，由 $|\delta_t^{(m)} - \delta_{\mathrm{cut}}^{(m)}|$ 计算（block 24），使偏离 cutoff 越远的样本监督越强。

where N is the number of supervised tokens and $\mathbf { q } _ { t }$ is the stopping-weighted mixture in Equation 4. At iteration m, we supervise the decider on all tokens with $a _ { t } ^ { ( m ) } = 1$ , using cost-sensitive weights $w _ { t } ^ { ( m ) }$ computed from ${ | \delta _ { t } ^ { ( m ) } - \delta _ { \mathrm { c u t } } ^ { ( m ) } }$ | to strengthen supervision farther from the cutoff. We set coverage $\rho = 0 . 9 9$ and the decider-loss coefficient $\alpha _ { D } = 0 . 0 5 ;$ further details are provided in Appendix D.



> 💡 **超参数与实现细节**：$N$ 为被监督 token 数；$\mathbf{q}_t$ 是 Eq. 4 的 stopping-weighted mixture；第 $m$ 轮对所有 $a_t^{(m)}=1$ 的 token 监督 decider；$w_t^{(m)}$ 由 $|\delta_t^{(m)} - \delta_{\mathrm{cut}}^{(m)}|$ 计算，距 cutoff 越远监督越强。取 $\rho = 0.99$、$\alpha_D = 0.05$，更多细节见 Appendix D。批注：“$\alpha_D$ 较小说明辅助任务不主导训练”是解释性推断，原文只给出数值并指向 Appendix D。

