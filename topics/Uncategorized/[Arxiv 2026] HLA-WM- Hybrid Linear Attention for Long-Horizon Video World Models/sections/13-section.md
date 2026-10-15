[← 返回 README](../README.md)

## A.2.1 Overall Inference Procedure

Algorithm 1 summarizes the complete inference procedure of HLA-WM. The pairwise geometric overlap scores are first computed from the prescribed camera trajectory using Eq. 21. At each autoregressive step, we determine the recent-history budget according to the within-chunk trajectory change, select the historical chunks using Eq. 13, and recompose the corresponding main-GDN state. The current chunk is then generated using the resulting query-specific memory. After generation, an additional $t = 0$ forward pass caches the afine summary of the completed chunk for subsequent retrieval. Throughout this process, camera-control and local convolutional states retain their original chronological updates.



> 💡 **机制拆解（依据原文校验）**: 本段是 HLA-WM 推理流程的路线图，按执行顺序可拆成五步：
> 1. **几何重叠预计算**：先用 Eq. 21 从给定的相机轨迹（prescribed camera trajectory）计算成对的几何重叠分数（pairwise geometric overlap scores），作为后续 chunk 相关性的依据。
> 2. **预算决策**：每个自回归步根据 chunk 内的轨迹变化（within-chunk trajectory change）确定 recent-history budget，也就是本步回看多远的历史。
> 3. **历史 chunk 选择与状态重组**：用 Eq. 13 选出历史 chunk，并重组对应的 **main-GDN state**，得到 query-specific memory。
> 4. **生成当前 chunk**：用上一步重组出的内存生成当前 chunk。
> 5. **缓存 affine summary**：生成完成后额外做一次 $t = 0$ 的前向传播，把已完成 chunk 的 affine summary 缓存下来供后续检索使用。
> 同时，camera-control 和 local convolutional states 始终按原始时间顺序更新，不参与这套重组。
> 
> ⚠️ **初稿纠错**: 初稿称本段“根据 Eq. (4) 动态选择并重组过去的 KV cache”“用重组后的 KV cache 做 Transformer 层计算（Eq. 5）”“更新 $h_t$ 和新 KV cache 条目（Eq. 6-7）”。这些公式编号与 KV cache 表述在原文中均不存在：原文重组的是 **main-GDN recurrent memory**，选择依据是 **Eq. 13**，几何分数来自 **Eq. 21**，缓存的是 **affine summary**。初稿属于超出证据的推断，已按原文改写为上述流程。
> 
> 💡 **Q&A 批注记录**:
> - Q: 为什么需要“重组”而不是直接保留全部历史？
> - A: 按原文逻辑，先用量化的几何重叠分数刻画历史 chunk 与当前相机关联，再按 chunk 内轨迹变化分配 recent-history budget，只重组被选中的 main-GDN state。原文并未在本节给出“固定内存预算”或复杂度数字，这类收益只能作为设计动机推断，不能声称为论文已量化验证的结论。

The retrieval procedure accesses only historical chunks, and the selected chunks are always recomposed in their original temporal order. Importantly, HLA-WM recomposes only the main-GDN recurrent memory; the camera-GDN recurrent state and local convolutional states follow the original streaming recurrence. Historical chunks are therefore never re-evaluated through the Transformer.



> 💡 **机制拆解（因果边界与状态分工）**: 本段是上一段流程的约束条件，限定了检索的因果性和重组范围：
> - **只访问历史**：检索只访问 historical chunks，不含当前或未来 chunk，保证自回归因果性。
> - **按原时序重组**：被选中的 chunk 始终按原始时间顺序重组，不会打乱时序，因此 query-specific memory 仍保留时间结构。
> - **只重组 main-GDN**：HLA-WM 只重组 main-GDN recurrent memory；camera-GDN recurrent state 和 local convolutional states 继续走原始 streaming recurrence。即相机控制状态与局卷积状态不被检索机制改写。
> - **历史 chunk 不重算**：历史 chunk 不会再次经过 Transformer 评估，这是该设计节省计算的直接来源。
> 
> ⚠️ **初稿纠错**: 初稿称“KV cache 大小随时间线性增长”“通过稀疏化/压缩策略（由 Eq. 4 定义）降低单步推理时间与空间复杂度”“将新 KV 对存入缓存（Eq. 6-7）”，并把机制概括为“稀疏化 KV cache”。原文没有出现 KV cache，也没有 Eq. 4 / Eq. 6-7 或复杂度分析。本段真正给出的效率证据是“历史 chunk 不再过 Transformer”。初稿中“特别适合实时或资源受限的视频理解任务”属于原文未声明外推，应标为推断或删除。
> 
> 💡 **可追问点（待证）**: 只重组 main-GDN state、不重组 camera-GDN 与 local convolutional states，在剧烈相机运动或长程遮挡下是否会损失一致性？本节未给消融或实验对比，需查后续实验章节确认。

