[← 返回 README](../README.md)

## REPRODUCIBILITY STATEMENT

All experiments use publicly available base models and data. Section 4 specifies the model family and objective; Appendices A and D give the full loss, architecture and baseline definitions, FLOPs accounting, training hyper-parameters and evaluation protocol. Training and evaluation code, configuration files, and checkpoints will be released upon publication.



> 💡 **复现性声明拆解**: 原文把可复现性拆成三层可核查的承诺，逐条对应如下——
> - **资源层**: “All experiments use publicly available base models and data”，即不依赖私有基座权重或未公开语料；但原文未说明数据规模、算力预算等复现成本，若关心实测可行性需另行查正文/附录（此项超出本段证据，属推断与待证）。
> - **文档层（来源分两处，需分开记）**: Section 4 负责 “the model family and objective”；附录 A 与 D 负责 “the full loss, architecture and baseline definitions, FLOPs accounting, training hyper-parameters and evaluation protocol”。初稿把附录职责概括为“损失函数、架构、FLOPs 计算及超参细节”，**漏掉了 baseline definitions 与 evaluation protocol**，且未区分 Section 4 与附录各自承担的部分，此处已补正。
> - **代码层（注意时态）**: 原文为 “Training and evaluation code, configuration files, and checkpoints **will be released upon publication**”，是将来时承诺，不是当前可获取状态。初稿写“发表后开源”方向正确，但不应读作已可用；在该声明生效时点，完整复现只能依赖正文与附录的文字描述。
> 
> 💡 **Q&A 批注记录**:
> - Q: 这条声明是否意味着 checkpoint 现在就能下载？
> - A: 不能。原文用 “will be released upon publication”，属未兑现承诺，证据仅到“承诺”层级；是否实际发布需以论文页面/仓库为准（外部核验不在本段证据范围内）。
> - Q: 想复现需要读哪些位置？
> - A: 模型族与目标看 Section 4；损失、架构、基线定义、FLOPs 口径、训练超参与评估协议看附录 A、D；实现细节待代码发布。

