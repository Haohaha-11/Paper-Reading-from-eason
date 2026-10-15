[← 返回 README](../README.md)

## 1. Introduction

Spatial reasoning is a foundational capability for Large Vision-Language Models (LVLMs), underpinning applications from autonomous driving and embodied navigation to robotic manipulation [11, 15, 37]. While LVLMs achieve strong performance on many visual tasks [2, 43, 70], they still fail on complex spatial problems that involve viewpoint transformation, cross-view correspondence, and reasoning about unobserved regions [25, 68, 69, 72]. These tasks go beyond passively extracting relations from the given observations, as they require the model to reason about the three-dimensional scene from perspectives that the input images do not directly provide.



> 💡 **问题动机**: 指出 LVLM 在需要视角变换、跨视图对应及推理未观测区域的复杂空间任务上表现不佳。强调这类任务要求模型超越被动提取关系，主动从输入图像未直接提供的三维场景视角进行推理。

We argue that a core capability missing from current LVLMs is the ability to proactively determine which alternative viewpoints would reveal useful evidence and to construct what the scene looks like from those viewpoints mentally [54]. We term this joint capability Active Spatial Mentalization (ASM), comprising two complementary skills: view planning, which selects task-relevant viewpoints given the question and current observations, and view imagination, which mentally constructs the spatial layout expected from the selected viewpoints (Fig. 2(c)). Teaching ASM to LVLMs is dificult. Recent data-centric eforts have expanded spatial supervision through large-scale datasets built from 3D annotations and videos [26, 41, 71], but these datasets provide answer-only QA pairs rather than Chain-of-Thought (CoT) trajectories that show how to select informative viewpoints and imagine the corresponding observations (Fig. 2(a)). Current LVLMs are unreliable at bootstrapping this supervision themselves, as they often produce incorrect viewpoint plans and hallucinated scenes when asked to generate ASM trajectories from scratch.



> 💡 **核心概念 ASM**: 提出“主动空间心理化”（ASM）概念，包含视角规划和视角想象两个互补技能。指出现有数据集缺乏展示如何规划与想象的 CoT 轨迹，且 LVLM 自身难以可靠地生成这些轨迹作为监督信号。

![](../images/35134933ea2cb431a1fe7c3157ef9ad5d7a3f4a587af41f3fc33d017d566016b.jpg)  
Figure 1: SpatialASM improves LVLM spatial reasoning by eliciting active exploration and imagination. (a) The qualitative example shows that the baseline LVLM (red) fails to associate objects across views, whereas our model (green) uses the internal viewpoint-planning skill and reasons over the imagined observation to reach the correct answer. (b) Quantitative experiments demonstrate that SpatialASM delivers consistent performance gains on seven benchmarks.



> 💡 **Figure 1 解读**: 图示对比了基线模型因无法关联跨视图对象而失败，而 SpatialASM 通过内部视角规划和想象观察成功推理。定量结果展示了该方法在七个基准测试中的一致性性能提升。

An alternative is to attach an external world model or spatial tool at inference time to generate supplementary observations [10, 73, 80] (Fig. 2(b)). While this provides useful visual evidence, it ties spatial reasoning to an external simulator at inference time, even when the LVLM learns when to invoke it [74, 84]. Tool distillation [38, 83] removes this dependence, but targets perceptual tools such as depth and cognitive maps rather than viewpoint planning and view imagination. Most such methods also adopt an egocentric interface that reaches a target viewpoint through a sequence of camera-centric actions, so each query requires a multi-step rollout [10, 73]. Closer to our setting, World2VLM [78] uses a world model at training time, but it supervises egocentric view association without producing any reasoning trajectory.



> 💡 **现有方案局限**: 推理时外接世界模型或空间工具虽能补充观测，但把空间推理绑在外部模拟器上，即使模型学会何时调用；工具蒸馏去掉了这一依赖，蒸馏对象却是深度、认知地图等感知工具，而非视角规划与视角想象。多数这类方法还用自我中心接口，需相机为中心的多步 rollout 才能到达目标视角；World2VLM 只在训练时用世界模型，且不产生推理轨迹。

We introduce SpatialASM, a framework that equips LVLMs with ASM capabilities by using an allocentric novel-view generator (NVG) as a training-time spatial simulation engine rather than an inference-time tool (Fig. 2(c)). Given a spatial QA sample, the NVG renders views from question-conditioned viewpoints planned by the LVLM, grounding hypothetical perspectives in concrete visual evidence. SpatialASM transforms these into two complementary forms of supervision: explicit ASM reasoning trajectories for supervised fine-tuning (SFT) and automatically verifiable perspective-taking tasks for reinforcement learning with GRPO [53]. The rendered views serve only as temporary scafolding: in the final SFT data, they are replaced by text captions that describe the imagined observations, so the LVLM learns to construct spatial layouts internally from the original input images alone (Fig. 1(a)).



> 💡 **方法概述**: SpatialASM 用 allocentric（非自我中心）新视图生成器 NVG 作训练时模拟引擎，按 LVLM 规划的问题条件视角渲染视图，把假设视角落到具体视觉证据上。渲染视图只作临时脚手架：最终 SFT 数据中它们被替换为描述想象观测的文本，迫使模型仅凭原始输入图像内在地构造空间布局。

The design of SpatialASM is guided by two key mechanisms (Fig. 3). The allocentric viewpoint formulation specifies a target view by its location and direction in a single step, avoiding the multi-step rollouts of egocentric interfaces. A Geometry Perturbation (GeoP) metric provides a model-based proxy for the geometric consistency of rendered views; together with an answer-consistency check, it admits only samples that pass pre-defined thresholds into the training data.



> 💡 **关键机制**: allocentric 视角表示用位置和方向一步指定目标视图，避免自我中心接口的多步 rollout。GeoP（Geometry Perturbation）是渲染视图几何一致性的 model-based proxy，与答案一致性检查一起，只放行通过预设阈值的样本进入训练数据。

Across four backbones from the Qwen3-VL and Qwen3.5 series [3, 48], SpatialASM improves average accuracy over seven spatial benchmarks (Tab. 1), with average gains ranging from +1.2 to +3.5 points (Fig. 1(b)). The largest per-benchmark gains appear on tasks that demand spatial mental modeling and viewpoint transformation, reaching +8.7 on Spatial457-6d and +5.2 on MindCube. General-VQA performance remains within 0.6 points of the baselines across all four backbones.



> 💡 **实验结果**: 在 Qwen3-VL/Qwen3.5 四个骨架上，SpatialASM 于七个空间基准平均提升 +1.2 至 +3.5 分；单基准最大增益出现在需要空间心理建模与视角变换的任务上，为 Spatial457-6d 的 +8.7 和 MindCube 的 +5.2。General-VQA 与基线相差 0.6 分以内；推断：这提示通用能力未被明显牺牲，但原文未给出该差距的方向与显著性。

Our contributions are threefold. 1) We formulate Active Spatial Mentalization as the joint capability of proactively planning task-relevant viewpoints and imagining corresponding observations, and argue that the absence of ASM is a key bottleneck of spatial reasoning in current LVLMs. 2) We introduce SpatialASM, a scalable framework built on an allocentric novel-view generator and GeoP-based filtering that converts existing spatial QA data into explicit ASM trajectories for SFT and perspective-taking tasks for RL. 3) SpatialASM improves average spatial accuracy on four LVLM backbones over seven benchmarks while keeping general-VQA performance within 0.6 points.



> 💡 **贡献总结**: 三项贡献为：将 ASM 定义为主动规划任务相关视角与想象对应观测的联合能力，并称其缺失是当前 LVLM 空间推理的关键瓶颈；提出由 allocentric 新视图生成器与 GeoP 过滤构成的 SpatialASM，把已有空间 QA 数据转成 SFT 用的显式 ASM 轨迹与 RL 用的视角转换任务；在四个骨干、七个基准上验证平均空间准确率提升，同时通用 VQA 差距在 0.6 分以内。

![](../images/be739ff00991c141819068140b7ec44823f1f7d2c725247d438ba43df3b764e5.jpg)  
Figure 2: (a) Prior data-centric approaches mostly provide answer-only supervision. (b) Reasoning with external calls of world models adds unnecessary intermediate observations and extra inference time. (c) SpatialASM enables LVLMs to intrinsically produce plan-imagine-reason CoT.



> 💡 **Figure 2 解读**: 对比三种范式：(a) 先前数据驱动方法多只给答案监督；(b) 推理时调用外部世界模型会引入不必要的中间观测和额外推理时间；(c) SpatialASM 让 LVLM 内在地产生“计划-想象-推理”的 CoT，无需外部干预。

