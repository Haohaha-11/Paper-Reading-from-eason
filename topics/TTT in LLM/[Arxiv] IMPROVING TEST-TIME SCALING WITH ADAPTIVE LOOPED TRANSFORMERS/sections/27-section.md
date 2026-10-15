[← 返回 README](../README.md)

## B.2 INFERENCE DEPTH OUTSIDE THE TRAINING CEILING

We evaluate the M = 8 checkpoint with the inference ceiling lowered to 4 or raised to 12, without retraining. Table 12 reports AIME24–26 accuracy and mean realized depth. Lowering the ceiling to 4 reduces accuracy by 2.7–3.1 points across AIME24–26. Raising it to 12 leaves accuracy essentially unchanged while increasing mean depth by 20–27%. Additional depth at inference therefore neither breaks the model nor improves it beyond the trained ceiling.



> 💡 **机制拆解与证据链**: 本节是「训练深度」与「推理深度」解耦的关键控制变量实验：模型固定为 $M=8$ 的 TaH2 checkpoint，只把**推理时**的深度上限降到 4 或升到 12，**不做任何重训练**。
> - **向下截断有损**：上限降到 4 后，AIME24–26 准确率从 16.3/17.9/14.5 变为 13.6/15.1/11.4，各基准下降 2.7–3.1 点，与原文数字一致。这支持「模型的有效推理路径确实超过了 4 步」；若进一步说「截断破坏了长程依赖、造成表征失配」，则属解释性推断——原文与本节表格并未给出表征层面的机制证据。
> - **向上扩展无效（准确率维度）**：上限升到 12 时，三基准为 16.9/17.2/14.5，相对上限 8 只有 +0.6 / −0.7 / 0.0 的波动，Avg. 仍为 16.2；同时 Mean depth 从 2.22 升到 2.72（按该行均值计约 +22.5%，与原文所述 20–27% 区间一致）。
> - **结论边界**：原文自己的措辞是「neither breaks the model nor improves it beyond the trained ceiling」，即「不崩、也不涨」。可被本表直接支撑的是「在推理阶段堆更多深度无法带来超出训练上限的收益」；但**不能**据此推出多出来的步数是「噪声或冗余」——论文没有分析这些额外步数在算什么，也无法从本表区分「无效空转」与「采样噪声」，该说法应降级为待证推断。
> 
> 💡 **Q&A 批注记录**:
> - Q: 为什么提高推理上限会增加平均深度，却不提高准确率？
> - A: 事实层面由 Table 12 支撑：$M=8$ 模型在上限 8 与 12 时 Avg. 均为 16.2，而 Mean depth 由 2.22 增至 2.72。原文只报告「准确率基本不变 + 实际深度增加」这一现象，未给出机制解释；「模型缺少利用更深层的监督信号」「位置编码/注意力允许超出训练深度的执行」只是可能假说，本节与正文未提供证据，标记为**待证**。此外表中未报告方差，AIME24 的 +0.6 与 AIME25 的 −0.7 是否落在采样噪声范围内，同样待证。

Table 12: Effects of changing the inference depth ceiling. M denotes the training ceiling; the upper group provides reference models evaluated at their training ceilings. Accuracy (%) uses avg@32; Avg. is the mean across AIME24–26.
<table><tr><td>Model</td><td>Inference ceiling</td><td>AIME24</td><td>AIME25</td><td>AIME26</td><td>Avg.</td><td>Mean depth</td></tr><tr><td>Standard</td><td>1</td><td>11.0</td><td>13.0</td><td>11.9</td><td>12.0</td><td>1.00</td></tr><tr><td>TaH2 (M = 2)</td><td>2</td><td>15.7</td><td>16.5</td><td>14.3</td><td>15.5</td><td>1.23</td></tr><tr><td>TaH2 (M = 4)</td><td>4</td><td>16.3</td><td>16.9</td><td>14.2</td><td>15.8</td><td>1.49</td></tr><tr><td>TaH2 (M = 8)</td><td>4</td><td>13.6</td><td>15.1</td><td>11.4</td><td>13.4</td><td>1.55</td></tr><tr><td>TaH2 (M = 8)</td><td>8</td><td>16.3</td><td>17.9</td><td>14.5</td><td>16.2</td><td>2.22</td></tr><tr><td>TaH2 (M = 8)</td><td>12</td><td>16.9</td><td>17.2</td><td>14.5</td><td>16.2</td><td>2.72</td></tr></table>



> 💡 **Table 12 批读**: 读表口径：上半组是各训练上限下的参照模型（在其训练上限处评测），下半组是固定 $M=8$、只改推理上限；Accuracy (%) 用 avg@32，Avg. 是 AIME24–26 三基准的均值，Mean depth 是实际执行的平均深度。
> 1. **深度确实带来收益**：Standard（单步，Mean depth 1.00）Avg. 12.0；$M=8$、推理上限 8 时 Avg. 16.2，绝对提升 4.2 点。这是本表最主要的结果。
> 2. **训练上限的边际趋势**：$M=2 \to 4 \to 8$ 对应 Avg. 15.5 → 15.8 → 16.2，Mean depth 1.23 → 1.49 → 2.22。数字显示「更大的 $M$ 伴随更高准确率与更深实际深度」；但三行来自三个分别训练的模型，原文未做单变量控制或显著性检验，因此不宜读成「$M$ 增大导致性能提升」的严格因果，只能作为趋势观察。
> 3. **截断代价（修正初稿的数字关系）**：$M=8$ + 推理上限 4 的 Avg. 是 13.4，低于 $M=4$ 在自身上限下的 15.8，**也低于 $M=2$ 的 15.5**（相差 2.1 点），仅高于 Standard 的 12.0。因此准确表述是「被截断的 $M=8$ 退化到单步基线与 $M=2$ 之间的水平」，而非初稿所称「接近 $M=2$ 的水平」。顺带注意该行 Mean depth 1.55 仍高于原生 $M=4$ 的 1.49，说明它在被限到 4 步前更倾向走深路径——此为对表中数字的推断，原文未解释。
> 4. **表征失配是推断而非结论**：$M=8$ 被限到 4 步（13.4）明显不如原生 $M=4$（15.8），暗示深层模型的有效行为依赖其超过 4 步的推理路径；但「内部表征是为更深推理优化的、强行浅用会失配」属解释性推断，表中没有表征层面的证据。
> 5. **统计口径提醒（待证）**：本表未报告方差或置信区间。上限 12 时 AIME24 +0.6、AIME25 −0.7 这类量级的变化可能落在采样噪声内，因此「essentially unchanged」宜理解为原文的定性陈述，而非已被等价性检验确认的结论。

