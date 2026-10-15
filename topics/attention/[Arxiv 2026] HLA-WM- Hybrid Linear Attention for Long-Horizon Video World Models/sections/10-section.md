[← 返回 README](../README.md)

## A.1.1 Geometry-Aware Historical Retrieval

For each autoregressive chunk, we estimate its geometric overlap with historical chunks from the prescribed camera trajectory. The retrieval uses only camera intrinsics, extrinsics, and a scene-level median-depth scalar from the trajectory metadata, without relying on generated RGB frames, optical flow, semantic features, or dense depth maps.



> 💡 **问题动机与证据边界**: 本段交代检索的信号来源约束——只使用相机内参、外参，以及轨迹元数据中的一个场景级中位深度标量，明确不使用生成的 RGB 帧、光流、语义特征和稠密深度图。因此"检索信号完全来自相机轨迹元数据、与生成画面质量解耦"是正文直接支持的结论；但"传统方法计算量大、且易受生成误差累积影响"属于合理推断（正文只写 without relying on ...），批注中应标注为推断，而非论文原话。

For a video with N output frames and C autoregressive chunks, let $a _ { c }$ and $b _ { c }$ denote the starting and ending frame indices assigned to chunk $^ { c , }$ respectively. We compute them as



> 💡 **符号定义**: $a_c$ 与 $b_c$ 分别是被分配给第 $c$ 个自回归块的起始帧索引与结束帧索引。原文中 "chunk $^ { c , }$" 是 MinerU 把下标 $c$ 与随后的逗号错位的结果，实际意义就是 chunk $c$。这两个量在 Eq. 18 中给出闭式定义，是后面"每块约 24 个相机姿态"的前提。

$$
a _ { c } = \mathrm { r o u n d } \left( \frac { c N } { C } \right) , \qquad b _ { c } = \mathrm { m a x } \left( a _ { c } + 1 , \mathrm { r o u n d } \left( \frac { ( c + 1 ) N } { C } \right) \right) .\tag{18}
$$



> 💡 **公式批读 (Eq. 18)**: $a_c = \text{round}(cN/C)$ 是第 $c$ 块的名义起点；$b_c = \max(a_c+1,\ \text{round}((c+1)N/C))$。取 max 有两个作用：(1) 保证 $b_c \ge a_c+1$，即每块至少含 1 帧、不会出现空块；(2) 当下一块的名义起点在 $a_c$ 之后时按该起点截断，使相邻块首尾相接。需要修正初稿的措辞：这里取大的不是"前一块终点加 1"，而是"本块起点加 1"与"下一块名义起点"。格式上 `\tag{18}` 在 GitHub MathJax 中不生效，展示时按公式图片或去掉 tag 处理。

In our 60-second setting, $N = 9 6 1$ and $C = 4 0$ , yielding approximately 24 camera poses per chunk. For each camera frame, we uniformly sample a $7 \times 7$ image-plane grid over 5%–95% of the image extent. Each sampled ray is instantiated at three proxy depths,



> 💡 **关键参数与采样策略**: 60 秒设定下 $N=961$、$C=40$，$961/40\approx 24.0$，故每块约 24 个相机姿态。对每个相机帧，在图像平面 5%–95% 范围内均匀取 $7\times 7$ 网格（留出 5% 边缘），每条射线在三个代理深度处实例化，因此每帧最多 $7\times 7\times 3=147$ 个代理点。初稿说"避免边缘畸变干扰"是推断——正文仅写 uniformly sample over 5%–95%，未给出理由，应标注为推断。

$$
\mathcal { D } = \{ 0 . 5 d _ { \mathrm { m e d } } , d _ { \mathrm { m e d } } , 1 . 5 d _ { \mathrm { m e d } } \} ,\tag{19}
$$



> 💡 **公式批读 (Eq. 19)**: 代理深度集合取场景级中位深度的 0.5、1.0、1.5 倍。三层深度是对真实深度分布的粗略替代：不构建稠密深度图，只用一个全局标量做前后夹逼。关键点是这三层与像素无关，是整段共享的常量。

where $d _ { \mathrm { m e d } }$ denotes the scene-level median-depth proxy. For a pixel coordinate $( u , v )$ , the corresponding camera-space ray is



> 💡 **过渡说明**: $d_{\mathrm{med}}$ 是场景级的中位深度代理标量，而非逐像素深度；随后定义像素坐标 $(u,v)$ 到相机空间射线的映射。"场景级" 与第 1 段"轨迹元数据中的标量"一致，但正文在此未说明 $d_{\mathrm{med}}$ 的具体估计方式（属待查／推断）。

$$
\mathbf { r } ( u , v ) = \left[ \frac { u - c _ { x } } { f _ { x } } , \frac { v - c _ { y } } { f _ { y } } , 1 \right] ^ { \top } .\tag{20}
$$



> 💡 **公式批读 (Eq. 20)**: $\mathbf{r}(u,v)=\left[\frac{u-c_x}{f_x},\frac{v-c_y}{f_y},1\right]^\top$ 是针孔相机下的反向投影方向向量：像素坐标减去主点、再除以焦距，$z$ 分量固定为 1。因此它是沿视线方向、**未归一化**的方向向量——既不是单位向量，也不是严格意义的归一化设备坐标（NDC）。初稿中"得到归一化设备坐标下的方向向量"的说法不准确，应改为"相机坐标系下的射线方向"。

Each proxy point at depth $d \in \mathcal { D }$ is then transformed into world coordinates using the camera-toworld transformation.



> 💡 **坐标变换**: 将射线方向按深度 $d\in\mathcal{D}$ 缩放后，用相机到世界（原文 MinerU 写作 "camera-toworld"）变换映射到世界坐标，得到第 $i$ 块的世界坐标代理点集合。注意 $\mathcal{P}_i$ 的正式记号在下一段才给出，此处属前向引用，不算超出证据。

Let $\mathcal { P } _ { i }$ denote the set of proxy points constructed from chunk i. We define the directed visibility score $V ( i  j )$ as the fraction of points in $\mathcal { P } _ { i }$ that project with positive depth into the image plane of at least one camera frame in chunk $j .$ The $\mathrm { s y }$ mmetric overlap score is then



> 💡 **机制拆解（有向可见性）**: $V(i\to j)$ 定义为 $\mathcal{P}_i$ 中"能以正深度投影进块 $j$ 至少一个相机帧像平面内"的点所占比例。原文只检查正深度与投影是否落入像平面，**不检查是否被其他几何遮挡**——因此这是视锥／可见性覆盖比例，而不是遮挡测试。初稿"基于投影的遮挡/视野交集测试"中的"遮挡"部分超出原文，应删除。

$$
R ( i , j ) = \frac { 1 } { 2 } [ V ( i  j ) + V ( j  i ) ] .\tag{21}
$$



> 💡 **公式批读 (Eq. 21)**: $R(i,j)=\frac{1}{2}[V(i\to j)+V(j\to i)]$，对称化就是对两个方向的有向可见性分数取算术平均，用于消除方向不对称。必须纠正初稿：Eq. 21 **已显式写出**该对称公式（初稿称"原文虽未显式写出最终公式"有误），MinerU 只是把箭头 "→" 解析丢空成了空格，阅读时应还原为 $V(i\to j)$、$V(j\to i)$。该分数在下一段被用作检索依据 $R(i,c)$。

This score provides a lightweight proxy for frustum overlap rather than exact 3D scene intersection, as it does not explicitly model occlusion or scene geometry.



> 💡 **局限性与证据边界**: 正文自述 $R$ 只是视锥重叠的轻量代理，不显式建模遮挡与场景几何。由此可推出两点，但都属**推断而非论文陈述**：(1) 使用固定中位深度的三层代理点，在物体大幅运动时会偏离真实可见性；(2) $7\times 7$ 网格 × 3 个深度的稀疏采样，在复杂遮挡下可能低估重叠度。初稿"假设场景静态或变化缓慢"同样是推断，应明确标注。

For each query chunk $^ { c , }$ we retain the sink and recent chunks and retrieve the top-K remaining historical chunks according to $R ( i , c )$ . The selected chunks are reordered chronologically before GDN state recomposition.



> 💡 **检索执行与顺序约束**: 对每个 query chunk $c$，先强制保留 sink 与 recent 块，再在其余历史块中按 $R(i,c)$ 取 Top-$K$；被选中的块在 GDN 状态重组前按时间顺序重排。可确认的是：检索在空间上是非连续的，但在输入序列层面恢复了时序因果顺序。两项**待证**内容不应在批注中断言：(1) GDN 的全称（初稿猜为 "Gated Deep Network"，本节未给出展开）；(2) sink 块的具体作用（初稿"用于稳定注意力分布"在本节无原文依据）。

