[← 返回 README](../README.md)

## A.9.2 Stage 1 + AR Refine

Figure 8 provides additional qualitative comparisons under causal AR refinement. Across all examples, HLA-WM better preserves previously observed scene content when the camera revisits the same region after an extended rollout. In the first example, the baseline substantially changes the distant skyline and foreground layout upon revisit, whereas HLA-WM better preserves the overall scene structure and characteristic background content. In the second example, the baseline noticeably alters the foreground geometry and loses much of the original structural layout, while HLA-WM better maintains the major scene elements and their spatial arrangement. In the third example, the baseline largely loses the distinctive foreground structure and changes the arrangement of surrounding objects, whereas HLA-WM more faithfully preserves these characteristic elements and their relative geometry. In the fourth example, HLA-WM better maintains the spatial arrangement of tables, people, and illuminated structures, while the baseline exhibits substantial changes in the revisited scene. Overall, these examples demonstrate improved preservation of both global scene structure and distinctive visual content over long-range revisits.



> 💡 **Figure 8 批读（定性证据）**: 本段是 Figure 8 的文字说明，四个案例共同的结构是「相机长程重访同一区域」后对比基线与 HLA-WM 的场景保持度：①基线改变远景天际线与前景布局；②基线改变前景几何并丢失结构布局；③基线丢失特征性前景结构、改变周边物体排布；④基线重访场景发生大幅变化，而 HLA-WM 维持桌子、人物、发光结构的空间排布。
> 
> > 💡 **证据边界（AI 批注）**: 需要区分三个层次。(1) 直接陈述：本节比较是在 causal AR refinement 设定下进行的，HLA-WM 在重访时对全局结构与特征内容的保持更好——这是原文明确写的。(2) 合理推断：这些案例可用来支持「AR 精修缓解长程漂移」这一机制性解释，但原文只说 outcome，没有给出漂移度量或误差分解，因此「缓解累积误差」应标为推断。(3) 无法核对：本次仅拿到正文文字与 caption，未获得 Figure 8 的图像视觉信息，四例的具体画面差异（天际线、前景几何等）只能转述原文描述，不能声称我独立看图核验过。此外，本段未出现基线名称，草稿用「基线」指代与原文一致，未越界。原文「over long-range revisits」对应草稿「长程重访」，属准确对应。

Table 10 Category-level results on SANA-WM-Bench after bidirectional refinement. Best results within each category are shown in bold.
<table><tr><td>Category</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td colspan="8">Simple-Trajectory Split</td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>13.74 14.20</td><td>0.3351 0.3553</td><td>0.5485 0.5377</td><td>9.7648 6.8672</td><td>1.7510 1.8842</td><td>1.8012 1.9156</td></tr><tr><td>Indoor</td><td>SANA-WM HLA-WM</td><td>12.67 13.03</td><td>0.3174 0.3401</td><td>0.6045 0.5980</td><td>21.6798 16.1626</td><td>1.4978 1.0450</td><td>1.6675 1.1800</td></tr><tr><td>Outdoor City</td><td>SANA-WM HLA-WM</td><td>12.79 13.05</td><td>0.2479 0.2676</td><td>0.5767 0.5644</td><td>7.1726 6.6284</td><td>1.7286</td><td>1.7495</td></tr><tr><td>Outdoor Nature</td><td>SANA-WM HLA-WM</td><td>12.97</td><td>0.2776</td><td>0.5849</td><td>6.9223</td><td>1.6204 2.9796</td><td>1.6402 2.9997</td></tr><tr><td>Hard-Trajectory Split</td><td></td><td>13.94</td><td>0.2960</td><td>0.5456</td><td>5.8709</td><td>2.9053</td><td>2.9150</td></tr><tr><td colspan="8"></td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>13.75 14.55</td><td>0.3443</td><td>0.5140</td><td>8.1256</td><td>1.4874</td><td>1.5398</td></tr><tr><td>Indoor</td><td>SANA-WM</td><td>12.96</td><td>0.3797 0.3148</td><td>0.4956 0.5846</td><td>9.1194 22.9948</td><td>1.5393 0.9594</td><td>1.5961 1.2115</td></tr><tr><td></td><td>HLA-WM</td><td>12.98</td><td>0.3248</td><td>0.5774</td><td>14.8387</td><td>0.7795</td><td>0.9455</td></tr><tr><td>Outdoor City</td><td>SANA-WM</td><td>12.46</td><td>0.2631</td><td>0.5644</td><td>13.5222</td><td>1.6371</td><td>1.7186</td></tr><tr><td></td><td>HLA-WM</td><td>12.89</td><td>0.2822</td><td>0.5585</td><td>11.4256</td><td>1.5799</td><td>1.6459</td></tr><tr><td>Outdoor Nature</td><td>SANA-WM</td><td>13.03</td><td>0.2667</td><td>0.5242</td><td>8.0424</td><td>2.5758</td><td>2.6078</td></tr><tr><td></td><td>HLA-WM</td><td>13.61</td><td>0.2702</td><td>0.5220</td><td>6.7236</td><td>2.2810</td><td>2.3046</td></tr></table>



> 💡 **Table 10 批读（分类别定量）**: 表格按 Simple-Trajectory / Hard-Trajectory 两个划分，分 Game Style、Indoor、Outdoor City、Outdoor Nature 四类比较 SANA-WM 与 HLA-WM，指标为 PSNR↑、SSIM↑、LPIPS↓、RotErr↓、TransErr↓、CamMC↓。
> 
> > 💡 **逐项核对与修正（AI 批注）**:
> > - **保真/感知指标**：在所有 OCR 可读的配对上，HLA-WM 的 PSNR、SSIM 均更高、LPIPS 均更低（如 Hard Game Style 13.75→14.55、0.3443→0.3797、0.5140→0.4956；Simple Indoor 12.67→13.03、0.3174→0.3401、0.6045→0.5980）。草稿「普遍优于」在可读行内成立，但 LPIPS 并非只在 Indoor/Outdoor Nature 更低，而是每类都更低，草稿表述不准确。
> > - **位姿指标**：RotErr 多数下降（Simple Indoor 21.6798°→16.1626°，Hard Indoor 22.9948°→14.8387°），但**存在反例**：Hard-Trajectory 的 Game Style RotErr 从 8.1256 升到 9.1194，草稿「RotErr 显著降低」属过度概括，应改为「多数类别下降，Hard Game Style 例外」。
> > - **TransErr 混合**：Simple Game Style 1.7510→1.8842（变差）；Simple Outdoor Nature 一行 OCR 错位，SANA-WM 为 1.6204/1.6402，HLA-WM 对应值落在 2.9796 或 2.9053 一组中，无论怎样对齐都明显变差（接近翻倍），草稿只举 Game Style 且用「略有上升」低估了该反例。CamMC 同样涨跌互现。
> > - **归属限定（重要）**: Table 10 的 caption 写的是「after bidirectional refinement」，而本节标题是「Stage 1 + AR Refine」、Figure 8 段说的是「causal AR refinement」。草稿把 Table 10 直接描述为「双向精修后」是正确的（忠于 caption），但由此把结论写成「AR 精修阶段提升了长期时空一致性」就把双向精修的结果记到 AR 精修名下了，属于超出证据的推论；该表与 AR refine 的对应关系在给定文本中未说明，标为待证。
> > - **OCR 质量**: 表格 HTML 存在明显错位与缺格（Simple Outdoor City 的 HLA-WM TransErr/CamMC 缺失；「Hard-Trajectory Split」标签与 Outdoor Nature 数值串行），因此部分行-值配对属推断，草稿提示「排版混乱」是诚实且必要的。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 本表能不能支持「位姿估计更稳」这一 claim？
> > - A: 只能部分支持。RotErr 多数下降支持旋转估计更准，但存在一个反例；TransErr 在部分类别明显变差，说明绝对位置一致性未被稳定改善。若论文正文未讨论该分化，应视为见表即可、claim 需限定类别。

