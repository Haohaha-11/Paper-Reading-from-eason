[← 返回 README](../README.md)

## A.9.1 Stage 1

Figure 7 provides additional qualitative comparisons for the Stage 1 outputs before downstream refinement. In the first example, the baseline exhibits clear drift in the revisit view, with noticeable changes in the mountain contours, foreground ridge shape, and cloud distribution. In contrast, HLA-WM better preserves the overall mountain silhouette, the foreground terrain layout, and the spatial relationship between the ridge and the cloud layer. In the second example, the baseline alters the street geometry upon revisit, leading to inconsistent crosswalk patterns, road markings, and building alignment. HLA-WM more faithfully maintains the width and orientation of the crosswalk, the road layout, and the arrangement of nearby buildings and trees. In the third example, the baseline largely loses the original library-like structure and replaces it with a substantially diferent interior configuration. HLA-WM better preserves the bookshelf arrangement, corridor layout, desk region, and window placement, resulting in a revisit view that remains much closer to the first visit. In the fourth example, the baseline shows substantial changes in the classroom structure, including distortions in the wall geometry, seating layout, and front display region. By comparison, HLA-WM better maintains the overall room geometry, the arrangement of desks and chairs, and the long horizontal display panels along the wall. Overall, these examples show that HLA-WM improves long-range scene consistency already at Stage 1, before any subsequent refinement.



> 💡 **Figure 7 批读：Stage 1 的定性证据边界**
> 
> **证据内容**：原文用四个重访场景做 baseline 与 HLA-WM 的对照——山景（山脉轮廓、前景山脊形状、云层分布）、街道（斑马线形态、路面标线、建筑对齐）、图书馆式室内（书架排列、走廊布局、桌区、窗户位置）、教室（墙面几何、桌椅布局、前部展示区、沿墙横向长条显示屏）。结论句为：baseline 在重访视角发生明显漂移与结构替换，HLA-WM 更好保持整体轮廓、地形/房间布局与物体间空间关系。
> 
> **批注要点**：
> - 该段落是纯定性描述，原文未给出任何数值指标；“HLA-WM improves long-range scene consistency already at Stage 1”是原文作者基于目视对比给出的结论，不是本文新增的量化结论。
> - 段落只说“before downstream refinement”（下游细化之前）；把它等同于“因果 AR（causal AR）细化之前”来自本节 Table 9 的 caption（“after causal AR refinement”），属于跨段落衔接推断，需按推断对待。
> - 四个例子的失效模式可归纳为两类：一类是几何/位姿漂移（山脉轮廓、街道几何、墙面几何），另一类是内容级语义替换（图书馆内部配置被换成另一种布置）。原文未区分这两类，也未说明漂移是位姿误差还是生成误差所致。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: Stage 1 为何就能保持结构稳定？
> > - A: 原文只报告现象，未做机制归因（如注意力、记忆模块）。任何关于“记忆模块/架构导致时序连贯性更强”的表述都是推断，待正文方法节与消融实验验证。

Table 9 Category-level results on SANA-WM-Bench after causal AR refinement. Best results within each category are shown in bold.
<table><tr><td>Category</td><td>Method</td><td>PSNR ↑</td><td>SSIM ↑</td><td>LPIPS ↓</td><td>RotErr (°) ↓</td><td>TransErr ↓</td><td>CamMC↓</td></tr><tr><td colspan="8">Simple-Trajectory Split</td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>15.19 15.79</td><td>0.3225 0.3505</td><td>0.5582 0.5492</td><td>19.8894 16.5280</td><td>2.1729 2.0506</td><td>2.3106 2.1496</td></tr><tr><td>Indoor</td><td>SANA-WM HLA-WM</td><td>13.64 14.08</td><td>0.3127 0.3172</td><td>0.6013 0.5956</td><td>35.3855 34.3931</td><td>1.5308 1.6263</td><td>1.8652 1.9040</td></tr><tr><td>Outdoor City</td><td>SANA-WM HLA-WM</td><td>14.21 14.26</td><td>0.2353 0.2510</td><td>0.5803 0.5777</td><td>15.1111 14.4444</td><td>1.9558 1.9104</td><td>2.0376 1.9801</td></tr><tr><td>Outdoor Nature</td><td>SANA-WM HLA-WM</td><td>14.70 15.13</td><td>0.2375 0.2302</td><td>0.5555 0.5503</td><td>13.3400 15.8077</td><td>2.9753 3.1768</td><td>3.0350 3.2625</td></tr><tr><td colspan="8">Hard-Trajectory Split</td></tr><tr><td>Game Style</td><td>SANA-WM HLA-WM</td><td>14.95 15.73</td><td>0.3220 0.3398</td><td>0.5269 0.5140</td><td>23.5136 18.0932</td><td>1.8901 1.8923</td><td>2.0707 2.0223</td></tr><tr><td>Indoor</td><td>SANA-WM HLA-WM SANA-WM</td><td>13.81 13.71</td><td>0.3003 0.3139</td><td>0.5923 0.5920</td><td>35.8487 30.6444</td><td>1.2361 1.1457</td><td>1.6201 1.4865</td></tr><tr><td>Outdoor City</td><td>HLA-WM SANA-WM</td><td>13.56 13.74</td><td>0.2507 0.2570</td><td>0.5812 0.5877</td><td>20.0231 19.7262</td><td>1.8331 1.8103</td><td>1.9407 1.9228</td></tr><tr><td>Outdoor Nature</td><td>HLA-WM</td><td>14.18 14.71</td><td>0.2147 0.2143</td><td>0.5478 0.5290</td><td>23.1465 19.6023</td><td>3.0804 2.8921</td><td>3.2001 2.9858</td></tr></table>



> 💡 **Table 9 批读：类别级定量结果的逐项核对**
> 
> **表结构与口径**：caption 说明这是 SANA-WM-Bench 上“因果 AR 细化后”的类别级结果，粗体为类别内最优。表分两个 split（Simple-Trajectory / Hard-Trajectory），每个 split 含 Game Style / Indoor / Outdoor City / Outdoor Nature 四类，每类两行（SANA-WM vs HLA-WM）。指标方向：PSNR↑、SSIM↑、LPIPS↓、RotErr↓、TransErr↓、CamMC↓。**不能跨 split 合并概括**。
> 
> **Simple-Trajectory（可逐项核对）**：
> - Game Style：HLA-WM 六项全优（PSNR 15.79 vs 15.19；SSIM 0.3505 vs 0.3225；LPIPS 0.5492 vs 0.5582；RotErr 16.5280 vs 19.8894；TransErr 2.0506 vs 2.1729；CamMC 2.1496 vs 2.3106）。
> - Outdoor City：HLA-WM 六项全优（PSNR 14.26 vs 14.21；SSIM 0.2510 vs 0.2353；LPIPS 0.5777 vs 0.5803；RotErr 14.4444 vs 15.1111；TransErr 1.9104 vs 1.9558；CamMC 1.9801 vs 2.0376）。
> - Indoor：HLA-WM 在 PSNR（14.08 vs 13.64）、SSIM（0.3172 vs 0.3127）、LPIPS（0.5956 vs 0.6013）、RotErr（34.3931 vs 35.3855）更优，但 TransErr（1.6263 vs 1.5308）与 CamMC（1.9040 vs 1.8652）反而更差。因此“Simple 中 HLA-WM 各项均优”的说法不成立。
> - Outdoor Nature：HLA-WM 仅在 PSNR（15.13 vs 14.70）与 LPIPS（0.5503 vs 0.5555）改善；SSIM（0.2302 vs 0.2375）、RotErr（15.8077 vs 13.3400）、TransErr（3.1768 vs 2.9753）、CamMC（3.2625 vs 3.0350）均退步，是该 split 内的明显反例。
> 
> **Hard-Trajectory（存在解析错位，归属不可靠）**：
> - Game Style：第二组数值在 PSNR（15.73 vs 14.95）、SSIM（0.3398 vs 0.3220）、LPIPS（0.5140 vs 0.5269）、RotErr（18.0932 vs 23.5136）、CamMC（2.0223 vs 2.0707）上更优，但 TransErr 略差（1.8923 vs 1.8901）——并非“全部更优”。若方法顺序与 Simple 行一致，第二组对应 HLA-WM，此为推断。
> - Indoor 行 Method 单元格同时出现三个方法名（SANA-WM / HLA-WM / SANA-WM）却只有两组数值；Outdoor City 行方法名顺序被反转为（HLA-WM / SANA-WM）；Outdoor Nature 行只列出一个方法名却有两组数值。**这些行的数值—方法归属无法可靠判定，相关比较只能记为待证。** 初稿称 Indoor Hard 的 TransErr（1.2361 vs 1.1457）“SANA-WM 略优”缺乏证据支持，且按 ↓ 方向 1.1457 更小、若第二组为 HLA-WM 则结论相反，不应保留该判断。
> 
> **推断界限**：源表未提供方差、置信区间、显著性检验或训练/推理配置，因此“细化步骤无法弥补基础生成缺陷”“室内遮挡下位姿估计更稳健”“自然场景非刚性导致位姿波动大”等因果解释均属超出表格证据的推断，只能作为待验证假设。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 这张表支撑了哪个 claim？
> > - A: 可支撑的是“在 Simple-Trajectory 的 Game Style 与 Outdoor City 两类上，HLA-WM 在全部报告的图像质量与相机指标上优于 SANA-WM”；不支持“全类别、全 split 全面领先”，也支撑不了任何机制层面的因果结论。
> > - Q: 为什么 Indoor 与 Outdoor Nature 会出现“图像指标改善但相机指标退步”的混合结果？
> > - A: 表中只给结果，正文其余部分需另行核对；在仅有本表的前提下应保留为待证。

