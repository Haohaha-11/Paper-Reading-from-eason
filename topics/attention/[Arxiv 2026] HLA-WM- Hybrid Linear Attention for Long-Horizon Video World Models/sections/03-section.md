[← 返回 README](../README.md)

## 3 Empirical Insights

We first investigate the long-range forgetting behavior of GDN in SANA-WM [7]. At the chunk level, the recurrent update of chunk i can be written as



> 💡 **问题动机**（AI 批注）: 本节开门见山，目标是在 SANA-WM 中考察 GDN 的 long-range forgetting 行为，切入点是 chunk 粒度——把逐 chunk 的 recurrent update 显式写出来，为后文"记忆被中间转移矩阵反复作用"的量化做铺垫。两处措辞需要设边界：(1) 原文只写 "GDN"，从未展开全称，也只在后文以 "SANA-WM Benchmark"、"worldmodel rollouts" 出现，因此初稿的"Gated Delta Networks"全称与"世界模型"定位属于外部知识/后文信息的合理补充，本批读标注为推断，不视作原文陈述；(2) 原文是 can be written as，即"写出"这一更新式，不宜说成本文首次"定义"了该递归结构。

$$
S _ { i } = S _ { i - 1 } A _ { i } + B _ { i } ,\tag{1}
$$



> 💡 **公式批读**（AI 批注）: Eq. (1) 是 chunk 级递推 $S_i = S_{i-1}A_i + B_i$：新状态 = 旧状态经 $A_i$ 变换 + 本 chunk 的写入量。符号语义要以后一段（block 3）的原文界定为准：$A_i$ 是作用于历史记忆的 state transition，$B_i$ 是当前 chunk 新写入的信息。所以初稿"$A_i$ 决定旧记忆如何变形/衰减"可接受，但"衰减"部分是后文（保留度下降）才引入的解读；而把该式称为"标准线性 RNN/GDN"的递归属外部归类，原文未做此表述，应记为推断。

where $A _ { i }$ denotes the state transition applied to historical memory and $B _ { i }$ represents the information newly written by the current chunk. Expanding the recurrence over multiple chunks gives



> 💡 **机制拆解**（AI 批注）: 这段实际完成两件事——界定 $A_i$、$B_i$（$A_i$ 作用于历史记忆，$B_i$ 为本 chunk 新写入信息），并宣布把递推展开到多个 chunk（Expanding the recurrence over multiple chunks gives）。初稿把"早期信息须经 $A_{i+1}\cdots A_j$ 连续作用"放在这里作为预告可以，但该结论的直接依据是下一段的 Eq. (2)，本段原文尚未展开。更需收紧的是初稿的"如果 $A$ 具有收缩性质，早期信息将被指数级稀释"：原文只强调 repeated transformation，既未假设各 $A$ 收缩，也未给出"指数级"衰减的结论；当中间矩阵的范数分布未知时，衰减速度无法由本式断定，此为推断/待证。

$$
S _ { j } = \cdots + B _ { i } A _ { i + 1 } A _ { i + 2 } \cdots A _ { j } + \cdots + B _ { j } .\tag{2}
$$



> 💡 **公式批读**（AI 批注）: Eq. (2) 把递推展开成显式多项和：$S_j = \cdots + B_iA_{i+1}A_{i+2}\cdots A_j + \cdots + B_j$，即每个历史 chunk 的写入量各自乘上它之后全部转移矩阵的连乘。原文由此得到的结论是：早期 chunk 写入的信息在影响远处未来场景前，会被所有后续转移矩阵反复变换（repeatedly transformed）。初稿"已被中间的变换链完全重塑"比原文更强，宜改为"被反复变换"；同时要提醒连乘本身不蕴含信息必然丢失——最终方向取决于各 $A$ 的性质，这是原文未判定、本文亦不应替其判定的部分。

Thus, information written by an early chunk is repeatedly transformed by all subsequent transition matrices before it can influence a distant future scene. We quantify its cumulative retention from chunk i to chunk j using the average norm of the intervening transition product:



> 💡 **指标定义**（AI 批注）: 原文给出量化口径：用中间转移乘积的平均范数（average norm of the intervening transition product）来度量 chunk i 到 chunk j 的 cumulative retention。这里有一个方向上的张力值得记录——原文把它称作 cumulative retention（累积保留），但同段及 block 7 的读数规则是"值越小衰减越强"，因此该量实际是衰减度量而非单调保留度量。批注沿用原文字面名称，同时提醒读者不要按字面把 $W$ 理解为"越大保留越多"。此外原文只给出 AvgNorm 这一选择，未说明为何不用其他谱量，这属未说明项。

$$
W _ { i \to j } = { \mathrm { A v g N o r m } } \left( A _ { i + 1 } A _ { i + 2 } \cdot \cdot \cdot A _ { j } \right) ,\tag{3}
$$



> 💡 **公式批读**（AI 批注）: Eq. (3) $W_{i \to j} = \mathrm{AvgNorm}(A_{i+1}A_{i+2}\cdots A_j)$ 把上一段的口头描述形式化。初稿"用平均范数而非最大奇异值，旨在反映典型情况下的信号强度衰减"属于对设计动机的推断：原文既没有提最大奇异值，也没有说明 AvgNorm 的统计口径（在多少样本/位置上平均、如何归一），这些应标为待证。真正有原文依据的只有：该量刻画中间转移乘积的典型幅度，从而刻画从 chunk i 到 j 的累积保留/衰减程度。

where a smaller $W _ { i \to j }$ indicates stronger attenuation from chunk i to $j .$



> 💡 **解释补充**（AI 批注）: 原文明确读数规则：$W_{i \to j}$ 越小，表示从 chunk i 到 j 的衰减越强。初稿把它说成"为 Figure 2 提供理论阈值、当 $W$ 极低时模型就无法利用该记忆"——原文既没有给任何阈值，也没有建立从 $W$ 到"必然无法利用"的因果链。Figure 2 展示的只是一条代表性轨迹上保留影响降到 0.0416 与生成场景偏离同时出现，属一致性/相关性证据，不是阈值判定，更不是因果证明。这是本段最需要收紧的地方。

Figure 2 illustrates this phenomenon on a representative trajectory from the SANA-WM Benchmark. The camera leaves the region observed at Chunk 3, traverses a large loop, and eventually returns to a nearby viewpoint at Chunk 35. Since the two chunks observe highly overlapping regions, the information stored at Chunk 3 should remain particularly relevant when generating Chunk 35. However, its retained influence steadily diminishes as the rollout progresses and drops to only 0.0416 by the time the camera revisits the scene. Correspondingly, the scene generated at Chunk 35 deviates substantially from the earlier observation, indicating that the model can no longer efectively recover the distant but relevant scene information.



> 💡 **证据分析（Figure 2 案例）**（AI 批注）: 原文案例是：相机在 Chunk 3 观测某区域后离开、走了一个大回环，在 Chunk 35 回到附近视点；由于两 chunk 观测区域高度重叠，Chunk 3 存下的信息在生成 Chunk 35 时"本应"仍然相关。但保留影响随 rollout 推进持续下降，到相机重访该场景时只剩 0.0416，同时 Chunk 35 生成的场景与先前观测明显偏离，说明模型已无法有效恢复远处但相关的场景信息。需纠正两处：(1) 初稿"中间跨越了 32 个无关视角"——Chunk 3 到 Chunk 35 相隔 32 个 chunk 步，中间 chunk 是 4–34 共 31 个，且下一段原文说的是"大多数中间 chunk 对应不同视角"，并非全部无关；(2) 把该数值记作 $W_{3\to35}$ 是合理映射推断，原文只写 its retained influence drops to only 0.0416，并未显式写出下标。另外"证实空间相关性丢失"过强，本节是单条轨迹上的现象观察，应改为"提供了支持该解释的证据"。

More importantly, most intermediate chunks correspond to diferent viewpoints and are only weakly related to the scene revisited at Chunk 35. Nevertheless, their state-transition matrices are still sequentially applied to the memory written by Chunk 3, causing that memory to undergo repeated transformations even when the intermediate observations provide little useful information for the later revisit. As a result, scene information that becomes relevant again can be substantially attenuated before it is needed. This exposes a fundamental mismatch in conventional GDN memory: historical information is updated according to temporal progression, whereas its relevance in worldmodel rollouts is often determined by spatial and geometric proximity. This mismatch motivates selectively preserving and composing scene-relevant historical states instead of propagating all past information uniformly through every intermediate chunk.



> 💡 **核心洞察与局限**（AI 批注）: 这是本节结论段，给出 mismatch 陈述：即使中间 chunk 与 Chunk 35 重访的场景只有弱相关，它们的状态转移矩阵仍会被顺序作用到 Chunk 3 写入的记忆上，使该记忆在中间观测几乎不提供有用信息时也不断被变换；于是"重新变得相关"的场景信息可能在真正被需要之前就已被大幅衰减。原文的对照是：历史信息按时间进程（temporal progression）更新，而它在 world-model rollout 中的相关性往往由空间与几何邻近性（spatial and geometric proximity）决定。初稿"中间无关帧的 $A$ 矩阵无差别地扭曲了有用记忆"中的"扭曲"是价值判断，原文只说 repeatedly transformed / undergo repeated transformations，建议改为"反复变换"，以免把中性变换写成破坏性失真；末句"本文方法由该错配引出：选择性保留并组合场景相关历史状态，而非让所有过去信息均匀穿过每个中间 chunk"与原文一致。

![](../images/363a05791c5db9ed9f377d96bb586d0e0dd15cd82645161fa1c767c092aa1863.jpg)  
Figure 3 Overview of HLA-WM. Historical video chunks are represented by independent GDN summaries. Given the current query, geometry-guided retrieval selects scene-relevant historical chunks together with persistent sink and recent context. The selected summaries are then composed in chronological order to construct a query-specific recurrent state.



> 💡 **Figure 3 批读（含更正）**（AI 批注）: 本 block 是 Figure 3 的图片与 caption，不是 Figure 2。初稿把它当成 Figure 2 的"记忆衰减曲线"是张冠李戴，必须纠正为 Figure 3 批读。据 caption：HLA-WM 的整体流程是——历史视频 chunk 各自表示为独立的 GDN summary；给定当前 query，geometry-guided retrieval 选出与场景相关的历史 chunk，并与 persistent sink 和 recent context 一并取出；被选中的 summary 再按时间顺序组合，构造一个 query-specific 的 recurrent state。也就是说这张图对应的是下一节的方法框架（检索 + 组合），恰好回应 block 9 提出的"不要均匀传播所有过去信息"的动机。需说明：原文本节未提供该图的视觉细节，本批注仅依据 caption 文字；图内模块连线与视觉设计未获图片信息，不作断言。

