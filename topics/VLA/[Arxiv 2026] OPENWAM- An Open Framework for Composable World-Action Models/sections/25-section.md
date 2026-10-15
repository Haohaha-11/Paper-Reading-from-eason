[← 返回 README](../README.md)

## E. External Dynamics Comparison

We compare OPENWAM with UVA [44], the closest released LIBERO system in our related work that exposes policy, inverse-dynamics, and forward-dynamics objectives within one model. Table 14 covers two settings: independently trained local-context dynamics components and a single multi-objective checkpoint trained jointly across policy, IDM, and FDM objectives.



> 💡 **对比对象与设置**: UVA 是 LIBERO 中最接近的基线，因其单模型集成策略、IDM 和 FDM。实验涵盖两种配置：独立训练的局部上下文动态组件，以及多目标联合训练的单检查点。

For FDM, we report RGB prediction error in a common agent-view image space. For IDM, the two systems use different action parameterizations, so we compare the resulting end-effector trajectory error after executing the predicted actions from the same simulator state.



> 💡 **评估指标对齐**: FDM 在统一 Agent 视角图像空间比较 RGB 预测误差。因 IDM 动作参数化不同，改用执行预测动作后的末端执行器轨迹误差作为公平对比标准。

