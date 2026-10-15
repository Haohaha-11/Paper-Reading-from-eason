[← 返回 README](../README.md)

## A.9.3 Stage 1 + Bidirectional Refine

Figure 9 provides additional qualitative comparisons under bidirectional refinement. In the first example, the baseline exhibits noticeable drift in the corridor geometry upon revisit, including changes in the floor pattern, wall modules, and spatial arrangement of the side structures. In contrast, HLA-WM better preserves the corridor layout, perspective, and characteristic interior elements. In the second example, the baseline substantially alters the lake surface, shoreline, and surrounding landscape, with pronounced distortions appearing in the revisited view. HLA-WM more faithfully maintains the mountain silhouette, tree line, shoreline geometry, and their reflections on the water. In the third example, the baseline introduces a large wooden structure that is absent from the first visit and significantly changes the foreground composition. HLA-WM avoids this spurious structural change and better preserves the open-water layout, ice distribution, and distant mountain ridge. In the fourth example, the baseline shows considerable changes in the rooftop geometry, foreground structures, and distant skyline, whereas HLA-WM better retains their relative arrangement and the overall scene identity. Together, these examples further demonstrate that selective long-range memory retrieval improves the preservation of both global geometry and distinctive scene content during bidirectional refinement.



> 💡 **Figure 9 定性描述批读**:
> - **文本性质**: 本段是纯定性案例叙述，不含任何数值。结尾"selective long-range memory retrieval improves the preservation of both global geometry and distinctive scene content during bidirectional refinement"是作者自述主张，本段无指标支撑。
> - **四个案例的对应关系**: ①走廊——baseline 出现地板图案、墙体模块与侧向结构空间排布的漂移；②湖泊——水面、岸线与周边景观被大幅改写，重访视角出现明显畸变，HLA-WM 保持山形剪影、树线、岸线几何及其水面反射；③前景——baseline 引入首次访问中不存在的木质结构（凭空结构）并改变前景构图，HLA-WM 避免该结构性变化，保持开阔水面、冰面分布与远山山脊；④屋顶——baseline 的屋顶几何、前景结构与远景天际线变化明显，HLA-WM 更好保持其相对排布与场景身份。
> - **机制指向**: 这四例是为佐证双向细化下记忆检索对"全局几何 + 场景特异内容"的保持能力，而非提升新视角生成质量。
> - **待证问题**: 一致性提升是否以牺牲新视角多样性为代价——本段与 Table 11 均未给出多样性类指标（如 FVD），属未决问题。
> - **视觉信息缺失**: 未获得 Figure 9 的像素内容，以上全部依据正文文字，无法独立核实图像中漂移的具体形态。

![](../images/5c1de5e2041be1faf08fb95e223e49a5525a8327a877430818df4cb0e73fdad3.jpg)  
Figure 7 Additional qualitative comparisons for Stage 1 outputs. Each example shows the first visit, two intermediate views, and a later revisit along the same camera trajectory. Revisit consistency is evaluated between the first-visit and revisit frames within each method.



> 💡 **Figure 7 批读（Stage 1）**:
> - **图注事实**: 图注仅声明这是 Stage 1 输出的额外定性对比；每个案例包含首次访问、两个中间视图以及沿同一相机轨迹的后续重访，一致性在同一方法的首次访问帧与重访帧之间评估。
> - **可确认项**: 图为多案例多帧序列，"Stage 1"标识其属于未细化阶段。
> - **推断（图注未直接给出）**: 把它当作 Causal AR Refine / Bidirectional Refine 的"基准参照"是合理推断，但图注并未这样声明；初稿"此图通常用于……"属推测性表述，已改为明确标注的推断。
> - **视觉信息缺失**: 未获得图片像素内容，无法核对图中场景与方法排列。

![](../images/eace347ca2f4f4506bc07c042c2a40c522f2a3649335a979b6708b7986a268c5.jpg)  
Figure 8 Additional qualitative comparisons under causal AR refinement. Each example shows the first visit, two intermediate views, and a later revisit along the same camera trajectory. Revisit consistency is evaluated between the first-visit and revisit frames within each method.



> 💡 **Figure 8 批读（Causal AR Refine）**:
> - **图注事实**: 声明这是因果 AR 细化下的额外定性对比；帧组织与 Figure 7 一致（首次访问 + 两个中间视图 + 重访），一致性在各方法内部、首次访问帧与重访帧之间评估。
> - **推断（待证）**: "因果细化可利用历史帧减少漂移，但受限于因果性仍无法完全解决非相邻帧间的几何冲突"是由"causal"字面推出的机制假设，本小节正文与图注都未给出误差累积曲线或对应实验，应视为待证假设而非事实。
> - **视觉信息缺失**: 未获得图片像素内容，无法核实漂移/修正的具体表现。

![](../images/2e2c2162d53470b7cf565796abd3acd20b6656c47be1a1d27a065e76428941c6.jpg)  
Figure 9 Additional qualitative comparisons under bidirectional refinement. Each example shows the first visit, two intermediate views, and a later revisit along the same camera trajectory. Revisit consistency is evaluated between the first-visit and revisit frames within each method.



> 💡 **Figure 9 批读（Bidirectional Refine）**:
> - **图注事实**: Fig. 7/8/9 三张图的图注除细化模式外完全相同，均为"首次访问 + 两个中间视图 + 重访"的一致性对比；Figure 9 对应双向细化。
> - **与正文的关系**: 正文 Block 1 的四案例叙述即针对此图，图注本身不提供额外信息。
> - **编排观察**: 本节标题为"Stage 1 + Bidirectional Refine"，但连续放入 Fig. 7（Stage 1）、Fig. 8（Causal AR Refine）、Fig. 9（Bidirectional）三图，Table 11 亦同时覆盖三种模式，实际内容超出标题范围。
> - **推断（待证）**: "双向机制可同时利用过去与未来上下文，因而更好约束几何"属方法层推断；本节证据仅限图像对比与 Table 11，未做正/反向上下文消融。
> - **视觉信息缺失**: 未获得图片像素内容，无法核实走廊布局、水面反射、凭空结构是否如正文所述。

Table 11 Detailed Top-1 results on the four oficial MBench-A subsets under diferent inference modes. Best results within each subset and mode are shown in bold.
<table><tr><td>Subset</td><td>n</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td></tr><tr><td>Stage 1</td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>Causal</td><td>100</td><td>SANA-WM HLA-WM</td><td>10.4068 11.0022</td><td>0.28982 0.31830</td><td>0.65461 0.61659</td></tr><tr><td>Human</td><td>120</td><td>SANA-WM HLA-WM</td><td>9.5147</td><td>0.26845</td><td>0.67977</td></tr><tr><td>Environment</td><td>227</td><td>SANA-WM HLA-WM</td><td>10.2161 10.4140</td><td>0.30275 0.24056</td><td>0.63759 0.59436</td></tr><tr><td>Object</td><td>100</td><td>SANA-WM</td><td>11.4626 10.1755</td><td>0.27144 0.23386</td><td>0.54857 0.59953</td></tr><tr><td></td><td></td><td>HLA-WM</td><td>10.9412</td><td>0.25989</td><td>0.55808</td></tr><tr><td>Causal AR Refine</td><td></td><td>SANA-WM</td><td>12.4358</td><td></td><td></td></tr><tr><td>Causal</td><td>100</td><td>HLA-WM</td><td>12.6230</td><td>0.33422 0.34587</td><td>0.67586 0.66550</td></tr><tr><td>Human</td><td>120</td><td>SANA-WM HLA-WM</td><td>11.8551 12.2041</td><td>0.30453 0.31959</td><td>0.70110 0.68707</td></tr><tr><td>Environment</td><td>227</td><td>SANA-WM HLA-WM</td><td>12.6355 13.0913</td><td>0.29205 0.30450</td><td>0.62765 0.61762</td></tr><tr><td>Object</td><td>100</td><td>SANA-WM HLA-WM</td><td>12.2440</td><td>0.27644</td><td>0.64322</td></tr><tr><td>Bidirectional Refine</td><td></td><td></td><td>12.6283</td><td>0.28724</td><td>0.63458</td></tr><tr><td></td><td></td><td>SANA-WM</td><td>12.8197</td><td></td><td></td></tr><tr><td>Causal</td><td>100</td><td>HLA-WM</td><td>13.2118</td><td>0.36602 0.37457</td><td>0.58537 0.57395</td></tr><tr><td>Human</td><td>120</td><td>SANA-WM HLA-WM</td><td>12.0657</td><td>0.33279</td><td>0.62407</td></tr><tr><td>Environment</td><td></td><td>SANA-WM</td><td>12.5213 13.3562</td><td>0.34830 0.32284</td><td>0.60919 0.52598</td></tr><tr><td></td><td>227</td><td>HLA-WM</td><td>14.1215</td><td>0.33725</td><td>0.51069</td></tr><tr><td></td><td></td><td>SANA-WM</td><td>13.1585</td><td>0.31224</td><td></td></tr><tr><td></td><td>100</td><td></td><td></td><td></td><td>0.53320</td></tr><tr><td>Object</td><td></td><td>HLA-WM</td><td>13.6448</td><td>0.31892</td><td>0.52774</td></tr></table>

> 💡 **Table 11 定量结果批读**:
> - **表格结构**: 表头为 Subset / n / Method / PSNR↑ / SSIM↑ / LPIPS↓；行分组为 **Stage 1**、**Causal AR Refine**、**Bidirectional Refine** 三种推理模式，每种模式覆盖 MBench-A 四个子集（Causal n=100、Human n=120、Environment n=227、Object n=100）。注意这里是**三种推理模式 × 四个子集**；初稿写"四种推理模式"却只列出三项，属笔误，已修正。
> - **OCR 错位警告（关键）**: 提取出的 HTML 表格行列已严重错位——多行把 SANA-WM 与 HLA-WM 的方法名挤进同一单元格，SSIM 单元格常出现两个数值（如 Causal 行 "0.33422 0.34587"），因此**数值与方法名的逐格配对不可确信**，以下解读均基于就近配对，属推断。
> - **较有把握的解读**: Stage 1 的 Causal 子集 SANA-WM 为 PSNR 10.4068 / SSIM 0.28982 / LPIPS 0.65461，HLA-WM 为 11.0022 / 0.31830 / 0.61659；同子集 HLA-WM 的 PSNR 在细化后升至 12.6230（Causal AR Refine）与 13.2118（Bidirectional Refine）。PSNR 随细化上升这一方向较明确。
> - **必须修正的初稿表述（LPIPS 非单调改善）**: 初稿"细化过程均显著提升 PSNR 和 SSIM，降低了 LPIPS"不成立——Causal AR Refine 的 Causal 行出现 LPIPS 0.67586 / 0.66550，高于 Stage 1 Causal 行的 0.65461 / 0.61659，即该模式下感知指标反而变差。
> - **Environment 子集**: 表中 0.51069 与 PSNR 14.1215、SSIM 0.33725 同处 Bidirectional Refine 的 Environment 区域，看起来是该列最优；但同区域还出现 0.52598、0.60919，方法归属因错位无法确定，且 0.51069 与 0.52598 差距很小。故初稿"HLA-WM 的 LPIPS 降至 0.51069，显著低于其他模式"中的方法归属与"显著"均改为待确认。
> - **Object 子集**: Stage 1 区域出现 SANA-WM 的 0.54857 与紧邻的 0.59953，以及单独一行 HLA-WM 的 0.55808；初稿"Stage 1 中 SANA-WM LPIPS 0.54857 优于 HLA-WM 0.59953"忽略了同区域还有 0.55808，且配对本身不可靠。Bidirectional Refine 的 Object 区域可见 HLA-WM 13.6448 / 0.31892 / 0.52774，SANA-WM 13.1585 / 0.31224 与 0.53320，HLA-WM 在 LPIPS 上略优（0.52774 vs 0.53320），幅度很小，"反超"不宜作为强结论。
> - **证据界限**: 表注称"每个子集与模式下最优结果加粗"，加粗信息在文本提取中丢失，无法据本段核实；此外 HLA-WM 相对 SANA-WM 的优势幅度（PSNR 约 0.4–0.6 dB 量级）主要体现 Stage 1 与细化之间的落差，而非同阶段内 HLA-WM 对基线的领先，不应混淆这两类增益。

