[← 返回 README](../README.md)

## A.2. Spatial data synthesis

A wide spectrum of previous work lay emphasis on large-scale high-quality data curation pipeline to improve spatial reasoning in LVLMs. A straightforward approach is to construct such supervision via human annotation [8], but it is hardly afordable to scale. Some other pipelines resort to proprietary/open-source large-language models [15, 52]. However, current models’ performance on relatively challenging spatial reasoning tasks, especially in multi-view or video modalities, remains far from satisfactory and may introduce abundant errors to curated QA pairs.



> 💡 **现有局限**: 人工标注虽然直接，但成本高到无法规模化；改用专有或开源 LLM 生成监督时，模型在 multi-view、video 等较难空间任务上表现仍差，会把大量错误带进 QA 对。

To formulate scalable spatial supervision with high accuracy and quality, various synthesis frameworks emerge with two predominate characteristics: leveraging public scene datasets with explicit geometric annotations, and extensively employing visual expert models to recover otherwise unavailable 3D information from ordinary images. i) Numerous approaches (e.g., SpatialRGPT, OpenSpatial) leverage open-source scene datasets (e.g., ScanNet) with diverse annotated scenes as their foundational data [37, 41, 47]. These datasets provide 3D annotations, such as object classification, semantic voxel labels, and 3D segmentations, which can be easily augmented to spatial QA tasks. ii) A complementary direction further adds necessary annotations for constructing spatial supervision through specialized visual models [11, 15, 20]. These models have grown relatively mature in the traditional field of computer vision and perform well in tasks such as segmentation [42], metric depth estimation [30], object detection [79], and pose estimation [61], thereby lifting 2D imagery to



> 💡 **两条合成范式**: 一是复用带显式几何标注的公开场景数据集（如 ScanNet，提供物体类别、语义体素标签、3D 分割，可较容易改写成空间 QA）；二是调用分割、度量深度估计、目标检测、位姿估计等成熟视觉专家模型，从普通图像里恢复原本拿不到的 3D 信息来补监督。

3D context.



> 💡 **3D 上下文**: 这半句补全前一句，指出视觉专家模型的共同作用是把普通 2D 图像提升到 3D 上下文，这是自动构建空间监督的前提。

While these pipelines greatly expand the scale of spatial supervision, the resulting data are largely constrained by the expert models or the information already encoded in the source public datasets. Recent works therefore explore richer sources of supervision. For instance, VST [71] broadens the coverage of spatial supervision across single-image, multi-image, and video scenarios by leveraging a 3D scene simulator to generate novel observations. Spatial-SSRL [44] bypasses explicit spatial annotation by constructing five self-supervised pretext tasks which provides naturally verifiable rewards for spatial reinforcement learning. Holi-Spatial [26] turns raw video streams into geometrically optimized 3D Gaussian Splatting (3DGS) scenes and automatically derives multi-level supervision (e.g., 3D grounding instances, QA pairs). SenseNova-SI [9] scales spatial intelligence to a new level, curating millions of spatial training samples by incorporating and filtering QA pairs from an incredibly large variety of datasets.



> 💡 **前沿探索**: 既有范式受限于专家模型和源数据集既有信息，于是出现更丰富的监督来源：VST 用 3D 场景模拟器生成新观测以覆盖单图/多图/视频，Spatial-SSRL 用五个自监督 pretext 任务给空间强化学习提供可验证奖励，Holi-Spatial 把原始视频重建为 3DGS 场景并自动派生多级监督，SenseNova-SI 则汇总并过滤大量数据集，整理出数百万条空间训练样本。

Despite the increasing scale and diversity of these datasets, most of them only provide answer-only supervisory signals. Specifically, for those relatively challenging tasks (e.g., perspective-taking allocentric transformation, mental reconstruction), the answers remain centered on what spatial relation or camera motion should be predicted, rather than explicitly demonstrating how a model can approach the correct answer through proactively acquiring missing evidence during reasoning.



> 💡 **核心痛点**: 数据集规模再大，监督信号基本也只有答案——即便在 perspective-taking 的 allometric 变换、mental reconstruction 这类难题上，也只告诉模型该预测什么空间关系或相机运动，而没有展示如何通过主动获取缺失证据逼近答案。

VST-R [71] constitutes an important step towards reasoning supervision by providing 135K curated sample with explicit step-by-step reasoning traces. However, its synthesis process requires ground-truth 3D bounding boxes in the original scene data to form intermediate Bird’s-Eye View (BEV) images. Moreover, its reasoning supervision primarily teaches the model to derive spatial conclusions from the provided visual evidence, rather than to execute the perception-action loop of proactive exploration. It is not yet publicly released as well. In contrast, SpatialASM grounds an explicit question-conditioned reasoning process of when additional viewpoint reasoning is needed, where the model should mentally observe, and what evidence should be expected from that viewpoint, which is more demanding and human-like. Importantly, SpatialASM can synthesize ASM reasoning trajectories for existing spatial QA data without requiring any prerequisite ground-truth 3D annotations.



> 💡 **对比优势**: VST-R 提供 135K 条带显式步骤推理的训练样本，但合成需要原始场景的 GT 3D 边界框来生成中间 BEV 图像，其推理监督只教模型从已给视觉证据推结论，不覆盖主动探索的感知—行动闭环，且尚未公开。SpatialASM 不需要任何前置 GT 3D 标注，直接合成“何时需要额外视角推理、应在何处心理观察、预期看到什么证据”的问题条件推理轨迹。

World2VLM [78] is particularly relevant to our work in that it also employs a generative world model in its data engine and distills its spatial imagination into the LVLMs. World2VLM synthesizes future views based on given parameterized camera trajectories to teach LVLMs to associate multi-view observations with egocentric motion transitions. Though efective in enhancing view-imagination capabilities, World2VLM fails to teach how to initialize a thinking process with imaginary world-model-like motions when encountering spatial problems, nor does it provide any reasoning content in its supervision. Our objective is diferent: SpatialASM starts from the spatial question itself and teaches the model to determine which viewpoint would provide useful missing evidence, rather than starting from a predefined egocentric motion trajectory and learning the corresponding visual outcome. The generated views serve only as temporary scafolding for synthesizing SpatialASM-128k, whose final supervision contains long-CoT traces which explicitly couples question-conditioned view planning, viewpoint-conditioned imagination, and reasoning with the imagined evidence.



> 💡 **机制差异**: World2VLM 同样用生成式世界模型蒸馏空间想象，但它从给定的参数化相机轨迹出发合成未来视图，教模型把多视图观测与自我中心运动变化对应起来；SpatialASM 改从空间问题本身出发，判断哪个视角能补上有用的缺失证据，生成的视图只是合成 SpatialASM-128k 长 CoT 轨迹的临时脚手架。

