[← 返回 README](../README.md)

## A.5 Number of Retrieved Historical Chunks

We study the efect of the retrieval budget K on the Hard-Trajectory split using Stage 1 outputs. All variants use the same sink memory and turn-aware recent-context strategy, difering only in the number of historical chunks retrieved by geometric overlap. As shown in Table 6, performance remains broadly comparable across diferent retrieval budgets, and increasing K does not consis tently improve either revisit fidelity or camera controllability. Notably, K = 1 achieves the best PSNR, SSIM, and LPIPS while using the smallest retrieval budget. Larger values provide modest gains in some camera-control metrics, with K = 5 achieving the lowest RotErr and K = 4 the lowest TransErr and CamMC. These results indicate that a single highly relevant historical chunk is often suficient for efective long-range recall. We therefore adopt K = 1 as the default setting for its favorable trade-of between retrieval quality and eficiency.



> 💡 **问题动机与实验设计**: 本段要回答的是“检索多少历史块 K 才合适”。设计上做了干净的单变量控制：所有变体共享同一 sink memory 与 turn-aware recent-context 策略，唯一差异是“按几何重叠检索的历史块数量”。因此后续差异可归因于 K 本身。
> 
> **结论与数字核对**: 原文的结论是三层——(1) 性能在不同 K 下 broadly comparable，增大 K 并不一致地提升 revisit fidelity 或 camera controllability；(2) K=1 取得最佳 PSNR/SSIM/LPIPS（对应 Table 6 的 10.11 / 0.1997 / 0.5848），同时检索预算最小；(3) 更大 K 只在部分相机控制指标上有 modest gains，其中 K=5 最低 RotErr、K=4 最低 TransErr 与 CamMC。作者据此采用 K=1。
> 
> **推断界限**: 原文对“为什么 K=1 就够”只给出定性解释——“a single highly relevant historical chunk is often sufficient for effective long-range recall”；它并未解释大 K 变差的原因。因此“过大检索窗口会引入噪声或稀释关键信息”属于本文之外的推断，不能写成原文结论。同样，原文只说 K=1 在 retrieval quality 与 efficiency 之间有 favorable trade-off，并未给出效率的具体数值，“显著降低计算开销”缺乏数据支撑。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 这一节到底改变了哪个中间变量？
> > - A: 只改变“从历史中按几何重叠检索出来的 chunk 数量 K”，sink memory 与 turn-aware recent-context 策略保持不变；这使效果差异可归因于 K，而非上下文构造方式。

Table 5 Ablation of GDN-state recomposition and KV-cache alignment on MBench-A under Top-1 retrieval. Best results within each inference setting are shown in bold.
<table><tr><td>Pipeline</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td></tr><tr><td rowspan="4">Stage 1</td><td>Baseline</td><td>10.17</td><td>0.2545</td><td>0.6251</td></tr><tr><td>GDN-only</td><td>10.61</td><td>0.2626</td><td>0.6052</td></tr><tr><td>KV-only</td><td>10.43</td><td>0.2606</td><td>0.6091</td></tr><tr><td>HLA-WM</td><td>11.01</td><td>0.2848</td><td>0.5823</td></tr><tr><td rowspan="4">Causal AR Refine</td><td>Baseline</td><td>12.36</td><td>0.2996</td><td>0.6554</td></tr><tr><td>GDN-only</td><td>12.57</td><td>0.3064</td><td>0.6492</td></tr><tr><td>KV-only</td><td>12.35</td><td>0.3016</td><td>0.6547</td></tr><tr><td>HLA-WM</td><td>12.73</td><td>0.3122</td><td>0.6447</td></tr><tr><td rowspan="4">Bidirectional Refine</td><td>Baseline</td><td>12.94</td><td>0.3310</td><td>0.5597</td></tr><tr><td>GDN-only</td><td>13.28</td><td>0.3389</td><td>0.5477</td></tr><tr><td>KV-only</td><td>13.04</td><td>0.3325</td><td>0.5563</td></tr><tr><td>HLA-WM</td><td>13.52</td><td>0.3431</td><td>0.5470</td></tr></table>



> 💡 **Table 5 批读（表格归属与证据边界）**: 该表 caption 明确为 “Ablation of GDN-state recomposition and KV-cache alignment on MBench-A under Top-1 retrieval”，即它考察的是 GDN-state 重组与 KV-cache 对齐这两类机制，评测集是 MBench-A，检索设定为 Top-1——与本节主题（K 的数量）不是同一组消融。它出现在附录 A.5 中，依据现有正文与 caption 只能说明它与 K 的消融无直接对应关系，不能断言是“误置”。本段也没有配套正文解读，因此表格含义只能依据 caption 与数值判断。
> 
> **数值核对**: 三个 pipeline（Stage 1 / Causal AR Refine / Bidirectional Refine）下，HLA-WM 的 PSNR/SSIM/LPIPS 分别为 11.01 / 0.2848 / 0.5823、12.73 / 0.3122 / 0.6447、13.52 / 0.3431 / 0.5470，均优于同行的 Baseline、GDN-only、KV-only。但要注意一个例外：在 Causal AR Refine 行，KV-only 的 PSNR 为 12.35，略低于 Baseline 的 12.36，说明“KV-cache 对齐单独使用总是带来增益”并不成立；而 Bidirectional Refine 行 KV-only 的 SSIM 0.3325 也低于 Baseline 的 0.3310（略高，LPIPS 0.5563 优于 0.5597），整体趋势是 GDN-only 增益大于 KV-only。
> 
> **推断界限**: HLA-WM 在各设定一致最优，可支撑“两类机制联合使用有效”的推断；但“验证了架构本身的鲁棒性”“联合优化”属于从表格推出的解读，原文本节并未给出对应论述，宜标注为推断。

Table 6 Ablation on the retrieval budget K on the Hard-Trajectory split using Stage 1 outputs. Best results are shown in bold.
<table><tr><td rowspan="2">K</td><td colspan="3">Revisit Consistency</td><td colspan="3">Camera Control</td></tr><tr><td>PSNR ↑</td><td>SSIM↑</td><td>LPIPS↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td>1</td><td>10.11</td><td>0.1997</td><td>0.5848</td><td>15.0887</td><td>1.8388</td><td>1.9482</td></tr><tr><td>3</td><td>10.06</td><td>0.1973</td><td>0.5865</td><td>14.1537</td><td>1.7658</td><td>1.8661</td></tr><tr><td>4</td><td>10.00</td><td>0.1936</td><td>0.5885</td><td>12.2795</td><td>1.6985</td><td>1.7832</td></tr><tr><td>5</td><td>9.89</td><td>0.1886</td><td>0.5915</td><td>11.5435</td><td>1.7200</td><td>1.7970</td></tr></table>



> 💡 **Table 6 批读（直接支撑 K 选择的证据）**: 本表是 A.5 唯一与 K 直接对应的证据，caption 为 “Ablation on the retrieval budget K on the Hard-Trajectory split using Stage 1 outputs”。
> 
> **数字核对（K=1→5）**: 保真度侧——PSNR 10.11 → 10.06 → 10.00 → 9.89（单调下降），SSIM 0.1997 → 0.1973 → 0.1936 → 0.1886（单调下降），LPIPS 0.5848 → 0.5865 → 0.5885 → 0.5915（单调上升即变差）。相机控制侧——RotErr 15.0887° → 14.1537° → 12.2795° → 11.5435°（单调改善）；TransErr 1.8388 → 1.7658 → 1.6985 → 1.7200（K=4 最低，K=5 反而回升）；CamMC 1.9482 → 1.8661 → 1.7832 → 1.7970（同样 K=4 最低，K=5 回升）。这验证了正文“K=5 最低 RotErr、K=4 最低 TransErr 与 CamMC”的表述，也说明相机控制指标随 K 增大并非单调改善，而保真度指标随 K 增大是单调变差的。
> 
> **推断界限**: “这种权衡解释了为何选择 K=1：优先保证画面一致性，因为相机误差可通过其他机制补偿，而视觉伪影更难修复”——这句解释在原文中没有任何对应论述，属于超出证据的推断，不能当作论文主张。可支持的只是：K=1 在保真度上最优、预算最小，且相机控制损失的幅度相对有限（RotErr 相差约 3.5°，TransErr 相差约 0.14）。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 增大 K 带来的相机控制收益是否稳定？
> > - A: 不稳定。RotErr 随 K 单调改善，但 TransErr 与 CamMC 在 K=4 触底后于 K=5 回升，因此正文用 “modest gains in some camera-control metrics” 而非“整体改善”。

