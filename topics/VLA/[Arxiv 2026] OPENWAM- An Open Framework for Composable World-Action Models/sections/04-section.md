[← 返回 README](../README.md)

## 4. Experiments

We evaluate OPENWAM along three dimensions: native policy performance, component composability, and local dynamics modeling. We test whether the shared robot-video foundation supports different video–action interaction programs, whether independently trained action components remain useful when paired with adapted visual predictors, and whether local-context forward dynamics models actiondependent visual outcomes.



> 💡 **实验维度**: 三项评估各自对应一个问题：共享的机器人-视频基础模型是否支持不同的 video–action 交互程序、独立训练的动作组件配上适配后的视觉预测器后是否仍然有用、局部上下文的 forward dynamics 能否建模依赖动作的视觉结果。

## 4.1. Experimental Setup

Robot-video pretraining and simulation. We initialize the visual backbone from Wan2.2-5B [75] and further pretrain it without action supervision on heterogeneous robot and human-interaction video, including UMI/MV-UMI [12, 64], AgiBot World [9], RoboMIND [81], InternData-A1 [73], RoboCOIN [84], FastUMI [102], Ego-Exo4D [19], and Open X-Embodiment [58]. Unless otherwise stated, downstream OPENWAM variants use this common initialization.



> 💡 **预训练数据**: 视觉骨干网基于 Wan2.2-5B 初始化，并在无动作监督的异构机器人及人类交互视频上进一步预训练。数据集涵盖 UMI、AgiBot World 等主流来源，为下游任务提供统一初始化。

The four interaction programs share the same dual-stream MoT backbone and downstream training recipe; they differ in generation order and future cross-modal attention. Video is processed in four-frame latent chunks, each predicting a 16-step action chunk, with proprioception provided as a perchunk conditioning signal. Actions consist of end-effector position, axis-angle rotation, and a gripper command. We use a two-stage training procedure: causal robot-video pretraining followed by joint video–action training on the target suite.



> 💡 **训练配置**: 四种交互程序共用双流 MoT 骨干网和下游训练配方，只在生成顺序与 future cross-modal attention 上不同，这是消融中的受控变量。视频切成 4 帧 latent chunk，每块预测 16 步动作 chunk，proprioception 按块作条件；动作含末端位置、轴角旋转和夹爪指令，训练分因果预训练与联合视频-动作两阶段。

We evaluate Joint, VTA, ATV, and Decoupled on LIBERO-Object, LIBERO-Goal, LIBERO-Spatial, and



> 💡 **仿真基准**: 在 LIBERO 的 Object、Goal、Spatial、Long 四个套件上比较 Joint、VTA、ATV、Decoupled 四种程序；这句原文跨块，LIBERO-Long 出现在下一段开头。

Table 2. Real-world bimanual manipulation tasks.
<table><tr><td>Task</td><td>Objective</td><td>Main challenge</td></tr><tr><td>Toast Bread</td><td>Insert bread and activate toaster</td><td>Deformability and precise insertion</td></tr><tr><td>Rubik&#x27;s Cube</td><td>Restore final layer of 2 × 2 cube</td><td>Contact-rich push and rotation</td></tr><tr><td>Sort Cups</td><td>Sort cups according to color</td><td>Variable ordering and sequencing</td></tr></table>



> 💡 **真实任务定义**: Table 2 列出三个双臂任务及其主要难点：烤面包（面包形变与精准插入）、复原 2×2 魔方最后一层（接触丰富的推动与旋转）、按颜色给杯子分类（顺序可变）。

LIBERO-Long [51]. Component transfer uses LIBERO-Long as the downstream source distribution and four LIBERO-90 targets (Table 1) chosen to represent qualitatively different forms of transfer. These targets are outside the LIBERO-Long source-task set; robot-video pretraining is treated as a separate stage.



> 💡 **迁移设置**: 使用 LIBERO-Long 作为源分布，四个 LIBERO-90 目标作为下游测试集以评估组件迁移。目标任务不在源集合中，且机器人-视频预训练被视为独立阶段。

Real-world tasks. We evaluate on a bimanual platform comprising two Franka Research 3 arms with parallel-jaw grippers and three RGB views: two wrist-mounted cameras and one centered third-person camera. The three task definitions are listed in Table 2. Figure 4 shows the real-world tasks and the LIBERO-90 transfer targets.



> 💡 **硬件平台**: 真实世界实验基于双 Franka Research 3 机械臂平台，配备平行夹爪和三个 RGB 视角（两个腕部相机和一个第三人称中心相机），用于执行 Table 2 中的任务。

Dynamics supervision. Native task models are trained from task demonstrations. For local dynamics, we additionally construct LIBERO-Long-CF by executing alternative action continuations from states encountered in LIBERO-Long demonstrations. Branches sharing an initial state remain in the same train, validation, or test partition. IDM and FDM experiments use demonstration-only, counterfactualonly (CF-only), or mixed supervision. Mixed supervision samples 60% of transitions from $\mathcal { D } _ { \mathrm { c f } }$ and 40% from $\mathcal { D } _ { \mathrm { t a s k } } ^ { \mathrm { l o c } }$ corresponding to η = 0.6 in Eq. 7.



> 💡 **动态监督构造**: LIBERO-Long-CF 由 LIBERO-Long 演示中遇到的状态出发、执行替代动作续写得到；共享初始状态的各分支被留在同一 train/validation/test 划分，避免跨划分泄漏。IDM 与 FDM 可用演示、纯反事实或混合监督，混合时 60% 的 transition 来自 $\mathcal{D}_{cf}$、40% 来自 $\mathcal{D}_{task}^{loc}$，对应 Eq. 7 的 $\eta=0.6$。

Evaluation. Closed-loop task success is the primary policy and composition metric. For forward dynamics, we report visual prediction error and outcome identification accuracy. Given K realized futures from the same state, the metric tests whether the prediction is closest in RGB MSE to its matching outcome. We report both pairwise identification (K = 2) and identification among all 16 action branches (K = 16).



> 💡 **评估指标**: 主要指标为闭环任务成功率。对于前向动力学，报告视觉预测误差和结果识别准确率。识别任务要求预测结果在 RGB MSE 上与匹配的未来最接近，分为 K=2 和 K=16 两种设定。

## 4.2. Native Policy Performance

Broad LIBERO evaluation. We compare the four interaction programs across the LIBERO suites and report the average success rate over three random seeds in Table 3. The principal OPENWAM configurations achieve strong performance across all four suites, establishing that the shared foundation supports multiple world–action generation orders.



> 💡 **LIBERO 总体表现**: 在四个 LIBERO 套件上报告三次随机种子的平均成功率（Table 3），作者据此认为共享基础模型能支持多种 world–action 生成顺序。

Backbone initialization ablation. Holding the VTA architecture, task supervision, and downstream training procedure fixed, the original Wan2.2-5B initialization reaches 68.4% success on LIBERO-Long, while our robot-video-pretrained causal backbone reaches 97.8% (+29.4 points). This comparison measures the combined effect of robot-video pretraining and causal adaptation, rather than attributing the gain to either factor individually.



> 💡 **骨干网消融**: 固定 VTA 架构、任务监督和下游训练流程，原始 Wan2.2-5B 初始化在 LIBERO-Long 上为 68.4%，换用机器人视频预训练的因果骨干后升至 97.8%（+29.4 点）。作者自己说明这一对比测的是预训练与因果适配的合并效果，不能拆分归因到某一因素。

Table 3. Closed-loop success (%) on four LIBERO suites. Baselines are previously reported results.
<table><tr><td>Method</td><td>Object</td><td>Goal</td><td>Spatial</td><td>Long</td><td>Mean</td></tr><tr><td>OpenVLA [36]</td><td>88.4</td><td>79.2</td><td>84.7</td><td>53.7</td><td>76.5</td></tr><tr><td>OpenVLA-OFT [37]</td><td>98.4</td><td>97.9</td><td>97.6</td><td>94.5</td><td>97.1</td></tr><tr><td>π0 [6]</td><td>98.8</td><td>95.8</td><td>96.8</td><td>85.2</td><td>94.1</td></tr><tr><td> $\pi _ { 0 . 5 } \ [ 3 3 ]$ </td><td>98.2</td><td>98.0</td><td>98.8</td><td>92.4</td><td>96.9</td></tr><tr><td>GR00T-N1 [5]</td><td>97.6</td><td>93.0</td><td>94.4</td><td>90.6</td><td>93.9</td></tr><tr><td>Motus [3]</td><td>99.8</td><td>96.6</td><td>96.8</td><td>97.6</td><td>97.7</td></tr><tr><td>Fast-WAM [94]</td><td>100.0</td><td>97.0</td><td>98.2</td><td>95.2</td><td>97.6</td></tr><tr><td>LingBot-VA [43]</td><td>99.6</td><td>97.2</td><td>98.5</td><td>98.5</td><td>98.5</td></tr><tr><td>OPENWAM-VTA</td><td> $9 9 . 4 _ { \pm 0 . 3 }$ </td><td> $9 8 . 4 _ { \pm 0 . 5 }$ </td><td> $9 8 . 6 { \scriptstyle \pm 0 . 2 }$ </td><td> $9 7 . 8 _ { \pm 0 . 4 }$ </td><td>98.6</td></tr><tr><td>OPENWAM-ATV</td><td> $9 8 . 0 { \scriptstyle \pm 0 . 4 }$ </td><td> $9 7 . 2 _ { \pm 0 . 2 }$ </td><td> $9 6 . 6 { \scriptstyle \pm 0 . 6 }$ </td><td> $9 5 . 4 _ { \pm 0 . 3 }$ </td><td>96.8</td></tr><tr><td>OPENWAM-Joint</td><td> $9 8 . 2 { \scriptstyle \pm 0 . 2 }$ </td><td> $9 7 . 8 { \scriptstyle \pm 0 . 4 }$ </td><td> $9 7 . 6 { \scriptstyle \pm 0 . 3 }$ </td><td> $9 6 . 6 { \scriptstyle \pm 0 . 5 }$ </td><td>97.6</td></tr><tr><td>OPENWAM-Decoupled</td><td> $9 9 . 0 { \scriptstyle \pm 0 . 3 }$ </td><td> $9 8 . 0 { \scriptstyle \pm 0 . 2 }$ </td><td> $9 7 . 8 _ { \pm 0 . 5 }$ </td><td> $9 7 . 0 { \scriptstyle \pm 0 . 4 }$ </td><td>98.0</td></tr></table>



> 💡 **Table 3 解读**: OPENWAM-VTA 平均 98.6%（各套件 98.0–99.4%，三次种子），在表内方法中平均最高，但领先幅度很小：最强基线 LingBot-VA 为 98.5%，且在 LIBERO-Long 上 LingBot-VA 的 98.5% 高于 VTA 的 97.8%。并非所有变体都领先——OPENWAM-ATV 平均 96.8%，低于 OpenVLA-OFT 的 97.1% 和 π0.5 的 96.9%；此外基线数字为原论文报告结果，并非在同一设置下重跑。

Table 4. Closed-loop success (%) on real-world bimanual manipulation tasks.
<table><tr><td>Method</td><td>Toast</td><td>Cube</td><td>Cups</td><td>Mean</td></tr><tr><td>OPENWAM-VTA</td><td>92.0</td><td>90.0</td><td>94.4</td><td>92.1</td></tr><tr><td>OPENWAM-Joint</td><td>90.0</td><td>94.0</td><td>91.7</td><td>91.9</td></tr></table>



> 💡 **Table 4 解读**: 真实世界双臂操作中，OPENWAM-VTA 平均成功率 92.1%，略高于 Joint 的 91.9%。VTA 在杯子分类任务上表现最佳（94.4%），Joint 在魔方任务上更优（94.0%）。

Real-world native policies. Table 4 reports closed-loop success for VTA and Joint on the three bimanual tasks of Table 2, which test the same framework under multi-view, deformable, contact-rich, and multi-object manipulation.



> 💡 **真实世界结果**: Table 4 报告 VTA 与 Joint 在 Table 2 三个任务上的闭环成功率，覆盖多视角、形变、接触丰富和多物体操作场景。表中每个任务只给单一成功率数值、未标方差，说其“鲁棒”属于推断而非原文结论。

## 4.3. Component Composability

We next study whether action-grounding components remain useful when the visual predictor changes. Full-context and local-context IDMs are evaluated under transitionsupervision regimes, separating the effects of broader localtransition data from the restriction of task-level context.



> 💡 **实验设计**: 本节探究视觉预测器更换后，动作接地组件是否仍然有用。做法是把全上下文与局部上下文的 IDM 放在不同转换监督方案下比较，从而把“更广的局部转换数据”与“任务级上下文的限制”两个因素分开。

Frozen action-component reuse. For each LIBERO-90 target, the visual producer is taken from a task-tuned VTA model while source-trained action components remain frozen. Thus, the experiment measures reuse of an action component after visual-task adaptation, rather than targettask learning without action supervision. All reused executors are evaluated with the same target predictor and rollout protocol. Table 6 reports source-task composition on LIBERO-Long.



> 💡 **冻结复用策略**: 在 LIBERO-90 各目标上，视觉预测器取自任务微调后的 VTA 模型，源训练的动作组件保持冻结，因此测的是视觉任务适配之后动作组件的复用，而不是没有动作监督的目标任务学习。所有被复用的执行器用同一目标预测器和 rollout 协议评估；Table 6 给出 LIBERO-Long 上的源任务构成。

Conditioning scope and supervision interact strongly (Tables 6 and 5). Mixed supervision raises mean target success from 21.5% to 84.0% for local-context IDMs, compared with 44.5% to 47.0% for full-context IDMs. Under matched mixed supervision, local conditioning improves target success by 37.0 percentage points while retaining 94.4% source-task success. Counterfactual-only and mixed supervision both substantially improve local-context IDM transfer, achieving comparable mean success of 84.5% and 84.0%, respectively, across the four LIBERO-90 targets. These results show that broader transition coverage supports the reuse of local-context dynamics components after visual task adaptation.



> 💡 **关键结果**: 混合监督使局部上下文 IDM 的平均目标成功率从 21.5% 升到 84.0%，而全上下文 IDM 只从 44.5% 升到 47.0%。在匹配的混合监督下，局部条件化把目标成功率提高 37.0 个百分点，同时保留 94.4% 的源任务成功率；CF-only 与混合监督效果相当（84.5% 与 84.0%），说明更广的转换覆盖支撑了局部上下文动力学组件在视觉任务适配后的复用。

The per-task results also reveal uneven transfer. The demonstration-only full-context IDM performs well on Tasks 64 and 74 but obtains 0% success on Tasks 21 and 45, whereas CF-only and mixed local-context IDMs remain effective across all four targets. This contrast highlights their more consistent reuse across the evaluated task changes.



> 💡 **迁移一致性**: 仅用演示训练的全上下文 IDM 在 Task 64、74 上表现良好，但在 Task 21、45 上成功率为 0%；而 CF-only 与混合监督的局部上下文 IDM 在全部四个目标上都保持有效。这凸显后者在不同任务变化下复用更稳定。

## 4.4. Action-Conditioned Forward Dynamics

We evaluate local-context FDMs on 2,560 counterfactual futures from 160 contexts across ten LIBERO tasks, with 16 action branches per context. Models share the same initial observations, actions, and realized futures. In addition to RGB prediction error, we measure whether the predicted future preserves the identity of its conditioning action through outcome identification at $K = 2$ and $K = 1 6$



> 💡 **FDM 评估协议**: 在十个 LIBERO 任务的 160 个上下文上评估局部上下文 FDM，每个上下文 16 条动作分支，共 2,560 条反事实未来；各模型共享相同的初始观测、动作和真实未来，保证对比条件公平。除 RGB 预测误差外，还以 $K=2$ 和 $K=16$ 的结果识别检验预测未来是否保留其条件动作的身份。

As shown in Table 7, CF-only and mixed supervision reduce RGB MSE by 34.5% and 33.0%, respectively, relative to demonstration-only training. CF-only supervision raises outcome accuracy from 68.3% to 93.6% for $K = 2 ,$ , and from 21.1% to 71.3% for $K = 1 6 .$ . Mixed supervision shows a similar improvement, reaching 91.8% and 67.7%, respectively. These results show that broader transition supervision improves both visual prediction accuracy and the ability to distinguish action-dependent futures.



> 💡 **监督增益**: 相对仅演示训练，CF-only 与混合监督把 RGB MSE 分别降低 34.5% 和 33.0%。CF-only 把结果识别准确率从 68.3% 提到 93.6%（$K=2$）、从 21.1% 提到 71.3%（$K=16$），混合监督接近（91.8% 与 67.7%），说明更广的转换监督同时改善视觉预测精度与区分动作依赖未来的能力。

