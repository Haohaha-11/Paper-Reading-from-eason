[← 返回 README](../README.md)

## 6 Limitations and Future Work

• Multi-interface training is understudied. We train the velocity encoder concurrently with the motion tracker, but the velocity-tracking reward updates only the velocity encoder and not the shared decoder (Appendix B). Concurrent training is therefore the same as training the velocity encoder after the motion tracker, and might not be necessary. Whether concurrent training helps once the velocity-tracking reward also updates the decoder is untested.



> 💡 **训练机制局限**: 速度编码器与运动跟踪器并行训练，但速度跟踪奖励只更新速度编码器、不更新共享解码器（附录 B），因此并行训练等价于在跟踪器之后再单独训练速度编码器，作者据此怀疑并行并非必要。原文强调，属于未验证之列的只是“奖励同时更新解码器时并行训练是否有益”这一情形。

• Simulator-only dynamics. The world model learns only from simulator transitions. Real-robot data could refine it, and its differentiability could support system identification.



> 💡 **数据源单一**: 世界模型仅基于仿真环境数据学习，缺乏真实机器人数据的校准。作者认为引入真机数据可优化模型，并利用其可微分特性进行系统辨识，但这部分工作尚未开展。

• No terrain or object interaction. SuperMimic trains only on flat ground. Extending it to terrain and object interaction is ongoing work.



> 💡 **场景覆盖缺失**: SuperMimic 当前仅在平地条件下训练，不支持复杂地形或物体交互。将能力扩展至这些非结构化场景是作者正在进行的后续工作方向。

