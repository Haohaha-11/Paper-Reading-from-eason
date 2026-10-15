[← 返回 README](../README.md)

## D.2. Effect of the Mixture-of-Transformers Architecture

We isolate the effect of the Mixture-of-Transformers (MoT) parameterization while keeping the pretrained visual foundation fixed. Both variants start from the same robot-videopretrained video checkpoint and use the same downstream data and training procedure. In the non-MoT variant, video and action tokens are processed by the same pretrained DiT; the MoT variant retains the pretrained video expert and introduces a separate action expert initialized from width-adapted copies of the corresponding video-expert layers. As shown in Table 13, the shared-DiT model already achieves strong performance, reaching 92.8% for VTA and 93.6% for Joint. MoT further improves success to 97.8% and 96.6%, corresponding to gains of 5.0 and 3.0 percentage points, respectively.



> 💡 **MoT 消融**: 控制变量做得干净——两组都从同一个 robot-video 预训练 checkpoint 出发，下游数据与训练流程相同，只改参数化方式：non-MoT 让 video 与 action token 走同一个预训练 DiT，MoT 保留视频专家、另加由视频专家层宽度适配初始化的动作专家。共享 DiT 已达 VTA 92.8%、Joint 93.6%，MoT 升到 97.8% 与 96.6%，即 +5.0 和 +3.0 个百分点（是百分点，不是相对提升）。

Table 14. Policy and dynamics accuracy on LIBERO-Long. LC-IDM and LC-FDM are independently trained specialist checkpoints; the multi-objective model trains policy, IDM, and FDM within a single checkpoint. Lower is better for MSE and IDM error; higher is better for success and SSIM.
<table><tr><td></td><td>Policy</td><td colspan="4">Real demonstrations</td><td colspan="4">Counterfactual transitions</td></tr><tr><td>Model</td><td>Succ. (%)</td><td>FDM MSE ↓</td><td>FDM SSIM ↑</td><td>IDM pos. (cm) ↓</td><td>IDM rot. (°) ↓</td><td>FDM MSE ↓</td><td>FDM SSIM ↑</td><td>IDM pos. (cm) ↓</td><td>IDM rot. (°) ↓</td></tr><tr><td>OPENWAM, LC-IDM, CF-only</td><td>一</td><td></td><td></td><td>1.76</td><td>2.39</td><td></td><td>一</td><td>1.17</td><td>3.60</td></tr><tr><td>OPENWAM, LC-IDM, mixed</td><td>1</td><td></td><td></td><td>0.44</td><td>1.21</td><td></td><td></td><td>1.22</td><td>3.68</td></tr><tr><td>OPENWAM, LC-FDM, CF-only</td><td>一</td><td>0.00734</td><td>0.9126</td><td>1</td><td>一</td><td>0.00940</td><td>0.8889</td><td>一</td><td>一</td></tr><tr><td>OPENWAM, LC-FDM, mixed</td><td>1</td><td>0.00186</td><td>0.9776</td><td>一</td><td>1</td><td>0.00962</td><td>0.8859</td><td></td><td>1</td></tr><tr><td>OPENWAM, multi-objective</td><td>92.8</td><td>0.00058</td><td>0.9934</td><td>0.43</td><td>1.10</td><td>0.01416</td><td>0.8398</td><td>1.73</td><td>3.55</td></tr><tr><td>UVA</td><td>88.0</td><td>0.00279</td><td>0.9645</td><td>2.03</td><td>1.60</td><td>0.01514</td><td>0.8134</td><td>3.57</td><td>12.99</td></tr></table>



> 💡 **Table 14 解读**: 表报 LIBERO-Long 上的策略与动力学精度，LC-IDM/LC-FDM 是各自单独训练的专家 checkpoint，multi-objective 用一个 checkpoint 同时训 policy、IDM、FDM；指标方向为 Succ./SSIM 越高越好，FDM MSE 与 IDM 位置(cm)/旋转(°) 误差越低越好，所以不能只看某一列下结论。真实演示数据上 multi-objective 全面最好（Succ. 92.8%、FDM MSE 0.00058、SSIM 0.9934、IDM 0.43 cm / 1.10°），优于 UVA（88.0%、0.00279、0.9645、2.03 cm、1.60°）；反事实过渡数据上它四项均差于单独训练的 LC-IDM/LC-FDM（IDM pos. 1.73 cm vs 1.17/1.22，rot. 3.55° vs 3.60/3.68，MSE 0.01416 vs 0.00940/0.00962，SSIM 0.8398 vs 0.8889/0.8859），但仍全面优于 UVA（3.57 cm、12.99°、0.01514、0.8134）。注意本节正文引用的是 Table 13，而表中并没有 shared-DiT 与 MoT 的对照行，别把两组证据混着读；表内还散布占位符“一”与空白单元格。

