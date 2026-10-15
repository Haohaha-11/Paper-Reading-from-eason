[← 返回 README](../README.md)

## C. LIBERO-Long-CF: Counterfactual Dynamics Data

LIBERO demonstrations contain task-directed behavior and cover only a small fraction of the local state–action–outcome distribution. From each visited state, they provide essentially one successful action continuation. This is well suited to imitation learning but provides limited supervision for inverse and forward dynamics, which must model how alternative actions correspond to alternative physical outcomes.



> 💡 **动机与局限**: 指出 LIBERO 演示数据仅覆盖成功路径，缺乏对“替代动作导致不同物理结果”的监督。这解释了为何需要构建反事实数据集以支持动力学模型训练，而非仅用于模仿学习。

Table 9. Scale of LIBERO-Long-CF and the source LIBERO-Long demonstration corpus.
<table><tr><td>Quantity</td><td>Demonstrations</td><td>LIBERO-Long-CF</td></tr><tr><td>Tasks</td><td>10</td><td>10</td></tr><tr><td>Stored sequences</td><td>500</td><td>32,000</td></tr><tr><td>Sequences / task</td><td>50</td><td>3,200</td></tr><tr><td>Controls / sequence</td><td>276.2 mean</td><td>128</td></tr><tr><td>Total controls</td><td>138,090</td><td>4,096,000</td></tr><tr><td>Duration</td><td>1.92 h</td><td>56.9 h</td></tr></table>



> 💡 **Table 9 解读**: 对比显示新数据集序列数增加 64 倍（500→32k），总控制步数增加近 30 倍。关键差异在于每条序列的控制长度从平均 276.2 缩短为固定 128，表明采用了更短的反事实分支片段而非完整轨迹。

We construct LIBERO-Long-CF by branching from taskrelevant states in the ten LIBERO-Long tasks and executing modified future action sequences. The dataset contains 32,000 counterfactual segments, balanced across tasks. Each segment contains 128 future controls and 129 synchronized two-view observations. The original tasks, assets, and simulator physics are preserved.



> 💡 **构建方法**: 通过从十个 LIBERO-Long 任务的 task-relevant 状态分支并执行修改后的未来动作序列生成数据。每个片段含 128 个未来控制与 129 个同步双视角观测，原始任务、资产与模拟器物理均保留。推断：这让反事实片段与源演示共享同一物理分布，便于做受控对比。

