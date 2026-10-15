[← 返回 README](../README.md)

## B.1.2. Construction Method

Given a 3D scene video in $D L 3 D V ,$ we first select a few observations as $\mathcal { T } = \{ I _ { 1 } , . . . , I _ { n } \}$ , and another frame as $I _ { \mathrm { g t } }$ . Critically, $I _ { \mathrm { g t } }$ is excluded from $\mathcal { T }$ and the frame indices of any two images in ℐ difer by at least 30, ensuring a noticeable viewpoint diference between them.



> 💡 **采样策略**: 从 DL3DV 视频中选若干帧组成输入集 $\mathcal{T}=\{I_1,...,I_n\}$，另取一帧 $I_{gt}$ 作真值。关键约束是 $I_{gt}$ 不进入 $\mathcal{T}$，且任意两帧的帧索引至少相差 30，作者称这是为了保证它们之间视角差异明显。

Since the image-editing model cannot directly accept the viewpoint query $v = ( i , x , y , d )$ as its input, so we separate and integrate it into the image and prompt inputs. In practice, we lay a visual marker on the pixel (�, �) of the anchor image $I _ { i } ,$ and we transform � into a textual prompt. Here we will explain how we derive $( x , y )$ from the metadata provided in ��3��.



> 💡 **查询分解**: 因编辑模型无法直接接收向量查询$v=(i,x,y,d)$，作者将其解耦：在锚点图像像素$(x,y)$处放置视觉标记，并将方向$d$转化为文本提示。本节重点阐述如何从元数据推导坐标$(x,y)$。

Assume that $I _ { 1 }$ is the anchor image we select, then we need to compute the viewpoint of $I _ { \mathrm { g t } }$ to derive (�, �) in the query based on the coordinate system of $I _ { 1 }$ . We can easily collect their corresponding camerato-world transformation matrices $T _ { 1 } , T _ { \mathrm { g t } } \in S E ( 3 )$ from the original dataset. ��(3) denotes the set of 3D rigid transformations, each represented by a 4 × 4 homogeneous matrix that combines a 3D rotation and a translation. The transformation from camera-gt to camera 1 is:



> 💡 **坐标系转换**: 以$I_1$为锚点，需计算$I_{gt}$相机相对于$I_1$相机的位姿。利用数据集提供的SE(3)变换矩阵$T_1, T_{gt}$，通过相对变换确定目标相机在参考坐标系下的位置，为后续投影做准备。

$$
T _ { g t  1 } = T _ { 1 } ^ { - 1 } T _ { g t } = [ \begin{array} { c c } { R _ { g t  1 } } & { \mathbf { t } _ { g t  1 } } \\ { \mathbf { 0 } ^ { \top } } & { 1 } \end{array} ] ,\tag{11}
$$



> 💡 **公式 11 批读**: $T_{gt \to 1} = T_1^{-1} T_{gt}$ 把坐标从 camera-gt 系变换到 camera-1 系；$T_1, T_{gt}$ 是相机到世界的 SE(3) 矩阵，所以先取 $T_1^{-1}$ 再复合 $T_{gt}$。结果是一个 4×4 齐次矩阵，右上块 $R_{gt\to1}$ 是旋转，$t_{gt\to1}$ 是平移。

where $R _ { g t \to 1 } \in \mathbb { R } ^ { 3 \times 3 }$ is the relative rotation of camera-gt with respect to camera 1, and $\mathbf { t } _ { g t  1 } = [ t _ { x } , t _ { y } , t _ { z } ] ^ { T }$ is the position of camera-gt in the coordinate system of camera 1. This position is projected onto $I _ { 1 }$ as:



> 💡 **变量定义**: $R_{gt \to 1}$是相对旋转矩阵，$t_{gt \to 1}$是GT相机在$I_1$坐标系中的平移向量（即相机位置）。下一步将该3D位置投影回$I_1$的2D图像平面，从而确定视觉标记的具体像素坐标。

$$
x = f _ { x } \frac { t _ { x } } { t _ { z } } + c _ { x } , \qquad y = f _ { y } \frac { t _ { y } } { t _ { z } } + c _ { y } ,\tag{12}
$$



> 💡 **公式 12 批读**: 标准针孔投影：用焦距 $(f_x, f_y)$ 和主点 $(c_x, c_y)$，把相机坐标系中的位置 $(t_x, t_y, t_z)$ 除以深度 $t_z$ 后映射到像素 $(x, y)$。这是把 3D 几何落到 $I_1$ 上视觉标记位置的关键一步。

where $( f _ { x } , f _ { y } )$ are the focal lengths and $( c _ { x } , c _ { y } )$ is the principal point, also provided in ��3�� originally. A valid projection requires $t _ { z } > 0$ and the resulting pixel $( x , y )$ to lie inside the image boundary. Finally, we derive the allocentric viewpoint direction � based on the relative rotation around the �-axis, which can be computed from $R _ { g t  1 }$ and expressed as a pitch angle in degrees. And we assign � to the corresponding direction in {forward, left, back, right}.



> 💡 **有效性检查**: 投影有效需 $t_z \gt 0$ 且 $(x,y)$ 落在图像边界内。方向 $d$ 取自 $R_{gt\to1}$ 的相对旋转：换算为俯仰角（pitch angle，单位为度）后，离散到 {forward, left, back, right} 之一。

