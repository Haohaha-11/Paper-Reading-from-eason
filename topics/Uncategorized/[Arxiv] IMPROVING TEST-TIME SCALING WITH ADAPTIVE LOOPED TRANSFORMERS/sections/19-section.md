[← 返回 README](../README.md)

## A.2.2 ARCHITECTURE SUMMARY AND PARAMETER COUNTS

Table 6 summarises the architectures; Table 7 separates the updater and decider parameters at each scale. Together, they account for less than 3% of total parameters.



> 💡 **机制拆解**: 本段是附录 A.2.2 的结论句，交代两张表的分工——Table 6 汇总各架构（模型、Loop scope、Depth allocation、Added module、Added params），Table 7 按 backbone 规模把 updater 与 decider 的参数拆开，并给出总量口径主张：both tables account for "less than 3% of total parameters"。
> 
> **数字核对**: Table 7 三档规模的 Added (%) 分别为 2.61%、1.76%、2.20%，全部小于 3%，该主张在表内成立，无需修改。另需注意 Table 6 中 TaH2 的 "46.2M (2.6%)" 与 Table 7 中 1.7B 的 46.16M / 2.61% 是同一数量的四舍五入写法，两表互不矛盾。
> 
> **证据边界（重要）**: 原文此处只给出参数计数与占比，没有任何准确率、损失或速度对照。因此不能由本段推出"性能提升来自架构设计而非参数堆砌"这类因果结论——那需要正文的实验表支撑，本附录块不构成该因果的证据。若初稿把它写成"为论证性能提升并非来自参数堆砌提供基础数据支撑"，应明确标为批注者推断而非原文陈述。
> 
> **待证**: 本段与两表均未说明 TaH2、Ouro、Huginn 中哪个是本文提出的方法、哪些是对比基线；方法归属须回到正文确认，不能在本块自行认定。

Table 6: Architectures used in the 1.7B comparison. Added parameters are reported as counts and fractions of total model parameters.
<table><tr><td>Model</td><td>Loop scope</td><td>Depth allocation</td><td>Added module</td><td>Added params</td></tr><tr><td>Standard</td><td>none (M = 1)</td><td></td><td></td><td>0</td></tr><tr><td>TaH2</td><td>all L layers</td><td>per-token adaptive</td><td>updater + decider</td><td>46.2M (2.6%)</td></tr><tr><td>TaH2-fixed</td><td>all L layers</td><td>fixed</td><td>updater</td><td>21.0M (1.2%)</td></tr><tr><td>Ouro (fixed-depth)</td><td>all L layers</td><td>fixed</td><td></td><td>0</td></tr><tr><td>Ouro (adaptive)</td><td>all L layers</td><td>per-token adaptive</td><td>exit gate</td><td>2.0K (&lt;0.001%)</td></tr><tr><td>Huginn</td><td>layers 8–21</td><td>fixed</td><td>input adapter</td><td>4.2M (0.24%)</td></tr></table>



> 💡 **Table 6 批读**: 该表在 1.7B 规模下横比五种架构的参数开销（括号内为占总参数比例），逐行核对如下：
> - Standard：Loop scope 为 "none (M = 1)"，无 Added module，Added params 为 0。
> - TaH2："all L layers" + "per-token adaptive"，新增 "updater + decider"，"46.2M (2.6%)"。
> - TaH2-fixed："all L layers" + "fixed"，只加 "updater"，"21.0M (1.2%)"。
> - Ouro (fixed-depth)："all L layers" + "fixed"，无 Added module，0 参数。
> - Ouro (adaptive)："all L layers" + "per-token adaptive"，新增 "exit gate"，"2.0K (<0.001%)"。
> - Huginn：只覆盖 "layers 8–21" + "fixed"，新增 "input adapter"，"4.2M (0.24%)"。
> 
> **自洽性换算**: Table 7 中 1.7B 行的 updater 20.98M 与 decider 25.18M 相加为 46.16M，正是 TaH2 的总增量，说明 "updater + decider" 可与 Table 7 的拆分对齐；TaH2-fixed 的 21.0M 对应 Table 7 中 1.7B 的 updater 20.98M，而 2.0K / 1720.57M ≈ 0.00012%，故 "<0.001%" 成立。
> 
> **表述边界**: 表中给的是参数计数，没有计算量（FLOPs）、显存或延迟数据，因此"几乎零成本"只能严格限定为"新增参数极少"，不能推及推理开销；"实现了动态深度控制"也只能理解为表内 Depth allocation 列标注的 per-token adaptive 配置，表中并未给出门控效果或深度分布结果。
> 
> **待证**: 该表未标注 TaH2、Ouro、Huginn 各自是本文方法还是对比基线。若初稿把 TaH2 称为"论文提出的完整自适应版本"，属超出本块证据的归属判断，应标为待证。

Table 7: Updater and decider parameters across backbone scales. Counts include projections, MLPs and RMSNorm scales and are rounded independently. Percentages are relative to total model parameters.
<table><tr><td>Scale</td><td>Backbone</td><td>Updater</td><td>Decider</td><td>Total added</td><td>Added (%)</td></tr><tr><td>1.7B</td><td>1720.57M</td><td>20.98M</td><td>25.18M</td><td>46.16M</td><td>2.61%</td></tr><tr><td>4B</td><td>4022.47M</td><td>32.78M</td><td>39.33M</td><td>72.11M</td><td>1.76%</td></tr><tr><td>8B</td><td>8190.74M</td><td>83.90M</td><td>100.68M</td><td>184.59M</td><td>2.20%</td></tr></table>



> 💡 **Table 7 批读**: updater、decider、总增量与占比在原文表格文字中已逐项给出，不需要依赖图片视觉信息或前文推断：
> - 1.7B：backbone 1720.57M，updater 20.98M，decider 25.18M，total added 46.16M，Added 2.61%
> - 4B：backbone 4022.47M，updater 32.78M，decider 39.33M，total added 72.11M，Added 1.76%
> - 8B：backbone 8190.74M，updater 83.90M，decider 100.68M，total added 184.59M，Added 2.20%
> 
> **口径核对**: caption 说明 "Percentages are relative to total model parameters"。用 backbone + added 复算：46.16 / (1720.57 + 46.16) = 2.61%、72.11 / (4022.47 + 72.11) = 1.76%、184.59 / (8190.74 + 184.59) = 2.20%，与表内数值一致，说明分母包含新增参数。三行均满足 updater + decider = total added。
> 
> **趋势观察（AI 批注推断，非原文结论）**: 占比并非恒定，而是 2.61% → 1.76% → 2.20%，先降后升。从 1.7B 到 8B，updater 与 decider 各约增长 4.0 倍，而 backbone 增长约 4.76 倍，因此占比被压在 1.8%–2.6% 区间。原文并未解释先降后升的原因，这里只做表内数字的算术观察。
> 
> **结构观察**: 三档规模中 decider 参数量都大于 updater（25.18 > 20.98、39.33 > 32.78、100.68 > 83.90），与 Table 6 中 TaH2-fixed 去掉 decider 后参数由 46.2M 降到 21.0M 相呼应。caption 另注明 "rounded independently"，即各行独立取整，不应在表内小数位上追求更高精度的一致性。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 初稿称此表"未直接显示数值（依赖图片视觉信息或前文上下文）"，是否成立？
> > - A: 不成立。Table 7 的 backbone、updater、decider、total added 与百分比在原文文字表格中逐项给出，可直接引用并复算。

