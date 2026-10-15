[← 返回 README](../README.md)

# HLA-WM: Hybrid Linear Attention for Long-Horizon Video World Models

Zhuokun Chen<sup>1</sup> Feng Chen<sup>2</sup> Xi Lin<sup>3</sup> Xiyu Wu<sup>3</sup> Jiahao He<sup>3</sup> Jianfei Cai<sup>1</sup> Bohan Zhuang<sup>3</sup>



> 💡 **作者与机构批读**: 该行为作者列表，共 7 人：Zhuokun Chen(¹Monash)、Feng Chen(²Adelaide)、Xi Lin(³ZJU)、Xiyu Wu(³ZJU)、Jiahao He(³ZJU)、Jianfei Cai(¹Monash)、Bohan Zhuang(³ZJU)。上标数字需与下一行的机构行对照才能确定归属。
> 💡 **推断（待证）**: 由标题与摘要可推断该团队工作重心之一是视频世界模型的长时程记忆，但原文此处仅给出姓名与编号，未声明研究方向；不必据此推断团队分工。

<sup>1</sup>Monash University <sup>2</sup>The University of Adelaide <sup>3</sup>Zhejiang University



> 💡 **机构批读**: 该行把上标数字映射为三家机构——¹Monash University、²The University of Adelaide、³Zhejiang University。结合上行可知：Monash 对应第 1、6 作者，Adelaide 对应第 2 作者，Zhejiang University 对应第 3、4、5、7 作者。
> 💡 **证据边界**: 原文只提供机构署名，未说明各机构承担的具体工作（如谁负责基准构建、谁负责方法设计）。任何关于“跨机构互补优势”“数据集/基准分工”的说法都超出本行证据，属外部推测，不应写入批注。

Abstract. Long-horizon video world models require persistent memory to preserve scene consistency over extended rollouts. Softmax attention retains the full generation history through a growing KV cache, whereas recurrent linear attention compresses history into fixed-size states with substantially lower memory cost. However, we identify severe long-range forgetting in Gated DeltaNet (GDN), where information from distant but relevant scenes is progressively attenuated by subsequent state updates. To address this limitation, we propose HLA-WM, a training-free hybrid linear-attention framework that combines coarse-grained geometry-guided retrieval with fine-grained recurrent linear-state computation. HLA-WM exploits the afine structure of GDN to cache compact chunk-wise transition summaries, retrieve scene-relevant historical chunks using camera geometry, and recompose them into query-specific recurrent states. On the 60-second SANA-WM-Bench, HLA-WM improves all six aggregate revisit-consistency and camera-control metrics of the base autoregressive generator without additional training, including a 0.74 dB PSNR gain and a 28.5% reduction in rotation error. The improvements persist after downstream refinement and generalize to MBench-A, where HLA-WM consistently improves all three revisit-consistency metrics across all four subsets and all evaluated inference modes over 547 samples. At a 60-second context, HLA-WM reduces historical-state memory by 12× relative to full KV caching while incurring at most a 1.6% reduction in inference throughput. These results demonstrate that selectively addressable recurrent memory can improve long-range scene recall while preserving the eficiency advantages of GDN.



> 💡 **机制拆解与核心贡献**:
> 1. **问题设定**: 长时程视频世界模型需要持久记忆来维持长时间 rollout 的场景一致性。两类路线对比——softmax attention 以不断增长的 KV cache 保留完整生成历史；recurrent linear attention 把历史压缩进固定大小状态，内存开销显著更低。
> 2. **痛点识别**: 作者指出 Gated DeltaNet (GDN) 存在严重的“长程遗忘”（severe long-range forgetting）：来自远处但相关场景的信息会被后续的状态更新逐步衰减。
> 3. **方法**: HLA-WM 是 **training-free（免额外训练）** 的混合线性注意力框架，把“粗粒度几何引导检索”与“细粒度循环线性状态计算”结合：利用 GDN 的 affine 结构缓存紧凑的 chunk-wise transition summaries，用相机几何检索与场景相关的历史 chunk，再重组为 query 特定的循环状态。
> 4. **实验结果（数字已逐项核对）**: 在 60 秒的 SANA-WM-Bench 上，对基础自回归生成器在不做额外训练的前提下，全部 6 项聚合的 revisit-consistency 与 camera-control 指标均有提升，其中 PSNR +0.74 dB、旋转误差降低 28.5%；经下游 refinement 后提升仍保持，并泛化到 MBench-A——在 547 个样本上、全部 4 个子集与所有评测推理模式下，3 项 revisit-consistency 指标一致提升。
> 5. **效率**: 在 60 秒上下文下，历史状态内存相对 full KV caching 减少 12×，推理吞吐最多下降 1.6%。
> 💡 **文字勘误**: 原文摘要写作 “afine structure”，按上下文应为 “affine”（仿射）之拼写笔误；批注按“仿射结构”理解。
> 💡 **Q&A 批注记录**:
> - Q: 为什么选择“免训练”这一设定？
> - A: 摘要只陈述了 “training-free” 这一事实，以及提升是在不给基础自回归生成器做额外训练的条件下取得的（“without additional training”）。至于动机是否为“即插即用”“避免训练成本”“验证架构通用性”，摘要未给出依据，属**推断（待证）**，需查阅 Introduction/Method 确认。

Project Page: https://caesarhhh.github.io/hla-wm/



> 💡 **项目页批读**: 该行给出项目主页链接 `https://caesarhhh.github.io/hla-wm/`，与 arxiv_id 2610.05739 一并构成复现入口。
> 💡 **证据边界**: 本次核对只能确认该 URL 出现在原文，**未访问**该页面，故不对其是否包含代码、权重、额外可视化或补充实验作任何断言。

Keywords: Video World Models, Linear Attention, Long-Range Memory, Gated DeltaNet



> 💡 **关键词批读**: 关键词为 Video World Models（应用场景）、Linear Attention（注意力形式）、Long-Range Memory（要解决的瓶颈）、Gated DeltaNet（所针对的具体 backbone/状态更新机制）。这四个词与摘要的叙事完全对应：问题在长程记忆，载体是线性注意力中的 GDN，落点是视频世界模型。
> 💡 **推断（待证）**: 关键词本身不构成论证；把它称为“后续方法论章节的理论基石”是修辞性推断，实际的方法论依据需以正文（尤其是 GDN 仿射结构的形式化描述）为准。

