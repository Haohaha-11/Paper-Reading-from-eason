[← 返回 README](../README.md)

## A.4.2 DECODING FLOPS

Compact form. For TaH2, summing over token positions processed during decoding gives



> 💡 **问题动机（AI 批注）**: 本小节推导 TaH2 在解码阶段的 FLOPs。TaH2 的深度是按 token 动态决定的，无法用一个固定常数表示，因此必须写成对所有解码 token 位置、以及每个 token 实际执行的迭代次数 $m_t$ 的求和。原文这句只是给出“紧凑形式”的入口，真正的成本结构由 Eq. 14 与 Eq. 16 承担。

$$
\begin{array} { r l r } { \mathrm { F L O P s } _ { \mathrm { d e c } } = \displaystyle \sum _ { t } \Big [ \displaystyle \sum _ { m = 1 } ^ { m _ { t } } \mathrm { F L O P s } _ { \mathrm { p a s s } } ( t , m ) + ( m _ { t } - 1 ) \mathrm { F L O P s } _ { \mathrm { u p d } } } & { } & \\ { + \displaystyle \operatorname* { m i n } ( m _ { t } , M - 1 ) \mathrm { F L O P s } _ { \mathrm { d e c i d e r } } \Big ] , } \end{array}\tag{14}
$$



> 💡 **公式批读（Eq. 14，AI 批注）**: 解码 FLOPs 的紧凑求和形式，三项分别对应：
> - $\sum_{m=1}^{m_t}\text{FLOPs}_\text{pass}(t,m)$：该 token 实际执行的每一次前向 pass（含 backbone、attention、LM head）的开销；
> - $(m_t-1)\,\text{FLOPs}_\text{upd}$：Updater 只需为“第一次之后的每次迭代”做准备，共 $m_t-1$ 次；
> - $\min(m_t, M-1)\,\text{FLOPs}_\text{decider}$：Decider 在“最后一次被允许的迭代”（深度 $M$）之后不再调用。因此当 $m_t \lt M$（提前停止）时，Decider 在每次迭代后都被调用，共 $m_t$ 次——正是这次调用决定了停止；只有 $m_t = M$ 时才只调用 $M-1$ 次，两者统一写成 $\min(m_t, M-1)$。
> ⚠️ 修正初稿：初稿写“达到最大深度或提前停止时，最后一次迭代后不再调用 Decider”，对提前停止的情形不成立，已按原文“the decider is not called after the final permitted iteration”改写。

where $\mathrm { F L O P s } _ { \mathrm { p a s s } } ( t , m )$ includes the backbone, attention and LM head. The updater prepares each iteration after the first, and the decider is not called after the final permitted iteration.



> 💡 **机制拆解（AI 批注）**: 正文逐项交代 Eq. 14 的物理含义：(1) $\text{FLOPs}_\text{pass}(t,m)$ 覆盖 backbone、attention 与 LM head 三部分；(2) Updater 只服务于“第一次之后的每次迭代”；(3) Decider 不在最后一次被允许的迭代之后调用。
> 关键区分：$(m_t-1)$ 是 **Updater** 的系数，而 **Decider** 的系数是 $\min(m_t,M-1)$，两者不是同一个逻辑。初稿把这两件事并到一句里说“这解释了为什么系数是 $m_t-1$ 或类似逻辑”，已拆分修正。

Positions and visible keys. Consider one response with a P-token prompt. Its first output token is sampled from the prefill, which we exclude, so decoding processes positions $t = 1 , \ldots , N$ , where N is the number of output tokens minus one. Prompt position $j$ keeps KV streams for $r _ { j }$ iterations. Under extended duo-causal attention, a query at position t and depth m sees every KV entry from earlier positions at depths up to m, together with its own entries from depths 1 to m:



> 💡 **定义与可见性规则（AI 批注）**:
> - $P$：prompt 长度；$N$：解码处理的位置数，正文明确 $N = $ 输出 token 数 $- 1$，因为第一个输出 token 由 prefill 采样得到、不计入解码；
> - prompt 位置 $j$ 的 KV 只保留 $r_j$ 次迭代；
> - extended duo-causal attention 下，位置 $t$、深度 $m$ 的 query 可见：(1) 更早 prompt 位置在深度不超过 $m$ 的 KV，(2) 更早生成位置 $u \lt t$ 在深度不超过 $m$ 的 KV，(3) 自身位置在深度 $1 \sim m$ 的 KV。
> 这条规则是 Eq. 15 中 $S_t^{(m)}$ 的直接来源，也是 TaH2 与固定深度基线在注意力成本上的主要结构差别。
> ⚠️ 修正初稿：初稿只写“排除 prefill、从第 1 个生成 token 算起”，未给出 $N = $ 输出 token 数 $-1$ 这一原文明写的定量关系，已补上。

$$
S _ { t } ^ { ( m ) } = \sum _ { j = 1 } ^ { P } \operatorname* { m i n } ( r _ { j } , m ) + \sum _ { u < t } \operatorname* { m i n } ( m _ { u } , m ) + m .\tag{15}
$$



> 💡 **公式批读（Eq. 15，AI 批注）**: 可见 KV 条目数 $S_t^{(m)}$：
> - $\sum_{j=1}^{P}\min(r_j, m)$：prompt 端，位置 $j$ 只保留 $r_j$ 次迭代，最多贡献 $\min(r_j,m)$ 条；
> - $\sum_{u \lt t}\min(m_u, m)$：历史生成 token $u$ 已缓存 $m_u$ 条，最多贡献 $\min(m_u,m)$ 条；
> - $+m$：当前 token 自身在深度 $1\sim m$ 的 $m$ 条 KV。
> 该式把“动态深度”翻译成一个可计算的注意力规模变量，它是 Eq. 16 中注意力项 $4Ld\sum_m S_t^{(m)}$ 的输入。（引文与公式本体保持原样，此处注释中的 $u \lt t$ 按 GitHub MathJax 规范写法处理。）

Expanded form. Each pass applies the LM head, whose per-depth predictions feed the decider and the stopping-weighted mixture, so $\mathrm { F L O P s } _ { \mathrm { p a s s } } ( t , \hat { m _ { \it } } ) = \mathrm { \dot { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } + }$ $\mathrm { F L O P s } _ { \mathrm { a t t n } } ( S _ { t } ^ { ( m ) } )$ . Substituting into Equation 14 gives



> 💡 **从 Eq. 15 到 Eq. 16（AI 批注）**:
> - 原文明确“每个 pass 都要过 LM head，其逐深度预测要喂给 decider 和 stopping-weighted mixture”，这解释了为什么 TaH2 的 head 成本带 $m_t$ 因子，而 Ouro/Huginn 只有一次（见 block 11 的对照）；
> - 原文给出分解 $\text{FLOPs}_\text{pass}(t,m) = \text{FLOPs}_\text{bb} + \text{FLOPs}_\text{head} + \text{FLOPs}_\text{attn}(S_t^{(m)})$；
> - 原文此处下标写作 $\hat{m_t}$（解析损坏），从上下文看 pass 的成本只通过 $S_t^{(m)}$ 依赖深度，应读作 $m$。
> ⚠️ 初稿补写的“$\text{FLOPs}_\text{attn} \approx 4Ld\,S_t^{(m)}$”：本段原文并未给出该系数，$4Ld$ 是初稿由 Eq. 16 反推的**推断**（$L$ 层数、$d$ 隐藏维度），引用时须注明来源是 Eq. 16 而非本段。

$$
\begin{array} { l } { \mathrm { F L O P s } _ { \mathrm { d e c } } ^ { \mathrm { T a H 2 } } = \displaystyle \sum _ { t = 1 } ^ { N } \left[ m _ { t } \big ( \mathrm { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } \big ) + 4 L d \sum _ { m = 1 } ^ { m _ { t } } S _ { t } ^ { ( m ) } \qquad } \\ { \qquad + ( m _ { t } - 1 ) \mathrm { F L O P s } _ { \mathrm { u p d } } + \operatorname* { m i n } ( m _ { t } , M - 1 ) \mathrm { F L O P s } _ { \mathrm { d e c i d e r } } \right] . } \end{array}\tag{16}
$$



> 💡 **公式批读（Eq. 16，AI 批注）**: TaH2 解码 FLOPs 的展开式，把 Eq. 14 中的 $\text{FLOPs}_\text{pass}$ 代入后得到：
> - $m_t(\text{FLOPs}_\text{bb}+\text{FLOPs}_\text{head})$：$\sum_{m=1}^{m_t}$ 只作用于常量部分，故化为乘 $m_t$；
> - $4Ld\sum_{m=1}^{m_t}S_t^{(m)}$：注意力项保留内层求和，因为可见 KV 数 $S_t^{(m)}$ 随深度 $m$ 变化，不能像 backbone 那样约成乘法；
> - $(m_t-1)\text{FLOPs}_\text{upd}$ 与 $\min(m_t,M-1)\text{FLOPs}_\text{decider}$ 原样保留。
> ⚠️ 初稿说“每一层的注意力都要重新计算一次”，措辞含糊：$L$ 在这里只是层数系数，真正随深度变化的是每个位置、每个深度各自的 $S_t^{(m)}$，已改为“每个深度按当时的可见 KV 数各自累加注意力开销”。

Baselines. Fixed-depth models execute all M iterations at every position. Under causal attention, earlier positions expose only their base stream, so a query at depth m attends to $P + t - 1 + m \mathrm { k e y s } ;$ when every iteration keeps its own KV stream, it attends to $\dot { P } + t$ keys. TaH2-fixed uses causal attention and applies the LM head and updater as TaH2 does. Ouro and Huginn keep one KV stream per iteration and apply the LM head only after the final iteration; Huginn executes $7 + 1 4 M + 7$ of the 28 layers and its input adapter once per core iteration. This gives



> 💡 **基线设定拆解（AI 批注）**:
> - 固定深度模型在每个位置都跑满 $M$ 次迭代；
> - 因果注意力下，更早位置只暴露 base stream，于是深度 $m$ 的 query 看到 $P + t - 1 + m$ 个 key：$P$ 个 prompt base KV、$t-1$ 个历史生成位置的 base KV、以及自身累积的 $m$ 条 KV——这正对应 TaH2-fixed 的注意力项（Eq. 18 内层 $\sum_{m=1}^{M}(P+t-1+m)$）；
> - 而“每次迭代各保留一份 KV stream”的模型看到 $P+t$ 个 key，对应 Ouro/Huginn（Eq. 19 的 $M(P+t)$）；
> - TaH2-fixed 用因果注意力，但 LM head 与 updater 与 TaH2 相同；Ouro 与 Huginn 只在一系列迭代的最后才过 LM head；
> - Huginn 在 28 层中执行 $7+14M+7$ 层，且其 input adapter 每进行一次 core iteration 执行一次。
> ⚠️ 修正初稿：初稿称 TaH2-fixed 处的说明“略有歧义”，其实原文与 Eq. 18 一致、无歧义；初稿把 Huginn 表述为“层跳过（layer skipping）或适配器复用”，原文给的是明确的 $7+14M+7$ / 28 层执行比例，应保留具体数字。另原文该处 $\dot{P}+t$ 的 dot 为解析残迹，应读作 $P+t$。

(17)



> 💡 **为什么只剩编号“(17)”（AI 批注，待证）**: 该块只残留公式编号，公式本体未出现在解析文本中。从上下文位置（位于 baseline 说明段之后、Eq. 18 之前）**推断**它应是第一条 baseline 公式，但具体内容无法从现有材料判定，故标记为“解析缺失 / 内容待证”，不做内容补写。
> ⚠️ 修正初稿：初稿写成“编号错位或纯文本提取错误”，是对成因的确定性猜测，缺乏依据；只能说该编号对应的公式在此解析结果中缺失。

$$
\begin{array} { r l } & { \displaystyle \mathrm { F L O P s } _ { \mathrm { d e c } } ^ { \mathrm { S t a n d a r d } } = \sum _ { t = 1 } ^ { N } \big [ \mathrm { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } + 4 L d \left( P + t \right) \big ] , } \\ & { \displaystyle \mathrm { F L O P s } _ { \mathrm { d e c } } ^ { \mathrm { T a H 2 - f i x e d } } = \sum _ { t = 1 } ^ { N } \Big [ M \big ( \mathrm { F L O P s } _ { \mathrm { b b } } + \mathrm { F L O P s } _ { \mathrm { h e a d } } \big ) + ( M - 1 ) \mathrm { F L O P s } _ { \mathrm { u p d } } } \\ & { \qquad + 4 L d \sum _ { m = 1 } ^ { M } ( P + t - 1 + m ) \Big ] , } \end{array}\tag{18}
$$



> 💡 **公式批读（Eq. 18，AI 批注）**:
> - Standard 项 $\sum_t[\text{FLOPs}_\text{bb}+\text{FLOPs}_\text{head}+4Ld(P+t)]$：每个 token 只按一次 backbone+head 计费、注意力只涉及 $P+t$ 个 key。原文只写了“Fixed-depth models execute all M iterations at every position”，并未逐字称 Standard 为“单深度标准解码”，该对应关系是**由公式无 $M$ 因子反推的推断**，需标明。
> - TaH2-fixed 项：$M(\text{FLOPs}_\text{bb}+\text{FLOPs}_\text{head})+(M-1)\text{FLOPs}_\text{upd}+4Ld\sum_{m=1}^{M}(P+t-1+m)$。内层求和来自因果注意力下每个深度的可见 key 数不同：prompt 的 $P$ 条与历史位置的 $t-1$ 条 base KV 固定，自身累积条数随 $m$ 增长，故按 $m=1\ldots M$ 逐项累加。
> ⚠️ 修正初稿：初稿说 $(P+t-1+m)$“暗示看到前 $m-1$ 层的额外 KV 或某种累积窗口”，方向接近但表述不确定；按原文应是“当前位置自身累积的 $m$ 条 KV”，并且历史位置只计 base stream。

$$
\mathrm { F L O P s _ { d e c } ^ { O u r o } } = \sum _ { t = 1 } ^ { N } \left[ M \mathrm { F L O P s _ { b b } } + \mathrm { F L O P s _ { h e a d } } + 4 L d M ( P + t ) \right] ,\tag{19}
$$



> 💡 **公式批读（Eq. 19，AI 批注）**:
> - $M\,\text{FLOPs}_\text{bb}+\text{FLOPs}_\text{head}$：backbone 跑 $M$ 次，而 LM head 只在最后一次迭代后执行一次，与原文“Ouro and Huginn … apply the LM head only after the final iteration”一致；
> - $4LdM(P+t)$：每次迭代各保留一份 KV stream，故每次迭代可见 $P+t$ 个 key，$M$ 次迭代合计 $M(P+t)$。
> ⚠️ 修正初稿：初稿把它解释为“每层都看完整上下文、层间独立”，混淆了 $M$（迭代次数）与 $L$（层数）；正确来源是“每次迭代各保留一份 KV stream ⇒ 可见 key 数为 $P+t$”。
> ✅ 初稿遗留的疑问“TaH2 是否每次 pass 都算 head”已由原文解答：block 6 明写“Each pass applies the LM head…”，因此 TaH2 的 head 成本带 $m_t$ 因子，而 Ouro 只算一次。

$$
\begin{array} { r l r } { \mathrm { F L O P s } _ { \mathrm { d e c } } ^ { \mathrm { H u g i n n } } = \displaystyle \sum _ { t = 1 } ^ { N } \Big [ \frac { 1 4 + 1 4 M } { 2 8 } \big ( \mathrm { F L O P s } _ { \mathrm { b b } } + 4 L d ( P + t ) \big ) } & { } & \\ { + \mathrm { F L O P s } _ { \mathrm { h e a d } } + 2 M d ^ { 2 } \Big ] . } \end{array}\tag{20}
$$



> 💡 **公式批读（Eq. 20，AI 批注）**:
> - 缩放因子 $\frac{14+14M}{28}$ 有原文支撑：Huginn 在 28 层中执行 $7+14M+7$ 层，而 $7+14M+7 = 14+14M$，即 7 层前导 + 14 层核心重复 $M$ 次 + 7 层收尾，故 backbone 与注意力按该比例缩放；
> - $\text{FLOPs}_\text{head}$ 只出现一次，与“最后才过 LM head”一致；
> - $+2Md^2$：对应原文所说“its input adapter once per core iteration”，即 $M$ 次 core iteration 各一次 adapter 开销，因此 $2Md^2 = M \times 2d^2$。但原文并未给出单次 adapter 为 $2d^2$ 的定义，这一逐项对应属于**推断**。
> ⚠️ 修正初稿：初稿写“原文提到 Huginn 执行 7+14M+7 中的某些层”，原文实为“executes $7+14M+7$ of the 28 layers”，28 是明写的总层数；初稿的“层稀疏化或共享”过于笼统，已按前导/核心/收尾三段式层结构表述。

