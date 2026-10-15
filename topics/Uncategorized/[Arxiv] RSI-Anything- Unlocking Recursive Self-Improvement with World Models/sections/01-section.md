[← 返回 README](../README.md)

## Abstract

Recursive self-improvement (RSI) aims to transform interaction experience into persistent improvements, yet external feedback is costly and world-model predictions may become unreliable as policies evolve. We introduce RSI-Anything, a domaingeneral framework built on JEPA-Anything that uses predictive world models to guide action and learning-data selection. Observed feedback drives policy or workflow improvement and conditional world-model adaptation, while component and compatibility checks govern the retention of updated policy–model pairs. Across diverse computational and scientific domains, RSI-Anything demonstrates substantial gains. In code repair, with the coder fixed, success increases from 26.3% with a static verifier to 29.2% through predictive selection and 31.2% after world-model adaptation, at comparable test usage. Graphical user interface (GUI) evaluations showed higher task completion, while average actions decrease by 39.4%. Across five successive policy versions, laboratory-planning scores more than double from 21.80% to 46.01%. In reaction optimization, RSI-Anything achieves 96.15% mean best observed yield versus 82.16% for Bayesian optimization under the same 40- experiment budget. An end-to-end immune-science application further connects workflow evolution and predictive modeling with therapeutic hypothesis generation. These results demonstrate how predictive world models and retained system updates enable recursive improvement across diverse environments.



> 💡 **问题动机**: 递归自我改进（RSI）的目标是把交互经验转化为可持续的改进，但存在两个瓶颈：一是外部反馈成本高，二是世界模型的预测会随策略演化而变得不可靠。注意原文说的是 external feedback（外部反馈）昂贵，并未限定为“人工反馈”，因此不宜直接等同为“无需人工标注”。
> **机制拆解**: 提出 RSI-Anything，一个建立在 JEPA-Anything 之上的 domain-general（领域通用）框架，用预测性世界模型来指导**动作选择**与**学习数据选择**。闭环包含三个环节：①观测到的反馈驱动策略或工作流改进；②条件化的世界模型适配（conditional world-model adaptation）；③由组件检查与兼容性检查（component and compatibility checks）决定更新后的 policy–model 配对是否被保留。这里的“保留”机制是原文明示的关键设计，用于避免更新后系统退化。
> **关键证据（数字逐项核对）**:
> 1. **代码修复**：在 coder 固定的条件下，成功率从静态验证器的 26.3% → 经预测选择达 29.2% → 经世界模型适配达 31.2%，且测试用量相当（comparable test usage）。三个数字与条件（coder fixed）均在原文中明确。
> 2. **GUI 评测**：任务完成率更高，平均动作数减少 39.4%。原文未给出 GUI 的具体完成率数值，只有“higher”，因此不应补造百分比。
> 3. **实验室规划**：跨 5 个连续策略版本，得分从 21.80% 增长到 46.01%（原文为 more than double，即增长超过一倍，而非仅“翻倍”）。
> 4. **反应优化**：在相同 40 次实验预算下，RSI-Anything 的平均最佳观测收率 96.15%，对比贝叶斯优化的 82.16%。
> 5. **免疫科学端到端应用**：原文还提到一个端到端免疫科学应用，把工作流演化与预测建模连接到治疗假设生成——这一条在初稿中被遗漏，应予补上，否则会低估论文的应用范围。
> **推断与待证**: 摘要只给出“diverse computational and scientific domains”的笼统表述，未说明“领域通用”的边界条件；JEPA-Anything 如何在非视觉的科学领域保持泛化、以及兼容性检查的具体判据（阈值？回滚？），摘要均未交代，属于待查正文/附录的问题，不能在此断言。
> **局限/追问点**: “动作减少 39.4%”是否伴随完成率代价？实验室规划 5 个版本的提升是否依赖人工设定的版本边界？反应优化中贝叶斯优化的超参是否同等调优？

