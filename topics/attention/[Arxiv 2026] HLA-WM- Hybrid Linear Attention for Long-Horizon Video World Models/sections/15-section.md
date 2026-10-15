[← 返回 README](../README.md)

## A.4 GDN-State Recomposition and KV-Cache Alignment

We further isolate the roles of GDN-state recomposition and softmax KV-cache alignment under the Top-1 retrieval setting. GDN-only applies selective recomposition to the main GDN memory while retaining the native softmax KV history. KV-only applies the selected history only to the softmax KV cache while keeping the native GDN states. The full HLA-WM applies the same retrieved history to both memory pathways. Tables 4 and 5 report the results on SANA-WM-Bench and MBench-A, respectively.



> 💡 **AI 批注**
> **实验设置拆解**: 本节在 Top-1 检索设定下隔离 HLA-WM 的两个组件：**GDN 状态重组**（作用于递归记忆）与 **softmax KV 缓存对齐**（作用于注意力上下文）。对比含四个 pipeline：Baseline、`GDN-only`（只对主 GDN 记忆做选择性重组，保留原生 softmax KV 历史）、`KV-only`（只把检索历史用于 softmax KV 缓存，保持原生 GDN 状态）、`HLA-WM`（把同一检索历史同时用于两条记忆路径）。
> **动机（解读，非原文原话）**: 作者要回答“两条记忆路径各自贡献什么、是否必须同时对齐”；原文的表述仅为 isolate the roles。结果分别报在 SANA-WM-Bench（Table 4）与 MBench-A（Table 5），本次源块只含 Table 4。

The two memory pathways play complementary but asymmetric roles. At Stage 1, GDN-only already yields substantial gains in revisit consistency on both SANA-WM-Bench splits, whereas KV-only provides comparatively smaller improvements. Their combination further improves long-range recall, with the full HLA-WM achieving the best PSNR, SSIM, and LPIPS on both splits. KV alignment additionally improves camera stability, although its efect varies across trajectory types. Under causal AR refinement, the full method achieves the strongest revisit performance on both splits and improves all six metrics on Hard trajectories. These results indicate that GDN-state recomposition is the primary source of long-range memory improvement, while KV-cache alignment provides complementary stabilization of the attention-based context.



> 💡 **AI 批注**
> **机制洞察——非对称互补**:
> - **GDN 主导长程回忆**: Stage 1 中 `GDN-only` 已在两个 split 的重访一致性上带来显著提升（Simple：PSNR 9.45 vs Baseline 9.18；Hard：9.78 vs 9.37），说明修正递归状态直接缓解长距离遗忘。
> - **KV 提供稳定性**: `KV-only` 在视觉指标上提升较小，但对相机稳定性有额外贡献，且效果随轨迹类型波动。
> - **组合效应**: `HLA-WM` 在两个 split 上取得最佳 PSNR/SSIM/LPIPS；在因果 AR 细化下取得最强重访性能，并在 Hard 轨迹上改善全部六项指标。注意原文措辞是“最强重访性能 + Hard 六项指标改善”，并未说因果 AR 下全面超越基线。
> **结论**: GDN 状态重组是长程记忆改进的主要来源，KV 缓存对齐是对注意力上下文的补充稳定化手段。

Table 4 Ablation of GDN-state recomposition and KV-cache alignment on SANA-WM-Bench under Top-1 retrieval. Best results within each split and inference setting are shown in bold.
<table><tr><td>Pipeline</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td colspan="8">Simple-Trajectory Split</td></tr><tr><td rowspan="4">Stage 1</td><td>Baseline</td><td>9.18</td><td>0.1729</td><td>0.6327</td><td>19.7040</td><td>2.2625</td><td>2.3974</td></tr><tr><td>GDN-only</td><td>9.45</td><td>0.1812</td><td>0.6219</td><td>17.9934</td><td>2.2832</td><td>2.4083</td></tr><tr><td>KV-only</td><td>9.50</td><td>0.1866</td><td>0.6149</td><td>14.4283</td><td>1.9569</td><td>2.0556</td></tr><tr><td>HLA-WM</td><td>9.91</td><td>0.1999</td><td>0.6049</td><td>13.5864</td><td>2.0928</td><td>2.1798</td></tr><tr><td rowspan="4">Stage 1 + AR Refine</td><td>Baseline</td><td>14.44</td><td>0.2770</td><td>0.5738</td><td>20.9315</td><td>2.1587</td><td>2.3121</td></tr><tr><td>GDN-only</td><td>14.65</td><td>0.2784</td><td>0.5687</td><td>21.2344</td><td>2.3596</td><td>2.5147</td></tr><tr><td>KV-only</td><td>13.88</td><td>0.2703</td><td>0.5812</td><td>20.9084</td><td>2.2087</td><td>2.3662</td></tr><tr><td>HLA-WM</td><td>14.82</td><td>0.2872</td><td>0.5682</td><td>20.2933</td><td>2.1910</td><td>2.3240</td></tr><tr><td rowspan="4">Stage 1 + Bi. Refine</td><td>Baseline</td><td>13.04</td><td>0.2945</td><td>0.5786</td><td>11.3848</td><td>1.9893</td><td>2.0545</td></tr><tr><td>GDN-only</td><td>13.26</td><td>0.2998</td><td>0.5746</td><td>10.7564</td><td>1.8627</td><td>1.9310</td></tr><tr><td>KV-only</td><td>13.17</td><td>0.2999</td><td>0.5762</td><td>9.6939</td><td>1.8866</td><td>1.9417</td></tr><tr><td>HLA-WM</td><td>13.56</td><td>0.3148</td><td>0.5614</td><td>8.8823</td><td>1.8637</td><td>1.9127</td></tr><tr><td colspan="8">Hard-Trajectory Split</td></tr><tr><td rowspan="4">Stage 1</td><td>Baseline</td><td>9.37</td><td>0.1774</td><td>0.6044</td><td>20.3759</td><td>1.9952</td><td>2.1507</td></tr><tr><td>GDN-only</td><td>9.78</td><td>0.1890</td><td>0.5929</td><td>20.0838</td><td>1.9502</td><td>2.1075</td></tr><tr><td>KV-only</td><td>9.52</td><td>0.1805</td><td>0.6000</td><td>13.3992</td><td>1.7635</td><td>1.8616</td></tr><tr><td>HLA-WM</td><td>10.11</td><td>0.1997</td><td>0.5848</td><td>15.0887</td><td>1.8388</td><td>1.9482</td></tr><tr><td rowspan="4">Stage 1 + AR Refine</td><td>Baseline</td><td>14.12</td><td>0.2719</td><td>0.5621</td><td>25.6330</td><td>2.0099</td><td>2.2079</td></tr><tr><td>GDN-only</td><td>14.35</td><td>0.2797</td><td>0.5576</td><td>23.1860</td><td>1.9519</td><td>2.1296</td></tr><tr><td>KV-only</td><td>13.54</td><td>0.2599</td><td>0.5721</td><td>24.1145</td><td>2.0425</td><td>2.2302</td></tr><tr><td>HLA-WM</td><td>14.47</td><td>0.2812</td><td>0.5557</td><td>22.0165</td><td>1.9351</td><td>2.1043</td></tr><tr><td rowspan="4">Stage 1 + Bi. Refine</td><td>Baseline</td><td>13.05</td><td>0.2972</td><td>0.5468</td><td>13.1713</td><td>1.6649</td><td>1.7694</td></tr><tr><td>GDN-only</td><td>13.49</td><td>0.3304</td><td>0.5601</td><td>33.0406</td><td>2.1843</td><td>2.4584</td></tr><tr><td>KV-only HLA-WM</td><td>13.26</td><td>0.3047</td><td>0.5469 0.5384</td><td>11.4688 10.5268</td><td>1.5860 1.5449</td><td>1.6696</td></tr><tr><td></td><td>13.51</td><td>0.3142</td><td></td><td></td><td></td><td>1.6230</td></tr></table>



> 💡 **AI 批注**
> **Table 4 数据解读（SANA-WM-Bench，Top-1 检索）**:
> - **Simple-Trajectory**:
>   - Stage 1: HLA-WM PSNR 9.91 vs Baseline 9.18；`KV-only` 的 RotErr 14.43 / CamMC 2.06 优于 `GDN-only` 的 17.99 / 2.41，印证 KV 对齐对几何一致性的帮助。
>   - Bi. Refine: HLA-WM 取得最高 PSNR 13.56、最高 SSIM 0.3148、最低 LPIPS 0.5614、最低 RotErr 8.88、最低 CamMC 1.9127；但 **TransErr 最低的是 GDN-only（1.8627），HLA-WM 为 1.8637**，因此不能写成“所有指标最优”。
> - **Hard-Trajectory**:
>   - Stage 1: `GDN-only` PSNR 9.78 > `KV-only` 9.52，支持 GDN 对视觉质量的主导作用。
>   - Bi. Refine: `GDN-only` 的 RotErr 飙升至 33.04°（Baseline 13.17°），且其 SSIM 最高（0.3304 > HLA-WM 0.3142）；HLA-WM 把 RotErr 拉回 **10.53°** 并取得最佳 PSNR/LPIPS/三项相机指标——与正文“最佳 PSNR、LPIPS 与三个相机控制指标”的表述一致，正文并未宣称 SSIM 最优。
> - **数据可靠性**: 末两行 OCR 错位，`KV-only` 与 `HLA-WM` 被并入同一 `<tr>`，数值需按列拆分还原为 KV-only（13.26 / 0.3047 / 0.5469 / 11.4688 / 1.5860 / 1.6696）与 HLA-WM（13.51 / 0.3142 / 0.5384 / 10.5268 / 1.5449 / 1.6230）；该还原属推断，建议对照 PDF 原表复核。

The bidirectional setting further illustrates the interaction between the two pathways. On Hard trajectories, GDN-only substantially improves SSIM but increases RotErr from 13.17<sup>◦</sup> to 33.04<sup>◦</sup>. This suggests that modifying only the recurrent history can create a mismatch with the accompanying softmax context. Applying the same historical selection to the KV cache restores consistency between the two memory pathways, and the full method achieves the best PSNR, LPIPS, and all three camera-control metrics. Thus, KV alignment is particularly important for maintaining stable generation when the recurrent history is selectively recomposed.



> 💡 **AI 批注**
> **关键发现——路径失配风险**:
> 在 Hard 轨迹的双向细化设置中，`GDN-only` 虽把 SSIM 提到最高（0.3304，高于 HLA-WM 的 0.3142），却使旋转误差 RotErr 从 Baseline 的 13.17° 剧增至 33.04°。
> **原因（原文解释）**: 只修改递归历史而未同步更新注意力上下文，会造成两种记忆机制之间失配，进而破坏几何稳定性。
> **解决**: 把同一历史选择同步应用到 KV 缓存后，`HLA-WM` 恢复两路径一致性，并在该设置下取得最佳 PSNR、LPIPS 与三项相机指标（RotErr 10.53°、TransErr 1.5449、CamMC 1.6230）。这说明 KV 对齐在递归历史被选择性重组时对维持稳定生成尤为关键。
> **注意**: 此处原文未宣称 SSIM 最优，概括时不要写成“全部指标最优”。

MBench-A exhibits an even clearer pattern. Across Stage 1, causal AR refinement, and bidirectional refinement, GDN-only consistently provides larger gains than KV-only, while the full HLA-WM achieves the best PSNR, SSIM, and LPIPS in every setting. This confirms that directly modifying the recurrent GDN memory is the main driver of improved long-range recall, whereas KV-cache alignment alone provides limited gains. Combining the two yields the strongest and most consistent overall performance.



> 💡 **AI 批注**
> **MBench-A 结果佐证**:
> 1. **GDN > KV**: 在 Stage 1、因果 AR 细化与双向细化三种设置下，`GDN-only` 的提升幅度一致地大于 `KV-only`，支持“递归 GDN 记忆是长程召回核心驱动”的判断。
> 2. **Full > Parts**: `HLA-WM` 在每一设置下取得最佳 PSNR/SSIM/LPIPS，组合带来最强且最一致的总体性能。
> **边界**: 本段结论对应的 Table 5 未出现在所提供的源块中，具体数值无法在本次核对中验证，只能依据正文转述，待补充表格后再确认。

