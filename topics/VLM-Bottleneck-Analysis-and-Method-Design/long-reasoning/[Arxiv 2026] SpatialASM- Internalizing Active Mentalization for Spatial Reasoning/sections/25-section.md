[← 返回 README](../README.md)

## C.2. Benchmark Overview

We evaluate the LVLMs on seven spatial reasoning (SR) benchmarks. For Spatial457 [63], we only select its most challenging 6d subtask with 1837 questions. ESI-Bench [29], MindCube [60], MMSI-Bench [72], and Spatial457-6d are the most dificult among the seven benchmarks, requiring LVLMs to go beyond directly reading spatial relations from the given observations and instead integrate incomplete evidence, mentally construct a 3D scene representation, and reason about unobserved or hypothetical viewpoints. ViewSpatial-Bench [35] represents an intermediate level of dificulty, with camera-perspective questions which can often be answered directly from the observed coordinate frame and person-perspective questions which require some basic perspective-taking capabilities. Finally, 3DSRBench [45] and EmbSpatial-Bench [22] are relatively perception-oriented, covering spatial properties such as height, orientation, location, and egocentric spatial relations. Taken together, these benchmarks provide a comprehensive evaluation of spatial reasoning across complementary dimensions, ranging from fundamental spatial perception and egocentric relation recognition to perspective transformation, multi-view scene reconstruction, and imagination-driven reasoning about unobserved spaces.



> 💡 **基准难度分层**: 七个空间推理基准按难度分三档：Spatial457-6d（只取最难的 6d 子任务，1837 题）、ESI-Bench、MindCube、MMSI-Bench 最难，要求模型整合不完整证据、心理构建 3D 场景并推理未观测或假设视角；ViewSpatial-Bench 居中，相机视角问题常可直接从观测坐标系回答，人物视角问题才需基本透视转换；3DSRBench 与 EmbSpatial-Bench 偏感知，覆盖高度、方向、位置和自我中心关系等属性。

We additionally evaluate on three general-VQA benchmarks to examine whether SpatialASM preserves the broad visual capabilities of the base LVLMs. MMMU [75] evaluates expert-level multimodal understanding and reasoning across 6 major disciplines. MMStar [12] evaluates 18 detailed ability axes in 6 major categories, spanning fine-grained and coarse visual perception, instance-level reasoning, and science and technology, with an emphasis on genuine visual dependency. RealWorldQA [65] focuses on basic visual understanding in real-world scenes, including counting, object size comparison, relative direction, optical character recognition (OCR), etc. Together, these benchmarks measure whether the spatial improvements introduced by SpatialASM come at the expense of general visual understanding.



> 💡 **通用能力回归测试**: 引入MMMU、MMStar和RealWorldQA三个通用VQA基准，目的是验证SpatialASM在提升空间推理性能时是否损害了模型原有的广泛视觉理解能力（如专家级推理、细粒度感知及OCR），确保改进是“无损”或“低损”的。

