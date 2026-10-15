[← 返回 README](../README.md)

## B.3. SpatialASM-RL-58k

We construct the SpatialASM-RL-58k automatically by employing SpatialASM-NVG without any LVLM/human annotation. Only the original 2D images come from Holi-Spatial [26] and DL3DV [40]. SpatialASM-RL-58k consists of 58,346 training samples, with 28,594 from Holi-Spatial and 29,752 from DL3DV.



> 💡 **数据构成**: SpatialASM-RL-58k 由 SpatialASM-NVG 自动生成，不含任何 LVLM 或人工标注。共 58,346 个训练样本，其中 28,594 个来自 Holi-Spatial、29,752 个来自 DL3DV，只有原始 2D 图像取自这两个数据集。

In Sec. 3.3 we introduce the answer selection strategy to reduce potential ambiguity in the two multi-choice tasks and ensure the quality of the rendered images. A pre-defined parameter $\theta _ { R L }$ is applied in this strategy. During the construction process, we find that generated images generally have higher GeoP values when the queried allocentric viewpoint is set to backwards than when it is set to other directions. This is because the backward direction is more likely to reveal regions not visible in the input images, requiring the NVG to synthesize more unobserved content. Therefore, even when the generated images exhibit similar levels of geometric consistency, those generated for the backward direction tend to receive higher GeoP. After manually inspecting a large number of generated images and their corresponding GeoP values, we set to $\theta _ { R L }$ to 1.2 for images whose corresponding target direction is backwards, and 0.6 for all other directions.



> 💡 **参数设定**: 作者发现查询的 allocentric 视角设为向后（backward）时，生成图像的 GeoP 普遍高于其他方向，因为后向更可能暴露输入图像中不可见的区域、需要 NVG 合成更多未观测内容。人工检查大量生成图像及其 GeoP 后，作者把答案选择策略中的阈值 $\theta_{RL}$ 对后向目标设为 1.2，其他方向设为 0.6。

In Fig. 7, we give example VQAs of the two tasks we construct for RL: generated-view localization and pose-conditioned view selection. The formulation of both tasks are illustrated in Sec. 3.3.



> 💡 **任务示例**: Fig. 7 展示了 RL 阶段两个核心 VQA 任务的实例：生成视角定位与位姿条件视角选择，具体公式定义参见 Sec. 3.3。

