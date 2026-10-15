[← 返回 README](../README.md)

## GUI task completion and action behavior

![](../images/4d263f763df44ddec549b59738c19d1b00e8217680b6fc09143a4ccd8a0d904d.jpg)



> 💡 **Figure 4a–b 批读**: 该图块本身无文字，只能依 Figure 4 的 caption 定位：a/b 为 MiniWoB++ 复选框选择与 OSWorld 电子表格补全任务的示意，展示初始态与目标态，用来界定“任务完成”的判定基准。⚠️ 未获得图像视觉信息，图中具体控件、布局与初始/目标差异无法核实，子图与图块的对应关系属推断。

![](../images/99adf258c59562f7d0d9e81c2a413b20229cf31866d7eb12f9a8e11ff77dc21d.jpg)  
d



> 💡 **Figure 4c–d 批读（依 caption）**: 该图块文本仅残留标签 `d`，MinerU 已打乱子图与标签顺序，不能据此断定该块就是 d。按 caption：c 报告 MiniWoB++ click-checkboxes 在 baseline、adapted、final 三种配置下的成功率，并含 World-based 对照；d 报告 OSWorld 相对初始 agent 的成功率增益，annotation 给出 RSI-Anything 相对 RLAnything 的**额外相对增益**，且所有增益都是相对百分比而非绝对分数。图块↔子图对应为推断，待图像核对。

c  
![](../images/2621b3bba24ff8a58711238cb6d1c7b640663b0b70a879ae1c7c164a5962da43.jpg)



> 💡 **Figure 4e–g 批读（依 caption）**: caption 说明 e、f、g 依次为平均每 episode 动作数、重复点击计数、出现错误提交的 episode 数，三者均针对 c 中评估的 RSI-Anything 各配置，且“曲线连接被评估的各配置”说明横轴是配置序列而非时间。注意：原文文本未给出这三项的具体数值或显著性，趋势需回图核实；该图块的子图归属为推断。

![](../images/ef5533711283ec8506d5d38f24ce12f91165864a71ee042b4c94d1e199380d60.jpg)



> 💡 **Figure 4 组图整体批读**: 按 caption 的结构，该组图分为三层：任务定义（a、b 的初始态/目标态）→ 性能结果（c 的成功率曲线、d 的相对增益）→ 行为统计（e–g 的动作数、重复点击、错误提交）。这一分层是 caption 的显式结构，而非本文文本的论断；原文正文只给出配置维度与对比对象，未给数值、方差或显著性，因此不能用该组图推断“效率提升导致成功率提升”之类的因果链。

e  
f  
![](../images/d1258256ca0bffb37f36769df4d4fe6df43d25728d100833803d676b8c8952d7.jpg)



> 💡 **Figure 4e–g 批读（因果界限）**: 初稿提出“错误提交减少是否由重复点击减少驱动”，这是一个有研究价值的问题，但 caption 只是并列列出三个指标、并未建立三者间因果，原文全文也未作此断言，故该因果读法仅为推断、待图内数值与配置标签核实后才能判断。可以确认的是：三指标属于同一批 c 中的配置，且图内曲线连接的是被评估的各配置，因此可做配置间的同口径比较，但不能做指标间的因果归因。

![](../images/6401d1da76b4ec5660e5c92a2d764b6de9f0bebe66d76c34f0dd23e4a8ac4bb0.jpg)



> 💡 **Figure 4g 批读**: 依 caption，该子图统计的是“出现错误提交的 episode 数”（count，而不是“错误提交率”），对象仍是 c 中的 RSI-Anything 配置。把它解释为“策略鲁棒性提升的直接证据”超出 caption 与正文表述范围，属推断。可稳妥的读法是：该指标衡量任务完成过程中的负向行为频次，需与 c 的成功率、e 的动作数一起看，才能判断增益来自更有效的交互而非更多次试错。

g  
![](../images/09c9e372686a71de45d6816285032d6cfc99a03df9dea719646b7489219a4f01.jpg)  
Figure 4 | Task completion and action behavior in GUI environments. $\mathsf { a } , \mathsf { b } ,$ , Illustrative MiniWoB++ checkbox selection and OSWorld spreadsheet completion tasks, showing initial and target states. c, MiniWoB++ click-checkboxes success across baseline, adapted, and final configurations for RSI-Anything and the World-based control. d, OSWorld success-rate gains relative to the initial agent. The annotation gives the additional relative gain of RSI-Anything over RLAnything; all gains are relative percentages. e–g, Mean actions per episode, repeated-click count, and number of episodes with incorrect submissions, respectively, for the RSI-Anything configurations in c. Lines connect evaluated configurations.



> 💡 **Figure 4 Caption 批读 — 子图定位表**: a/b = 示意任务（MiniWoB++ 复选框、OSWorld 电子表格，展示初始与目标态）；c = MiniWoB++ click-checkboxes 成功率，含 baseline/adapted/final 配置与 World-based 对照；d = OSWorld 相对初始 agent 的成功率增益，annotation 为 RSI-Anything 相对 RLAnything 的额外相对增益；e–g = 平均每 episode 动作数、重复点击计数、错误提交 episode 数。两条容易被忽略的限定语：所有增益都是**相对百分比**（不是绝对百分点），以及“Lines connect evaluated configurations”。⚠️ 本条为文本/ caption 层面的核对，未获得图像视觉信息。

The Final Score increased across all five evaluated versions: 21.80%, 38.03%, 41.99%, 44.56%, and 46.01% (Figure 5). The final version improved on the initial version by 24.21 percentage points. This progression shows continued improvement in protocolaligned planning as successive policy versions were retained within the RSI-Anything process.



> 💡 **关键数字批注（LabHorizon L2 Final Score 逐版本）**: 五个版本的 Final Score 依次为 21.80% → 38.03% → 41.99% → 44.56% → 46.01%，最终版比初始版高 24.21 个百分点（46.01−21.80，与原文一致）。⚠️ 纠正：这不是“线性增长”——相邻增量约为 16.23、3.96、2.57、1.45 个百分点，呈明显递减，说明收益集中在早期版本，后续版本边际收益收窄。原文把这一趋势归因于 successive policy versions 被保留在 RSI-Anything 流程内、使 **protocol-aligned planning** 持续改进；“累积效应”是本文语境下的合理措辞，但原文未做机制层面的归因验证。

For comparison with reference systems, the final 4B version achieved a Level-2 score of 46.01%, compared with 45.32% for the Actor–Simulator–Selector reference [22], a diference of 0.69 percentage points. On Level-1 next-action selection, RSI-Anything achieved an accuracy of 0.700, compared with 0.665 for the reference (Table 3).



> 💡 **基准对比批注**: 最终 4B 版本 L2 得分 46.01%，Actor–Simulator–Selector 参考 [22] 为 45.32%，差 0.69 个百分点；Level-1 next-action selection 准确率 0.700 vs 参考的 0.665（对应 Table 3）。⚠️ 界限：本段只有这两项对比、且对手是同一个参考系统，因此只能说“在该参考系统上实现小幅超越”；把结论扩写为“超越多个大型模型”需要 Table 3 支撑，且该表的评测口径由 [22] 定义，原文未描述复现细节。

Laboratory planning across successive policy versions  
![](../images/3b08d463767b010ee8339fc846d2b694afc9a21a598d51d1caf509412ded112c.jpg)



> 💡 **Figure 5a 批读（依 caption）**: 该图块由标题行“Laboratory planning across successive policy versions”加一张图组成，按 caption 对应子图 a：规划工作流把协议上下文、4B 策略、对动作序列与参数的预测性评估、执行计划与评估串起来；虚线反馈路径表示框架内的更新路径。请注意本块已不属于 GUI 内容，而是实验室规划部分。⚠️ 图块与子图的对应关系为推断，未获图像视觉信息，虚线具体指向哪些模块无法核实。

![](../images/665c9d29b152f58398d0cc90908f98aa2a0e01643c1be1317db0193f602e34d2.jpg)



> 💡 **Figure 5b 批读（依 caption）**: 按 caption，子图 b 展示同一条 4B 策略在一次自改进过程中产生的五个**连续版本**。⚠️ 纠正：初稿“直观展示自我改进过程的非离散性”是原文没有的判定，“非离散”并非本文表述；可支持的只有“五个连续版本来自同一次自改进过程”，版本之间的参数或数据差异未在本段说明，待正文/附录核实。

![](../images/49b1733ef86c2e74ad1161ab6febb95d3c3b9390f51ca463fde471085c0d04a0.jpg)  
Figure 5 | Successive policy improvement with RSI-Anything on the 200-task LabHorizon F200 Level-2 benchmark [22]. a, Illustrative planning workflow connecting protocol context, a 4B policy, predictive evaluation of action sequences and parameters, an execution plan, and assessment. Dashed feedback paths depict update routes in the framework; the quantitative comparison below follows retained policy versions. b, Five consecutive versions of the same 4B policy from one self-improvement process. c, Final Score for each version, combining action-sequence similarity and parameter accuracy. The dashed line marks the Actor–Simulator–Selector reference at 45.32%. Versions are ordered by development sequence; spacing is schematic.



> 💡 **Figure 5c 批读（依 caption）**: 评测为 200-task LabHorizon F200 Level-2 基准 [22]；c 为各版本的 Final Score，由**动作序列相似度与参数准确度**共同构成；虚线标出 Actor–Simulator–Selector 参考 45.32%。⚠️ 纠正：并非“后期版本突破该参考线”——五版中仅最终版 46.01% 高于 45.32%，其余四版（21.80、38.03、41.99、44.56）都低于该线。另需留意 caption 的限定：版本按开发顺序排列，**间距为示意**，因此不能从横轴距离读成时间或算力投入的线性刻度。

Table 3 | LabHorizon benchmark scores for reference systems [22] and RSI-Anything.
<table><tr><td>System</td><td>L1 Acc.</td><td>L2 Final</td></tr><tr><td>Grok 4.3 [23]</td><td>0.555</td><td>0.3244</td></tr><tr><td>Gemini 3.1 Pro [24]</td><td>0.465</td><td>0.3263</td></tr><tr><td>GPT-5.5 [25]</td><td>0.535</td><td>0.2276</td></tr><tr><td>Kimi K2.6 [26]</td><td>0.550</td><td>0.3150</td></tr><tr><td>Qwen3.6-35B-A3B [27], direct</td><td>0.475</td><td>0.2534</td></tr><tr><td>Qwen3.6-35B-A3B, trained</td><td>0.635</td><td>0.4100</td></tr><tr><td>Actor-Simulator-Selector reference</td><td>0.665</td><td>0.4532</td></tr><tr><td>RSI-Anything (4B)</td><td>0.700</td><td>0.4601</td></tr></table>



> 💡 **Table 3 批读（逐行核对）**: Grok 4.3 0.555/0.3244；Gemini 3.1 Pro 0.465/0.3263；GPT-5.5 0.535/0.2276；Kimi K2.6 0.550/0.3150；Qwen3.6-35B-A3B direct 0.475/0.2534、trained 0.635/0.4100；Actor-Simulator-Selector 0.665/0.4532；RSI-Anything (4B) 0.700/0.4601。RSI-Anything 在 L1 Acc. 与 L2 Final 两列均为表内最高，这一读法与数值一致。⚠️ 两处需限定：(1) 表中未给出各系统的参数量，闭源模型规模未知，因此不能说 RSI-Anything “以小博大胜过更大的模型”，只能说“在该表所列系统中数值最高”；(2) 同表内横向比较依赖 [22] 的评测协议，原文未描述采样次数、解码设置或方差，单点分数差（如 0.700 vs 0.665）能否称为稳定优势属待证。此外 Qwen3.6-35B-A3B 的 direct→trained 提升（0.475→0.635、0.2534→0.4100）与本方法的对比条件也不相同。

## 3.4. Prediction- and feedback-guided search improves reaction yields at matched experiment counts

We examined the use of limited external feedback in experimental science by comparing reaction-condition search at matched experiment counts. RSI-Anything used worldmodel yield predictions to rank candidate conditions for experimental evaluation. Each measured yield extended the observation history used to guide subsequent condition choices, linking prediction, experiment, and the next selection. We evaluated this process in two Suzuki–Miyaura screens, comparing RSI-Anything with Bayesian optimization [28] within each screening protocol.



> 💡 **实验设计批注（匹配实验次数的反应条件搜索）**: 方法条件可拆为：① RSI-Anything 用 world-model 的产率预测对候选条件排序，再交实验评估；② 每个实测产率被并入用于指导后续选点的观测历史，形成“预测 → 实验 → 下一轮选择”的闭环；③ 在**两个 Suzuki–Miyaura 筛选**内、于 matched experiment counts 下分别与 Bayesian optimization [28] 比较。第③点是控制变量的关键：对照是同一筛选协议内的等预算比较，而非跨协议或跨反应类型比较。初稿描述与原文一致，未发现超证据之处；需注意原文将外部反馈限定为“limited external feedback”，即实验预算本身就受限。

Figure 6 shows yield progression with cumulative experiments $( \mathtt { b } , \mathtt { e } )$ and endpoint comparisons at matched experiment counts (c, f). After 20 experiments in the GC-analyzed Pd–NHC–DMS screen, the best observed yield reached 93.0% with RSI-Anything and 87.5% with Bayesian optimization, a gain of 5.5 percentage points. In the flow LC–MS screen using $\mathrm { P d } ( \mathrm { O A c } ) _ { 2 }$ , the mean best observed yield after 40 experiments was 96.15%, compared with 82.16% for Bayesian optimization, a gain of 13.99 percentage points.



> 💡 **关键结果批注（数字逐项核对）**: GC 分析的 Pd–NHC–DMS 屏，20 次实验后**最佳观测产率**（best observed yield，非均值）93.0% vs 贝叶斯优化 87.5%，差 5.5 个百分点；flow LC–MS 屏（Pd(OAc)₂），40 次实验后**平均最佳观测产率**（mean best observed yield）96.15% vs 82.16%，差 13.99 个百分点。⚠️ 纠正与待证：两屏的反应体系、催化剂/分析手段（GC vs flow LC–MS）完全不同，把第二屏更大的差距解释为“高预算下优势扩大”属于混淆变量的外推，只能记为推断；要检验预算效应，需要在同一筛选内对比不同预算端点。另外“均值最佳”只适用于第二屏，第一屏报的是单次最佳，二者不是同一统计量，不能直接横向比较。

At the matched endpoints within each screen, prediction- and feedback-guided search achieved higher best-yield metrics from the same number of experiments. These results support using world-model forecasts together with measured outcomes to guide condition selection under a limited experimental budget.



> 💡 **结论批注（证据范围）**: 原文的结论严格限定为：在各筛选的匹配端点上，预测与反馈引导的搜索用**相同实验次数**取得更高的 best-yield 指标，从而支持“在有限实验预算下，用世界模型预测结合实测结果来指导条件选择”。⚠️ 纠正：初稿的“优于纯贝叶斯方法”属范围外概括（证据仅两个 Suzuki–Miyaura 筛选，且对照只有一种 BO 配置），“支持世界模型在科学发现中的实用价值”也是泛化。本段未报告重复次数、方差或显著性检验，因此这些增益应视为该实验条件下的观测差异。

## 3.5. An end-to-end application of RSI-Anything in immune science

We applied RSI-Anything to an end-to-end immune-science workflow that connects persistent workflow development, predictive world-model construction, and evidenceguided scientific analysis. During construction, Agent Genesis searched scientific-agent workflows and model configurations and selected a model-building agent workflow [13]. The retained workflow produced an action-conditioned immune world model. During downstream analysis, an analysis agent used this model with fixed parameters to coordinate simulation, evaluation, tool use, analysis revision, and hypothesis prioritization.



> 💡 **免疫学端到端工作流批注（两阶段拆解）**: 构建阶段：Agent Genesis 搜索 scientific-agent workflows 与 model configurations，选定 model-building agent workflow [13]，被保留的 workflow 产出 action-conditioned immune world model。分析阶段：analysis agent 以**固定参数**使用该模型，协调 simulation、evaluation、tool use、analysis revision、hypothesis prioritization。初稿的两阶段划分与“固定参数”表述均与原文一致；已补出初稿遗漏的 analysis revision 与 hypothesis prioritization。需要区分的是：被保留并沿用下来的对象是构建阶段的 workflow 与 predictor，不是每一轮都重新搜索 workflow。

The model represents cellular, tissue, and individual immune states and predicts the consequences of explicit interventions (Figure 7). Measured perturbation evidence supports model development, and withheld interventions and biological contexts assess forecasting and action composition. In the analysis stage, model forecasts and experimental evidence inform intervention ranking and self-correction. The construction stage retains the selected model-building workflow and resulting predictor; subsequent analysis uses the constructed predictor to revise candidate interventions and hypotheses.



> 💡 **模型能力与自我修正批注**: 模型表示 cellular、tissue、individual 三个尺度的免疫状态，并预测显式干预的后果（Figure 7）；实测扰动证据用于支持模型开发，held-out 的干预与生物情境用于评估 forecasting 与 action composition；分析阶段中，模型预测与实验证据**共同** inform intervention ranking 与 self-correction。⚠️ 两处纠正：(1) 初稿“利用实测扰动证据进行自我修正”把 self-correction 归因于证据，原文是模型预测与实验证据共同起作用，且 self-correction 发生在分析阶段；(2) 本段为流程与能力描述，**未给出任何定量结果**（无产率、无准确率、无基线对比），因此不能表述为“证明了可行性”，只能说是案例性展示，Figure 7 的具体内容需回图核实。

In therapeutic hypothesis generation, self-correction removed the screened cytokine-pair candidates, and the integrated evidence led to a prospective nomination combining SIRP� inhibition with IL-36�-axis modulation [13]. This case connects a retained scientific-agent workflow, a predictive world model, and evidence-guided analysis within one scientific workflow, culminating in a concrete hypothesis for experimental testing.



> 💡 **机制拆解**: 本段描述的是一个“前瞻性提名”（prospective nomination）的形成过程，而非已验证的发现。原文两处关键动作：一是 self-correction 移除了已被筛掉的 cytokine-pair 候选；二是 integrated evidence 把保留下的线索整合，指向一个联合方案。这里要特别注意原文用词是 “a prospective nomination” 和 “a concrete hypothesis for experimental testing”，即该假设尚待实验验证，不能读成已完成的闭环发现。
> 
> **OCR 与推断标注**: 原文此处字符损坏，显示为 “SIRP inhibition with IL-36-axis modulation”。按免疫学术语推断应为 SIRPα 抑制与 IL-36γ 轴调节，但这是**推断修复**，原文现有文本并未明确写出 α/γ，引用时需回原文 PDF 确认。
> 
> **可追问点**: “self-correction” 的具体算法逻辑原文未在本段给出——是基于置信度阈值、还是基于实验反馈迭代？“integrated evidence” 整合的是哪些模态（文献证据、组学、模型打分）？这两个问题本段均未回答，属待查。

GC screen  
a  
20 experiments  
![](../images/fe6e0b2e6394da247aeaede9184d072022752cf065491dbd428ee1a95d6399e7.jpg)  
b



> 💡 **Figure 6 子图定位（a 区）**: 本块仅有文字标签 “GC screen / a / 20 experiments” 加一张图片路径，本身不含 caption。它指向 Figure 6 的 GC 筛选流程示意（面板 a）以及“20 experiments”的实验预算标注。
> 
> **证据边界**: 关于该工作流中“4-氯茴香醚 + 4-(三氟甲基)苯硼酸、Pd–NHC–DMS 预催化剂、催化剂/碱/溶剂选项”等细节，**不来自本块**，而来自块 27 的 Figure 6 caption。此外，本应用仅提供图片路径与标签，未提供像素级视觉信息，故对图中箭头、配色、反馈回路的描述均属依据 caption 的推断，而非直接读图所得。

![](../images/baf8b5fbb8e31cddea776242e4e9b0fa4c952fca11022272c487d97090ef8bc3.jpg)  
Flow LC–MS screen



> 💡 **归属待证，不能定为 Figure 6b**: 本块文字是 “Flow LC–MS screen” 加一张图片。初稿把这一块判为 Figure 6b（Best observed GC yield versus cumulative experiment count），但 6b 描述的是**气相色谱产率曲线**，与本块写明的 “Flow LC–MS screen” 不一致。因此该图更可能与流动化学筛选（对应 caption 的 d/e 区）相关，具体归属无法由现有文本确定，标为**待证**。
> 
> **证据边界**: 未获得该图的视觉信息，无法确认曲线数量、颜色或坐标轴，不对其内容作进一步断言。

c  
![](../images/5e340bd4e19d1a97e316c5495305e8fb90b166ea560c0a63646069f6ffc3f5c0.jpg)  
40 experiments



> 💡 **标签与文字错位，归属待证**: 本块是标签 “c” 加图片再加 “40 experiments”。而 Figure 6 caption 中，c 指的是“20 次实验后的 GC 产率：93.0% vs 87.5%”，40 次实验对应的是 f（96.15% vs 82.16%）。可见 MinerU 提取时面板标签与实验次数文字发生错位，不能据此把本块直接当作 6c 或 6f。
> 
> **关于 5.5 个百分点的位置**: caption 说明括号数值为两个 screen 各自的增益（5.5 与 13.99 个百分点），其中 5.5 对应 93.0−87.5 的 GC 比较。初稿把“+5.5 个百分点”写在本块之下，由于本块归属未定，该数字应回归到 Figure 6 caption（块 27）层面陈述，而非绑定本块。

d  
![](../images/26e46474bf62f270ac7db10365dfe91d39d28a64bd9f661d3029f8f4f04e06a3.jpg)



> 💡 **仅含标签 d，内容无法判定**: 本块只有面板标签 “d” 和一张图片，无任何说明文字。按 Figure 6 caption，d 应对应“底物 1c 与 2b 的流动筛选工作流（Pd(OAc)₂、配体、碱、溶剂选择 + LC–MS 分析）”，但这是**依据 caption 的推断**，本块自身无法证实。未获得该图的视觉信息，不对其示意图内容作断言，标为待证。

e  
![](../images/a1a85817c825ad867ced70d8045a818f826bfee38ee3e1d6a32cb92bc4261455.jpg)



> 💡 **仅含标签 e，内容无法判定**: 本块只有面板标签 “e” 和一张图片。按 caption，e 应为“流动 LC–MS 筛选中平均最佳产率对累积实验次数的曲线”。但本块无文字支撑，该归属属**推测**。同时未获得图像视觉信息，无法确认曲线收敛形态或两条曲线的分离位置，故不对其趋势下判断。

f  
![](../images/bced884357312a0979240799c7358a67b0405dfc41f09071225dce28a6ec0916.jpg)  
Figure 6 | Reaction-condition optimization with RSI-Anything (green) and Bayesian optimization (gray). a, Illustrative GC-screen workflow connecting the reaction setup, catalyst/base/solvent options, prediction-guided condition selection, and measured yield. The reaction couples 4-chloroanisole and 4-(trifluoromethyl)phenylboronic acid using a Pd–NHC–DMS precatalyst. $\mathbf { b } ,$ Best observed GC yield versus cumulative experiment count. c, GC yield after 20 experiments: 93.0% for RSI-Anything and 87.5% for Bayesian optimization. ${ \mathsf { \mathbf { d } } } ,$ Illustrative flow-screen workflow for substrates 1c and $^ { \mathrm { 2 b , } }$ with Pd(OAc)<sub>2</sub>, ligand, base, and solvent selection followed by LC–MS analysis. e, Mean best observed yield versus cumulative experiment count in the flow LC–MS screen. f, Yield after 40 experiments: 96.15% and 82.16%, respectively. Brackets indicate gains of 5.5 and 13.99 percentage points within the corresponding screen. Condition examples and feedback arrows illustrate the framework; curves and bars report the two separate screens.



> 💡 **Figure 6 批读（数字逐项核对）**: caption 给出的关键数字与原文完全一致，可直接引用：
> - GC 筛选（面板 a 流程，20 次实验后，面板 c）：RSI-Anything 93.0%，贝叶斯优化 87.5%。
> - 流动 LC–MS 筛选（面板 d 流程，40 次实验后，面板 f）：96.15% 与 82.16%。
> - 括号标注的增益为 5.5 和 13.99 个百分点，分别对应上述两个 screen。
> 
> **因果界限（重要）**: 两个 screen 的底物与体系不同（GC 用 Pd–NHC–DMS 预催化剂耦合 4-chloroanisole 与 4-(trifluoromethyl)phenylboronic acid；流动筛选用底物 1c 和 2b，Pd(OAc)₂/配体/碱/溶剂）。因此不能把“13.99 > 5.5”直接归因于“实验预算增加使优势扩大”——这是**跨体系比较的推断**，原文并未做此因果声明。可作为待验证假设提出。
> 
> **图表读法**: caption 末句明确“Condition examples and feedback arrows illustrate the framework; curves and bars report the two separate screens”，即示意图部分是流程说明，曲线与柱状图才是两个独立 screen 的实测数据，两类信息不能混为一谈。

![](../images/c3bd7c60ab24f1748c9067a0106af2cb2875b62ff9cf1f0ac7610a372e825422.jpg)  
Figure 7 | An end-to-end application of RSI-Anything in immune science. A, evolution of scientific-agent workflows, construction of the immune world model, and model-guided analysis. B, action-conditioned transitions over structured cellular, tissue, and individual immune states, with readouts for interpretation, ranking, and hypothesis generation. Panels A and B adapted from Figure 1 of [13].



> 💡 **Figure 7 批读**: caption 说明这是 RSI-Anything 在免疫科学中的端到端应用示意。A 面板包含：科学代理工作流的演进、免疫世界模型的构建、模型引导分析；B 面板包含：在结构化细胞、组织、个体免疫状态上的动作条件化状态转移，以及用于解释、排序与假设生成的读数。初稿对 A/B 面板的描述与 caption 基本一致。
> 
> **必须补充的关键限定**: caption 末句写明 “Panels A and B adapted from Figure 1 of [13]”，即该图 A、B 两个面板是**改编自文献 [13] 的 Figure 1**。因此它属于引用/改编的示意框架图，而非本文新产生的实验结果；不能据此图声称模型在多尺度生物系统上的应用得到实证。初稿“展示了模型在多尺度生物系统中的应用潜力”的表述应下调为“示意性框架”。
> 
> **与块 21 的呼应**: 块 21 的 SIRPα/IL-36γ 联合提名正是该免疫工作流的输出示例，但该提名本身仍是待实验验证的前瞻性假设。

