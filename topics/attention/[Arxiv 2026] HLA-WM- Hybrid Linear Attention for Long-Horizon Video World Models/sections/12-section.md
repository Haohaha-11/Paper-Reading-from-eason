[← 返回 README](../README.md)

## A.2 Implementation of State Recomposition

The afine state composition described in the main text is implemented directly on top of the original GDN kernels. After each chunk is completed, we cache its frame-wise Phase-A suficient statistics, which characterize its afine efect on an arbitrary incoming recurrent state. For each query chunk, the statistics of the selected historical chunks are gathered in chronological order and passed to the original Phase-B forward scan to reconstruct the query-specific GDN state. This avoids re-evaluating historical chunks through the Transformer.



> 💡 **机制拆解（实现路径）**:
> - **复用对象**：每个 chunk 完成后缓存其 frame-wise Phase-A *sufficient statistics*（原文拼写为 suficient）。按原文定义，该统计量刻画的是该 chunk 对**任意**传入 recurrent state 的 affine 效果——关键词是“任意”，说明缓存物与具体输入状态无关，可以跨 query 复用。
> - **重组流程**：对每个 query chunk，把被选中的历史 chunk 的统计量**按时间顺序**收集起来，再交给**原有的 Phase-B forward scan** 重建 query-specific GDN state。注意实现要点：Phase-B 的扫描逻辑本身不改，改的只是它的输入来源（缓存统计量而非原始历史序列）。
> - **收益来源与边界**：原文只声明 “This avoids re-evaluating historical chunks through the Transformer.”，即避免对历史 chunk 的重复前向。原文在本段**没有**给出复杂度阶数、加速比或内存开销数字。
> - **推断与待证（不采纳初稿数字）**：初稿所述“复杂度从 $O(N^2)$ 降至线性”“长上下文推理加速”在本段无出处，属于外部推断，本节批注不将其作为结论；若要成立，需正文复杂度分析或实验部分佐证。
> - **追问点**：被选中历史 chunk 的“选择”依据是什么、选择粒度与上限如何定，本段未交代，应回到正文 Selective recomposition 的定义处核对。

Selective recomposition is applied only to the main GDN matrix and normalization states. The camera-GDN recurrent state, GDN short-convolution state, and FFN temporal-convolution state retain their original chronological updates. We additionally batch compatible GDN blocks during recomposition to reduce kernel-launch and data-movement overhead without changing the underlying recurrence.



> 💡 **选择性重组的适用范围**:
> - **做重组的状态**：仅 main GDN matrix 与 normalization states。
> - **不做重组、保持原时间顺序更新的状态**：camera-GDN recurrent state、GDN short-convolution state、FFN temporal-convolution state。这是本节最硬的一条边界条件，说明 recomposition 不是全状态等价替换，而是按状态类型分治。
> - **工程优化**：在重组时把兼容的 GDN blocks 批处理，用来降低 kernel-launch 与 data-movement 开销；原文强调 “without changing the underlying recurrence”，即批处理只是实现层等价优化，不改变递推语义。
> 
> 💡 **Q&A 批注记录**:
> - Q: 为什么只对 main GDN matrix 和 normalization states 做重组？
> - A: 原文只陈述“只对……做”，未给出理由。可**推断（待证）**：camera-GDN、short-convolution、FFN temporal-convolution 这三类状态的时间耦合方式或状态语义与主 GDN 矩阵不同，无法用同一套 Phase-A affine 充分统计量刻画。此为本批注的推断，需正文主体或附录佐证，不能当作原文主张。
> - Q: 初稿称“FFN 层可并行或流式处理”“注意力层/KV Cache 重组”“动态内存池 + 滑动窗口支持任意长度”是否成立？
> - A: 不成立或无出处。原文明确 FFN temporal-convolution state **保留原有的按时间顺序更新**，并非“可并行或流式处理”；本段也未出现注意力层、KV Cache、内存池、滑动窗口等表述，故这些说法已从批注中删除。
> - Q: 批处理 GDN blocks 会不会破坏正确性？
> - A: 原文以 “without changing the underlying recurrence” 作为保证，属于实现等价性声明；本节未提供验证实验，若需实证应查附录或消融部分。

