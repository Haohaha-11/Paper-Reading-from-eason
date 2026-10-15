[← 返回 README](../README.md)

## E.1. Independent Dynamics Components

The LC-IDM and LC-FDM specialists are trained either on counterfactual transitions only or on a 60/40 mixture of counterfactual and demonstration transitions. CF-only specialists are strongest on the held-out counterfactual distribution, while mixed supervision substantially improves fidelity on real demonstrations. Both remain well ahead of UVA on the counterfactual dynamics metrics.



> 💡 **训练策略权衡**: LC-IDM 和 LC-FDM 专家有两条训练路线：只用反事实转移，或按 60/40 混合反事实与演示转移。结果是 CF-only 在 held-out 反事实分布上最强，而混合监督显著提升对真实演示的保真度；两种配置在反事实动力学指标上仍都明显优于 UVA。

