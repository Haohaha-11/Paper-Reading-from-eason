[← 返回 README](../README.md)

## A.3. Tool-Assisted Spatial Reasoning

Recent studies increasingly augment LVLMs with paradigms of external harness to improve spatial intelligence. The tools can be primarily categorized into three groups: specialized visual tools, 3D reconstruction tools, and generative world models.



> 💡 **工具分类**: 指出当前增强 LVLM 空间智能的做法是引入外部工具，并把工具明确划分为专用视觉工具、3D 重建工具、生成式世界模型三类。原文用 increasingly 描述趋势，未断言其已是唯一主流路径。

Specialized visual tools are computer-vision models which provide more reliable low-level or metric spatial evidence, such as pose/depth estimation and segmentation. Multiple studies demonstrate that the accurate specialist perception substantially improves spatial reasoning when LVLMs are specifically optimized for determining which tools to invoke and how to integrate their outputs [16, 19, 83]. Pretrained 3D reconstruction models recover point clouds and camera poses from scene images [32, 39] so that LVLMs can actively manipulate the reconstructed scene and inspect spatial configurations from alternative viewpoints [80]. A third direction employs generative world models to synthesize observations that are not present in the original visual inputs [73, 74, 84]. These methods primarily enhance spatial reasoning by augmenting the external evidence, which introduce additional inference overhead due to external tool execution and LVLM-tool interaction. Moreover, specialized tool-use training on LVLMs may introduce an additional trade-of between tool proficiency and the original general capabilities [14].



> 💡 **机制与局限**: 专用视觉工具提供深度/位姿估计等底层度量证据，3D 重建模型从场景图像恢复点云与相机位姿供 LVLM 换视角检查，生成式世界模型则合成原始输入中不存在的观测。代价是外部工具执行与 LVLM 和工具交互带来额外推理开销，且专门的工具使用训练可能在工具熟练度与原有通用能力之间造成权衡。

SpatialASM targets a fundamentally diferent yet complementary dimension from the above tool-assisted approaches: strengthening the intrinsic spatial reasoning process itself. While tool-assisted methods improve the availability and precision of external spatial evidence, SpatialASM improves the model’s ability to reason about what spatial evidence is needed and how it should be used. The two paradigms can thus be naturally combined, improving spatial intelligence along complementary axes of intrinsic reasoning capability and external evidence accessibility.



> 💡 **互补定位**: 阐明 SpatialASM 走的是另一条互补路线——不靠更准的外部证据，而是强化模型判断“需要什么空间证据、该怎么用”的内在推理能力，因此可与工具辅助方法叠加使用。

