# Uncategorized · 待归类论文

由 HaOceanLab 的 AI 精读流程自动生成、尚未归入研究主线的论文。每篇目录下有完整原文、按章节的中文批注、README 概览和研究卡片；确定主线后整个目录移到对应 topic 即可。

| 论文 | arXiv | 一句话 |
|---|---|---|
| [SpatialASM: Internalizing Active Mentalization for Spatial Reasoning](./%5BArxiv%202026%5D%20SpatialASM-%20Internalizing%20Active%20Mentalization%20for%20Spatial%20Reasoning/README.md) | [2610.13832](https://arxiv.org/abs/2610.13832) | 用训练阶段的异中心新视角生成器合成空间推理思维链，把“依问题规划视角、并在心里想象该视角所见”的能力内化进 LVLM，使模型在推理时不再需要外部模拟器或空间工具也能答得更准。 |
| [HLA-WM: Hybrid Linear Attention for Long-Horizon Video World Models](./%5BArxiv%202026%5D%20HLA-WM-%20Hybrid%20Linear%20Attention%20for%20Long-Horizon%20Video%20World%20Models/README.md) | [2610.05739](https://arxiv.org/abs/2610.05739) | 长时程视频世界模型用 Gated DeltaNet (GDN) 这类循环线性注意力把全部历史压进固定大小状态，相机长距离回访时早期场景会被后续更新逐步“洗掉”；HLA-WM 免训练地把每个 chunk 的 GDN 状态转移缓存为紧凑仿射摘要 $(A_i,B_i)$，用相机几何检索几何相关的历史 chunk，再与 sink 块、近期块一起按时间序重组为 query 特定的循环状态。在 60 秒 SANA-WM-Bench 上，Stage 1 直接把 Hard 轨迹 PSNR 提升 0.74 dB、旋转误差降低 26.0%，并把 60 秒上下文的历史状态内存较 full KV 缓存减少 12×。 |
| [IMPROVING TEST-TIME SCALING WITH ADAPTIVE LOOPED TRANSFORMERS](./%5BArxiv%5D%20IMPROVING%20TEST-TIME%20SCALING%20WITH%20ADAPTIVE%20LOOPED%20TRANSFORMERS/README.md) | [2609.35748](https://arxiv.org/abs/2609.35748) | 在 post-training 设定下研究循环 Transformer 的测试时扩展：现有循环模型斜率更陡但在匹配算力下精度更低；作者提出 TaH2，用 lookahead depth supervision 联合后训练骨干与 token 级 iteration decider，把额外迭代分配给真正受益的 token。 |
| [RSI-Anything: Unlocking Recursive Self-Improvement with World Models](./%5BArxiv%5D%20RSI-Anything-%20Unlocking%20Recursive%20Self-Improvement%20with%20World%20Models/README.md) | [2610.13912](https://arxiv.org/abs/2610.13912) | RSI-Anything 是构建在 JEPA-Anything 预测式世界模型之上的领域通用递归自我改进框架：世界模型预测被用来挑选候选动作、排序外部评估、选择或构造学习数据；外部反馈驱动策略学习或工作流修订，并按可靠性、数据与预算条件触发世界模型适配；组件检查与策略–模型配对兼容性检查决定保留哪一对，被保留的一对再塑造下一轮的候选与经验。 |
