[← 返回 README](../README.md)

## C.3. Evaluation Details

Evaluation prompt. In Tab. 1, we evaluate the LVLMs on two major task categories: spatial reasoning and general-VQA (Gnr.-VQA). For spatial reasoning, we apply the same system prompt as training (in Appendix C.1) to let LVLMs reason before giving the answer. For general-VQA benchmarks, we apply another system prompt to enable direct responses of final answers from LVLMs: Directly output your answer without any reasoning. The correct option letter is:.



> 💡 **评估提示策略**: 空间推理任务复用训练时的系统提示（附录 C.1），让模型先推理再作答；通用 VQA 则改用另一条系统提示，直接输出最终答案与选项字母，不要求推理过程。推断：按任务匹配是否需要思维链是这种差异化设置的可能动机，原文只陈述做法，未说明理由。

Representative spatial models. Four popular spatial LVLMs of previous studies are also evaluated on the benchmarks. SpaceR-7B [47] and VST-RL-7B [71] are trained upon Qwen2.5-VL-7B [58]. Their oficial repository provides system prompts. For the SR evaluation, we follow them exactly to guide the models to produce structured reasoning and final answers. Holi-Spatial [26] and SenseNova-SI-1.1-Qwen3-VL-8B [9] are trained upon Qwen3-VL-8B [3], similar to out SpatialASM-8B. Since the two models do not provide oficial system prompt, we follow our system prompt during evaluation.



> 💡 **基线提示对齐**: SpaceR-7B、VST-RL-7B 基于 Qwen2.5-VL-7B，SR 评估严格沿用其官方仓库提供的结构化推理提示；Holi-Spatial 与 SenseNova-SI-1.1-Qwen3-VL-8B 基于 Qwen3-VL-8B，因无官方提示，改用本文的系统提示。推断：这让四者的输入条件在形式上统一，但本文提示未必贴合这两个模型的训练分布，原文未讨论这一点。

