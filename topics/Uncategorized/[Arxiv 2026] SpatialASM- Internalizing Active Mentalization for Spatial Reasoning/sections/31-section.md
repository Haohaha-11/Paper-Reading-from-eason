[← 返回 README](../README.md)

## D.2. Results of Novel-view Generators

To showcase qualitative examples of the rendering results of diferent novel-view generators, we select some generated observations from the experiment in Tab. 3 with Kimi-k2.5 as the LVLM for planning and reasoning.



> 💡 **实验设置**: 本段说明定性结果来源：选取 Tab.3 中由 Kimi-k2.5 规划生成的观测，用于展示不同新视角生成器的渲染效果。

![](../images/94b9a10d327914a614926b3cb46abc8f6a95170e98a059d5c27b6c34a086e376.jpg)  
Figure 8: Examples of novel-view observations rendered by SpatialASM-NVG.



> 💡 **Figure 8 批读**: 图 8 展示 SpatialASM-NVG 生成的具体图像示例。因未提供视觉细节，仅确认其为该方法的定性输出样本。

The generated images are shown in Fig. 9 and Fig. 10. Evidently, the formulation of target viewpoints varies greatly. Prior approaches(e.g., SVC [82]) use ego-centric motions from the camera position of an anchor image, whereas SpatialASM-NVG defines the target location directly by marking a pixel on an image. Note that in both figures we assume that the input images all have the size (100, 100).



> 💡 **机制对比**: 核心差异在于视角定义：SVC 基于锚点相机的自车运动，而 SpatialASM-NVG 通过标记像素直接定义目标位置（假设输入尺寸 100x100）。

Case 1 (Fig. 9): The query target viewpoints of the two novel-view generators are approximately the same. SpatialASM-NVG has a much lower GeoP value because the red fire-fighting unit and the black disinfectant unit in the output image more closely resemble their counterparts in the input images.



> 💡 **Case 1 解读**: 在目标视角近似时，SpatialASM-NVG 的 GeoP 更低，因为输出图中的红色消防和黑色消毒单元与输入图像对应物更相似。

Case 2 (Fig. 10): Although SVC’s output image is more geometrically consistent with the visual inputs (lower GeoP), its egocentric formulation results in relatively small camera motions and its output remains highly similar to the anchor image, providing little additional information for further LVLM reasoning. In contrast, our allocentric formulation induces larger viewpoint changes and provides more informative output observations, which could also possibly help the LVLM better understand the relative camera poses across input images and the spatial layout of the 3D scene.



> 💡 **Case 2 解读**: 作者承认 SVC 输出的几何一致性更高（GeoP 更低），但指出其 ego-centric 定义导致相机运动小、输出与锚点图像高度相似，对后续 LVLM 推理的额外信息有限；随后是推测性表述（原文 could also possibly help）：SpatialASM-NVG 的 allocentric 定义带来更大视角变化，可能有助于 LVLM 理解相对相机位姿与三维场景布局。

