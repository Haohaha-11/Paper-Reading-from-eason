[← 返回 README](../README.md)

## 5. Discussion and Conclusion

We presented OPENWAM, an open framework and pretrained foundation spanning video prediction, robot control, and local dynamics modeling. Its shared causal backbone supports joint, sequential, and decoupled video–action generation within a common training and evaluation stack. Strong performance across four LIBERO suites and real-world bimanual tasks demonstrates the framework’s control capabilities, while the initialization ablation establishes the substantial benefit of robot-video pretraining. OPENWAM thus supports multiple effective modeling configurations rather than a single specialized policy.



> 💡 **核心贡献**: OPENWAM 是覆盖视频预测、机器人控制与局部动力学建模的开放框架和预训练基础，其共享因果骨干支持多种视频–动作生成配置。四个 LIBERO 套件与真实双臂任务的强劲表现，加上初始化消融，显示 robot-video 预训练带来实质收益。

Beyond native control, the framework supports independently trained inverse and forward dynamics components. In the transfer study, counterfactual-only and mixed supervision both substantially improve the reuse of local-context IDMs after visual task adaptation, while retaining strong source-task performance. Counterfactual FDM supervision



> 💡 **迁移机制**: 框架支持独立训练的逆/正向动力学组件。迁移实验显示，仅反事实监督或混合监督都能在视觉任务适配后显著提升局部 IDM 的复用率，同时保持较强的源任务性能。

Table 5. Frozen inverse dynamics transfer to four LIBERO-90 targets. Target rows use task-tuned video predictors while source-trained IDMs remain frozen. Mean is averaged over the four target tasks.
<table><tr><td>Video / policy</td><td>Action component (frozen)</td><td>64 Composition</td><td>74 Retarget</td><td>21 Object/grasp</td><td>45 Scene shift</td><td>Transfer mean</td></tr><tr><td></td><td>Task-tuned VTA (finetuned policy reference)</td><td>94%</td><td>86%</td><td>92%</td><td>100%</td><td>93.0%</td></tr><tr><td>Task-tuned video</td><td>Full-context IDM, demo-only</td><td>88%</td><td>90%</td><td>0%</td><td>0%</td><td>44.5%</td></tr><tr><td>Task-tuned video</td><td>Full-context IDM, mixed</td><td>54%</td><td>92%</td><td>4%</td><td>38%</td><td>47.0%</td></tr><tr><td>Task-tuned video</td><td>Local-context IDM, demo-only</td><td>36%</td><td>50%</td><td>0%</td><td>0%</td><td>21.5%</td></tr><tr><td>Task-tuned video</td><td>Local-context IDM, CF-only</td><td>80%</td><td>92%</td><td>90%</td><td>76%</td><td>84.5%</td></tr><tr><td>Task-tuned video</td><td>Local-context IDM, mixed</td><td>74%</td><td>86%</td><td>88%</td><td>88%</td><td>84.0%</td></tr><tr><td colspan="2">Original VTA (unadapted policy reference)</td><td>42%</td><td>0%</td><td>0%</td><td>0%</td><td>10.5%</td></tr></table>



> 💡 **Table 5 解读**: 冻结 IDM 在四个 LIBERO-90 目标上的迁移结果。Local-context IDM 用 CF-only/Mixed 监督平均成功率 84.5%/84.0%，接近全微调策略参考 93.0%，而 Local-context demo-only 仅 21.5%、Full-context demo-only 44.5%。

Table 6. Source-task composition on LIBERO-Long, all action components evaluated with LIBERO-Long video predictor.
<table><tr><td>Action component</td><td>Supervision</td><td>Success (%)</td></tr><tr><td>Full-context IDM</td><td>Demo-only (Native VTA)</td><td>97.8</td></tr><tr><td>Full-context IDM</td><td>Mixed</td><td>92.2</td></tr><tr><td>Local-context IDM</td><td>Demo-only</td><td>25.8</td></tr><tr><td>Local-context IDM</td><td>CF-only</td><td>90.6</td></tr><tr><td>Local-context IDM</td><td>Mixed</td><td>94.4</td></tr></table>



> 💡 **Table 6 解读**: LIBERO-Long 源任务上，所有动作组件都用 LIBERO-Long 视频预测器评估。Local-context IDM 在 demo-only 下仅 25.8%，引入 CF-only/Mixed 监督后升至 90.6%/94.4%，表明反事实监督对局部上下文模型很关键；Full-context IDM 则是 demo-only 最高（97.8%）。

Table 7. Local-context FDM prediction on counterfactual transitions. MSE values are reported in units of $1 0 ^ { - 3 }$ . Outcome accuracy measures identification of the matching realized future by RGB MSE among K same-state outcomes.
<table><tr><td>Supervision</td><td>RGB MSE↓</td><td>Acc. K=2 ↑</td><td>Acc. K=16 ↑</td></tr><tr><td>Demo-only</td><td>14.35</td><td>68.3</td><td>21.1</td></tr><tr><td>CF-only</td><td>9.40</td><td>93.6</td><td>71.3</td></tr><tr><td>Mixed</td><td>9.62</td><td>91.8</td><td>67.7</td></tr></table>



> 💡 **Table 7 解读**: 局部 FDM 预测精度。CF-only 监督将 RGB MSE 降至最低（9.40e-3），并在 K=16 时实现 71.3% 的结果识别准确率，显著优于仅用演示数据的基线。

also improves both visual prediction accuracy and identification of action-dependent outcomes.



> 💡 **CF 监督增益**: 这句与上文“Counterfactual FDM supervision”连读：反事实 FDM 监督既提升视觉预测精度，也提升对动作依赖结果的识别能力，对应 Table 7 的 MSE 与 outcome accuracy 两项指标。

Several questions remain open. Composability is established primarily in simulation; demonstrating comparable reuse on real robots will require more data-efficient physical dynamics supervision. Our target-task experiments adapt the video predictor and therefore do not establish zero-shot transfer of the complete video–action model. Appendix E shows that policy, IDM, and FDM objectives can be trained jointly in one checkpoint, but we have not yet shown that a single multi-objective model can match the strongest specialized OPENWAM variants across all modes. The present FDM study also focuses on local, short-horizon prediction, leaving long-horizon planning and physical deployment as natural extensions.



> 💡 **局限与展望**: 当前可组合性主要在仿真中验证；零样本完整模型迁移尚未确立；多目标联合训练虽可行但未超越最强专用变体；长程规划及物理部署仍是未来方向。

OPENWAM brings these capabilities into one open foundation: strong policies, configurable prediction–action interaction, and independently reusable dynamics. It provides both capable world–action models and a common basis for building, comparing, and composing the next generation.



> 💡 **总结**: OPENWAM 统一了策略生成、预测-动作交互配置及独立可复用的动力学模型，为构建下一代世界-动作模型提供了开放的基础设施。

