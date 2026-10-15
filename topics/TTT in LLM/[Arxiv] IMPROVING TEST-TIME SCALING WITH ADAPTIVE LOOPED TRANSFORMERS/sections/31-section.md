[← 返回 README](../README.md)

## C.3 PARALLEL TEST-TIME SCALING

Test-time compute can also scale in parallel, by sampling several responses and aggregating them with majority voting. Figure 10 measures this axis with cons@n on AIME24–26: for each problem, we vote over n of the 32 recorded full-length responses, drawn without replacement and averaged over random draws, and count decoding FLOPs as n times the mean per-response cost. TaH2 at $M = 2$ reaches 27.3% at $n = 3 2$ compared with 21.9% for Standard, and its curve stays above every fixed-depth baseline at matched FLOPs.



> 💡 **机制拆解（AI 批注）**: 本段定义的是 test-time scaling 的**并行轴**：不再只延长单条推理链，而是同时采样多条 response，再用 majority voting 聚合成一个答案。原文给出的实验载体是 Figure 10，指标名为 cons@n（consensus@n，即对 n 条 response 投票后的准确率），评测集为 AIME24–26。与串行扩展（单条链更长的 fixed-depth 基线）形成对照，比较的基准是**等 decoding FLOPs**，而不是等样本数。
> 
> 💡 **协议批读（AI 批注）**: 投票协议有两个细节值得记牢：一是候选不是重新在线采样，而是从每题已记录的 32 条**全长** response 中**不放回**抽取 n 条；二是对“随机抽取”这一随机性做了多次重复并取平均，以减少抽样运气带来的方差。计算成本的记账方式被显式定义为 decoding FLOPs = n × 单条 response 的平均成本，这解释了为什么横轴按 FLOPs（而非 n）对齐后才公平。
> 
> 💡 **数字核对（AI 批注）**: 两个可核对的数字是 27.3% 与 21.9%，条件分别是 TaH2（$M = 2$）在 $n = 32$，以及 Standard 在 $n = 32$；即差值约 5.4 个百分点，且 TaH2 的曲线在等 FLOPs 下高于所有 fixed-depth 基线。注意原文在全文语境里用的是 TeH2（文中出现过 TaH2 的拼写，疑为同一对象的 OCR/笔误，此处按原样保留），这一点需要以正文与图注为准。
> 
> 💡 **推断与待证（AI 批注）**: 由于 32 条记录中不放回抽 $n = 32$，可推断 $n = 32$ 实际等于使用全部记录，不再有抽样方差——这是基于文中“drawn without replacement”和“32 recorded responses”的推断，原文未直接明说。另两点本段未给出、不能替读者补全：（1）$M = 2$ 的具体含义（需回正文定义）；（2）并行投票带来的额外工程开销（同步、验证器、显存）本段未讨论，任何关于延迟的结论都属于外部推断而非本文证据。

