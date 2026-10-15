[← 返回 README](../README.md)

## 5. Discussion

The results support two roles for world models in recursive improvement: informing which actions receive external feedback and providing a predictor that can itself improve from that feedback. Predictive selection and model adaptation yielded successive gains in code repair, while retained policy versions improved laboratory planning. In GUI tasks, RSI-Anything updated both the policy and the world model and achieved higher task completion, with fewer repeated clicks and incorrect-submission episodes on the checkbox task. Together with measured-feedback search and the end-to-end immunescience workflow, these findings connect prediction-guided decisions to learning and retained system changes. The common contribution is a procedure for using prediction– observation pairs to guide current actions and assess changes to the system that will make subsequent decisions.



> 💡 **问题动机与核心结论**：本段总结世界模型在递归自我改进（RSI）中的双重角色——一是告知哪些动作接收外部反馈（即筛选受评估的动作），二是提供可从该反馈中改进的预测器。证据链分三块：代码修复中“预测选择+模型适应”带来连续增益；实验室规划中“保留策略版本”带来改进（注意：原文把实验室规划的增益归因于 retained policy versions，而非预测选择+模型适应，两者不可混为一谈）；GUI 任务中 RSI-Anything 同时更新策略与世界模型，提升了任务完成率，并在 checkbox 任务上减少重复点击与错误提交（减少错误操作这一条有 checkbox 任务限定）。最后一段把“测量反馈搜索”和端到端免疫科学工作流一并纳入，将预测引导的决策与学习及保留的系统变更连接起来。共同贡献点是：一套用“预测-观测对”指导当前动作、并评估系统变更的程序。
> 
> **机制拆解**：这里的闭环逻辑是——预测先引导行动，行动产生观测，观测既用于判断候选动作效用，也用于判断预测器可信度，从而让同一份经验同时服务于决策与系统更新。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 世界模型的“两个角色”分别对应哪类证据？
> > - A: 角色一（筛选受反馈的动作）对应预测选择/搜索；角色二（本身可改进的预测器）对应模型适应与 RSI-Anything 中世界模型的更新。二者在代码修复、实验室规划、GUI 任务中分别有对应证据。

The design links decisions about actions to decisions about system versions. Pairing predictions with observations allows the same experience to inform candidate utility and predictive reliability. Policies and world models can change on diferent schedules: a reliable predictor can support policy or workflow development, and new outcome evidence can motivate predictor adaptation while the policy remains fixed. Reliability checks govern when model adaptation is attempted, and component and compatibility checks govern which version is carried forward. Retaining accepted versions makes subsequent experience depend on the system’s accumulated improvements.



> 💡 **机制拆解：解耦更新节奏**：本段的核心设计是把“关于动作的决策”与“关于系统版本的决策”分开。配对预测与观测后，同一份经验同时用于两件事：判断候选动作的效用、判断预测器本身是否可靠。策略与世界模型可以按不同时间表演化——预测器可靠时可以支撑策略/工作流开发；出现新结果证据时可以促使预测器适应，而策略保持不动。
> 
> **检查机制的分工**：原文区分两类检查——“可靠性检查”控制何时尝试模型适应，“组件与兼容性检查”控制哪个版本被推进（carried forward）。保留已接受版本后，后续经验就建立在此前累积的改进之上。
> 
> **证据边界（推断标注）**：初稿把这里的检查机制解读为“解决了 RSI 中常见的灾难性遗忘或震荡问题”，原文并未出现“灾难性遗忘”“震荡”或任何针对此类失效的明确主张，也没有提供对应实验。因此该表述应降级为推断/待证：可以合理推测检查机制有防止无效更新被保留的作用，但“解决灾难性遗忘或震荡”属于超出证据的延伸。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 为什么允许策略和世界模型异步更新？
> > - A: 原文给出两条动机：可靠的预测器可独立支撑策略/工作流开发；新的结果证据可反向驱动预测器适应而策略不变。这样避免两者必须同步变更。

Domain generality concerns the reuse of this improvement mechanism across domainspecific world models, policies, and evaluation procedures. The evaluations support predictive selection, predictor adaptation, successive policy improvement, and feedbackguided search in their respective settings. Joint multiround evaluation is needed to establish sustained mutual improvement of the policy and world model within a single task. The present laboratory results assess protocol-aligned planning; reliable physical execution requires evaluation on the relevant equipment and workflows. The chemistry results cover two Suzuki–Miyaura screens under the reported experiment budgets, and the therapeutic hypothesis generated in the immune-science application awaits experimental testing.



> 💡 **局限性与未来方向**：本段界定通用性主张的边界。领域通用性指的是该改进机制能否在“领域特定的世界模型、策略与评估程序”之间复用；评估目前分别在各自设置中支持了预测选择、预测器适应、连续策略改进和反馈引导搜索。
> 
> **明确的待证事项**：
> 1. 原文要求通过“联合多轮评估”才能确立单一任务内策略与世界模型的持续相互改进——即当前尚未证明这种持续 mutual improvement。
> 2. 实验室结果只覆盖“协议对齐的规划”，可靠物理执行需要在相关设备与工作流上另行评估（原文并未说这些实验是“模拟环境”或“半自动流程”，此类表述为推断，应避免作为原文结论）。
> 3. 化学结果限于报告实验预算下的两个 Suzuki–Miyaura 筛选，样本/预算范围有限。
> 4. 免疫科学应用中产生的治疗假设仍待实验检验。
> 
> **证据边界（推断标注）**：初稿称“当前的 RSI 更多是模拟环境或半自动流程下的验证”，原文没有对实验室/化学实验的环境属性（真实 vs 模拟、自动 vs 半自动）做此判断，仅说明规划层面评估与需在真实设备上评估执行可靠性。该句为推断，不应视为作者结论。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 论文声称的“通用性”到什么程度？
> > - A: 只在“各自设置内”分别成立（预测选择、预测器适应、策略连续改进、反馈引导搜索各自有评估支撑）；跨领域复用同一机制、以及单一任务内策略-世界模型长期互促，均被作者列为需要进一步联合多轮评估的事项。

