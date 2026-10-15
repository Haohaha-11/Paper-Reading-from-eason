[← 返回 README](../README.md)

## A.1. Proactive Spatial Intelligence

LVLMs have exhibited outstanding performance on various tasks, such as image and video captioning [67, 70], math reasoning [17, 76], agent harness [21, 56], and document understanding [24, 64]. In spatial reasoning, with the progress of LVLMs, recent benchmarks are moving beyond static spatial recognition and understanding towards more demanding spatial reasoning tasks where LVLMs still perform significantly worse than ordinary human beings. For example, Spatial457 [63] progressively evaluates spatial understanding from object recognition and 2D localization to complex 6D reasoning, revealing a pronounced performance degradation as geometric complexity increases. MindCube [60] studies whether LVLMs can construct a coherent spatial mental model from only a few observations, which evaluates cognitive mapping, perspective taking, and mental simulation of hypothetical movements. Similarly, MMSI-Bench [72] targets multi-view spatial intelligence and identifies scene reconstruction, cross-view correspondence, and perspective-transformation reasoning as major failure modes of current LVLMs. These benchmarks suggest that successful spatial reasoning often requires more than extracting relations from the observations already provided: the model must reason about how the scene would be perceived under alternative unobserved viewpoints.



> 💡 **现状与痛点**: 指出当前 LVLM 在复杂空间推理（如 6D、心理模拟）上远逊于人类。核心论点是：现有基准揭示模型仅能处理已观测关系，缺乏对未观测视角的推断能力。

The recent ESI-Bench [29] delves deeper into the perception-action loop and treat it as a higher level of spatial intelligence. An agent can selectively employ locomotion, and manipulation to uncover information that cannot be resolved from the initial observation alone. By reasoning under the active multiple setting, where the models can proactively execute movements and interact with the simulation environments, the performance significantly outperforms that under the passive single setting. However, the accuracy of the active multiple setting lags far behind the GT passive setting, where the best ground-truth passive trajectories are provided. These results suggest that current LVLMs lack intrinsic proactive exploration capability when solving spatial problems, unless they are explicitly prompted to do so. And their trajectory-planning capability is still far from saturated.



> 💡 **主动探索局限**: ESI-Bench 显示主动多视角设置虽优于被动单视角，但仍远低于提供理想轨迹的 GT 被动设置。作者据此判断当前 LVLM 缺乏内在主动探索能力（除非被明确提示），且轨迹规划能力远未饱和。

To internalize the thinking pattern of ‘perception-active loop’ into LVLMs, Geothinker [36] proposes active geometry integration, in which semantic representations selectively query task-relevant geometric features. However, such geometry-enhanced approaches [36, 81] introduce an additional pretrained feature branch and architecture-specific fusion modules into the LVLMs, and require alignment between geometric and semantic representations, potentially limiting their adaptability across heterogeneous VLM architectures. Unlike their notion of ‘active’ at the feature-retrieval level, our ASM operates at the viewpoint and observation level. It reasons about where useful evidence should be acquired and what should be observed from the corresponding hypothetical viewpoint. And we focus on data-centric pipeline where such capability can be internalized through of-the-shelf post-training strategies without introducing geometry module or making architectural modification.



> 💡 **方法差异对比**: 区别于 GeoThinker 等需引入几何模块或修改架构的方法，本文提出的 ASM 在“视点与观察”层面运作。其优势在于通过数据中心的后训练策略内化能力，无需额外分支或架构改动。

