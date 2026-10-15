[← 返回 README](../README.md)

## 3. Results

We examined how world-model predictions improve action selection, how retained updates support further improvement, and how prediction and feedback guide decisions under limited interaction budgets. Code repair and GUI interaction cover general computational tasks, alongside physical-domain laboratory planning and experimental reaction optimization. Within these settings, we evaluate predictive selection, model adaptation, task completion, successive policy versions, and search at matched experiment counts. We then examine an end-to-end application of RSI-Anything in immune science, connecting scientific-agent workflow evolution, world-model construction, and scientific analysis.



> 💡 **Section 概览｜实验组织逻辑**：本节要回答三个问题——世界模型预测如何改善动作选择、保留的更新如何支撑继续改进、在有限交互预算下预测与反馈如何指导决策。评测覆盖五个方面：预测性选择、模型适应、任务完成、连续策略版本、以及等实验次数下的搜索。任务域上，代码修复与 GUI 交互代表通用计算任务，另有物理领域的实验室规划与反应优化；最后给出 RSI-Anything 在免疫科学中的端到端应用（科学代理工作流演化 → 世界模型构建 → 科学分析）。批读提醒：本段是全景预告，给出的 3.x 小节实际只呈现了代码修复与 GUI 两块结果，物理域与免疫科学内容不在可见结果块中，不能据本段推断其结论。

## 3.1. World-model-guided selection and adaptation improve code repair

We evaluated two contributions of RSI-Anything in code repair: introducing world-model predictions for candidate selection and obtaining further gains by updating the predictor with execution feedback. The ReasonFlux-Coder-7B policy was held fixed [14]. We compared RSI-Anything with a fixed world model (CURE-WM-Lite) and RSI-Anything with world-model adaptation. All selection methods used the same coder and candidate budget: four revisions were proposed before one was selected for execution. Evaluation covered 200 CodeContests tasks [15] across three seeds and up to three feedback rounds, with online tests supplying feedback and hidden tests measuring final success.



> 💡 **3.1 实验设置｜控制变量清单**：代码修复检验两个增量——(1) 用世界模型预测做候选选择；(2) 用执行反馈更新预测器。策略固定为 ReasonFlux-Coder-7B（[14]），不随选择方法变化；所有选择方法共享同一 coder 与候选预算：每轮提出 4 个修订、只选 1 个执行。数据为 CodeContests 200 题（[15]）、3 个随机种子、最多 3 轮反馈；在线测试提供反馈，隐藏测试衡量最终成功率。批读要点：'策略固定 + 候选预算固定'是后面把增益归因于选择与预测器、而非归因于多采样/多交互的前提条件，是本节实验可解释性的关键控件。

Introducing world-model-guided selection increased final hidden-test success to 29.2% (175/600), compared with 26.3% for a capacity-matched static verifier and 26.0% for random selection (Figure 3; Table 2). The paired gain over the static verifier was +2.83 percentage points (95% interval [+0.17, +5.50]). In this fixed-world-model versus staticverifier comparison, candidate-ranking accuracy increased from 66.5% to 72.8%, and the five-class Brier score [16] decreased from 0.488 to 0.358. Online test-suite usage was similar: 3.71 calls per task for the fixed-world-model configuration and 3.72 for the static verifier. These results connect improved prediction and ranking with higher repair success under the same candidate budget.



> 💡 **主结果 1｜固定世界模型 vs 容量匹配静态验证器**：隐藏测试全通过率 29.2%（175/600），高于容量匹配静态验证器的 26.3% 与随机选择的 26.0%（Figure 3；Table 2）。相对静态验证器的配对增益 +2.83 个百分点，95% 区间 [+0.17, +5.50]——区间下界大于 0，按常规解读可视为统计上可区分，但原文并未直接写 'significant'，此判断属对区间数值的推断。预测质量同步：候选排序准确率 66.5%→72.8%，五类 Brier 分数 0.488→0.358（[16]）。在线测试调用 3.71 vs 3.72 次/题，几乎一致。原文的因果措辞是 'connect improved prediction and ranking with higher repair success under the same candidate budget'（把预测/排序改善与同预算下的成功率提升相联系），因此这是同预算下的相关性证据，不能改写成'证明提升源于预测质量'。

Feedback-driven world-model adaptation produced an additional gain over fixed-model selection. With the coder and candidate budget retained, RSI-Anything with worldmodel adaptation reached 31.2% hidden-test success, exceeding the fixed-world-model configuration by 2.0 percentage points. Online test-suite usage was 3.72 calls per task. Execution feedback informed program repair and revised the predictor used for subsequent candidate selection. The two comparisons show successive gains from introducing predictive selection and then adapting the world model, with similar online test usage.



> 💡 **主结果 2｜世界模型自适应带来额外增益**：在 coder 与候选预算不变的前提下，带世界模型自适应的 RSI-Anything 达到 31.2% 隐藏测试成功率，比固定世界模型配置高 2.0 个百分点；在线调用 3.72 次/题。机制拆解：执行反馈既用于程序修复，也用于修正后续候选选择所用的预测器，形成'预测→执行→反馈→更新预测'的闭环。两点限定：原文只说 3.72 与固定配置的 3.71 相近，未对 2.0 个百分点做显著性检验，只能作描述性增量解读；本节也未给出自适应配置相对基线的绝对百分点（基线见 block 4 与 Table 2）。

On APPS Introductory [17] and LiveCodeBench v6 [18], JEPA-Anything-guided candidate selection achieved final full-test success rates of 46.37% and 33.05%, respectively,



> 💡 **跨基准泛化（前半句，被图片切断）**：在 APPS Introductory（[17]）与 LiveCodeBench v6（[18]）上，JEPA-Anything 引导的候选选择最终全测试通过率分别为 46.37% 与 33.05%。此句在原文中被 Figure 3 的图片块截断，其比较对象在后一段（block 16）续写为'超过随机选择与静态验证器'。待证点：这里的 JEPA-Anything 与 Table 2 中作为固定世界模型的 CURE-WM-Lite 名称不同，两者是否同一世界模型、还是不同基准上使用了不同预测器，正文这两块没有交代，需回到 Figure 3e,f 图注或对应小节确认，不能默认等同。

Code repair: predictive selection and world-model adaptation



> 💡 **图内分组标题**：'Code repair: predictive selection and world-model adaptation' 是 Figure 3 的分组标题而非正文论点，作用是把下方 a–f 子图归入'代码修复：预测性选择 + 世界模型自适应'这一实验组。批注价值在于提示读图时把 b–d（CodeContests 主实验）与 e–f（APPS / LiveCodeBench 跨基准）看成同一条证据链的两段。

a  
![](../images/f8f77f2050d3cdea705d90a9d1d5168beab119ab9eabd9daf6b05d0657e37df5.jpg)



> 💡 **Figure 3a（图片，未获得视觉信息）**：该块仅含标签 'a' 与图片文件名。依据 caption：a 是非收敛循环的示例修复流程——候选补丁与预测结果 → 执行 → 测试反馈；A–C 为示例补丁；基准固定 coder、每轮 4 个候选；反馈在自适应配置中用于更新预测器。以上全部来自 caption 转述，图内具体代码、补丁文本与预测分数均未获取，不能据此补充细节。

b  
![](../images/ede0a8648414f2954b1e35351776b15467020348b652d968ea542336bf0f002a.jpg)



> 💡 **Figure 3b（图片，标签 'b'，未获得视觉信息）**：按 caption，b 报告 200 道 CodeContests 任务、3 个种子、最多 3 轮反馈下的最终隐藏测试 all-pass 率。因此它是 block 4 中 26.0% / 26.3% / 29.2% 与 block 5 中 31.2% 这些数字的图形式呈现。柱形状、误差条是否给出、是否标注显著性均无法从文本确认，属推断范围。

c  
![](../images/87c3d325b8b3d6dea785213a92a91a86690102f945604f6649246b756b47e28f.jpg)



> 💡 **Figure 3c（图片，标签 'c'，未获得视觉信息）**：按 caption，c 展示静态验证器与固定世界模型配置的候选排序准确率与五类 Brier 分数，对应正文的 66.5%→72.8% 与 0.488→0.358；排序准确率越高、Brier 越低表示预测越好。是否采用双轴并列或两条独立子图，不能从文本判断，本批注不作断言。

![](../images/c911a769be6442dd954fb611a0418b26abb1b7c0dddac8cf00934729a3da157b.jpg)



> 💡 **Figure 3 子图（图片，无标签，未获得视觉信息）**：此块既无 a–f 标签也无文字，无法确认其子图身份。若按块序推测可能接近 d（每题平均在线测试调用次数），但这是纯顺序推断，不构成证据，不应写成确定的 'Figure 3d'。图内内容未获取。

d  
![](../images/3c52fde30dcdcb80c08197f8a88a2ce5d58b79d27b1d2d26ed1e9aa8dad0cc2f.jpg)



> 💡 **Figure 3d（图片，标签 'd'，未获得视觉信息）**：按 caption，d 是每题平均在线测试套件调用次数；正文给出的读数为固定世界模型 3.71、静态验证器 3.72，即各方法交互成本基本持平（Table 2 亦为 3.71–3.72）。图内数值刻度未获取，本批注只引用正文与 caption 的数字。

e  
![](../images/258de9c60ab970edccad8a6dff3db195996e1e427f91a763d02316f588707652.jpg)



> 💡 **Figure 3e（图片，标签 'e'，未获得视觉信息）**：按 caption，e 是 APPS Introductory（1,000 题）上 3 个种子平均的最终全测试成功率，对应正文 block 6 的 46.37%。原文未在该段给出比较对象的百分点差，具体差距需读图或对应表格，属待证内容。

![](../images/286c3d93a64acf81712a1d688732c4cef5e0fd2228eed3ec166bc4eaa901c6b3.jpg)



> 💡 **Figure 3 子图（图片，无标签，未获得视觉信息）**：该块同样无标签，无法确认子图身份。按 caption，尚未被前 13 个块标注覆盖的剩余面板是 f（LiveCodeBench v6，1,055 题，正文 33.05%），因此它很可能对应 f，但这仅是顺序推断，未获视觉确认，不能当作确定结论。

Figure 3 | Prediction-guided selection and world-model adaptation in code repair. a, Illustrative repair of a non-convergent loop, from candidate patches and predicted outcomes to execution and test feedback. A–C depict example patches; the benchmark uses a fixed coder and four candidates per round. Feedback supports predictor updates in the adaptive configuration. Panels b–d report results on 200 CodeContests tasks across three seeds with up to three feedback rounds. b, Final hidden-test all-pass rate. c, Candidate-ranking accuracy and five-class Brier score for the static verifier and the fixed-world-model configuration. Higher ranking accuracy and lower Brier score indicate better predictive performance. d, Mean online test-suite calls per task. e,f, Final full-test success averaged over three seeds on APPS Introductory (1,000 problems) and LiveCodeBench v6 (1,055 problems), respectively. Fixed WM denotes RSI-Anything with a fixed world model (CURE-WM-Lite); Adaptive WM denotes RSI-Anything with world-model adaptation. Green shades identify world-model-based methods; gray shades identify baselines.



> 💡 **Figure 3 caption 批读（本图唯一可依据的文字证据）**：逐项对应——a 为示例修复流程（候选补丁 A–C、预测结果、执行与测试反馈；基准固定 coder + 每轮 4 候选；反馈支持自适应配置的预测器更新）；b–d 为 200 道 CodeContests × 3 种子 × 最多 3 轮反馈的结果，其中 b = 最终隐藏测试 all-pass 率，c = 排序准确率与五类 Brier，d = 每题平均在线调用；e、f 分别为 APPS Introductory（1,000 题）与 LiveCodeBench v6（1,055 题）上 3 种子平均的最终全测试成功率。命名约定：Fixed WM = 固定世界模型（CURE-WM-Lite）的 RSI-Anything，Adaptive WM = 带世界模型自适应的 RSI-Anything；绿色表示基于世界模型的方法，灰色表示基线。批读提醒：颜色语义只有在能看到图时才可用，本批注未获得任何图像视觉信息，故不描述图内配色以外的排版细节。

exceeding random selection and the static verifier (Figure 3e,f). These comparisons used the same coder, with four repair candidates per round and at most three repair rounds.



> 💡 **跨基准结论与公平性口径**：承接 block 6，JEPA-Anything 引导的选择在 APPS 与 LiveCodeBench 上超过随机选择与静态验证器（Figure 3e,f）；比较使用同一 coder、每轮 4 个修复候选、最多 3 轮修复，与 CodeContests 主实验口径一致，因此可视为同一控制条件下的跨基准复制。证据边界：原文用 'exceeding' 陈述相对优势，本段未给百分点或置信区间，具体差距需查 Figure 3e,f 或相应表格，不能自行外推数值大小。

Table 2 | Three-round repair on CodeContests [15] under a shared coder and candidate budget. Both RSI-Anything configurations use world-model-guided candidate selection: one keeps the world model fixed (CURE-WM-Lite), and the other additionally adapts it using feedback.
<table><tr><td>Selection method</td><td>Hidden all-pass</td><td>Online calls/task</td></tr><tr><td>Random-K</td><td>26.0%</td><td>3.72</td></tr><tr><td>Capacity-matched static verifier</td><td>26.3%</td><td>3.72</td></tr><tr><td>RSI-Anything with a fixed world model (CURE-WM-Lite)</td><td>29.2%</td><td>3.71</td></tr><tr><td>RSI-Anything with world-model adaptation</td><td>31.2%</td><td>3.72</td></tr></table>



> 💡 **Table 2 数据核对**：表内共 4 行选择方法——Random-K 26.0% / 3.72 次在线调用；容量匹配静态验证器 26.3% / 3.72；固定世界模型（CURE-WM-Lite）29.2% / 3.71；世界模型自适应 31.2% / 3.72（行数是 4，不是 3）。读法：四者每题在线调用几乎一致，故成功率阶梯 26.0 < 26.3 < 29.2 < 31.2 不能归因于多消耗交互预算，这与正文'同等候选预算与同等测试调用'的设定互相印证。局限：表格只给点估计，未列种子方差或显著性，2.0 / 2.83 个百分点级的差异稳定性需结合 Figure 3b 的分布信息判断。

## 3.2. RSI-Anything improves task completion in GUI environments

We evaluated graphical user interface (GUI) task completion on MiniWoB++ click-checkboxes, which requires selecting specified checkboxes and submitting the selection [19], and on OSWorld [20]. The checkbox task additionally provides action-level diagnostics. RSI-Anything used interaction feedback to update both the policy and the world model, retaining these updates to guide subsequent GUI actions.



> 💡 **3.2 GUI 实验设置**：任务为 MiniWoB++ click-checkboxes（勾选指定复选框并提交选择，[19]）与 OSWorld（[20]）；click-checkboxes 额外提供动作级诊断，便于细粒度分析。机制差异（本节最关键的一点）：RSI-Anything 用交互反馈同时更新策略与世界模型，并保留更新指导后续动作；而 3.1 中策略被显式固定（ReasonFlux-Coder-7B），只更新用于选择的预测器。因此两节的自适应对象不同，比较增益时不能把 3.1 的'策略冻结'结论直接搬到 GUI 场景。

On click-checkboxes, success increased from 51% at baseline to 79% in the adapted configuration and 99% in the final RSI-Anything configuration (Figure 4c). Mean actions per episode decreased from 5.30 to 3.21, a 39.4% reduction from baseline; recorded repeated clicks fell from 237 to 0, and episodes with incorrect submissions fell from 9 to 1 (Figure 4e–g). The adapted configuration used fewer actions (2.93 per episode) but produced 21 incorrect-submission episodes. The final configuration therefore combined higher success with fewer submission errors despite a modest increase in action count relative to the adapted configuration. The World-based control achieved 49%, 96%, and 98% success across the three configurations (Figure 4c).



> 💡 **GUI 结果｜成功率阶梯与代价（含被初稿遗漏的数字）**：click-checkboxes 成功率 51%（基线）→79%（自适应配置）→99%（最终 RSI-Anything 配置）（Figure 4c）。动作效率与错误：平均每回合动作 5.30→3.21，较基线下降 39.4%；记录到的重复点击 237→0；错误提交回合 9→1（Figure 4e–g）。关键权衡：自适应配置动作更少（2.93/回合）却产生 21 个错误提交回合；最终配置在成功率更高（99%）的同时把提交错误压到 1，代价是动作数相对自适应配置回升（3.21 > 2.93）。对照 World-based control 在三档配置上分别为 49%、96%、98%（Figure 4c）。批读提示：最终配置 99% 相对对照 98% 只高 1 个百分点，本段未给种子或方差信息，'巨大潜力'之类的强判断应降级为待证；成功率 51%→99% 的跃升同时也伴随动作数下降，两者共同构成该任务上的核心证据。

On OSWorld, RSI-Anything improved task success rate by 5.5% relative to RLAnything [21] and by 18.4% relative to the initial agent (Figure 4d). RLAnything improved success by 12.3% relative to the initial agent.



> 💡 **数字核对与机制拆解**: 本段给出三个必须区分参照对象的数字：
> - RSI-Anything 相对 **RLAnything [21]** 的任务成功率提升 5.5%；
> - RSI-Anything 相对 **初始 agent** 提升 18.4%；
> - **RLAnything 相对初始 agent** 提升 12.3%（对应 Figure 4d）。
> 初稿只保留“5.5% 相对 RL 基线”并据此称 RSI-Anything“优于传统 RL”，与原文不符：RLAnything 是一个具体的基线方法（引用 [21]），不是泛指的传统 RL；而且 18.4% 与 12.3% 正是判断“自改进增益是否超过基线自身增益”的关键对照，不能省略。原文未报告绝对成功率、方差或样本量，因此“5.5% 的相对增益实际意义有多大”目前无法从本段判定。
> 
> 💡 **Q&A 批注记录**:
> - Q: 5.5% 与 18.4% 是否互相矛盾？
>   A: 不矛盾，二者参照对象不同（RLAnything 与初始 agent）；12.3% 描述的是基线 RLAnything 相对初始 agent 的进步。此结论由本段 relative to 结构与 Figure 4d 直接支持。
> - Q: 为什么选 OSWorld 作为评估基准？
>   A: 本段未说明选择理由，需回实验设置章节核对；在正文给出依据前应标为待证，不应断言其“更适合体现复杂规划能力”。

## 3.3. Successive policy updates improve laboratory planning

We next examined whether successive policy updates with RSI-Anything improved physical-domain planning. LabHorizon evaluates laboratory actions subject to reagent, instrument, and ordering constraints through Level-1 visually grounded next-action selection and Level-2 protocol-aligned sequence generation [22]. We followed five consecutive versions of a 4B policy from one self-improvement process and evaluated them in development order on the same 200-task F200 Level-2 benchmark. The Final Score combines action-sequence similarity and parameter accuracy.



> 💡 **机制拆解（实验室规划实验设置）**: 本段不是标题，而是一段实质性的实验设置描述，初稿称其“仅包含标题 3. Results，无实质内容”属明显误读。要点：
> - 评测对象：同一 self-improvement process 产出的 **4B 策略的五个连续版本**（five consecutive versions），并按 development order 在**同一** 200-task F200 Level-2 基准上评测；
> - 评测环境 LabHorizon [22]：在试剂、仪器、顺序约束下评测实验室动作，分 **Level-1 视觉落地的下一步动作选择**与 **Level-2 与协议对齐的序列生成**；
> - 指标：**Final Score 组合动作序列相似度与参数准确率**。
> 因此本段支撑的是“连续策略更新能否改善物理域规划”这一问题的**评测设计**，而非结果本身：原文在此未给出具体分数、逐版本趋势、是否单调上升，也未说明两个子分数如何加权，这些都需对应图表补齐，不能由本段直接推断改进成立。
> 
> 💡 **Q&A 批注记录**:
> - Q: “successive policy updates”是同一模型迭代，还是多个独立模型？
>   A: 原文写作 one self-improvement process 中 4B policy 的五个连续版本，可直接理解为同一自改进轨迹上的连续检查点；此为对原文措辞的解读，非外部信息。
> - Q: Final Score 是否分别按 Level-1/Level-2 计算？
>   A: 原文只说它组合序列相似度与参数准确率，未说明层级划分与权重，属未明示项，需查附录或后续表格确认。

