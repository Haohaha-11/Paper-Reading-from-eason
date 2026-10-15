[← 返回 README](../README.md)

## A.4.1 PRELIMINARIES

Conventions. We count model matrix-multiplication FLOPs, with a multiply-add as two operations. Elementwise operations (normalization, activations, softmax, top-k selection and the stoppingweighted mixture), sampling, memory traffic and serving overhead are excluded. A linear map from a to b features therefore costs 2ab FLOPs per token.



> 💡 **计算约定与范围界定**：把成本口径限定为矩阵乘法 FLOPs（一次乘加记 2 次运算），因此 $a \to b$ 线性层为每 token $2ab$ FLOPs。原文明确排除的项包含逐元素运算（归一化、激活、softmax、top-k 选择与 stopping-weighted mixture）、采样、内存流量与服务开销——初稿只写到「归一化、激活、采样及内存开销」，遗漏 softmax/top-k/stopping mixture 与服务开销，已补全。另外，「避免不同论文因包含非核心操作导致比较偏差」是初稿自行推出的动机，原文只讲口径本身，未讨论跨论文可比性，此处标注为推断。

Notation. The L-layer backbone has hidden width $d ,$ total key–value width $d _ { \mathrm { k } } ,$ and MLP width $d _ { \mathrm { f f } } .$ and V is the vocabulary size. For the Qwen3 models used here, the query width $n _ { \mathrm { h } } d _ { \mathrm { h e a d } }$ equals d. The TaH2 updater and decider have MLP widths $d _ { \mathrm { u } }$ and $d _ { \mathrm { d } } .$ , and the decider receives the k largest prediction probabilities (Appendix A.2.1). Token t executes $m _ { t } \le M$ iterations.



> 💡 **符号定义体系**：$L$ 层骨干的隐藏宽度 $d$、KV 总宽度 $d_{\mathrm{kv}}$、MLP 宽度 $d_{\mathrm{ff}}$ 与词表大小 $V$；Qwen3 的查询宽度 $n_{\mathrm{h}} d_{\mathrm{head}}$ 等于 $d$（这是后文 Q/K/V 投影项写成 $4d(d+d_{\mathrm{kv}})$ 的前提）；TaH2 updater/decider 的 MLP 宽度为 $d_{\mathrm{u}}$、$d_{\mathrm{d}}$，decider 接收 $k$ 个最大预测概率（原文指向 Appendix A.2.1）；token $t$ 执行 $m_t \le M$ 次迭代。注意：本节只给了迭代上限 $M$，未给 $M$ 的取值，也不含平均迭代次数，因此不能用本节单独估算整段生成的总成本。

Per-call costs. One backbone pass, one LM-head call, and one call of each TaH2 module cost



> 💡 **过渡句**：把上一段的符号约定落到可计量的三类调用上——骨干前向一次、LM head 一次、TaH2 两个模块各一次，紧接 Eq. 13 给出各自表达式。关键限定词是「one ... pass / one call」，即这里都是单次调用成本，不是一条序列或一次完整推理的总成本；总成本还需乘上 token 数、每 token 迭代数 $m_t$ 以及注意力可见 key 数 $S$。

$$
\begin{array} { r l r } & { \mathrm { F L O P s } _ { \mathrm { b b } } = L \big [ 4 d ( d + d _ { \mathrm { k v } } ) + 6 d d _ { \mathrm { f f } } \big ] , } & { \mathrm { F L O P s } _ { \mathrm { b e a d } } = 2 d V , } \\ & { \mathrm { F L O P s } _ { \mathrm { u p d } } = 4 d ^ { 2 } + 6 d d _ { \mathrm { u } } , } & { \mathrm { F L O P s } _ { \mathrm { d e c i d e r } } = 2 d ( 2 d + k ) + 6 d d _ { \mathrm { d } } + 2 d . } \end{array}\tag{13}
$$



> 💡 **Eq. 13 逐项核对**：
> - $\mathrm{FLOPs}_{\mathrm{bb}} = L[4d(d+d_{\mathrm{kv}}) + 6dd_{\mathrm{ff}}]$：前一项对应 Q/K/V/O 四个投影，后一项对应三个 SwiGLU 矩阵；
> - $\mathrm{FLOPs}_{\mathrm{head}} = 2dV$：输出层映射到词表（原文此处 OCR 作 $\mathrm{FLOPs}_{\mathrm{bead}}$，据上下文与 Table 9 的「LM head」行判定应读作 head）；
> - $\mathrm{FLOPs}_{\mathrm{upd}} = 4d^2 + 6dd_{\mathrm{u}}$；
> - $\mathrm{FLOPs}_{\mathrm{decider}} = 2d(2d+k) + 6dd_{\mathrm{d}} + 2d$，其中 $k$ 来自 decider 额外读取的 top-$k$ 概率。
> 初稿对 $4d(d+d_{\mathrm{kv}})$ 写下「似乎合并了 Q 和 KV 的计算逻辑或假设特定架构」的猜测，并自问 $d_{\mathrm{k}}$ 是否应为 $d_{\mathrm{kv}}$；这两点在第 2 段（$d_{\mathrm{kv}}$ 即 KV 总宽度）与第 5 段（明确说明覆盖 query/key/value/output 投影）中已有答案，故删除猜测保留结论。

In $\mathrm { F L O P s } _ { \mathrm { b b } } , 4 d ( d + d _ { \mathrm { k v } } )$ covers the query, key, value and output projections and $6 d d _ { \mathrm { f f } }$ the three SwiGLU matrices. The updater applies $\mathbf { W } _ { \mathrm { u } } ( 2 d  d )$ and $\scriptstyle B _ { \mathrm { u } }$ , and the decider applies ${ \bf W } _ { \textrm { d } } ( 2 d + k $ $d ) , B _ { \mathrm { d } }$ and ${ \bf W } _ { \mathrm { s c o r e } } ( d \to \bar { 1 ) }$ . Attention cost depends on context length: a query that attends to S keys adds $\operatorname { F L O P s } _ { \mathrm { a t t n } } ( S ) = 4 L d S$ for the $Q K ^ { \top }$ and attention-weighted value products. Table 9 lists all per-call costs for the 1.7B model; the backbone pass dominates, and the updater and decider each cost less than 2% of it.



> 💡 **公式项的物理对应**：$4d(d+d_{\mathrm{kv}})$ 覆盖 query/key/value/output 四个投影，$6dd_{\mathrm{ff}}$ 覆盖三个 SwiGLU 矩阵；updater 施加 $\mathbf{W}_{\mathrm{u}}(2d \to d)$ 与 $B_{\mathrm{u}}$；decider 施加 $\mathbf{W}_{\mathrm{d}}((2d+k) \to d)$、$B_{\mathrm{d}}$ 与 $\mathbf{W}_{\mathrm{score}}(d \to 1)$。注意力不进上述固定成本，而是随可见 key 数变化：$\mathrm{FLOPs}_{\mathrm{attn}}(S) = 4LdS$，覆盖 $QK^\top$ 与 attention-weighted value 两次乘积——这意味着长上下文下注意力项持续增长，总量级的相对关系并非永远由骨干主导。最后一句给出「骨干占主导、updater 与 decider 各自不到其 2%」；用 Table 9 数值复核：0.042/2.819≈1.5%、0.050/2.819≈1.8%，均小于 2%，支持该句。

Table 9: Per-call matrix-multiplication FLOPs for Qwen3-1.7B $( L = 2 8 , d = 2 0 4 8 , d _ { \mathrm { k v } } = 1 0 2 4 ,$ $d _ { \mathrm { f f } } = 6 1 4 4 , V = 1 5 1 , 9 3 6 , k = d _ { \mathrm { u } } = d _ { \mathrm { d } } = 2 0 4 8 )$
<table><tr><td>Component</td><td>FLOPs per call</td><td>1.7B (GFLOPs)</td></tr><tr><td>Backbone pass</td><td> $L [ 4 d ( d + d _ { \mathbf { k } \mathbf { v } } ) + 6 d d _ { \mathbf { f } } ]$ </td><td>2.819</td></tr><tr><td>LM head</td><td> $2 d V$ </td><td>0.622</td></tr><tr><td>Attention, per visible key</td><td> $4 L d$ </td><td> $2 . 2 9 \times 1 0 ^ { - 4 }$ </td></tr><tr><td>TaH2 updater</td><td> $4 d ^ { 2 } + 6 d d _ { \mathrm { u } }$ </td><td>0.042</td></tr><tr><td>TaH2 decider</td><td> $2 d ( 2 d + k ) + 6 d d _ { \mathrm { d } } + 2 d$ </td><td>0.050</td></tr><tr><td>Huginn input adapter</td><td> $2 d ^ { 2 }$ </td><td>0.008</td></tr></table>



> 💡 **Table 9 数据核验**（代入 $L=28, d=2048, d_{\mathrm{kv}}=1024, d_{\mathrm{ff}}=6144, V=151936, k=d_{\mathrm{u}}=d_{\mathrm{d}}=2048$）：Backbone pass 2.819 GFLOPs、LM head 0.622 GFLOPs、每可见 key 的注意力 $2.29\times10^{-4}$ GFLOPs、TaH2 updater 0.042、TaH2 decider 0.050、Huginn input adapter 0.008。数量关系与 Eq. 13 一致：backbone 约为 LM head 的 4.5 倍，updater 与 decider 各自约为 backbone 的 1.5%/1.8%。需注意三点：(1) 表中均为单次调用成本，不含迭代数与序列长度；(2) 按本节约定不含逐元素、采样、内存与服务开销，故不能直接换算成端到端延迟；(3) 初稿「TaH2 模块成本不足 Backbone 的 2%」表述不精确——原文是每个模块各不到 2%，两者相加约 3.3%。

