[← 返回 README](../README.md)

## 4. Related Work

## 4.1. Recursive self-improvement

Recursive self-improvement (RSI) has evolved from improving reasoning and code to adapting interactive agents and their underlying systems. In coding, CURE co-evolves code generation and unit testing through execution feedback [14], while Self-Improving Coding Agent and Darwin Godel Machine iteratively modify agent implementations and retain empirically validated improvements [29, 30]. AIDE<sup>2</sup> extends this approach to self-improving AI research agents [31]. Beyond code, Voyager accumulates reusable executable skills [32], while GenEnv and Agent0 adapt task curricula to evolving agent capabilities [33, 34]. RLAnything jointly optimizes the policy, reward model, and environment [21], and OpenClaw-RL enables online policy improvement from user interactions and tool feedback [35]. Long-horizon RSI further requires improvements to persist across extended interactions. Recuris couples Experiential and Working Memory with validation-gated memory evolution [36], while ScienceBuddy introduces nested selfimprovement through harness refinement and model reinforcement learning [37]. These approaches improve agent code, policies, memories, harnesses, or training environments. RSI-Anything extends this line by explicitly incorporating predictive world models into the recursive loop: predictions guide candidate selection, observations assess model reliability, and policy–model compatibility determines which updated components are retained for subsequent interactions.



> 💡 **RSI 演进脉络与本文定位**
> 本段把递归自我改进（RSI）的谱系按“改进对象”分层：
> - **代码/实现层**：CURE 通过执行反馈共演化代码生成与单元测试 [14]；Self-Improving Coding Agent 与 Darwin Godel Machine 迭代修改 agent 实现并保留经验证有效的改进 [29, 30]；AIDE² 把该思路扩展到自我改进的 AI 研究代理 [31]。
> - **技能/课程层**：Voyager 累积可复用的可执行技能 [32]；GenEnv 与 Agent0 让任务课程适配 agent 能力的演化 [33, 34]。
> - **系统耦合层**：RLAnything 联合优化 policy、reward model 与 environment [21]；OpenClaw-RL 从用户交互与工具反馈做在线策略改进 [35]。
> - **长程持久层**：原文指出长程 RSI 额外要求“改进能在扩展交互中持续”，Recuris 把 Experiential Memory、Working Memory 与 validation-gated memory evolution 耦合 [36]，ScienceBuddy 通过 harness refinement 加 model RL 引入嵌套自我改进 [37]。
> 
> > 💡 **修改说明**：初稿把被改进对象写成“agent 代码、策略或记忆”，漏了 harnesses 与 training environments；原文的完整枚举是 code / policies / memories / harnesses / training environments，已补全。
> 
> RSI-Anything 的定位是显式把**预测世界模型**纳入递归循环，并给出三段式机制：预测指导候选选择 → 观测评估模型可靠性 → 策略–模型兼容性决定哪些更新后的组件被保留到后续交互。需注意原文只把“长程持久化”作为该方向的要求条件，并未声称 RSI-Anything 已解决它——是否解决属于**待证**，要靠实验章节支撑。

## 4.2. Verification and policy self-improvement

Verifiers, self-training, preference optimization, and verbal feedback turn evaluated trajectories into signals for selection and learning [1–5]. CURE [14] co-evolves a coder and unit tester, providing the ReasonFlux-Coder policy used in our code evaluation. RLAnything jointly optimizes the policy, reward model, and environment through feedback [21]. These approaches motivate the feedback-to-policy part of RSI-Anything. Our framework additionally makes world-model reliability and policy–model compatibility explicit in update acceptance: changing the policy changes the candidate distribution on which its predictor must be evaluated.



> 💡 **feedback→policy 谱系，以及本文在“更新接受”上的差异**
> 本段的证据链是：验证器、自训练、偏好优化与 verbal feedback 把被评估的轨迹转成用于选择与学习的信号 [1–5]；RLAnything 通过反馈联合优化 policy、reward model 与 environment [21]。原文明确说这些工作启发了 RSI-Anything 的 “feedback-to-policy” 部分。
> 
> > 💡 **修改说明（关键遗漏）**：初稿只举了 RLAnything，漏掉一个直接影响实验的事实——CURE [14] 共演化 coder 与 unit tester，**并提供本文代码评测所用的 ReasonFlux-Coder policy**。也就是说 CURE 对本文不是单纯“启发”，而是策略来源级的复用，已补入。
> 
> 本文宣称的差异在于：把 **world-model reliability** 与 **policy–model compatibility** 显式提升为 update acceptance 的判据。原文给出的理由是：改变 policy 会改变其 predictor 必须被评估的那个候选分布。
> 
> > 💡 **证据界限**：原文只陈述“换策略 ⇒ 换候选分布 ⇒ predictor 要在新分布上评估”这一因果，并未写出“必须同时验证两者一致性”这样的规范性结论；初稿的“必须同时验证两者的一致性”属于对原文的合理外推，已在此标注为**推断**而非原文断言。

## 4.3. Predictive world models

World Models [6], MuZero [8], Dreamer [7], and Genie [38] establish predictive environments as a basis for control, planning, and interactive generation. Joint-embedding predictive architectures learn latent targets without reconstructing every observation detail [10–12], and JEPA-Anything extends this line through OPF across heterogeneous data types [9]. Building on these predictive capabilities, RSI-Anything organizes how predictions and observed outcomes guide action selection and the acceptance of subsequent policy or world-model versions.



> 💡 **预测世界模型的架构基座，以及 RSI-Anything 的组织角色**
> 本段列出两类基座：
> - 预测环境类：World Models [6]、MuZero [8]、Dreamer [7]、Genie [38]，把预测环境确立为控制、规划与交互生成的基础。
> - 联合嵌入预测类：JEPA 系学习 latent targets 而**不重建每个观测细节** [10–12]；JEPA-Anything 通过 OPF 把这条线扩展到异构数据类型 [9]。
> 
> > 💡 **修改说明 1**：初稿列举经典工作时漏了 Genie [38]，已补。
> 
> > 💡 **修改说明 2（超出证据）**：初稿写 JEPA“强调通过潜在目标学习而非像素重建来**降低计算开销**”。原文只说不重建每个观测细节，**并未提及计算开销**作为动机；把该表述改为原文口径，效率动机属于**未获原文支持的推断**。
> 
> RSI-Anything 在此的角色是组织层：安排 predictions 与 observed outcomes 如何指导 action selection，以及如何决定后续 policy 或 world-model 版本的接受。初稿“从预测能力到递归决策逻辑的跨越”是对该角色的一种概括性解读，非原文原话，标为解释性表述；OPF 在原文中未展开定义，此处不代为补全。

## 4.4. Scientific and laboratory agents

Vision-language-action and cross-embodiment datasets motivate action-conditioned models for physical systems [39–41]. LabHorizon measures visual next-action prediction and protocol-aligned planning [22]. In chemistry, language-model tools, yield predictors, Bayesian optimization, and robotic laboratories provide complementary components for selecting and testing reactions [28, 42–46]. ReactionT5 [47] is a reaction-pretrained transformer that supports product, retrosynthesis, and yield prediction; our chemistry adapter combines this reaction-prediction backbone with the predictive design of JEPA-Anything [9] for action-conditioned yield forecasting. Single-cell foundation models and perturbation predictors support cellular-response forecasting [48–51]. RSI-Anything connects these domain capabilities through a common transition and feedback contract, so that the outcome of a program, experiment, or intervention can inform candidate selection and subsequent system updates.



> 💡 **科学/实验室代理的领域底座与本文的拼接方式**
> 本段按领域罗列可复用能力：
> - 具身侧：VLA 与 cross-embodiment 数据集促成物理系统的动作条件模型 [39–41]。
> - 规划基准：LabHorizon 度量 visual next-action prediction 与 protocol-aligned planning [22]。
> - 化学侧：语言模型工具、yield predictors、贝叶斯优化与机器人实验室提供互补组件 [28, 42–46]；ReactionT5 [47] 是反应预训练 transformer，支持 product、retrosynthesis 与 yield prediction。
> - 细胞侧：single-cell foundation models 与 perturbation predictors 支持 cellular-response forecasting [48–51]。
> 
> > 💡 **修改说明（关键归因纠正）**：原文只说明本文的 chemistry adapter 把 **ReactionT5 的 reaction-prediction backbone** 与 **JEPA-Anything [9] 的 predictive design** 组合，用于 action-conditioned yield forecasting。初稿写“利用现有的化学适配器（结合 ReactionT5）和视觉语言动作模型”，把 VLA 说成本文 adapter 的组件——原文中 VLA/cross-embodiment 只是物理系统动作条件建模的**动机引用**，并非本文 adapter 的构成，已改回原文归因。
> 
> 连接机制：RSI-Anything 以共同的 **transition and feedback contract** 把这些领域能力接起来，使 program、experiment 或 intervention 的结果能进入候选选择与后续系统更新。初稿“支持实验室环境下的自主发现与验证”是概括措辞，原文未使用该表述，标为解读而非原文主张。

