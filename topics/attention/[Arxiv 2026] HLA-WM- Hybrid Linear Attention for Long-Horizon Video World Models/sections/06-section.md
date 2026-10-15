[← 返回 README](../README.md)

## 6 Conclusion

We identify long-range forgetting as a key limitation of recurrent linear attention in video world models. Our chunk-level analysis of GDN shows that distant but relevant scene information is progressively attenuated by subsequent, often unrelated, state transitions. Based on this insight, we propose HLA-WM, which retrieves geometrically relevant chunk-wise memories and selectively recomposes them for long-range recall. Experiments on SANA-WM-Bench demonstrate consistent improvements in revisit fidelity and camera controllability, while retaining substantially lower memory cost than full KV caching and introducing only modest inference overhead.



> 💡 **结论段批读**: AI 批注
> - **核心发现**：作者把 long-range forgetting（长程遗忘）定位为 **video world models 中 recurrent linear attention** 的关键限制，而非一般意义上“VLM 的注意力衰减”。归因链条是：对 GDN 的 chunk-level 分析显示，遥远但相关的场景信息会被其后往往无关的状态转移逐步衰减（progressively attenuated）。
> - **方法命名核对**：本文提出的方法是 **HLA-WM**（检索几何相关的 chunk 级记忆并选择性重组以实现长程召回），**不是** SLM。批注初稿中的“SLM（Selective Long-term Memory）”属于凭空构造的模块名，已纠正。
> - **机制拆解**：HLA-WM = ① 按 chunk 组织历史记忆；② 用几何相关性做检索（retrieve geometrically relevant）；③ 对检索到的 chunk 记忆做选择性重组（selectively recompose），从而恢复长程信息。这里的动词是“重组/recompose”，不是“重排序/rerank”，两者语义不同，已按原文修正。
> - **证据边界**：本段只给出定性结论——在 SANA-WM-Bench 上 revisit fidelity 与 camera controllability 一致提升，同时内存开销显著低于 full KV caching，推理开销为 modest。结论段未给出具体数值，具体数字需回查实验章节；但不能据此说“本段未提供实验数据”，因为它明确陈述了在哪个 benchmark 上、哪两类指标提升、以及相对 full KV caching 的成本优势。
> - **可追问点**：modest inference overhead 的具体量级（延迟/显存）需要实验章节支撑；“几何相关”检索在相机运动剧烈或退化场景下是否仍成立，正文未在本段回答。

Limitations and Future Work. HLA-WM is currently training-free, which limits the achievable gains. Future work could integrate selective state retrieval into training to better preserve and exploit long-range memory. In addition, our retrieval relies solely on camera geometry and FOV overlap, without explicitly modeling occlusion, visibility, or semantic relevance. Incorporating richer or learned retrieval signals may further improve robustness in complex scenes.



> 💡 **局限性与未来方向批读**: AI 批注
> - **限制一：training-free 的上限**。HLA-WM 当前是免训练的，作者自述这限制了可获得的增益（limits the achievable gains）。未来方向是把 selective state retrieval 整合进训练，以更好地保留和利用长程记忆。也就是说，当前方案是推理期外挂式补偿，未改动预训练权重。
> - **限制二：检索信号单一**。检索仅依赖相机几何（camera geometry）与 FOV overlap，没有显式建模 occlusion、visibility、semantic relevance。因此“几何上相关”不等于“任务上或视觉上可见”，在复杂场景中可能出现检索到被遮挡或语义无关 chunk 的情况。
> - **改进路径**：引入更丰富或可学习（learned）的检索信号，可能提升复杂场景下的鲁棒性。作者措辞是 may further improve，属于展望而非已验证结论，不应表述为确定性收益。
> - **Q&A 批注记录**:
>   - Q: 为什么 training-free 会限制收益？
>   - A: 正文指出免训练意味着检索/重组逻辑无法与主干模型联合优化，只能作为推理期修正；把选择性状态检索纳入训练是作者给出的下一步（依据本段原文）。
>   - Q: 检索为什么不建模遮挡？
>   - A: 原文明确说检索“solely on camera geometry and FOV overlap”，遮挡/可见性/语义相关性均未显式进入检索打分，这是作者自陈的局限（推断：这可能正是复杂场景鲁棒性不足的来源，但正文未给出对应消融证据，属待证）。

