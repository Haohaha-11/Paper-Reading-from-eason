[← 返回 README](../README.md)

## 1 INTRODUCTION

Test-time scaling improves language model reasoning by spending additional inference compute (Jaech et al., 2024; Guo et al., 2025; Snell et al., 2024). This computation can support longer chains of thought in token space (Muennighoff et al., 2025), or repeated applications of shared layers in latent space, as in looped transformers (Dehghani et al., 2019; Geiping et al., 2025a; Zhu et al., 2025). Parameter sharing makes additional depth possible without increasing model size, but each iteration still incurs decoding cost. Prior scaling studies compare looped and non-looped models at matched parameter counts or per-token FLOPs (Prairie et al., 2026; Schwethelm et al., 2026b; Wang et al., 2026b). These comparisons do not establish whether looping improves accuracy– compute scaling as output-token budgets increase. We study this question through post-training, a practical way to introduce recurrence into pretrained LLMs without training a looped model from scratch (McLeish et al., 2025; Chen et al., 2025b; Fu et al., 2025b).



> 💡 **问题动机与机制拆解**：本段先给测试时扩展（test-time scaling）分了两条实现路径——(1) token 空间中更长的思维链（Muennighoff et al., 2025），(2) latent 空间中反复应用共享层，即 looped transformers（Dehghani et al., 2019; Geiping et al., 2025a; Zhu et al., 2025）。参数共享的作用被限定为「让额外深度在不增大模型体积的前提下成为可能」，代价是「每次迭代仍产生解码成本」——注意这里说的是增加了迭代/深度，而不是无条件变便宜。
> 
> **研究缺口（本段最关键的因果主张）**：此前的 scaling 研究是在「匹配参数量」或「匹配每 token FLOPs」下比较 looped 与非 looped 模型（Prairie et al., 2026; Schwethelm et al., 2026b; Wang et al., 2026b），这类比较**不足以回答**：当输出 token 预算增加时，looping 是否改善了 accuracy–compute 的缩放规律。这是本段把「已有结论」与「本文要回答的问题」切开的地方。
> 
> **方法选择**：作者不走「从头训练 looped 模型」，而是用 post-training，在预训练 LLM 上引入递归，引用 McLeish et al., 2025; Chen et al., 2025b; Fu et al., 2025b 作为实用路径的依据。
> 
> **推断界限**：本段未给出任何具体数值或公式；它只陈述研究问题与实验策略，缩放斜率、迭代深度 M 等数字出现在下一段（block 2）。

Using the same pretrained checkpoint and post-training data, we compare Standard (the non-looped baseline) with two principal looped architectures: full-stack recurrence in Ouro (at fixed depth or with its adaptive exit gate), and middle-block recurrence in Huginn (Zhu et al., 2025; Geiping et al., 2025a; Huang et al., 2026a). By varying the output-token cutoff up to 16K at test time, we measure the scaling slope as the accuracy gain per doubling of decoding FLOPs. In this post-training setting, fixed-depth and adaptive Ouro (maximum iteration depth M = 2) and Huginn (M = 3) yield steeper scaling slopes than Standard (2.12, 2.27 and 2.26 versus 1.79), yet remain less accurate over the overlapping compute range (Figure 1a). This motivates our central question:



> 💡 **实验设定与核心数字**：同一预训练 checkpoint、同一 post-training 数据，对比 **Standard（非循环基线）** 与两个循环架构：**Ouro 的 full-stack recurrence**（分固定深度与自适应 exit gate 两种）和 **Huginn 的 middle-block recurrence**（Zhu et al., 2025; Geiping et al., 2025a; Huang et al., 2026a）。**易错点**：Huginn 在此处被明确描述为 middle-block recurrence（中层块循环），不是「混合架构」，初稿的「混合架构」表述与原文不符，已在本次修正中改回 middle-block recurrence。
> 
> **操作化定义**：测试时把 output-token cutoff 变动到最高 16K；scaling slope 被定义为「解码 FLOPs 每翻一倍所获得的准确率增量」（accuracy gain per doubling of decoding FLOPs）。这一定义是后面所有斜率数字的比较基准。
> 
> **结果与方向性**：固定深度 Ouro、自适应 Ouro（最大迭代深度 M = 2）与 Huginn（M = 3）的缩放斜率分别为 2.12、2.27、2.26，均高于 Standard 的 1.79；但在**重叠的计算区间内它们仍然更不准确**（Figure 1a）。因此本段主张是「斜率更陡 + 当前区间精度仍落后」这一组合，而非「循环模型已经更好」。
> 
> **修正与推断界限**：初稿「在低预算下表现不佳，但在高预算下展现出更陡峭的缩放斜率」把斜率优势错置到「高预算区间」，原文只说在重叠区间内仍更不准确，未声明超过 Standard 的交叉点在哪里；初稿与摘要中的「超线性缩放能力」也超出证据——更陡的斜率 ≠ 超线性，原文未做该断言。初稿「优势依赖于足够的推理步数来积累信息」属于对机制的推测，原文未给出该因果解释，应视为**推断/待证**。另外，Figure 1a 仅有正文文字描述，未提供图像视觉信息，此处批注不对图中曲线细节作判断。

