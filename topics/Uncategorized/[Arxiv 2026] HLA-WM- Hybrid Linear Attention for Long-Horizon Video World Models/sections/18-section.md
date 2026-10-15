[← 返回 README](../README.md)

## A.7 Efect of Revisit Interval

Figure 6 analyzes the efect of revisit interval on the improvement of HLA-WM. We group revisit pairs into short (< 10 s), medium (10–40 s), and long $\mathrm { ( \ge 4 0 ~ s ) }$ intervals. Across Stage 1, AR refinement, and bidirectional refinement, the gains are generally larger for medium and long revisits. In particular, PSNR improvement increases consistently with the revisit interval across all three generation modes. LPIPS exhibits a similar trend, shifting from minor degradation at short intervals to clear improvement for long revisits. SSIM also shows substantially larger gains at medium or long intervals for Stage 1 and bidirectional refinement, although the trend under AR refinement is not monotonic. Overall, these results indicate that the benefit of selective long-range memory becomes more pronounced as the temporal distance between repeated observations increases, consistent with its efectiveness in mitigating long-horizon forgetting.



> 💡 **机制与实验解读**: 本节是对 Figure 6 的文字解读，讨论重访间隔（revisit interval）如何改变 HLA-WM 相对基线的改进幅度。原文只引用 Figure 6，**没有出现 Table 6，也没有给出任何具体 dB / 百分点数值**，因此任何量化差值都属于初稿自行补写。
> 
> 原文的变量与条件可以精确复述为：
> - 分组口径：short（$\lt 10$ s）、medium（10–40 s）、long（$\ge 40$ s）——注意初稿写的“短间隔 0–5s”“长间隔 $\gt 30$s”与原文不符。
> - 评估的三种生成模式：Stage 1、AR refinement、bidirectional refinement。
> - 总体趋势：medium 与 long 重访的增益“generally larger”（一般更大），原文用词是倾向性描述，初稿“增益爆发式增长”属于语气放大。
> 
> > 💡 **逐指标批读**:
> > - **PSNR**：在三种生成模式下都随重访间隔一致增大（原文: increases consistently with the revisit interval across all three generation modes）。这是本节最强的单调性陈述，但原文仍未给出具体数值。
> > - **LPIPS**：趋势类似，由 short 间隔的轻微退化（minor degradation）转为 long 间隔的明显改善（clear improvement）。这里区分了“退化—改善”的符号变化，是本节唯一明确的方向性翻转证据。
> > - **SSIM**：Stage 1 与 bidirectional refinement 在 medium/long 间隔上有 substantially larger gains；但 AR refinement 下的趋势**非单调**（not monotonic）。初稿完全漏掉了这个反例式的限定，会高估结论的一致性。
> 
> > 💡 **结论强度批读**: 原文的收束句是“the benefit of selective long-range memory becomes more pronounced as the temporal distance ... increases, consistent with its effectiveness in mitigating long-horizon forgetting”。关键词是 **consistent with**，即“与……相一致”，属于相容性表述而非因果证明。把结论写成“证明该模块有效缓解了信息丢失”会超出本文的证据边界。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 为什么短间隔下记忆模块的增益较小？
> > - A: 原文**没有给出机制解释**，只有现象描述。可以合理推断短间隔下相邻观测冗余度高、当前帧自身信息已较充分，但这属于推断，正文与附录本节均未论证，阅读笔记中应标注为“推断”。
> > - Q: 这个结论是否意味着间隔越长越好？
> > - A: 不能这样外推。原文只说增益随间隔增大而更明显（且在 AR refinement 下 SSIM 非单调），并未给出最优间隔或收益上限。
> > - Q: 初稿提到的“稀疏、非连续重访场景”有出处吗？
> > - A: 正文本节未使用“稀疏/非连续”这一表述，属于初稿追加的场景概括，应视为推断而非原文主张。

