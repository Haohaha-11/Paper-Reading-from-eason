[← 返回 README](../README.md)

## B.2. SpatialASM-128k

In Sec. 3.2, we explain how to synthesize the ASM reasoning trajectories based on triplets (�, ℐ, �) from existing answer-only spatial datasets. We construct 128k ASM trajectory samples, and use a subset of 66k samples for our SFT training. Our approach provides a scalable and convenient pathway to synthesizing high-quality spatial CoTs as it does not require any other metadata (e.g., BEV image, bounding box). It helps address the scarcity of reasoning content in prior large-scale spatial supervision and elicit active task-conditioned planning and imagination capabilities in LVLMs from scratch.



> 💡 **数据构造与规模**: 沿用 Sec. 3.2 的做法，以现有仅含答案的空间数据集中的三元组合成 ASM 推理轨迹，共构建 128k 条样本，其中 66k 条子集用于 SFT 训练。作者强调该路径不需要 BEV 图像或边界框等额外元数据，因此可扩展、便于合成高质量空间 CoT；其目的是补上此前大规模空间监督中缺失的推理内容，并让 LVLM 从零习得任务条件化的规划与想象能力。

