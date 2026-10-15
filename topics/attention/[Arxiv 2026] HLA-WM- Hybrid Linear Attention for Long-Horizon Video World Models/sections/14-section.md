[← 返回 README](../README.md)

## A.3 Component Ablation

Table 3 evaluates the major components of HLA-WM with K = 1 on the Hard-Trajectory split. Here, $^ { 6 6 } \mathrm { W / o }$ Select $A ^ { \mathfrak { s } }$ retains the transition terms $A _ { i }$ of all historical chunks while selectively composing their write terms, whereas $^ { 6 6 } \mathrm { W / o }$ Select $B ^ { \ast }$ retains all historical write terms $B _ { i }$ while selectively composing the transitions. Removing the sink degrades both revisit consistency and camera controllability, highlighting the importance of persistent global context. Retaining the transition terms of unselected chunks substantially reduces PSNR and SSIM and increases camera errors, supporting our observation that irrelevant intermediate transitions contribute to long-range forgetting. Similarly, retaining all historical write terms consistently degrades both revisit fidelity and camera controllability, indicating that selective composition is beneficial for both transition and write components of the recurrent memory. Removing the turn-aware recent context also reduces revisit consistency and worsens camera control, confirming its role in preserving local continuity under abrupt camera motion. Overall, the full HLA-WM achieves the best performance across all six metrics, demonstrating the complementary contributions of selective state composition, persistent sink memory, and adaptive recent context.



> 💡 **机制拆解与消融解读**: 本段是 Table 3 的文字解读，前提条件必须记住：$K=1$，且评测数据为 Hard-Trajectory split。先厘清两个变体的定义（这是最容易读反的地方）：
> - **w/o Select A**：保留**全部**历史 chunk 的 transition term $A_i$，但对 write term 做选择性组合；
> - **w/o Select B**：保留**全部**历史 write term $B_i$，但对 transition 做选择性组合。
> 
> 三条证据链分别是：
> 1. **w/o Sink**：移除持久全局上下文 → revisit consistency 与 camera controllability 同时变差，说明长期全局记忆锚点是必要的。
> 2. **w/o Select A**：保留未选中 chunk 的 transition → 论文原文用词是 *substantially* 降低 PSNR/SSIM 并抬高相机误差，且原文把这一现象与“无关的中间 transition 造成 long-range forgetting”这一观察挂钩。注意：**原文的 long-range forgetting 论断只针对 transition**，把它同样套到 write term 上属于批注者推断，不是原文结论。
> 3. **w/o Select B**：保留全部历史 write term → 原文只说 *consistently degrades* revisit fidelity 与 camera controllability（Table 3 确实显示 PSNR/SSIM 下降），措辞强度弱于 Select A。
> 4. **w/o Turn-aware**：移除转向感知近期上下文 → revisit consistency 下降、相机控制变差，原文解释是它在相机突变时维持局部连续性。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 为什么保留未选中 chunk 的 transition 会掉点？
> > - A: 原文（本节第 1 段）给出的解释是：无关的中间 transition 会助长 long-range forgetting。至于“引入噪声干扰当前帧生成”这一具体机制，原文并未展开，属于合理但未证实的推断。
> > - Q: Select A 和 Select B 哪个更关键？
> > - A: 从原文用词强度看 Select A（transition）被描述为 substantially 掉点，Select B 只说 consistently degrade；但两者都劣于完整模型。这说明“选择性组合”对 transition 与 write 两个分量都成立，而非只对其中之一有效。

Algorithm 1 Overall Inference Procedure of HLA-WM   
Require: Prescribed camera trajectory, retrieval budget K, and turn threshold τ<sub>turn</sub>   
1: Compute pairwise geometric overlap scores $R ( i , j )$ using Eq. 21   
2: Initialize the chunk-wise GDN summary bank and streaming states   
3: for query chunk q = 0, . . . , C − 1 do   
4: Compute the recent-history budget $r _ { q }$ using Eqs. 23–24   
5: if insuficient history for selective retrieval then   
6: $\mathcal { T } _ { q } \gets \{ 0 , \dots , q - 1 \}$   
7: else   
8: Select historical chunks $\mathcal { T } _ { q }$ according to Eq. 13   
9: Sort $\mathcal { T } _ { q }$ in chronological order   
10: Gather the cached afine summaries of chunks in $\mathcal { T } _ { q }$   
11: Recompose the query-specific historical state $S _ { q } ^ { \mathrm { h i s t } }$   
12: Replace the main-GDN recurrent state with $S _ { q } ^ { \mathrm { { h i s t } } }$   
13: end if   
14: Generate chunk q using the resulting streaming cache   
15: Perform the t = 0 cache-writing forward pass   
16: Cache the afine summary of chunk q   
17: Update camera-GDN and local convolutional states chronologically   
18: end for



> 💡 **算法流程批读**: Algorithm 1 给出 HLA-WM 的整体推理流程。需要说明的是，该算法按 MinerU 的解析顺序出现在本节中，但描述的是整体 inference procedure，而非仅服务于 A.3 的消融分析。
> 
> 流程按原文逐步对应：
> 1. **输入/前置**：预设相机轨迹、检索预算 $K$、转向阈值 $\tau_{turn}$；第 1 步用 Eq. 21 计算成对几何重叠分数 $R(i,j)$；第 2 步初始化 chunk-wise GDN summary bank 与 streaming states。
> 2. **逐 query chunk 循环**（$q=0,\dots,C-1$）：第 4 步用 Eqs. 23–24 计算近期历史预算 $r_q$。
> 3. **分支**：第 5 步判断历史是否不足以做 selective retrieval；不足则第 6 步直接取 $\mathcal{T}_q \gets \{0,\dots,q-1\}$；足够则第 8 步按 Eq. 13 选历史 chunk，第 9 步按时间排序。
> 4. **状态重组（核心）**：第 10 步取出 $\mathcal{T}_q$ 的 cached affine summaries，第 11 步重组 query-specific historical state $S_q^{\mathrm{hist}}$，第 12 步用它**替换 main-GDN 的 recurrent state**——这一步等价于“按检索结果动态注入记忆”。
> 5. **生成与写回**：第 14 步用更新后的 streaming cache 生成 chunk $q$；第 15 步做 $t=0$ 的 cache-writing forward pass；第 16 步缓存本 chunk 的 affine summary；第 17 步按时间顺序更新 camera-GDN 与局部卷积状态。
> 
> 注：原文 OCR 中出现的 “insuficient”“afine” 为识别误差，对应 insufficient / affine。
> 
> > 💡 **可追问点**: 第 11 步 “Recompose the query-specific historical state $S_q^{\mathrm{hist}}$” 的具体数学形式原文未在此算法块中给出，需结合 Eq. 13 附近正文与附录确认是加权平均还是注意力式聚合。**待证**。

Table 3 Component ablation on the Hard-Trajectory split using Stage 1 outputs. Best results are shown in bold.
<table><tr><td rowspan="2">Variant</td><td colspan="3">Revisit Consistency</td><td colspan="3">Camera Control</td></tr><tr><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td>HLA-WM</td><td>10.11</td><td>0.1997</td><td>0.5848</td><td>15.0887</td><td>1.8388</td><td>1.9482</td></tr><tr><td>w/o Sink</td><td>9.71</td><td>0.1867</td><td>0.6019</td><td>15.9351</td><td>1.9326</td><td>2.0531</td></tr><tr><td>w/o Select A</td><td>9.61</td><td>0.1887</td><td>0.5901</td><td>15.2426</td><td>1.8552</td><td>2.0562</td></tr><tr><td>w/o Select B</td><td>9.64</td><td>0.1892</td><td>0.5921</td><td>15.2326</td><td>1.9830</td><td>2.0564</td></tr><tr><td>w/o Turn-aware</td><td>9.96</td><td>0.1917</td><td>0.5876</td><td>15.1784</td><td>2.1096</td><td>1.9899</td></tr></table>



> 💡 **Table 3 逐数字核对**（Hard-Trajectory split，Stage 1 输出，加粗为最优）：
> 
> | 变体 | PSNR↑ | SSIM↑ | LPIPS↓ | RotErr↓ | TransErr↓ | CamMC↓ |
> |---|---|---|---|---|---|---|
> | HLA-WM | **10.11** | **0.1997** | **0.5848** | **15.0887** | **1.8388** | **1.9482** |
> | w/o Sink | 9.71 | 0.1867 | 0.6019 | 15.9351 | 1.9326 | 2.0531 |
> | w/o Select A | 9.61 | 0.1887 | 0.5901 | 15.2426 | 1.8552 | 2.0562 |
> | w/o Select B | 9.64 | 0.1892 | 0.5921 | 15.2326 | 1.9830 | 2.0564 |
> | w/o Turn-aware | 9.96 | 0.1917 | 0.5876 | 15.1784 | 2.1096 | 1.9899 |
> 
> 关键读数（注意易混淆项）：
> - 完整 HLA-WM 在六项指标上全部最优，与正文“best performance across all six metrics”一致。
> - PSNR 最低的是 **w/o Select A（9.61）**。
> - w/o Select A 的 TransErr 是 **1.8552**、CamMC 是 **2.0562**；**1.9326 与 2.0531 都属于 w/o Sink，不可移到 Select A 名下**。
> - LPIPS 最差的是 **w/o Sink（0.6019）**；w/o Select B 的 LPIPS 是 **0.5921**，不是 0.5901（0.5901 属于 w/o Select A）。
> - w/o Turn-aware 的 TransErr 2.1096 与 CamMC 1.9899 是两个反例：它在平移误差上最差，但 CamMC 优于 Sink/A/B 三个变体——即“某项指标最差”不等于“所有指标都最差”。
> 
> > 💡 **指标口径批读**: Revisit Consistency 用 PSNR/SSIM/LPIPS（保真度 + 感知质量），Camera Control 用 RotErr/TransErr/CamMC（几何误差）。消融的结论强度应按方向分别陈述：Select 类变体主要损伤 revisit fidelity，Sink 与 Turn-aware 的移除则同时损伤两类指标。

