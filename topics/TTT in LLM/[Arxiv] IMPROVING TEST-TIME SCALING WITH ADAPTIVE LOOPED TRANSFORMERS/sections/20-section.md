[← 返回 README](../README.md)

## A.3 EVALUATION PROTOCOL

Validation loss and the token-level analyses of Section 5.5 use a fixed validation set of 1,000 samples randomly drawn from the training mixture and excluded from training, scored under teacher forcing. Test-time scaling curves sweep the output-token cutoff and re-evaluate truncated responses; each figure specifies its cutoff range. For the 4K–16K curves in Figure 1a, linear fits of accuracy against log decoding FLOPs yield $R ^ { 2 } = 0 . 9 9 7$ for Ouro, 0.994 for Huginn and 0.955 for Standard. We estimate decoding FLOPs from the serving engine’s depth and token records as described in Appendix A.4.



> 💡 **机制拆解与数字核对（已修正）**: 本段界定两类评估设置。① 验证损失与 §5.5 的 token 级分析共用同一固定验证集：从训练混合中随机抽 1,000 个样本、排除出训练、以 teacher forcing 评分；原文只交代抽取与评分方式，未说该验证集“用于监控训练”，初稿此句属推断。② 测试时扩展曲线不是重新训练，而是扫描输出 token 截断点、对被截断回答重新评估，截断范围由各图标注。③ 关键数字：Figure 1a 的 4K–16K 区间内，accuracy 对 log 解码 FLOPs 做线性拟合，$R^2$ = 0.997（Ouro）、0.994（Huginn）、0.955（Standard）；这是**对数线性**关系，不能等同于“幂律增长”——幂律需在 log-log 坐标下检验，初稿此处外推过度。④ 解码 FLOPs 估计方式是从 serving engine 的 depth 与 token 记录换算，细节见 Appendix A.4。
> > - Q&A：为什么强调“截断后重新评估”？A：长 CoT 模型一旦截断，回答可能不完整，必须重打分才能让不同计算预算下的准确率可比，对应原文“sweep the output-token cutoff and re-evaluate truncated responses”。

Table 8: Benchmarks used in this paper. The default output-token limit is 32K.
<table><tr><td>Benchmark</td><td></td><td>Domain #Problems Metric</td><td></td><td>Subset</td></tr><tr><td>AIME24 / AIME25 / AIME26</td><td>math</td><td>30 / 30 / 30avg @32</td><td></td><td></td></tr><tr><td>AMC23</td><td>math</td><td></td><td>40 avg@32</td><td></td></tr><tr><td>MATH500 (Lightman et al., 2023)</td><td>math</td><td></td><td>500 avg@4</td><td></td></tr><tr><td>OlympiadBench (He et al., 2024)</td><td>math</td><td></td><td>675 avg@4</td><td></td></tr><tr><td>IMO-AnswerBench</td><td>math</td><td></td><td>400avg @4</td><td></td></tr><tr><td>GPQA (Rein et al., 2023)</td><td>QA</td><td></td><td>198 avg@8</td><td>Diamond</td></tr><tr><td>SuperGPQA</td><td>QA</td><td>7,050 avg@4</td><td></td><td>Hard</td></tr><tr><td>HumanEval (Chen et al., 2021)</td><td>code</td><td></td><td>164 avg@8</td><td></td></tr><tr><td>MBPP (Austin et al., 2021)</td><td>code</td><td></td><td>378avg@8</td><td></td></tr><tr><td>LiveCodeBench v6 (Jain et al., 2024) code</td><td></td><td></td><td>175 avg@4</td><td></td></tr><tr><td>BFCL v3 (Patil et al., 2025)</td><td>tool use</td><td></td><td>800 avg@1(greedy) Multi-turn</td><td></td></tr></table>



> 💡 **Table 8 基准配置核对**: 表格按领域覆盖 math、QA、code、tool use，按 benchmark 计共 13 个（AIME24/25/26 在表中合并为一行）。math：AIME24/25/26（各 30 题，avg@32）、AMC23（40，avg@32）、MATH500（500，avg@4）、OlympiadBench（675，avg@4）、IMO-AnswerBench（400，avg@4）；QA：GPQA（198，avg@8，Diamond 子集）、SuperGPQA（7,050，avg@4，Hard 子集）；code：HumanEval（164，avg@8）、MBPP（378，avg@8）、LiveCodeBench v6（175，avg@4）；tool use：BFCL v3（800，avg@1 greedy，Multi-turn）。默认输出上限 32K tokens。可核实事实是题量跨度 30–7,050、采样次数从 avg@1 到 avg@32。初稿中“支持长思维链 CoT”“以消除随机性噪声”“旨在全面评估泛化能力、而非只针对单一狭窄任务优化”均属合理但非原文的推断——本段只列子集、题量与指标，未解释选择动机，已明确标为推断而非论文结论。注意：解析结果中表格列对齐有错位（如 SuperGPQA 的 7,050 被排到 Metric 列前），应以原始 Table 图片为准。

Runtime measurement. We extend Mini-SGLang (Xu, 2025) to batch requests at different iteration depths in a shared forward pass, following continuous depth batching (Schwethelm et al., 2026a). Once a request completes the iterations for its current token, it proceeds to decode the next token without waiting for other requests to finish their iterations. We evaluate the 30 AIME26 problems with a 32K output-token limit on a single A800-80GB GPU, using bfloat16 and temperature 0.6; batch sizes 1 and 4 use one and four samples per problem, respectively. Table 2 reports decoding GFLOPs per generated token, computed from the per-call costs in Appendix A.4.1, together with mean end-to-end latency and aggregate output-token throughput. All results use the same evaluation seed.



> 💡 **运行时测量机制拆解（补充关键条件）**: 实现层面扩展 Mini-SGLang，按 continuous depth batching（Schwethelm et al., 2026a）把处于不同迭代深度的请求合并进同一次共享前向；某一请求完成当前 token 所需的迭代后立即解码下一个 token，不必等待同批其他请求结束迭代。据此可推断，测量的是混合深度批处理下的吞吐，而非单请求串行延迟。具体条件：30 道 AIME26、32K 输出上限、单张 A800-80GB、bfloat16、temperature 0.6；batch size 1 与 4 分别表示每问题采 1 与 4 个样本。Table 2 报告每生成 token 的解码 GFLOPs（由 Appendix A.4.1 的单次调用开销折算）、平均端到端延迟与聚合输出 token 吞吐，且所有结果共用同一评估种子。证据边界：“更真实模拟实际部署”“对早期退出/自适应计算的加速比至关重要”属推断，原文未如此表述；但“未报告硬件延迟指标”与原文相反——延迟与吞吐都在 Table 2 给出，这是初稿摘要必须纠正的一处硬伤。

