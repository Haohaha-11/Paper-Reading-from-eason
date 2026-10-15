[← 返回 README](../README.md)

## A.8.2 MBench-A Subset Results

MBench-A contains 547 samples across four oficial subsets: Causal, Human, Environment, and Object. For each sample, we first average its matched revisit pairs and then compute the macro average across samples. Table 11 reports the complete subset-level results under the three evaluated inference modes. Across all 12 mode–subset combinations, HLA-WM simultaneously improves PSNR, SSIM, and LPIPS, demonstrating consistent generalization across diverse scene and interaction types.



> 💡 **评测协议批读**: MBench-A 共 547 个样本，划分到 Causal、Human、Environment、Object 四个 official 子集（原文此处拼写为 "oficial"，系 OCR/排版原样，引用时保留）。聚合方式分两级：先对每个样本的 matched revisit pairs 取平均，再在样本之间做 macro average（宏平均）。这一协议的含义是每个样本权重相同，不受各子集样本数差异影响——这正是它能支撑"子集级结论"的前提。
> 
> 💡 **结果主张批读**: 作者称 Table 11 报告三种推理模式下完整的子集级结果，并断言在 12 个 mode–subset 组合上 HLA-WM 同时改善 PSNR、SSIM 与 LPIPS。12 = 3 种推理模式 × 4 个子集，与上一句给出的子集数自洽，可作为数字自洽性的一处核对点。注意三个指标的取向不同：PSNR、SSIM 越高越好，LPIPS 越低越好，因此"同时提升"在此应理解为三项指标均朝有利方向变化，而不是数值意义上的同时增大。
> 
> 💡 **证据边界说明**: 本小节文本只给出结论性陈述，未给出 Table 11 的具体数值。因此每个子集、每种推理模式下的具体提升幅度、以及各子集之间幅度差异，都无法在此处核验；此处能确认的只是作者主张的覆盖范围是"12 个组合无例外"。
> 
> 💡 **Q&A 批注记录**:
> - Q: 为什么先平均 revisit pairs 再做 macro average？
> - A: pairs 平均是把同一场景的重复访问配对合并为单个观测（协议写法见本段原文）；macro average 让各样本等权，避免样本量大的子集主导子集级结论。后一句的"动机"是基于协议写法的推断，原文未明言设计意图。
> - Q: 三种推理模式具体指什么？
> - A: 本段只说"三种被评估的推理模式"并以 Table 11 承载，未在本小节列出模式名称，需回到正文相应小节确认，属待证项。

The subset-level results confirm that the aggregate improvements are not driven by a particular subset. Although the gain magnitude varies across subsets and refinement modes, HLA-WM improves all three revisit-consistency metrics in every evaluated MBench-A setting.



> 💡 **论证作用批读**: 这段是上一段的补充论证，作用是排除"总体提升由某一子集单独拉动"的可能。因为主结果只在总体层面报告，审稿人自然会怀疑增益集中在某个容易的子集上，作者用子集级结果来回应。注意措辞是 "not driven by a particular subset"，属于定性排除，并未给出各子集对总增益的定量贡献分解。
> 
> 💡 **变量与限定条件批读**: 作者明确承认增益幅度（gain magnitude）在子集之间、以及在不同 refinement modes 之间会变化，但主张方向一致（all three revisit-consistency metrics、every evaluated MBench-A setting）。因此本文在此处的强主张是"符号/方向一致性"，并非"幅度一致性"；阅读时不宜把前者读成后者，否则会超出原文证据。
> 
> 💡 **Q&A 批注记录**:
> - Q: "12 个组合全部改善"能推出什么结论？
> - A: 只能推出方向一致性（每个 mode–subset 上三项 revisit-consistency 指标均朝有利方向变化）。推不出提升幅度均匀，也推不出统计显著性——本小节未提供方差、置信区间或显著性检验，Table 11 的数值也未随本段给出。
> - Q: 这里的 revisit-consistency 指标与一般生成质量指标是一回事吗？
> - A: 原文用词是 revisit-consistency（PSNR/SSIM/LPIPS 三个指标），限定在重复访问一致性语境下；本小节没有涉及其他评测维度，不应外推为整体生成质量或通用基准分数。

