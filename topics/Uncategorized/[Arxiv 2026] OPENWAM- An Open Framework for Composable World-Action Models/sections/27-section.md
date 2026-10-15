[← 返回 README](../README.md)

## E.2. Unified Policy and Dynamics Modeling

UVA trains policy generation, inverse dynamics, and forward dynamics within a single model. We therefore also evaluate one OPENWAM checkpoint trained with 60% real joint video–action supervision, 20% local-context IDM supervision, and 20% local-context FDM supervision; the IDM and FDM portions are each split equally between demonstration and counterfactual transitions.



> 💡 **训练配比**: 原文第一句说的是 UVA 在一个模型里训练策略生成、逆动力学(IDM)和正向动力学(FDM)，本文为对照额外评估一个统一 OPENWAM checkpoint，用 60% 真实视频–动作联合监督、20% 局部上下文 IDM 监督、20% 局部上下文 FDM 监督训练，且 IDM 与 FDM 两部分各由演示与反事实转移对半构成。

The unified OPENWAM checkpoint outperforms UVA on policy success and on the evaluated IDM and FDM metrics. Its counterfactual dynamics accuracy is weaker than that of the dedicated specialists, showing the expected tradeoff between multi-objective training and specialization. The unified OPENWAM checkpoint also gives the lowest IDM and FDM errors on real demonstrations. These numbers measure fit to the demonstration distribution, not held-out generalization, because some of the same demonstrations may also be seen by the policy objective during training. Its policy score is also lower than the policy-specialized OPEN-WAM models as only 60% of training updates optimize the policy objective.

> 💡 **结果与权衡**: 统一 checkpoint 在策略成功率和所评估的 IDM/FDM 指标上优于 UVA，但反事实动力学精度低于专用专家，作者将其称为多目标训练与专业化的预期权衡。需注意其真实演示上的最低 IDM/FDM 误差衡量的是对演示分布的拟合而非留出泛化（部分演示也可能被策略目标看到），且只有 60% 的训练更新用于策略目标，因此策略得分低于策略专用的 OPEN-WAM。

