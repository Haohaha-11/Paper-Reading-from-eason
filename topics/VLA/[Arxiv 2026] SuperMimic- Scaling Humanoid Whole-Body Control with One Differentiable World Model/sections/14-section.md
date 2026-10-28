[← 返回 README](../README.md)

## D.1 Algorithm

Algorithm A.1 summarizes the SuperMimic training loop of §3.1; its three steps correspond one-toone to the panels of Figure 2.



> 💡 **算法概览**: 本段把附录的 Algorithm A.1 与正文 §3.1、Figure 2 对齐，说明伪代码的三个步骤依次对应主图三个面板。本段未给出具体步骤内容，读 Algorithm A.1 时需与 Figure 2 并排对照。

Return-based reset sampling. Simulator episodes reset to points of the motion library, favoring those where the policy currently earns low return. The motion timeline is divided into 1 s bins, and each bin b keeps a running estimate $G _ { b }$ of the discounted return from it, updated on the policy’s own rollouts. Resets mix three distributions: 50% uniform over bins, 40% from softmax<sub>b</sub> $( - 2 5 G _ { b } / G _ { \mathrm { m a x } } )$ , where $G _ { \mathrm { m a x } }$ is the largest attainable discounted return, and 10% in proportion to each bin’s past softmax weight, so a hard motion keeps some practice after it is learned.



> 💡 **重置采样机制**: 环境重置点按 50% 均匀、40% 偏向低 $G_b$ 的 softmax $(-25G_b/G_{\mathrm{max}})$、10% 按历史 softmax 权重三条分布混合；$G_b$ 是每个 1 s bin 在策略自身 rollout 上更新的折扣回报估计，练习因此集中在当前回报差的片段。10% 这一支的原文理由是让已学会的高难度动作仍保留练习，原文未涉及防遗忘等其他解释。

