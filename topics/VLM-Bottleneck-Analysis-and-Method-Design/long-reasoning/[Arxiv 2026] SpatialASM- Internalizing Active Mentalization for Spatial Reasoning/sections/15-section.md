[← 返回 README](../README.md)

## B.1.3. Training samples

We provide two examples with single and multiple image inputs in Fig. 5. As in some training samples, when the queried viewpoint reveals regions outside the views of the original visual inputs, we allow and encourage �



> 💡 **训练样本策略**: 当查询视角落在原始输入视图之外时，作者允许并鼓励模型生成输入图像中不存在、但在现实世界中合理的物体；原文只说“允许并鼓励”，未给出理由（如增强空间推理），这类说法属推断。注意此句在 Fig. 5 处被截断，条件与举例在第 3 段接续。

![](../images/229128e974e588e2777e033ccade3b005e90f5cc1bb11c57f77edd49638f3de7.jpg)  
Figure 5: Examples in SpatialASM-edit140k. (Case 1, 2: |ℐ| = 1, Case 3: |ℐ| = 2)



> 💡 **Figure 5 批读**: caption 显示 SpatialASM-edit140k 的样本：Case 1、2 为单参考图 $|\mathcal{I}|=1$，Case 3 为双参考图 $|\mathcal{I}|=2$。正文说 Fig. 5 提供“two examples with single and multiple image inputs”，而 caption 列出三个 Case，两处措辞不一致（可理解为单图、多图两类示例）；图片视觉内容未提供，不描述编辑效果。

to imagine objects not present in the input images, provided that these imagined objects are plausible in the real world (e.g., the patio umbrella and the window on the left wall in $I _ { \mathrm { g t } }$ of Case 2).



> 💡 **合理性约束**: 这是上一句的接续，给出“想象”的边界：必须是现实中合理的物体，例如 Case 2 的 $I_{\mathrm{gt}}$ 中出现的露台伞与左墙窗户。由此也可看出监督目标 $I_{\mathrm{gt}}$ 本身可能包含输入图中没有的新物体。

