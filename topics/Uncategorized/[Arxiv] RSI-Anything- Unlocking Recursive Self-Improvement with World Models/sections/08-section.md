[← 返回 README](../README.md)

## 6. Experimental Methods

The domain settings instantiate the shared framework with diferent states, action spaces, prediction targets, and feedback sources. Our code execution predictor, W1, supplies execution-class probabilities for candidate–test pairs, ReactionT5–JEPA-Anything supplies a yield distribution, and the immune world model supplies immune-state and biological readouts. Table 4 summarizes the domain-specific models, prediction targets, feedback sources, and update pathways evaluated in each application. The following sections describe the evaluation settings and statistical reporting.



> 💡 **机制拆解**：本节先给出四域共用的实验框架——各域用不同的状态、动作空间、预测目标和反馈源实例化同一套协议。关键分工是：世界模型负责“预测后果”（W1 输出候选–测试对的执行类别概率；ReactionT5-JEPA-Anything 输出产率分布；免疫世界模型输出免疫状态与生物学读出），策略/工作流负责“提议或选择动作”。Table 4 汇总各域的模型、预测目标、反馈源与更新路径，是后文逐域设置的索引。
> 
> > 💡 **Q&A 批注记录**：
> > - Q: W1 与 ReactionT5-JEPA-Anything 在架构上有何共性？
> > - A: 原文仅在 §6.1 Code repair 明确 W1 采用 JEPA-Anything 的预测式设计；ReactionT5-JEPA-Anything 从命名与 Table 4 看同属该家族，但本节未给出其架构细节，因此“均基于 JEPA-Anything 设计”对 ReactionT5 而言是**基于命名的推断，待方法章节证实**。
> > - 核对提示：本节只说明 W1 提供“执行类别概率”、ReactionT5 提供“产率分布”，未涉及两者的损失与训练细节。

The experiments instantiate distinct parts of the full protocol. Code repair holds the coder fixed while testing predictive selection and predictor adaptation; laboratory planning follows five successive policy versions. GUI evaluations measure task completion and action behavior as RSI-Anything updates both the policy and the world model. Chemistry tests feedback-guided condition search, and the immune-science application separates workflow evolution and model construction from downstream analysis with a fixed predictor.



> 💡 **问题动机**：实验不是把完整协议在所有领域整体复现，而是“各域实例化协议的不同部分”：代码修复固定 Coder，只检验预测式选择与预测器自适应；实验室规划连续跟踪五个策略版本；GUI 评估在同时更新策略与世界模型的前提下测任务完成与动作行为；化学测反馈引导的条件搜索；免疫科学则把工作流演化/模型构建与“固定预测器的下游分析”分开。这种“部分实例化”便于在不同更新范围下分离变量。
> 
> > 💡 **可追问点 / 待证**：Table 4 的 Chemistry 行只写“实测产率引导后续条件选择”，**并未说明该域是否在线更新世界模型**。初稿曾断言“化学测试不更新模型而只搜索条件”，这属于“原文未提及”的**推断**；该推断若成立，其成本/机理理由（如实验昂贵、机理复杂）也均为本文未给出的推测，需回到正文或附录确认。

## 6.1. Domain-specific evaluation settings

Code repair. The state contains the current program, generated tests, and observed feedback; each action is a candidate revision. The ReasonFlux-Coder-7B policy proposes four revisions and is held fixed [14]. W1 is our 3B execution-outcome world model, built using the predictive design of JEPA-Anything [9] and adapted from Qwen2.5- Coder-3B-Instruct [52] with LoRA [53]. For every candidate–test pair, it returns the probability of each execution-result class. Both RSI-Anything configurations aggregate these probabilities to rank the four revisions before one revision is executed. The fixed-world-model configuration (CURE-WM-Lite) retains W1, whereas the world-modeladaptation configuration updates W1 with the coder held fixed. We evaluated 200 CodeContests tasks [15] across three seeds and at most three feedback rounds. The comparison included both RSI-Anything configurations, random selection, and a capacitymatched static verifier. Metrics were final hidden-test all-pass rate, online test calls per task, candidate-ranking accuracy, and five-class Brier score. Online tests supply feedback during selection; hidden tests supply the final reported success measure.



> 💡 **机制拆解（Code repair）**：
> 1. **状态与动作**：state = 当前程序 + 生成测试 + 观测反馈；action = 一个候选修订。
> 2. **角色固定**：ReasonFlux-Coder-7B 提出 4 个修订且**保持固定**；被检验的是执行结果世界模型 W1（3B，按 JEPA-Anything 的预测式设计，由 Qwen2.5-Coder-3B-Instruct 经 LoRA 适配），它对每个候选–测试对输出各类执行结果的概率。
> 3. **两种配置**：CURE-WM-Lite 冻结 W1；world-model-adaptation 在 Coder 固定下更新 W1。两者都用这些概率把 4 个修订排序，再执行其中一个。
> 4. **规模与对照**：200 个 CodeContests 任务、3 个随机种子、至多 3 轮反馈；对照包括随机选择与容量匹配的静态验证器。
> 5. **指标语义**：final hidden-test all-pass rate 是最终成败指标，online test calls per task 反映在线探测成本，candidate-ranking accuracy 与五类 Brier score 反映排序质量与概率校准；online tests 在筛选阶段提供反馈，hidden tests 仅用于最终报告。
> 
> > 💡 **公式/数据批读**：LoRA 适配说明构建领域专用预测器不必从头预训练；但“轻量适配足以胜任”以及“更新 W1 是为了验证更准的预测能否提升筛选效率”是**对本节设置目的的推断**，本节并未给出结果数值来支撑。

Table 4 | Domain bindings and reported update scope. The world model predicts consequences; the policy proposes or selects actions.
<table><tr><td>Setting</td><td>State; action</td><td>World model; prediction Policy / workflow;</td><td>feedback-driven changes</td><td>Feedback; evaluation</td></tr><tr><td></td><td>Code repair Program, tests, and feedback; candidate revision</td><td>JEPA-Anything-based W1 adapted from Qwen2.5-Coder-3B- Instruct [52] with LoRA [53]; five-class</td><td>Fixed ReasonFlux- Execution-test Coder-7B; fixed or feedback; final feedback-adapted W1</td><td>test-suite success and online test usage</td></tr><tr><td>GUI interaction GUI action</td><td></td><td>probabilities Interface state and task; GUI outcome predictor; Action policy and consequences of candidate interface actions</td><td>world model updated through interaction feedback</td><td>Task outcomes; completion and action-level diagnostics</td></tr><tr><td>Lab planning</td><td>Protocol context; candidate action sequence</td><td>JEPA-Anything-based protocol model; predicted consequences of a</td><td>4B planning policy; Development five consecutive versions in one improvement</td><td>feedback; L1 next-action accuracy and F200 L2 Final Score</td></tr><tr><td>Chemistry</td><td>Reaction; condition vector</td><td>candidate sequence ReactionT5-JEPA- Anything; yield distribution</td><td>process Measured yields guide subsequent condition choices</td><td>Measured GC/LC–MS yields; best observed yield at matched</td></tr><tr><td>Immune science</td><td>Cellular, tissue, and individual context; intervention</td><td>Immune world model [13]; multiscale transition and biological readouts</td><td>Workflow evolution and world-model construction; fixed development; predictor during downstream analysis</td><td>experiment counts Measured perturbations support withheld contexts assess forecasting</td></tr></table>



> 💡 **Table 4 批读**：这张表是全文实验设置的索引图，列结构把“谁预测后果”（World model; prediction）与“谁行动、哪些被反馈驱动改变”（Policy/workflow; feedback-driven changes）分开。
> - **Code repair**：W1（JEPA-Anything 式，Qwen2.5-Coder-3B-Instruct + LoRA）给出五类概率；ReasonFlux-Coder-7B 固定或反馈自适应；评价为测试套件成功率与在线测试用量。
> - **GUI**：界面状态与任务 → GUI 结果预测器；动作策略；世界模型通过交互反馈更新；评价为任务完成与动作级诊断。
> - **Lab planning**：JEPA-Anything 式协议世界模型预测候选序列后果；4B 规划策略；评价为 L1 下一动作准确率与 F200 L2 Final Score。
> - **Chemistry**：ReactionT5-JEPA-Anything 提供产率分布；实测产率引导后续条件选择；评价为 GC/LC-MS 实测产率与匹配实验次数下的最佳产率。
> - **Immune**：免疫世界模型 [13] 做多尺度转变与生物学读出；工作流演化与模型构建在开发阶段进行，下游分析用固定预测器。
> 
> > 💡 **待证 / 纠正提示**：表格原文经解析后**列间内容存在明显错位**（如 GUI 行单元格串行），以上归属是按确认过的正文与表头做的**谨慎复原**，引用具体单元格时应回查 PDF 原表。另外，把 Lab planning 的“五个连续版本”理解为**世界模型随时间进化**并不准确：§6.1 实验室规划明确这五个连续版本是 **4B 策略版本**，不是世界模型版本。

We additionally evaluated APPS Introductory (1,000 problems) and LiveCodeBench v6 (1,055 problems) using seeds 0, 1, and 2. Repair methods shared the initial program for each problem and seed and used the same ReasonFlux-Coder-7B policy, with four repair candidates per round and at most three repair rounds. Public-test feedback guided repair, which stopped when all public tests passed. Final success required passing the complete oficial test suite.



> 💡 **证据补充（跨数据集泛化）**：除 CodeContests 外，还在 APPS Introductory（1,000 题）与 LiveCodeBench v6（1,055 题）上用种子 0、1、2 评测。各修复方法共享同一初始程序与同一 ReasonFlux-Coder-7B 策略，每轮 4 个修复候选、至多 3 轮；公共测试反馈驱动修复，并在全部公共测试通过时停止，最终成功需通过完整官方测试套件。
> 
> > 💡 **批读**：“共享初始程序 + 固定策略”排除了生成能力差异这一混淆因素；“公共测试引导、官方测试判定”把可用反馈与评估信号严格分开，与 §6.1 中的 online/hidden 测试分工一致。至于“模拟真实编程竞赛环境”，是对该设计的**合理推断**，原文未如此表述。

GUI agents. We evaluated the MiniWoB++ click-checkboxes task [19] and OS-World [20]. World-model predictions guide GUI action selection, and observed interaction outcomes provide feedback for updating both the action policy and the world model. The retained policy–model pair is used in subsequent interactions. The checkbox evaluation compares baseline, adapted, and final configurations using task success rate, mean actions per episode, repeated-click count, and the number of episodes with incorrect submissions. Success rates are also shown for the World-based control. The action and error diagnostics in Figure $_ { 4 \mathrm { e } - \mathrm { g } }$ describe the RSI-Anything configurations. The OSWorld comparison includes the initial agent, RLAnything [21], and RSI-Anything. For success rates $s _ { \mathrm { m e t h o d } }$ and $s _ { \mathrm { r e f e r e n c e : } }$ , relative improvement is $1 0 0 ( s _ { \mathrm { m e t h o d } } / s _ { \mathrm { r e f e r e n c e } } - 1 ) \%$ . Figure 4d uses the initial agent as the common reference; the additional RSI-Anything comparison uses RLAnything as its reference.



> 💡 **机制拆解（GUI agents）**：
> 1. **评测对象**：MiniWoB++ 的 click-checkboxes 任务与 OS-World。
> 2. **双重更新**：世界模型预测指导动作选择，观测到的交互结果同时用于更新动作策略与世界模型，更新后的策略–模型对被用于后续交互——这是本域与代码修复的关键差别（后者 Coder 固定）。
> 3. **指标**：checkbox 评测比较 baseline / adapted / final 三种配置，用任务成功率、平均每回合动作数、重复点击次数、错误提交回合数，并给出 World-based 控制的成功率；Figure 4e–g 的动作与错误诊断对应 RSI-Anything 各配置。
> 4. **OSWorld 对比**：initial agent、RLAnything [21]、RSI-Anything。相对提升为 $100(s_{method}/s_{reference}-1)\%$；Figure 4d 以 initial agent 为共同参考，另一处 RSI-Anything 对比以 RLAnything 为参考。
> 
> > 💡 **批读**：重复点击与错误提交属于**行为过程指标**，比单看成功率更能暴露 Agent 是否陷入循环或误判；解读相对提升时必须先确认参照系（Fig 4d 与另一处对比的 reference 不同），否则倍数不可比。

Laboratory planning. LabHorizon evaluates Level-1 next-action accuracy and Level-2 protocol-aligned action sequences on its 200-task F200 setting [22]. Level-2 Final Score combines action-sequence similarity and parameter accuracy. The RSI-Anything policy versions use a 4B model to generate plans and a JEPA-Anything-based protocol world model to predict their consequences. The F200 evaluation follows five consecutive 4B policy versions from the same RSI-Anything self-improvement process, ranging from 0.2180 to 0.4601. The Actor–Simulator–Selector reference uses a trained Qwen3.6-35B-A3B Actor [27] and Gemini 3.1 Pro as Simulator and Selector [24], with a reported LabHorizon Level-2 score of 0.4532 [22]. Level-1 next-action accuracy was 0.700 for RSI-Anything and 0.665 for the reference.



> 💡 **机制拆解（Laboratory planning）**：
> 1. **数据集与指标**：LabHorizon 的 200 任务 F200 设置；Level-1 为下一动作准确率，Level-2 为协议对齐的动作序列，Level-2 Final Score 由动作序列相似度与参数准确率合成。
> 2. **模型配置**：RSI-Anything 的策略版本用 4B 模型生成计划，配 JEPA-Anything 式协议世界模型预测后果。
> 3. **自我改进轨迹**：F200 评测跟随同一自我改进过程产出的**五个连续 4B 策略版本**，Level-2 分数区间 0.2180–0.4601。
> 4. **参照系统**：Actor–Simulator–Selector 参考使用训练过的 Qwen3.6-35B-A3B 作 Actor，Gemini 3.1 Pro 作 Simulator 与 Selector，报告的 LabHorizon Level-2 为 0.4532 [22]；Level-1 准确率 RSI-Anything 0.700 vs 参考 0.665。
> 
> > 💡 **Q&A 批注记录**：
> > - Q: 为什么 F200 设置能体现“连续版本”的提升？
> > - A: 原文只说明 F200 评测**跟随同一自我改进过程中的五个连续策略版本**，因此可以追踪同一策略链的迭代轨迹；至于“它因此比单次训练更能排除随机波动”，属于**方法学推断**，本节未做该论证。
> > - 核对提示：0.4601 与 0.4532 来源不同（前者是本文五个版本的上界，后者是文献 [22] 报告的参考值），跨来源比较需注意口径。

Reaction optimization. We construct the ReactionT5–JEPA-Anything adapter by combining the ReactionT5 reaction-prediction backbone [47] with the predictive design of JEPA-Anything [9]. It maps a serialized reaction � and candidate condition vector � to a predicted yield distribution $p _ { \phi } ( y \mid r , c )$ . One screen varied Pd–NHC–DMS precatalyst, base, and solvent for the 4-chloroanisole coupling and measured GC analytical yield. A second screen used $\mathrm { P d } ( \mathrm { O A c } ) _ { 2 }$ with ligand, base, and solvent choices in flow, measured by LC–MS. Bayesian optimization is the baseline in both screens. Results are reported as best observed or mean best observed yield for each separate protocol. Measured yields guide subsequent condition choices, and performance is summarized by the best observed yield at matched experiment counts.



> 💡 **机制拆解**：ReactionT5–JEPA-Anything 适配器 = ReactionT5 反应预测骨干 [47] + JEPA-Anything 的预测式设计 [9]。输入是序列化反应 $r$ 与候选条件向量 $c$，输出是**产率分布** $p_{\phi}(y \mid r, c)$，而不是单点产率。
> 
> **两组筛选（不可混淆）**：
> - 筛选一：4-氯苯甲醚（4-chloroanisole）偶联，变化 Pd–NHC–DMS 预催化剂、碱、溶剂，用 GC 测分析产率。
> - 筛选二：以 $\mathrm{Pd(OAc)_2}$ 搭配配体、碱、溶剂，**在流动（in flow）体系中进行**，用 LC–MS 测量。初稿写成「LC–MS 测流式」不准确——原文的 in flow 修饰反应体系，LC–MS 只是测量手段。
> 
> **基线与指标口径**：两套筛选都以贝叶斯优化为基线；结果按**各自协议单独**报告为 best observed 或 mean best observed yield，最终性能以「相同实验次数下的最佳观测产率」比较。初稿只提 best observed，遗漏了 mean best observed 这一备选口径。测量产率会引导后续条件选择，这是本实验的闭环机制。
> 
> **待证**：适配器是否针对两套筛选分别训练/共享同一 $p_{\phi}$，原文未说明。

End-to-end immune-science application. The immune-science application uses an actionconditioned immune world model that encodes cellular state, tissue immune context, and individual context, and treats cytokines, genetic perturbations, or candidate therapies as explicit actions [13]. Agent Genesis evolved scientific-agent workflows and model configurations, producing a model for withheld-intervention forecasting, cross-scale prediction, and action composition. The selected model-building agent workflow is the retained policy configuration in this construction stage. During downstream intervention analysis, the resulting world-model parameters are frozen while the analysis state, candidate interventions, and hypothesis ranking evolve through model inference and self-correction. Therapeutic-hypothesis generation combined measured perturbation evidence with this model-guided analysis. The resulting prospective nomination combines SIRP� inhibition with IL-36�-axis modulation for subsequent experimental testing.



> 💡 **应用结构拆解**：动作条件免疫世界模型编码三类上下文——细胞状态、组织免疫背景、个体上下文；显式动作包括**细胞因子、基因扰动、候选疗法**三类（初稿漏了「候选疗法」）。
> 
> **Agent Genesis 的角色**：进化 scientific-agent 工作流与模型配置，产出的模型用于三类任务——withheld-intervention 预测、跨尺度预测、动作组合（初稿只保留「干预预测」，遗漏后两项）；此外原文明确「被选中的模型构建 agent 工作流是这一构建阶段**保留的策略配置**」，即最终采用的 workflow 本身也是产出。
> 
> **下游分析的关键约束**：世界模型参数被**冻结**，动态演化的只有分析状态、候选干预与假设排序（通过模型推断 + self-correction）。这是「冻结权重、演化假设」的条件，不是全流程联合训练。
> 
> **证据边界**：治疗假设生成 = 实测扰动证据 + 模型引导分析；最终提名 SIRPα 抑制联合 IL-36γ 轴调节，原文只是「prospective nomination … for subsequent experimental testing」，即**前瞻性提名、尚待后续实验验证**，不能当作已验证疗效。原文此处 OCR 为「SIRP」「IL-36」，α/γ 系依上下文推断，属推断而非原文直述。

## 6.2. Statistical reporting

Success rates are reported over 600 CodeContests task-seed evaluations. We report the paired percentage-point diference between RSI-Anything with a fixed world model and the static verifier, together with its 95% interval. Tasks recur across seeds, so task-level resampling is the unit for clustered inference. APPS and LiveCodeBench report the arithmetic mean of final full-test success rates across three seeds, corresponding to 3,000 and 3,165 problem–seed evaluations, respectively. LabHorizon F200 reports mean scores over 200 tasks [22]. The 24.21- and 0.69-percentage-point gains are diferences between these benchmark means. Chemistry reports the observed running best within each optimization sequence, with Bayesian optimization as the comparator. Evaluation settings for the immune-science application are described in [13].



> 💡 **统计口径分三块，需分开读**：
> 
> 1. **CodeContests**：600 次任务-种子评估；报告的是「**使用固定世界模型的** RSI-Anything」与静态验证器的配对百分点差及其 95% 区间。初稿简写为「RSI-Anything 与静态验证器」，丢掉 fixed world model 这一限定会改变比较含义。由于同一任务跨多个种子重复出现，聚类推断的单位是**任务级重采样**。
> 2. **APPS / LiveCodeBench**：报告三个种子 final full-test 成功率的**算术平均**，分别对应 3,000 与 3,165 次问题-种子评估。文中 24.21 与 0.69 个百分点的增益就是这些基准均值之间的差（该句未点明各自对应哪个基准，需回正文表格确认，属推断）。
> 3. **其他**：LabHorizon F200 为 200 个任务的均值分数 [22]；化学部分报告每条优化序列内的 running best，比较对象是贝叶斯优化；免疫科学应用的评测设置见文献 [13]，本节不复述。
> 
> **待证**：600 次评估是 CodeContests 全量还是子集，原文未说；「95% interval」原文未交代区间构造方式，不宜直接断言为置信区间。

