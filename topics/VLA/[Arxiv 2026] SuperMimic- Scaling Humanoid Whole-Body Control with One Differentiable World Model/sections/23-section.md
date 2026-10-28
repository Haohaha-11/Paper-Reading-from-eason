[← 返回 README](../README.md)

## G.2 Step test

Protocol. From standing, the policy receives a fixed command for 5 s; each command is tested in four trials. The relative steady-state error is the norm of the mean velocity error over the last 2 s, divided by the norm of the command, with the root velocity measured in the heading frame. The per-command value is the median over the trials that did not fall. The 672 step responses form a 15 × 15 grid on each of three planes, spanning the uniform command range above, with the standing command left out. In Figure 7, errors above 0.9 are drawn at 0.9, and a policy that fell in all four trials is drawn at the cap.



> 💡 **协议与误差定义**: 策略从站立开始接收 5 s 固定指令，每条指令测 4 次；相对稳态误差取最后 2 s 平均速度误差的模除以指令模，末端速度在航向系（heading frame）下测量，并按未摔倒的试次取中位数。672 个 step 响应来自三个平面各 15×15 的指令网格（去掉站立指令），Figure 7 把大于 0.9 的误差与四次全摔的策略都画在 0.9 上限。

Results. The RL encoder has the lower error on 650 of the 672 step responses, and the distillation encoder on 15. Their median relative errors are 0.16 and 0.49. Distillation falls in every trial on seven commands, all combining translation with a turn of at least 2.8 rad/s, where the motion library has almost no data. Each policy has one training seed.

> 💡 **性能对比**: 672 个 step 响应中 RL encoder 在 650 个上误差更低、distillation 仅 15 个，中位相对误差为 0.16 对 0.49。蒸馏在 7 条指令上四次试验全摔，作者归因于运动库几乎不含“平移叠加 ≥2.8 rad/s 转向”的数据；每个策略只训练一个 seed，两组差异未做统计检验。

