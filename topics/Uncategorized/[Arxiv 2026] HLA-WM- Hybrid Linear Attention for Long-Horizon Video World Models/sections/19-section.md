[← 返回 README](../README.md)

## A.8 Detailed Benchmark Results

We provide detailed category- and subset-level results to complement the aggregate results in the main paper. We first report the four scene categories of SANA-WM-Bench, followed by the four oficial subsets of MBench-A.



> 💡 **AI 批注（本节路线图）**: 本段交代 A.8 的组织顺序：先给 SANA-WM-Bench 的四个场景类别（Game Style / Indoor / Outdoor City / Outdoor Nature），再给 MBench-A 的四个官方子集（OCR 原文写作 "oficial"，应为 official，属解析原样）。
> - **推断（非原文断言）**: 原文只说这些细粒度结果用于 complement 主文的 aggregate 结果；"聚合结果可能掩盖场景差异"是据此推出的阅读动机，属于推断，不是论文声明。
> - **证据边界**: 本块实际只含 SANA-WM-Bench 的类别级表（Table 8），MBench-A 子集级结果未出现在已提供文本中，其具体数值待证，不可假定已覆盖。

Table 8 Category-level results on SANA-WM-Bench using Stage 1 outputs. Best results within each category are shown in bold.
<table><tr><td>Category</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td colspan="8">Simple-Trajectory Split</td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>9.85 11.06</td><td>0.2070 0.2639</td><td>0.6197 0.5866</td><td>16.5064 12.8640</td><td>1.9242 2.0047</td><td>2.0482 2.0939</td></tr><tr><td>Indoor</td><td>SANA-WM HLA-WM</td><td>9.38 10.05</td><td>0.2105 0.2338</td><td>0.6576 0.6306</td><td>38.6940 29.0323</td><td>1.6993 1.6196</td><td>2.0227 1.8481</td></tr><tr><td>Outdoor City</td><td>SANA-WM HLA-WM</td><td>8.96 9.25</td><td>0.1323 0.1561</td><td>0.6390 0.6240</td><td>12.2424 5.9851</td><td>2.0782 1.5976</td><td>2.1372 1.6170</td></tr><tr><td>Outdoor Nature</td><td>SANA-WM HLA-WM</td><td>8.52 9.29</td><td>0.1419 0.1459</td><td>0.6145 0.5785</td><td>11.3733 6.4643</td><td>3.3483 3.1493</td><td>3.3814 3.1603</td></tr><tr><td colspan="8">Hard-Trajectory Split</td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>9.84 11.41</td><td>0.2023 0.2550</td><td>0.5823 0.5505</td><td>14.2161 14.3713</td><td>1.8745 1.8250</td><td>1.9719 1.9349</td></tr><tr><td>Indoor</td><td>SANA-WM HLA-WM</td><td>9.22 9.63</td><td>0.2085 0.2305</td><td>0.6390 0.6330</td><td>36.5361 26.6061</td><td>1.2446 1.0675</td><td>1.6297 1.3277</td></tr><tr><td>Outdoor City</td><td>SANA-WM HLA-WM</td><td>9.17 9.56</td><td>0.1463 0.1611</td><td>0.6206 0.6122</td><td>16.0176 9.9119</td><td>1.8409 1.6669</td><td>1.9254 1.7070</td></tr><tr><td>Outdoor Nature</td><td>SANA-WM HLA-WM</td><td>9.23 9.84</td><td>0.1526 0.1521</td><td>0.5756 0.5437</td><td>14.7337 9.4654</td><td>3.0208 2.7958</td><td>3.0760 2.8233</td></tr></table>



> 💡 **AI 批注（Table 8 批读）**:
> - **表结构与条件**: 4 类场景 × 2 种轨迹划分（Simple-Trajectory Split / Hard-Trajectory Split），每类并排 SANA-WM 与 HLA-WM 两行；caption 明确限定为 **Stage 1 输出**（Stage 1 的具体定义本节未给出，待证）。指标分两组：PSNR/SSIM/LPIPS 是生成保真度，RotErr/TransErr/CamMC 是相机轨迹误差（越低越好）。
> - **PSNR**: HLA-WM 在 8/8 组中更高。Simple-Trajectory 提升 0.29–1.21 dB（Game Style 9.85→11.06；Indoor 9.38→10.05；Outdoor City 8.96→9.25；Outdoor Nature 8.52→9.29）；Hard-Trajectory 提升 0.39–1.57 dB（9.84→11.41；9.22→9.63；9.17→9.56；9.23→9.84）。
> - **SSIM**: 7/8 组更高，**唯一例外**是 Hard-Trajectory 的 Outdoor Nature：SANA-WM 0.1526 vs HLA-WM 0.1521，即 HLA-WM 反而略低。因此不能概括为"在两种轨迹难度上 SSIM 全面更优"。
> - **LPIPS**: 8/8 组更低（如 Hard-Trajectory Outdoor Nature 0.5756→0.5437）。
> - **相机误差**: 多数类别 HLA-WM 更低，最大改善出现在 Simple-Trajectory Outdoor City 的 RotErr（12.2424°→5.9851°，约降 6.26°）；但存在三处反向：Simple-Trajectory Game Style 的 TransErr（HLA 2.0047 vs SANA 1.9242）与 CamMC（2.0939 vs 2.0482），以及 Hard-Trajectory Game Style 的 RotErr（HLA 14.3713° vs SANA 14.2161°）。
> - **证据界限**: 以上仅是"Stage 1 输出 + 该基准 + 两种轨迹划分"下的观测；"HLA-WM 对简单几何约束更鲁棒、复杂动态下优势减弱"只能作为**待验证的推断**，论文本节未给出原因分析。caption 声称各类别最优值加粗，但抽出的文本表不含加粗信息，无法核验该标注。

