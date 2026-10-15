[← 返回 README](../README.md)

## 1. Introduction

Recursive self-improvement (RSI) turns interaction experience into lasting improvements in a system’s ability to act and learn. Within tasks, feedback refines solutions or guides actions [1, 2]. Across rounds, self-training and preference optimization support persistent policy or procedural updates [3–5]. Recursiveness requires these updates to be retained and shape subsequent interaction and learning. The central question is how experience from one round improves the system’s ability to act and learn in the next.



> 💡 **问题动机**: 界定 RSI 的核心问题——单轮交互经验如何转化为系统跨轮的持久行动与学习能力。原文把递归性明确为“更新被保留并塑造后续交互与学习”（retained and shape subsequent interaction and learning），并列出两条既有路径：任务内反馈精修解或引导动作 [1, 2]；跨轮的自训练与偏好优化带来策略/流程的持久更新 [3–5]。核对后修正一处推断边界：本段**未出现世界模型**，初稿“为引入世界模型做铺垫”属于结构推断（世界模型要到第 3 段才登场），可保留但应标注为推断而非本段主张。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 原文说“recursiveness requires these updates to be retained”，这个“retained”由谁判定？
> > - A: 本段只提出条件，未给机制；机制在 block 6 与 block 10 给出——由组件级检查与兼容性检查决定保留哪对策略-模型组合。

![](../images/1b312a28b78c7d6478bbdb915e0b9d0d194010cab97aaa03b6414f4145177062.jpg)  
Figure 1 | RSI-Anything overview. Building on the cross-domain predictive foundation of JEPA-Anything, RSI-Anything uses world-model predictions to support understanding, planning, and action selection across diverse tasks. Interaction feedback drives policy or workflow updates and conditional world-model adaptation. Component and compatibility checks determine which policy–model pair is retained to guide the next round of interaction and learning, closing the recursive self-improvement loop.



> 💡 **Figure 1 批读（仅依据 caption，未获得图片视觉信息）**: caption 描述的闭环是“JEPA-Anything 跨域预测基础 → 世界模型预测支持理解/规划/行动选择 → 交互反馈驱动策略或工作流更新与条件式世界模型适应 → 组件级与兼容性检查决定保留哪对策略-模型 → 进入下一轮”。核对要点：caption 中出现的是双检查（component and compatibility checks）与“conditional world-model adaptation”，初稿准确复述了这两点。需明确的边界：图中具体模块连线、是否画有可靠性检查的判定分支，仅凭 caption 无法确认，不能当作已核验的视觉事实。

Learning from external feedback requires executing or evaluating candidates before their consequences can guide improvement, limiting exploration under constrained interaction budgets. World models predict the efects of candidate actions on subsequent states or outcomes [6–8], enabling prospective evaluation and planning. Within RSI, these predictions can prioritize feedback collection and guide learning-data selection or construction. World models thus provide a predictive foundation for improvement under limited external feedback.



> 💡 **机制拆解**: 给出引入世界模型的充分理由，是一个“成本约束”论证：外部反馈必须先执行或评估候选，其后果才能用于改进，这限制了交互预算下的探索；世界模型预测候选动作对后续状态/结果的影响 [6–8]，于是可在真实执行前做前瞻评估与规划，并在 RSI 内部用于**排定反馈收集优先级**与**选择/构造学习数据**。注意原文的定位是“predictive foundation for improvement under limited external feedback”，即世界模型是加速器而非反馈替代品——它决定“先试哪个/用哪些数据学”，不产生新的外部监督信号。

JEPA-Anything provides a shared framework for world-model construction across heterogeneous domains [9]. Building on joint-embedding predictive architectures [10–12], it predicts target representations from observed context. Its orthogonal predictive factorization (OPF) decomposes latent targets into complementary factors, learns dedicated predictors, and recombines their outputs into a complete latent state [9]. This provides a predictive foundation across domains (Figure 1). However, prediction alone does not establish recursive improvement: predicted outcomes must guide interaction, while external feedback must produce updates that persist across subsequent rounds.



> 💡 **技术基础与转折**: 前半段交代底座——JEPA-Anything 面向异构域提供统一的世界模型构建框架 [9]，基于联合嵌入预测架构 [10–12]，从观测上下文预测目标表征；其 OPF 把 latent 目标分解为互补因子、各自训练专用预测器、再重组为完整 latent 状态 [9]（分解—专用预测—重组三部曲需与初稿描述一致，已核对无误）。后半段是全文的转折句：“prediction alone does not establish recursive improvement” —— 必须具备两条额外条件：(a) 预测结果要真正指导交互；(b) 外部反馈要产生跨轮持久的更新。初稿理解为“引出耦合难题”，方向正确。

Closing this loop introduces a coupled-update challenge. Policy updates change the candidate actions and experience distributions on which the world model operates, potentially undermining prediction reliability. Conversely, world-model updates alter candidate rankings, imagined trajectories, and the learning signals guiding policy improvement. Consequently, improving either component independently does not guarantee better joint performance. Reliable RSI therefore requires determining when to update each component, assessing prediction reliability under changing policies, and ensuring that retained policy–model pairs improve subsequent interaction and learning.



> 💡 **核心难点分析（耦合更新挑战）**: 原文给出的是一个双向耦合而非单向依赖：策略更新改变世界模型面对的候选动作与经验分布，可能削弱预测可靠性；世界模型更新改变候选排序、想象轨迹与指导策略改进的学习信号。因此“单独改进任一组件不保证联合性能更好”。对初稿的一处修正：原文要求的三个动作是 (1) 判断何时更新每个组件、(2) **评估变化策略下的预测可靠性**、(3) 确保被保留的策略-模型对能改进后续交互与学习；初稿写作“验证兼容性”把 block 6 才出现的 compatibility checks 提前塞进本段，属术语前置，已改为原文表述。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 为什么“独立优化任一组件”不够？
> > - A: 因为两者的优化目标互为对方的数据分布与打分函数来源：策略变了，world model 的输入分布与候选排序基准就变；world model 变了，策略看到的候选与学习信号也变。原文结论是改进其一不保证联合性能，见本段末句。

To address these challenges, we introduce RSI-Anything, a domain-general framework that integrates predictive world models with feedback-driven recursive improvement (Figure 2). World-model predictions guide action selection, prioritize external evaluations, and support learning-data selection or construction. External feedback drives policy learning or workflow revision and triggers world-model adaptation when reliability checks warrant it. The policy and world model can evolve asynchronously, with component-level checks evaluating individual updates and compatibility checks determining which policy–model pair is retained. This retained pair shapes subsequent candidates and learning experience, closing the recursive improvement loop.



> 💡 **方案概览**: RSI-Anything 的三条设计主线（Figure 2，此处仅有正文指代）：① 世界模型预测用于动作选择、给外部评估排优先级、支持学习数据选择或构造；② 外部反馈驱动策略学习或工作流修订，并在可靠性检查通过时**触发**世界模型适应（conditional，非每轮必更）；③ 策略与世界模型**异步演化**，组件级检查评估单个更新，兼容性检查决定保留哪对策略-模型。保留对再塑造下一轮候选与学习经验，形成闭环。初稿的“双重检查机制”概括准确，且与 block 10 的贡献陈述一致；需注意“异步”意味着两者更新频率可以不同步，这是初稿正确捕捉到的点。

Our evaluations examine whether introducing world-model predictions into RSI improves candidate selection, whether retained policy or model updates yield further improvements, and whether these gains can be achieved under limited external feedback. We investigate these questions across general computational tasks (code repair and GUI interaction), physical-domain planning, and experimental science. We further present an end-to-end application of RSI-Anything in immune science, spanning scientific-agent workflow evolution, immune world-model construction, analysis with self-correction, and prospective therapeutic hypothesis generation [13].



> 💡 **实验范围声明**: 三个评估问题分别是：世界模型预测的引入是否改进候选选择；被保留的策略或模型更新是否带来进一步增益；这些增益能否在**有限外部反馈**下取得。覆盖域为通用计算任务（代码修复与 GUI 交互）、物理域规划、实验科学；另有一个免疫科学端到端应用，跨科学智能体工作流演化、免疫世界模型构建、带自校正的分析、前瞻性治疗假设生成 [13]。核对修正：“暗示了从抽象理论到具体垂直领域的落地能力”是初稿的推断，原文只是陈述该应用的存在与环节，未评价其“落地能力”，已改写为对应用环节的客观转述。

Our contributions are threefold:



> 💡 **贡献列表引导**: 承上启下句，宣告下文三点式贡献，本身不含技术主张；批读时只需确认后文 block 9/10/11 与这里的“threefold”一一对应（框架、更新-保留机制、跨域实证），已核对为三条，无第四项隐藏贡献。

• World models for recursive self-improvement. We introduce RSI-Anything, a domain-general framework that integrates predictive world models into recursive self-improvement to anticipate action outcomes and guide experience collection. We formulate persistent improvement at the level of the policy–world-model system, making prediction reliability under evolving policies a central design requirement.



> 💡 **贡献 1：框架与问题表述**: 两个可区分的主张：(a) 工程性——把预测世界模型集成进 RSI，用于预期动作结果并指导经验收集；(b) 概念性——把“持久改进”表述在**策略-世界模型系统**这一层级，从而使“演变策略下的预测可靠性”成为核心设计需求。初稿准确。批读侧重：(b) 是本文与一般“世界模型 + 策略”工作的表述差异点，它把评价对象从单组件指标换成系统级跨轮保持性。

• A recursive update-and-retention mechanism. We connect prediction-guided action and learning-data selection with feedback-driven policy learning or workflow revision and conditional world-model adaptation. Component and compatibility checks determine which policy–model pair is retained, allowing asynchronous updates. The retained pair shapes subsequent candidates and learning experience, linking each accepted system revision to the next round of improvement.



> 💡 **贡献 2：递归更新与保留机制**: 链条为“预测指导动作与学习数据选择 → 反馈驱动策略学习或工作流修订 → 条件式世界模型适应”，出口由组件检查与兼容性检查决定保留哪对策略-模型，且允许异步更新；被保留对再塑造下一轮候选与学习经验。核对：原文写的是“linking each accepted system revision to the next round of improvement”，即**每次被接受**的系统修订才承接下一轮，初稿“确保递归循环的有效性”偏向效果断言，已回退为原文的“被接受修订承接下一轮”这一结构性陈述。

• Empirical evidence across domains. With the coder fixed, we demonstrate successive gains from introducing world-model-guided selection and adapting the predictor at similar online test usage. Further evaluations show higher GUI task completion, improvement across five successive laboratory-planning policy versions, and higher reaction yields than Bayesian optimization at matched experiment counts. An endto-end immune-science application illustrates how RSI-Anything connects retained workflows and predictive models with evidence-guided analysis and hypothesis generation.



> 💡 **实验证据链批读**:
> > - **核心主张**: 在 coder 固定的条件下，引入世界模型引导的选择 + 预测器在线适配能在**相近的在线测试用量**下带来逐次增益（successive gains）。
> > - **三项具体证据**: ① GUI 任务完成度更高；② 连续 5 个实验室规划策略版本均有改进；③ 在匹配实验次数下反应产率高于贝叶斯优化。
> > - **指标性质区分**: GUI 完成度与实验室规划版本改进属任务成功率/流程指标；反应产率属结果质量指标；与 BO 的对比被作者明确限定为“matched experiment counts”，即效率（同等实验预算下更优），而非无限预算下的最优性主张。
> > - **证据边界（修正初稿推断）**: 原文只写“With the coder fixed”，并未说明固定 coder 的**目的**是“隔离变量、确保提升归因于世界模型机制”；该解释是合理推断但非原文表述，应标为推断。同理，“at similar online test usage”是相近而非严格相等，不能读成严格等量对照。

