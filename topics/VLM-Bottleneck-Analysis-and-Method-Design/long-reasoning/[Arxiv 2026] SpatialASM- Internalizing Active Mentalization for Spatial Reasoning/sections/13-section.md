[← 返回 README](../README.md)

## B.1.1. Statistics

SpatialASM-edit140k consists of 139,730 samples, all automatically constructed from the large-scale public scene datasets DL3DV [40]. As illustrated in Sec. 3.1, each sample has the form of $( \mathcal { T } , v , I _ { \mathrm { g t } } )$ , containing the input images ℐ, the allocentric viewpoint $v ,$ and expected ground-truth novel-view observation. To enable � to generate novel views from multi-image inputs, we set the length of input images |ℐ| to diferent values, and the number of samples of each |ℐ| is provided in Tab. 5.



> 💡 **数据集构成**: SpatialASM-edit140k 共 139,730 个样本，全部自动构建自公开场景数据集 DL3DV [40]。每个样本记作 $(\mathcal{T}, v, I_{gt})$，其中 $v$ 是 allocentric（世界坐标系）视点，$I_{gt}$ 是期望的真值新视角观测，$\mathcal{T}$ 对应输入图像集合 ℐ。为训练模型从多图输入生成新视角，输入图像数 $|\mathcal{I}|$ 被设为不同值，各取值的样本数见 Table 5。

Table 5: The samples numbers of each input-image length.
<table><tr><td>Input Length | |Z| = 1 |Z| = 2 |Z| = 3</td></tr><tr><td></td></tr><tr><td>|I| = 4 Total 35,02634,83534,84635,023 |139,730</td></tr></table>



> 💡 **Table 5 解读**: 表按输入图像数 $|\mathcal{I}|=1,2,3,4$ 给出样本量，依次为 35,026、34,835、34,846、35,023，合计 139,730，四类各占约 25%，输入长度分布均衡。原文该表的列头排版错位，只清晰给出前三个 $|\mathcal{I}|$ 列头，$|\mathcal{I}|=4$ 一列与数值的对应是按列顺序读出的。

