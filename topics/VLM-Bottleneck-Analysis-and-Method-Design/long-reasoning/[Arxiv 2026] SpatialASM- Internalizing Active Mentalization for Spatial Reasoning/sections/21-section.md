[← 返回 README](../README.md)

## Generated-view localization

<image2>  
![](../images/44be6236229bc3074af3f348e4c78b6375aebe16bf2e6fbcaa95769402b9f783.jpg)  
<image1>



> 💡 **图像角色**: 本块给出问题中的两张输入图：<image2> 是在 <image1> 场景里标出四个候选位置的那张，<image1> 是真实 3D 场景本身，这层对应关系由下一块的题干文字说明。我只有图像路径、没有画面视觉信息，标记点在三维空间中的分布只能依据题干，不能描述。

![](../images/9aa8c5d7dfe4ecbe2250cf88da4fdfa904ad9d193d8ba584aae9173d71a4bf5f.jpg)



> 💡 **无标签插图**: 这张 jpg 在图注里没有 <imageN> 标签，题干也没说明它扮演什么角色，所以不宜断言它就是待匹配的目标视图（目标视图标签 <image3> 出现在下一块题干旁）。推断：它可能是重复插图或占位图，建议对照原 PDF 页面确认后再据其判断。

![](../images/be458e1ac0b7ddc7a5e3b989654c8c26fc3baf2d5e161fb55930381c34e22ad1.jpg)  
<image3>  
Q: 1 images is provided <image1>, depicting a real-world 3D scene. I select four places in <image1>, as shown in <image2>. The four locations are marked '1', '2', '3', and '4' respectively. From which marked location can I see Figure <image3>? A. 1 B. 2 C. 3 D. 4 GT: A



> 💡 **任务范式与GT**: 定义了“生成视图定位”的具体问答形式：给定场景图、标记点图和单张目标视图，判断目标视图源自哪个标记点。示例显示正确答案为位置1，确立了基准真值格式。

