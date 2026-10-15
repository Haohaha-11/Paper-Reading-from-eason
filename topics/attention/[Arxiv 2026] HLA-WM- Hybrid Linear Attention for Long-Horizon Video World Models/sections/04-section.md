[← 返回 README](../README.md)

## 4 Method

## 4.1 Overview

We propose HLA-WM, a hybrid linear-attention framework for long-horizon video world models. The key idea is to replace the single accumulated GDN memory with a collection of independently addressable chunk-wise memories. Specifically, we partition the generated sequence into temporal chunks and summarize the GDN transition induced by each chunk. For each query chunk, HLA-WM uses camera geometry to retrieve a sparse set of scene-relevant historical chunks and selectively composes their summaries into the recurrent state used for generation. As illustrated in Fig. 3, this design combines coarse-grained geometry-guided memory retrieval with recurrent linear attention, allowing distant but relevant information to bypass unrelated intermediate updates while preserving the compactness of linear-state memory.



> 💡 **问题动机**: 原文的出发点是把 “the single accumulated GDN memory” 替换为 “a collection of independently addressable chunk-wise memories”，从而让 “distant but relevant information” 绕过无关的中间更新。原文给出的收益是 “bypass unrelated intermediate updates while preserving the compactness of linear-state memory”；它并未声称长序列信息会被“遗忘”，故“遗忘”属批注推断而非原文结论。
> **机制拆解**: HLA-WM 是 hybrid linear-attention 框架，流程四步：(1) 把生成序列切分为 temporal chunks；(2) 为每个 chunk 的 GDN 转移提炼摘要 $(A_i,B_i)$；(3) 对每个 query chunk 用 camera geometry 检索稀疏的 scene-relevant 历史 chunk；(4) 选择性组合这些摘要，得到生成所用的 recurrent state。对应 Fig. 3，整体是“粗粒度几何检索 + 递归线性注意力”的混合设计。

## 4.2 Chunk-wise GDN Memory

Gated DeltaNet maintains a recurrent linear state through token-wise updates [15]. Let t index tokens in the recurrent scan order. For token $t ,$ the state update is



> 💡 **公式背景**: 回顾 Gated DeltaNet 的 token-wise 递归状态更新，$t$ 按 “the recurrent scan order” 索引 token。这是后续把 token 级更新压缩成 chunk 级仿射转移的起点，本身没有引入新机制。

$$
S _ { t } = \gamma _ { t } S _ { t - 1 } + \beta _ { t } \left( v _ { t } - S _ { t - 1 } k _ { t } \right) k _ { t } ^ { \top } ,\tag{4}
$$



> 💡 **公式批读**: Eq. 4 是标准 GDN 更新 $S_t = \gamma_t S_{t-1} + \beta_t (v_t - S_{t-1}k_t)k_t^\top$。$k_t$、$v_t$ 是当前 token 表示投影出的 key/value；$\gamma_t$ 控制 memory decay，$\beta_t$ 控制 delta update 的强度（这两点由紧随其后的 block 6 正文给出，不在公式本身）。结构上，状态更新 = 旧状态衰减 + 用预测误差 $(v_t - S_{t-1}k_t)$ 做修正。

where $k _ { t }$ and $v _ { t }$ are the key and value vectors projected from the current token representation, γ<sub>t</sub> controls memory decay, and $\beta _ { t }$ controls the strength of the delta update. Rearranging Eq. 4 gives



> 💡 **机制转换**: 原文此处只说 “Rearranging Eq. 4 gives”，即把 Eq. 4 重新整理成仿射形式 $S_t = S_{t-1}A_t + B_t$。初稿写成“为了便于 chunk 级并行和重组”，这个动机在原文此处并未声明，属批注推断；可靠的说法是：该重写的直接作用是让 token 级更新具备可组合的仿射结构，而“便于 chunk 化”是后文的推论。

$$
S _ { t } = S _ { t - 1 } A _ { t } + B _ { t } ,\tag{5}
$$



> 💡 **公式批读**: Eq. 5 给出单步仿射更新 $S_t = S_{t-1}A_t + B_t$：$A_t$ 乘在旧状态上、$B_t$ 是与旧状态无关的写入项。用后文 block 17 的措辞可对应理解为“$A$ 描述如何变换已有记忆、$B$ 描述本步写入的信息”，但该措辞原文是针对 chunk 级写的，把它直接挪到 token 级属批注类比。

where



> 💡 **定义引入**: “where” 引出 Eq. 5 中 $A_t$、$B_t$ 的具体定义，即下一条 Eq. 6。

$$
A _ { t } = \gamma _ { t } I - \beta _ { t } k _ { t } k _ { t } ^ { \top } , \qquad B _ { t } = \beta _ { t } v _ { t } k _ { t } ^ { \top } .\tag{6}
$$



> 💡 **公式批读**: Eq. 6 给出 $A_t = \gamma_t I - \beta_t k_t k_t^\top$、$B_t = \beta_t v_t k_t^\top$。$A_t$ 是单位矩阵减去一个秩-1 项（未归一化的 $k_t k_t^\top$），$B_t$ 是 value 与 key 的外积。此处不宜像初稿那样类比 Householder 变换：标准 Householder 为 $I - 2vv^\top$，要求 $v$ 为单位向量且结果正交，而这里既无归一化保证也无正交性，并且还叠加了 $\gamma_t$ 的缩放。稳妥表述是“带衰减因子的秩-1 修正”。

This afine form can be directly composed over consecutive tokens. Consider chunk i containing



> 💡 **逻辑推导**: 指出该仿射形式可以 “be directly composed over consecutive tokens”，即连续 token 的更新能够合并，这是后面把整段 chunk 压成单个 $(A_i, B_i)$ 的前提。

L tokens, and let $( i , \ell )$ denote its ℓ-th token. Unrolling the recurrence within the chunk gives



> 💡 **符号说明**: 定义 chunk $i$ 含 $L$ 个 token，$(i, \ell)$ 表示其第 $\ell$ 个 token，为在 chunk 内展开 recurrence 做符号准备。

$$
S _ { i } = S _ { i - 1 } A _ { i , 1 } A _ { i , 2 } \cdot \cdot \cdot A _ { i , L } + B _ { i , 1 } A _ { i , 2 } \cdot \cdot \cdot A _ { i , L } + \cdot \cdot \cdot + B _ { i , L } .
$$



> 💡 **推导过程**: 在 chunk 内 unroll recurrence：$S_i$ 等于 $S_{i-1}$ 乘上所有 $A$ 的连乘，再加上每个 $B$ 乘以其之后所有 $A$ 的连乘。前面的 $A$ 因子按扫描顺序排列，越早的写入项被越多的后续 $A$ 变换。

Therefore, the entire chunk can be represented by a single afine transition



> 💡 **结论陈述**: 由上述展开可知整段 chunk 可写成单个仿射转移 $S_i = S_{i-1}A_i + B_i$。存储上从“每 token 的状态”变为“每 chunk 一对矩阵”，这是本文用 $(A_i, B_i)$ 做记忆单元的数学依据。

$$
S _ { i } = S _ { i - 1 } A _ { i } + B _ { i } ,\tag{7}
$$



> 💡 **公式批读**: Eq. 7 是 chunk 级别的更新式 $S_i = S_{i-1}A_i + B_i$，形式与 Eq. 5 相同，但 $A_i$、$B_i$ 已聚合整个 chunk 的效果；下一式给出它们的构成。

where



> 💡 **定义补充**: “where” 引出 chunk 级 $A_i$、$B_i$ 由 token 级量构成的方式，即 Eq. 8。

$$
A _ { i } = A _ { i , 1 } A _ { i , 2 } \cdot \cdot \cdot A _ { i , L } , \qquad B _ { i } = \sum _ { \ell = 1 } ^ { L } B _ { i , \ell } \prod _ { u = \ell + 1 } ^ { L } A _ { i , u } .\tag{8}
$$



> 💡 **公式批读**: Eq. 8 给出 $A_i = A_{i,1}A_{i,2}\cdots A_{i,L}$ 以及 $B_i = \sum_{\ell=1}^{L} B_{i,\ell}\prod_{u=\ell+1}^{L} A_{i,u}$。注意 $B_i$ 的求和与连乘索引是 $\ell$、$u$（初稿写成 $j$ 与原文不一致，已按原文更正）；乘积顺序遵循 “the recurrent scan order”（block 17 明确说明）。

Here, the products follow the recurrent scan order, with an empty product defined as the identity. The pair $( A _ { i } , B _ { i } )$ therefore provides a suficient summary of chunk i: $A _ { i }$ describes how the chunk transforms existing memory, while $B _ { i }$ represents the information written by the chunk itself.



> 💡 **语义解释**: 原文把 $(A_i, B_i)$ 称为 chunk $i$ 的 “suficient summary”（原文拼写如此，应为 sufficient）：$A_i$ 描述该 chunk 如何变换已有记忆，$B_i$ 描述该 chunk 自身写入的信息。空乘积被定义为 identity。这为“不必保留原始视觉 token 就能复原状态影响”提供依据——注意“只保留这对矩阵”是本文摘要机制的必然含义，但原文此处并未展开讨论存储开销。

Equivalently, their summaries follow the composition rule



> 💡 **关键性质**: 引出两个摘要之间的组合规则，这是检索到非连续 chunk 后仍能快速重组状态的前提。

$$
( A _ { i } , B _ { i } ) \star ( A _ { j } , B _ { j } ) = ( A _ { i } A _ { j } , B _ { i } A _ { j } + B _ { j } ) .\tag{9}
$$



> 💡 **公式批读**: Eq. 9 定义 $(A_i,B_i)\star(A_j,B_j) = (A_iA_j,\; B_iA_j + B_j)$。按顺序验证：先施加 $i$ 得 $S' = SA_i + B_i$，再施加 $j$ 得 $S'' = S'A_j + B_j = S(A_iA_j) + (B_iA_j + B_j)$，与公式一致，因此“先 $i$ 后 $j$”的解读正确。

The operation is associative, enabling an arbitrary subset of historical chunk summaries to be eficiently recomposed without replaying their original visual tokens through the model.



> 💡 **优势分析**: 该操作满足结合律，因此 “an arbitrary subset of historical chunk summaries” 可以按顺序重新组合，而不必把原始视觉 token 再喂给模型。这正对应本文“按需检索历史 chunk”的设计目标：记忆访问从“全量重放”变为“摘要重组”。

## 4.3 Geometry-Guided Memory Retrieval

Given the individually addressable chunk-wise memories, we retrieve historical chunks according to their geometric relevance to the current view. For each query chunk q and an eligible historical chunk i, we estimate their overlap using only camera poses, intrinsics, and a scene-level mediandepth estimate. For a sampled image-plane location $( u , v )$ , we construct the camera-space ray



> 💡 **模块功能**: 几何引导检索的输入是 query chunk $q$ 与 eligible historical chunk $i$；只用 camera poses、intrinsics 和 scene-level median-depth estimate 来估计重叠。原文明确的两点优势是 “requires neither generated RGB content nor learned semantic features”（该句在 block 29），初稿把“保证鲁棒性”写在这里属额外推断，原文并未在 block 22 声称鲁棒性。

$$
\mathbf { r } ( u , v ) = \left[ \frac { u - c _ { x } } { f _ { x } } , \frac { v - c _ { y } } { f _ { y } } , 1 \right] ^ { \top } ,\tag{10}
$$



> 💡 **公式批读**: Eq. 10 用像素坐标 $(u,v)$、主点 $(c_x,c_y)$、焦距 $(f_x,f_y)$ 构造相机坐标系下的射线方向 $\mathbf{r}(u,v) = [(u-c_x)/f_x,\,(v-c_y)/f_y,\,1]^\top$。第三分量固定为 1，说明这是未归一化的方向向量，深度由后续的 proxy depth 提供，而不是由该向量本身携带。

and instantiate proxy 3D points at several depths around the scene-level median depth,



> 💡 **策略说明**: 原文说要 “instantiate proxy 3D points at several depths around the scene-level median depth”，即用几个围绕中值深度的代理点近似 3D 位置。初稿写“由于缺乏精确深度图”，这是对动机的推断，原文此处只说明做法（使用 scene-level median-depth estimate），并未陈述“缺乏深度图”这一前提。

$$
\mathcal { D } = \{ 0 . 5 d _ { \mathrm { m e d } } , d _ { \mathrm { m e d } } , 1 . 5 d _ { \mathrm { m e d } } \} .\tag{11}
$$



> 💡 **公式批读**: Eq. 11 把代理解剖深度离散为 $\mathcal{D} = \{0.5d_{med},\, d_{med},\, 1.5d_{med}\}$，即以中值深度为中心的三层采样。这是一个粗略的三层近似，用于在不知道真实几何的情况下覆盖一定深度范围。

These proxy points are transformed into world coordinates using the corresponding camera poses and projected into the camera views of the other chunk.



> 💡 **流程描述**: 代理点先用对应 camera pose 变换到世界坐标，再投影进另一 chunk 的相机视图。这一步把“同一 3D 点是否同时出现在两个 chunk 的视野中”变成可计算的几何判据。

Let $V ( i  q )$ denote the fraction of proxy points constructed from chunk i that project with positive depth into at least one camera view of chunk $q .$ We define the symmetric frustum-overlap score as



> 💡 **指标定义**: $V(i \to q)$ 定义为从 chunk $i$ 构造的代理点中，能以正深度投影到 chunk $q$ 的至少一个相机视图的比例。这是“正向投影成功率/可见比例”的度量，初稿用“可视性测试”概括可以接受，但严格说它基于代理点与正深度判据，而非真实遮挡计算。

$$
R ( i , q ) = \frac { 1 } { 2 } \left[ V ( i \to q ) + V ( q \to i ) \right] .\tag{12}
$$



> 💡 **公式批读**: Eq. 12 定义对称重叠分数 $R(i,q) = \frac{1}{2}[V(i\to q) + V(q\to i)]$，即两个方向的可视比例取平均，避免单向度量带来的不对称。

The score provides a lightweight proxy for geometric overlap rather than exact 3D scene intersection. Similar geometry-based criteria have been used to retrieve relevant historical context for longhorizon video generation and world simulation [16, 17]. Importantly, our retrieval requires neither generated RGB content nor learned semantic features. We rank the eligible historical chunks according to $R ( i , q )$ and retrieve the top-K most relevant ones. Further details of the camera sampling and overlap computation are provided in Appendix A.1.1.



> 💡 **局限性与优势**: 原文自承 $R(i,q)$ 是 “a lightweight proxy for geometric overlap rather than exact 3D scene intersection”，这一点初稿转述正确。需要修正两处：(1) 原文对 [16, 17] 的表述是 “Similar geometry-based criteria have been used to retrieve relevant historical context for long-horizon video generation and world simulation”，只是说明类似准则已被使用过，并未说这些工作“证明其有效性”；(2) “适合实时或大规模场景”是初稿的额外推断，原文此处未给实时性结论。另外 Top-K 选择与 Appendix A.1.1 的细节位置转述正确。

In addition to the retrieved historical chunks, we retain the first chunk as a persistent sink to preserve global context, together with recent chunks to maintain local temporal continuity. When the change in camera translation direction exceeds a threshold $\tau _ { \mathrm { t u r n } }$ , we temporarily expand the recent context to the three most recent chunks to improve robustness under abrupt camera



> 💡 **上下文管理**: 除检索到的历史 chunk 外，固定保留两类上下文：第一个 chunk 作为 persistent sink（原文理由：“to preserve global context”），以及 recent chunks（原文理由：“to maintain local temporal continuity”）。当 camera translation direction 的变化超过阈值 $\tau_{turn}$ 时，临时把 recent context 扩展到最近三个 chunk，原文给出的目的是 “improve robustness under abrupt camera motion”。初稿把 sink 解释为“提供全局锚点/起始状态”与原文 “preserve global context” 一致；“记忆断裂风险”属措辞演绎。

motion.



> 💡 **条件触发**: 承接上一段，说明扩展 recent context 的适用场景是 abrupt camera motion（剧烈的相机运动），即过渡到 block 32 定义检索候选集合前的条件收尾。

Let $\mathcal { H } _ { q }$ denote the remaining historical chunks after excluding the sink and recent context. The selected history is



> 💡 **集合定义**: 原文明确 $\mathcal{H}_q$ 是 “the remaining historical chunks after excluding the sink and recent context”，即剔除 sink 与 recent 之后剩下的历史块集合，Top-K 检索只在 $\mathcal{H}_q$ 内进行。

$$
\begin{array} { r } { \mathcal { T } _ { q } = \mathcal { T } _ { \mathrm { s i n k } } \cup \mathcal { T } _ { \mathrm { r e c e n t } } \cup \mathrm { T o p K } _ { i \in \mathcal { H } _ { q } } R ( i , q ) . } \end{array}\tag{13}
$$



> 💡 **公式批读**: Eq. 13 给出最终选定的历史集合 $\mathcal{T}_q = \mathcal{T}_{sink} \cup \mathcal{T}_{recent} \cup \mathrm{TopK}_{i\in\mathcal{H}_q} R(i,q)$：sink、recent 与按 $R(i,q)$ 排序的 Top-K 历史块取并集。注意并集对象是 chunk 集合，Top-K 的排序依据是几何分数 $R$。

This design combines sparse retrieval of geometrically relevant long-range memory with adaptive preservation of short-term context, reducing interference from unrelated intermediate scenes while maintaining local continuity during abrupt camera motion.



> 💡 **设计总结**: 原文的定位是 “sparse retrieval of geometrically relevant long-range memory” 加 “adaptive preservation of short-term context”，效果表述为 “reducing interference from unrelated intermediate scenes while maintaining local continuity during abrupt camera motion”。初稿说“过滤了无关中间场景的噪声”，原文用词是 interference（干扰）而非“噪声”，属措辞偏移；其余概括与原文一致。

## 4.4 Selective State Recomposition

After retrieval, the selected chunks are restored to their original chronological order. Denote the ordered set as



> 💡 **步骤说明**: 检索得到的是集合，但 GDN 状态更新依赖时间先后，因此必须先把选中 chunk 恢复为原始时间顺序，再做摘要组合。

$$
\pi _ { q } = ( \pi _ { 1 } , \pi _ { 2 } , \ldots , \pi _ { M } ) .\tag{14}
$$



> 💡 **公式批读**: Eq. 14 把有序集合记为 $\pi_q = (\pi_1,\pi_2,\ldots,\pi_M)$。原文只定义“有序集合”，未逐字说明 $\pi_m$ 是什么；把它理解为“按时间排序的第 $m$ 个选中 chunk 的索引”是根据组合规则（Eq. 9 需按顺序施加）作出的合理推断，属批注补充。

Using the associative rule in Eq. 9, their summaries can be directly composed as



> 💡 **算法执行**: 用 Eq. 9 的结合律把有序 chunk 的摘要链式合并，避免重放原始视觉 token，这是 4.4 相对 4.2 的增量步骤。

$$
A _ { \pi _ { q } } = \prod _ { m = 1 } ^ { M } A _ { \pi _ { m } } ,\tag{15}
$$



> 💡 **公式批读**: Eq. 15 计算组合后的变换矩阵 $A_{\pi_q} = \prod_{m=1}^{M} A_{\pi_m}$，即按时间顺序把所有选中 chunk 的 $A$ 相乘；乘积顺序不能颠倒，因为矩阵乘法不交换。

and



> 💡 **分项定义**: “and” 引出组合后的写入项 $B_{\pi_q}$ 的计算，即下一条 Eq. 16。

$$
B _ { \pi _ { q } } = \sum _ { m = 1 } ^ { M } B _ { \pi _ { m } } \prod _ { n = m + 1 } ^ { M } A _ { \pi _ { n } } .\tag{16}
$$



> 💡 **公式批读**: Eq. 16 给出 $B_{\pi_q} = \sum_{m=1}^{M} B_{\pi_m}\prod_{n=m+1}^{M} A_{\pi_n}$。含义是每个 chunk 的写入项 $B_{\pi_m}$ 还要乘上它之后所有 chunk 的 $A$，因为越早写入的信息会被越多后续变换作用；其结构与 Eq. 8 完全相同，只是作用在稀疏有序子集上。

The resulting historical recurrent state is



> 💡 **最终状态**: 用组合好的 $A_{\pi_q}$、$B_{\pi_q}$ 计算历史递归状态，作为当前 query chunk 生成的记忆起点。

$$
S _ { q } ^ { \mathrm { h i s t } } = S _ { 0 } A _ { \pi _ { q } } + B _ { \pi _ { q } } ,\tag{17}
$$



> 💡 **公式批读**: Eq. 17 给出 $S_q^{hist} = S_0A_{\pi_q} + B_{\pi_q}$。初稿写 “$S_0$ 是初始状态（通常为零或 sink 初始化）”，其中“通常为零或 sink 初始化”是额外推断：本条及 block 44 原文只说 $S_0$ 是 initial GDN state，未在此规定其取值，因此应标注为待证/推断，不能当作原文结论。

where $S _ { 0 }$ is the initial GDN state.



> 💡 **变量说明**: 原文确认 $S_0$ 是 “the initial GDN state”，即 Eq. 17 中旧状态的起点。这里的方法链条是：chunk 摘要 → 几何检索 → 有序重组 → 得到 $S_q^{hist}$；初稿“完成了从理论推导到具体状态计算的闭环”是修辞性总结，不属于原文主张。

Standard GDN composes every historical chunk in temporal order, causing information written by an early scene to be repeatedly transformed by all subsequent chunks. In contrast, HLA-WM shortens this propagation path by excluding geometrically irrelevant intermediate chunks. Distant but relevant memories are therefore exposed to substantially fewer unrelated transitions before being reused. Meanwhile, the original GDN dynamics within each selected chunk are fully preserved, rather than replacing them with direct averaging or summation of linear states.



> 💡 **机制拆解与动机**: 本段说明 HLA-WM 相对标准 GDN 的改动逻辑。原文的因果表述是：标准 GDN 按时间顺序组合**每一个**历史 chunk，因此早期场景写入的信息会被**所有后续 chunk** 反复变换（原文用 “all subsequent chunks”，并未限定这些 chunk 与当前查询“无关”）。HLA-WM 的做法是剔除“几何上无关的中间 chunk”（geometrically irrelevant intermediate chunks），从而缩短这条传播路径，使远端但相关的记忆在被复用前经历更少的无关状态转移（“substantially fewer unrelated transitions”）。第二个关键条件是：被选中 chunk 内部的原始 GDN 动力学被**完整保留**，而不是用线性状态的直接平均或求和来替代——也就是说，筛选发生在 chunk 选择层，而不是在 chunk 内部的状态聚合层。
> > 💡 修正说明：初稿把标准 GDN 的后续 chunk 直接称为“无关变换”，属于把 HLA-WM 的筛选视角套用到 baseline 上，原文只说“all subsequent chunks”；“保证局部时序信息完整性”应更严格表述为“保留每个选中 chunk 内部的原始 GDN 递推，而非平均/求和”。

The recomposition is performed directly from cached $( A _ { i } , B _ { i } )$ summaries and does not require reforwarding the selected historical chunks. When all previous chunks are selected, the composition reduces to the original chronological GDN recurrence, providing a direct consistency check for the chunk-wise formulation. For the accompanying softmax-attention pathway, we apply the same selected chunk set to the corresponding historical KV cache.



> 💡 **实现细节与一致性检查**: 三点。1）重组直接基于已缓存的仿射摘要 $(A_i, B_i)$ 完成，**不需要重新前向传播**被选中的历史 chunk。2）退化性质：当**所有先前 chunk 都被选中**时，该组合退化为原始按时间顺序的 GDN 递推，原文称这提供了 “a direct consistency check for the chunk-wise formulation”（对 chunk 化公式的一致性检查）——注意原文是“一致性检查”，不是“数学证明正确性”。3）对并行的 softmax-attention 通路，作者把**同一个选中 chunk 集合**应用到对应的历史 KV cache 上，以保证两条通路使用相同的记忆集合。
> > 💡 修正说明：初稿“证明了 chunk-wise 公式的正确性”把 consistency check 抬升为证明，超出原文证据；初稿“多模态/多路径处理的一致性”中的“多模态”无原文支持，原文只说 softmax-attention pathway 的 KV cache。

Eficient State Recomposition Kernel. We implement direct state recomposition on top of the chunk-wise Triton GDN kernels. During the original forward pass, the afine summaries $( A _ { i } , B _ { i } )$ of each chunk are computed once and cached independently of the incoming recurrent state. For each query chunk $q ,$ the summaries in $\mathcal { T } _ { q }$ are gathered in chronological order and directly composed through an eficient forward scan to reconstruct $S _ { q } ^ { \mathrm { h i s t } }$



> 💡 **内核实现（Kernel Implementation)**: 实现建立在 chunk-wise Triton GDN kernel 之上。流程是：在原始前向传播中，每个 chunk 的仿射摘要 $(A_i, B_i)$ 只计算一次，并且其缓存**独立于传入的递归状态**（cached independently of the incoming recurrent state）；对每个查询 chunk $q$，把 $\mathcal{T}_q$ 中的摘要按时间顺序 gather，再通过一次高效 forward scan 直接组合，重构出 $S_q^{\mathrm{hist}}$。这一段解释的是“缓存的粒度与重组路径”，而不是并行化收益。
> > 💡 修正说明：初稿“使得并行化和检索成为可能”在原文中找不到对应表述，属于作者的合理推断，已删除。若要保留，应显式标注为推断（待证）。另注：原文 OCR 写为 “Eficient”，属解析产物，核对时按原文保留。

Memory and Computational Overhead. HLA-WM introduces additional storage only for a fixed-size afine summary $( A _ { i } , B _ { i } )$ and lightweight camera metadata for each temporal chunk. Since historical information is cached at the chunk level rather than the token level, the memory footprint scales with the number of chunks instead of the number of visual tokens. During generation, only the selected summaries are involved in state composition, while geometry-based retrieval operates directly on camera information with minimal overhead. Consequently, HLA-WM provides selective access to long-range memory while largely preserving the memory and computational eficiency of recurrent linear attention.



> 💡 **开销分析（Overhead Analysis)**: 存储侧：HLA-WM 只额外保存每个时间 chunk 的一个**固定大小**仿射摘要 $(A_i, B_i)$ 以及轻量级相机元数据；由于历史信息缓存在 chunk 级而非 token 级，内存占用随 **chunk 数量**扩展，而不是随视觉 token 数量扩展。生成侧：只有被选中的摘要参与状态组合，几何检索直接作用于相机信息，开销很小。结论是 HLA-WM 在提供长程记忆选择性访问的同时，**基本保持**（largely preserving）循环线性注意力的内存与计算效率。
> > 💡 修正说明：初稿括注“（显著降低长视频处理的显存压力）”是推断——原文只给出“随 chunk 数而非 token 数扩展”的标度关系，未给出长视频显存的实测数据。此外用词应从“保持不变”收敛到原文的 “largely preserving”（基本保持）。

Table 1 Main results on SANA-WM-Bench under diferent generation stages and refinement modes. We report revisit consistency, camera controllability, and inference throughput. Best results within each setting are shown in bold.
<table><tr><td rowspan="2">Pipeline</td><td rowspan="2">Method</td><td colspan="3">Revisit Consistency</td><td colspan="3">Camera Control</td><td>Efficiency</td></tr><tr><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC ↓</td><td>FPS ↑</td></tr><tr><td colspan="7">Simple-Trajectory Split</td><td></td><td></td></tr><tr><td>Stage-1</td><td>SANA-WM HLA-WM</td><td>9.18 9.91</td><td>0.1729 0.1999</td><td>0.6327 0.6049</td><td>19.7040 13.5864</td><td>2.2625 2.0928</td><td>2.3974 2.1798</td><td>22.403 22.053</td></tr><tr><td>Stage-1 + AR Refine</td><td>SANA-WM HLA-WM</td><td>14.44 14.82</td><td>0.2770 0.2872</td><td>0.5738 0.5682</td><td>20.9315 20.2933</td><td>2.1587 2.1910</td><td>2.3121 2.3240</td><td>8.280 8.211</td></tr><tr><td>Stage-1 + Bi. Refine</td><td>SANA-WM HLA-WM</td><td>13.04 13.56</td><td>0.2945 0.3148</td><td>0.5786 0.5614</td><td>11.3848 8.8823</td><td>1.9893 1.8637</td><td>2.0545</td><td>7.701</td></tr><tr><td colspan="7">Hard-Trajectory Split</td><td>1.9127</td><td>7.700</td></tr><tr><td>Stage-1</td><td>SANA-WM HLA-WM</td><td>9.37 10.11</td><td>0.1774 0.1997</td><td>0.6044 0.5848</td><td>20.3759 15.0887</td><td>1.9952 1.8388</td><td>2.1507 1.9482</td><td>22.403 22.053</td></tr><tr><td>Stage-1 + AR Refine</td><td>SANA-WM HLA-WM</td><td>14.12 14.47</td><td>0.2719 0.2812</td><td>0.5621 0.5557</td><td>25.6330 22.0165</td><td>2.0099 1.9351</td><td>2.2079 2.1043</td><td>8.280 8.211</td></tr><tr><td>Stage-1 + Bi. Refine</td><td>SANA-WM HLA-WM</td><td>13.05 13.51</td><td>0.2972 0.3142</td><td>0.5468 0.5384</td><td>13.1713 10.5268</td><td>1.6649 1.5449</td><td>1.7694 1.6230</td><td>7.701 7.700</td></tr></table>



> 💡 **Table 1 批读（证据链核对）**: 该表在 SANA-WM-Bench 上对比 SANA-WM 与 HLA-WM，指标含重访一致性（PSNR↑、SSIM↑、LPIPS↓）、相机控制（RotErr↓、TransErr↓、CamMC↓）与效率（FPS↑），分 Simple-Trajectory / Hard-Trajectory 两种拆分、Stage-1 / +AR Refine / +Bi. Refine 三种设置。
> > - **支持的部分**：Stage-1 下两种拆分的重访一致性均改善（Simple：PSNR 9.18→9.91、SSIM 0.1729→0.1999、LPIPS 0.6327→0.6049；Hard：9.37→10.11、0.1774→0.1997、0.6044→0.5848），RotErr 改善明显（Simple 19.7040→13.5864；Hard 20.3759→15.0887）。
> > - **初稿过强、已修正的部分**：初稿称相机控制指标“均表现出显著提升”不成立——Simple-Trajectory 的 Stage-1 + AR Refine 中 TransErr 2.1587→2.1910、CamMC 2.3121→2.3240 实际上是变差；只有 Hard-Trajectory 同一设置（TransErr 2.0099→1.9351、CamMC 2.2079→2.1043）是改善。
> > - **已删除的无依据结论**：初稿称“在 Hard-Trajectory Split 下改善更明显”与表中数值方向不符——Stage-1 PSNR 提升量 Simple 为 +0.73、Hard 为 +0.74，SSIM 提升量 Simple +0.0270 反而大于 Hard +0.0223，RotErr 改善幅度 Simple（−6.12°）也大于 Hard（−5.29°）。两种拆分的改善幅度大体相近，不能下“困难轨迹更明显”的结论。
> > - **效率**：FPS 变化很小（Stage-1 22.403→22.053；Bi. Refine 7.701→7.700），说“轻微下降”可接受，但“巨大质量提升换取轻微吞吐牺牲”属主观权衡表述。
> > - **无法核验项**：caption 说明 “Best results within each setting are shown in bold”，但本次仅有表格文本、无渲染加粗信息，且该 Markdown 表格在转写中已错位（如 “Hard-Trajectory Split” 行与 Stage-1 + Bi. Refine 的 CamMC/FPS 值 1.9127、7.700 被错误分行），故加粗标记与个别单元格归属**未能核验**，需以原始表格图片为准。

