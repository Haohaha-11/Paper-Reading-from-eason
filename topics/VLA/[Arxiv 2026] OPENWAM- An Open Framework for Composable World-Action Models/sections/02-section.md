[← 返回 README](../README.md)

## 2. Related Work

## 2.1. Visuomotor Policies and Predictive Control

Behavior cloning provides the standard supervised formulation for visuomotor policy learning, mapping observations and task context directly to demonstrated actions. Action chunking and generative action prediction have become effective control primitives [13, 101], while large-scale vision– language–action models extend this paradigm through language conditioning, pretraining, and cross-task or crossembodiment learning [6–8, 33, 36, 58, 70].



> 💡 **行为克隆范式**: 指出视觉运动策略学习以行为克隆为标准监督式形式化，把观测与任务上下文直接映射到演示动作；近期由 Action Chunking 与生成式动作预测、以及 VLA 大模型的语言条件与跨任务/跨本体学习进一步扩展。

Predictive control introduces an additional interface by explicitly modeling future observations or latent states. Early visual-foresight methods combined action-conditioned video prediction with model-predictive control for robotic manipulation [16, 17]. Subsequent work learns latent or visual dynamics to support policy learning, planning, and imagined rollouts [11, 24, 25, 80, 82, 107]. Predictive rollouts have also been used to evaluate policy behavior [22, 47].



> 💡 **预测控制接口**: 预测控制的共同点是显式建模未来观测或潜在状态：早期做法把动作条件视频预测与 MPC 结合，后续工作改为学习潜在或视觉动力学，用于策略学习、规划与想象 rollout，也被用来评估策略行为。

Action-conditioned world models provide a complementary perspective by predicting how an environment evolves under supplied or candidate actions. Recent methods use such predictions for simulation, candidate evaluation, proposal refinement, and self-verifying planning [31, 61, 95, 106]. Together, these lines show that predictive models can support action generation, simulation, planning, and evaluation. However, action-conditioned prediction and video–action policy generation are often developed as separate components, leaving open how explicit prediction– action interfaces affect component reuse across tasks.



> 💡 **现有方法缺口**: 这一条明确本文的切入点：动作条件预测与视频–动作策略生成通常被分开开发，因此“显式预测–动作接口如何影响跨任务的组件复用”仍是开放问题。

## 2.2. Video Foundations and World–Action Architectures

Recent world–action models connect predictive video representations with robot control through several forms of video–action coupling. Joint generation models predict future visual content and actions together [1, 21, 66, 90], while causal video-first formulations generate or represent visual futures before action prediction [43, 71]. Other architectures use specialized video and action pathways with explicit cross-modal exchange [87, 96], or leverage egocentric and large-scale video priors for robot grounding and generalization [41, 62, 85, 105].



> 💡 **WAM 耦合形式**: 世界–动作模型连接视频预测表示与机器人控制的方式有几类：联合生成未来视觉与动作、因果式视频优先（先生成视觉未来再预测动作）、专用视频/动作双通路加显式跨模态交换，以及借助自我中心与大规模视频先验做机器人落地。

A closely related line studies the interface between predictive visual information and executable actions. Predicted visual states or representations can condition inverse dynamics [26, 46, 72], while other approaches translate generated futures through dense correspondence, pseudo-action labeling, intermediate denoising features, or latent representation alignment [34, 39, 55, 74]. These methods differ in where and how predictive visual information enters the action-generation pathway.



> 💡 **视觉-动作接口**: 这一支研究预测性视觉信息进入动作生成通路的位置：可条件化逆动力学，也可通过密集对应、伪动作标注、中间去噪特征或潜在表示对齐来把生成的未来转成动作。

Unified formulations instead expose multiple predictive and control modes within a shared system. UWM and UVA [44, 108] support combinations of policy, inversedynamics, forward-dynamics, and video prediction objectives, while related systems combine action generation with world prediction, simulation, planning, or evaluation [4, 10, 20, 38, 68].



> 💡 **统一系统架构**: UWM 与 UVA 这类统一方案在共享系统中同时暴露策略、逆动力学、前向动力学与视频预测等目标；其他系统则把动作生成与世界预测、仿真、规划或评估组合在一起。

Recent work further isolates particular choices in the WAM design space. Predictive computation can be reduced, sparsified, reordered, or scheduled asynchronously [28, 29, 48, 59, 69, 77, 89, 94]. Alternative work studies the representation through which world prediction interacts with control [35, 53, 63, 67, 76, 92, 99], or augments world– action learning with structural, geometric, and tactile supervision [18, 56, 104]. Temporal abstraction, data and model scaling, humanoid control, and long-horizon memory provide additional design axes [45, 52, 100, 103]; recent surveys summarize the rapidly expanding landscape [54, 78].



> 💡 **设计空间探索**: 近期工作把 WAM 的设计选择逐项拆开：预测计算可被削减、稀疏化、重排或异步调度；也有工作研究世界预测与控制交互所用的表示，或用结构化、几何、触觉监督增强世界–动作学习，并沿时间抽象、数据与模型规模、人形控制和长时记忆等轴扩展。

Concurrent work, also named OpenWAM [79], develops a modular framework for systematic WAM pretraining and design studies. Our focus is complementary: OPENWAM fixes a common causal robot-video foundation and training interface while explicitly varying generation order and future cross-modal visibility, enabling joint, video-then-action, action-then-video, and decoupled interaction to be compared under a shared evaluation protocol. We further study the reuse of independently trained inverse- and forwarddynamics components through explicit trajectory interfaces.



> 💡 **与并发工作的分工**: 并发工作 OpenWAM [79] 做的是模块化的 WAM 预训练与设计研究；本文固定统一的因果机器人视频基础与训练接口，显式改变生成顺序与未来跨模态可见性，在共享评测协议下比较联合、视频先动作、动作先视频与解耦四种交互，并研究通过显式轨迹接口复用独立训练的逆/前向动力学组件。

## 2.3. Dynamics Learning Beyond Successful Demonstrations

Successful demonstrations provide valid transitions, but they cover only the action–outcome relationships selected by the demonstrating policy. A complementary analysis shows that observational demonstrations alone may not identify the consequences of unsupported actions [93]. Classical imitation-learning methods address the resulting distribution shift by collecting supervision on learner-visited states, perturbing expert trajectories, or soliciting corrective interventions [27, 32, 40, 57, 65]. These approaches broaden state coverage beyond clean expert rollouts and improve robustness to execution errors.



> 💡 **演示数据局限**: 成功演示只覆盖演示策略所选动作–结果关系，观测型演示可能无法识别未被执行动作的后果；经典模仿学习通过在学习者访问过的状态上补监督、扰动专家轨迹或请求纠正性干预来缓解分布偏移。

More recent work expands supervision through synthetic failures, counterfactual augmentation, autonomous experience, and other non-successful interactions [2, 4, 15, 42, 60, 88, 91, 98]. Such data have been used for recovery, policy improvement, failure understanding, and predictive modeling. Particularly related to our setting, Fail2Progress [30] targets data collection toward observed failures to improve skill-effect models, while UniPi [14] decouples video planning from inverse dynamics and allows the inverse model to learn from a separate, potentially suboptimal dataset.



> 💡 **非成功交互价值**: 近期工作用合成失败、反事实增强、自主经验等非成功交互扩监督，用于恢复、策略改进、失败理解与预测建模；与本文最相关的是 Fail2Progress [30]（针对已观测失败定向采集数据以改进技能效果模型）和 UniPi [14]（解耦视频规划与逆动力学，让逆模型可从单独的、可能次优的数据集学习）。

OPENWAM focuses on a different question: how transition coverage and conditioning scope affect the reuse of independently trained dynamics components after visual task adaptation. We branch alternative action continuations from task-relevant simulator states and use the resulting state– action–outcome transitions as local dynamics supervision.



> 💡 **本文核心问题**: OPENWAM 关注的是另一个问题：视觉任务适应之后，转移覆盖度与条件化范围如何影响独立训练动力学组件的复用；做法是从任务相关的模拟器状态分支出替代动作续写，把产生的状态–动作–结果转移当作局部动力学监督。

