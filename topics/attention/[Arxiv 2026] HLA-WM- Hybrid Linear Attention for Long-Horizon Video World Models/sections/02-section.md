[← 返回 README](../README.md)

## 2 Related Work

Video World Models. Recent advances in video generation have enabled world models to predict future visual observations conditioned on actions, camera trajectories, or other control signals, supporting interactive and long-horizon environment simulation [3–7]. To maintain scene consistency over extended rollouts, existing approaches preserve historical information in diferent forms. Frame-based methods retain selected historical observations as references for future generation [8, 10]. Token- or KV-based methods preserve historical visual tokens or attention caches to provide direct access to past context [9, 19, 20]. Feature-memory methods summarize past observations into compact latent representations or dedicated memory tokens [21]. Spatial-memory approaches instead maintain explicit geometric or 3D representations that can be queried according to the current viewpoint [8, 10, 22]. Although these mechanisms improve long-range consistency, they introduce additional storage and retrieval overhead that can grow with the rollout. In particular, frame-based memories may require retrieved observations to be re-encoded and their attention caches recomputed, while token, feature, and spatial memories require maintaining and querying additional historical representations, limiting their eficiency for long-horizon generation.



> 💡 **AI 批注**: **问题动机与机制拆解**：本段把视频世界模型的长程记忆归为四类：帧级保留选定的历史观测作参考 [8,10]；Token/KV 级保留历史视觉 token 或注意力缓存 [9,19,20]；特征记忆把过去观测总结为紧凑潜表示或专用记忆 token [21]；空间记忆维护可按当前视角查询的显式几何/3D 表示 [8,10,22]。这些路线的共同点是提升长程一致性，但代价是额外的存储与检索开销会随 rollout 增长。原文用 “may require” 描述帧级记忆需要重新编码检索观测并重算注意力缓存，因此这是“可能”而非必然开销；批注已将初稿的“需要”修正为“可能需要”。关键对比在于：帧级重编码/重算，token、feature、spatial 则要维护和查询额外历史表示 [8,10,22]。

Linear Attention for World Models. Linear attention reformulates attention through associative computation, allowing historical information to be summarized into fixed-size recurrent states instead of maintaining a KV cache that grows with sequence length [13, 23]. Building on this formulation, subsequent works improve the expressiveness and memory management of linear states through more sophisticated recurrent updates. Gated Linear Attention introduces datadependent decay to selectively forget historical information [24], while DeltaNet employs the delta rule to enable targeted updates to the recurrent memory [25]. Gated DeltaNet (GDN) further combines these two mechanisms, providing both adaptive forgetting and selective memory updates within a fixed-size state [15]. More recently, recurrent linear attention has been extended to autoregressive video generation, where linear states serve as compact cross-frame memory for long-form generation [7, 14]. Despite their favorable memory eficiency, these methods continuously compress historical observations into a limited recurrent state, making distant information increasingly susceptible to attenuation and interference from subsequent updates during long-horizon generation.



> 💡 **AI 批注**: **线性注意力演进与局限**：本段先定义线性注意力的效率来源：通过关联计算把历史总结成固定大小的循环状态，而不是维护随序列长度增长的 KV cache [13,23]。随后是记忆更新的三级演进：GLA 用数据依赖衰减做选择性遗忘 [24]；DeltaNet 用 delta 规则做目标更新 [25]；GDN 结合两者，在固定状态中同时得到自适应遗忘和选择性更新 [15]。最后把这一路线接到自回归视频生成 [7,14]，并指出紧凑跨帧记忆的代价：历史被持续压缩进有限状态，远距离信息更易受后续更新的衰减（attenuation）和干扰（interference）影响。批注边界：初稿“难以在固定大小状态下完美保留长程细节”是超出原文的推断，原文只支持“远距离信息更易衰减/干扰”，未限定为“细节”，已改为证据边界内的表述。

![](../images/0626d700ecc0672a34b593ee33faada2b9097a1c4dad3a3d8c00ef58ba6d364e.jpg)  
Figure 2 Long-range forgetting in GDN. The camera follows a long loop and eventually returns near the region observed at Chunk 3 around Chunk 35. We visualize representative generated chunks along the trajectory together with the cumulative retention $W _ { 3 \to j }$ of Chunk 3. Although the early scene becomes relevant again near the end of the rollout, its retained influence continuously decreases as intermediate chunks are generated, reaching only 0.0416 at Chunk 35.



> 💡 **AI 批注**: **Figure 2 批读（长程遗忘证据）**：初稿误称该图“未提供 caption”，但原文 caption 明确给出实验设定：相机走一条长环，约在 Chunk 35 回到 Chunk 3 附近区域；图中可视化沿轨迹的代表性生成 chunk，并给出 Chunk 3 的累计保留量 $W_{3\to j}$。关键证据是：早期场景在 rollout 末端重新变得相关时，其保留影响仍随中间 chunk 的生成持续下降，在 Chunk 35 只有 0.0416。因此该图不是效率对比或方法框架图，而是用 GDN 的累计保留数值直接支撑“固定大小循环状态会遭受长程遗忘”的问题动机。未获得图片视觉细节，以上仅依据已提供的 caption 描述。

