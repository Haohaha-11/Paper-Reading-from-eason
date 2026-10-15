[← 返回 README](../README.md)

## B.3. Training Compute

Causal robot-video pretraining is performed on 32 NVIDIA B200 GPUs for 14 days. All downstream OPENWAM variants in the main experiments start from the resulting checkpoint unless otherwise specified.



> 💡 **训练算力**: 因果机器人视频预训练用 32 块 NVIDIA B200 GPU 连续跑 14 天，得到一个 checkpoint；主实验中的 OPENWAM 各变体除非另有说明都从该 checkpoint 初始化，而不是各自从零训练。推断：这样各变体的差异来自下游部分，但原文没给出 FLOPs、能耗或下游微调的算力数据。

