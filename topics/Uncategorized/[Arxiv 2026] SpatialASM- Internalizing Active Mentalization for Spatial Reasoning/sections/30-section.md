[← 返回 README](../README.md)

## D.1. SpatialASM-NVG Rendering

We obtain SpatialASM-NVG after training FLUX.2-klein-9B [6] on SpatialASM-edit140k. Here we provide three output examples rendered by SpatialASM-NVG in Fig. 8.



> 💡 **模型来源**: SpatialASM-NVG 是在 SpatialASM-edit140k 上训练 FLUX.2-klein-9B [6] 得到的。本节用 Fig. 8 给出三个渲染输出示例，用来展示新视角生成（NVG）的实际效果。

Case 1: Turning right at the target location reveals an area that was originally outside the input image’s field of view (beyond the sofa on the far right). SpatialASM-NVG successfully reconstructs the missing content in this region while retaining the objects in the original input (e.g., blue chair, white box).



> 💡 **视野外重建**: Case 1 中，在目标位置向右转向后暴露出原本位于输入图像视野之外（最右侧沙发之外）的区域。模型既补全了这块缺失内容，也保留了原图已有的物体（蓝椅、白盒）。

Case 2: The generated view is not only geometrically consistent with the input but also faithfully reproduces the lighting on the brown panel on the right and the shadows cast on the floor.



> 💡 **几何与光影**: Case 2 强调生成视图不仅与输入几何一致，还忠实复现了右侧棕色面板的光照和投射到地板上的阴影。注意作者只陈述了外观的还原，并未给出物理光照模型的验证。

Case 3: This case is indeed rather tricky, as the relative camera poses of the two input images are not immediately apparent. Nevertheless, our model correctly infers their spatial relationship and seamlessly integrates the tactile paving from Image 1 (invisible in Image 2) with the white panel on the inner left side of the bus stop from Image 2 (not visible in Image 1) into a single rendered image.



> 💡 **多源融合**: Case 3 是较难的一例：两张输入图的相对相机位姿并不直观。模型仍正确推断出空间关系，把图 1 中可见、图 2 中不可见的盲道，与图 2 中公交站内侧、图 1 中不可见的白板，整合进同一张渲染图。

