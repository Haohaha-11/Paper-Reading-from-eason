[← 返回 README](../README.md)

## References

[1] Z. Luo, Y. Yuan, T. Wang, C. Li, F. Castaneda, S. Chen, Z.-A. Cao, J. Li, D. Minor, Q. Ben,˜ J. Park, D. Sami, Z. Wang, X. Da, R. Ding, C. Hogg, L. Song, E. Lim, E. Jeong, T. He, H. Xue, W. Xiao, S. Yuen, J. Kautz, Y. Chang, U. Iqbal, L. Fan, and Y. Zhu. SONIC: Supersizing motion tracking for natural humanoid whole-body control. Science Robotics, 11(117):eaed4592, 2026. doi:10.1126/scirobotics.aed4592.



> 💡 **SONIC 引用**: 这是 Science Robotics 2026 年的论文，标题暗示其核心贡献是“超大规模动作追踪”，用于自然的人形全身控制。如果本文方法涉及类似的大规模数据或全身控制挑战，此文献是关键对比基准。

[2] X. B. Peng, P. Abbeel, S. Levine, and M. van de Panne. DeepMimic: Example-guided deep reinforcement learning of physics-based character skills. ACM Transactions on Graphics, 37 (4):143:1–143:14, 2018. doi:10.1145/3197517.3201311.



> 💡 **DeepMimic 经典**: Peng 等人 2018 年的 DeepMimic 是物理角色控制的里程碑工作，开创了基于示例的深度强化学习方法。它是后续许多模仿学习方法的理论或实践起点。

[3] Q. Liao, T. E. Truong, X. Huang, Y. Gao, G. Tevet, K. Sreenath, and C. K. Liu. BeyondMimic: From motion tracking to versatile humanoid control via guided diffusion. Science Robotics, 11(117):eadx8924, 2026. doi:10.1126/scirobotics.adx8924.



> 💡 **BeyondMimic 进展**: 同为 Science Robotics 2026 的文章，它用引导扩散把动作追踪扩展为通用人形控制，因此是生成式模型进入人形控制的代表性引用。

[4] J. Schulman, F. Wolski, P. Dhariwal, A. Radford, and O. Klimov. Proximal policy optimization algorithms. arXiv preprint arXiv:1707.06347, 2017. URL https://arxiv.org/abs/1707 .06347.



> 💡 **PPO 算法基础**: Schulman 等人的 PPO 是当前最主流的强化学习策略优化算法之一。如果本文使用 RL 训练控制器，很可能基于此或其变体，需注意其对稳定性的贡献。

[5] J. Merel, L. Hasenclever, A. Galashov, A. Ahuja, V. Pham, G. Wayne, Y. W. Teh, and N. Heess. Neural probabilistic motor primitives for humanoid control. In International Conference on Learning Representations, 2019. URL https://openreview.net/forum?id=BJl6TjRc Y7.



> 💡 **神经概率原语**: Merel 等人提出的神经概率运动基元（NPMP）是一种将技能分解为可组合单元的方法。它常用于解决复杂任务的分层控制问题，值得参考其架构设计。

[6] H. Yao, Z. Song, B. Chen, and L. Liu. ControlVAE: Model-based learning of generative controllers for physics-based characters. ACM Transactions on Graphics (TOG), 41(6):1–16, 2022. doi:10.1145/3550454.3555434.



> 💡 **ControlVAE 模型**: Yao 等人引入 VAE 进行基于模型的控制器学习。这表明作者可能关注潜空间表示对物理模拟效率或泛化能力的影响，属于生成式控制范畴。

[7] J. Won, D. Gopinath, and J. Hodgins. Physics-based character controllers using conditional VAEs. ACM Transactions on Graphics (TOG), 41(4):1–12, 2022. doi:10.1145/3528223.3530 067.



> 💡 **条件 VAE 控制**: Won 等人使用条件 VAE 构建物理角色控制器。与 [6] 类似，但强调“条件”输入，暗示通过外部信号（如目标姿态）来约束生成过程，提高可控性。

[8] Z. Luo, J. Cao, J. Merel, A. Winkler, J. Huang, K. Kitani, and W. Xu. Universal humanoid motion representations for physics-based control. In International Conference on Learning Representations, volume 2024, pages 56766–56782, 2024. URL https://proceedings.ic lr.cc/paper\_files/paper/2024/hash/fb19caf91f4966e426026685028cb73a-Abs tract-Conference.html.



> 💡 **通用运动表示**: Luo 等人在 ICLR 2024 提出通用的人形运动表示。这类工作旨在建立跨任务的统一特征空间，对于理解如何共享不同动作间的知识至关重要。

[9] C. Tessler, Y. Guo, O. Nabati, G. Chechik, and X. B. Peng. MaskedMimic: Unified physicsbased character control through masked motion inpainting. ACM Transactions On Graphics (TOG), 43(6):1–21, 2024. doi:10.1145/3687951.



> 💡 **MaskedMimic 机制**: Tessler 等人利用掩码运动修复实现统一的物理角色控制。这是一种自监督思路，通过遮挡部分信息迫使模型学习完整的运动上下文，增强鲁棒性。

[10] Z. Zhang, H. Lu, Y. Lian, Z. Chen, Y. Liu, C. Lin, H. Xue, Z. Zeng, Z. Qi, S. Zheng, Q. Luan, J. Wang, J. Xing, H. Wang, and L. Yi. Learning athletic humanoid tennis skills from imperfect human motion data. In Proceedings of the IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS), 2026. URL https://arxiv.org/abs/2603.12686.



> 💡 **不完美数据处理**: Zhang 等人在 IROS 2026 展示了如何从不完美的人类动作数据中学习网球技能。推断：本文若使用真实采集的动作数据，会遇到类似的噪声问题，可以参考它的处理方式。

[11] Z. Luo, J. Cao, A. Winkler, K. Kitani, and W. Xu. Perpetual humanoid control for real-time simulated avatars. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 10895–10904, 2023. URL https://openaccess.thecvf.com/content/IC CV2023/html/Luo\_Perpetual\_Humanoid\_Control\_for\_Real-time\_Simulated\_Avat ars\_ICCV\_2023\_paper.html



> 💡 **Perpetual 控制**: Luo 等人在 ICCV 2023 实现了实时模拟的永久人形控制。重点在于“实时”和“永久”，解决了长序列连续控制中的漂移或不稳定问题。

[12] L. Fussell, K. Bergamin, and D. Holden. SuperTrack: Motion tracking for physically simulated characters using supervised learning. ACM Transactions on Graphics (TOG), 40(6):1–13, 2021. doi:10.1145/3478513.3480527.



> 💡 **SuperTrack 监督学习**: Fussell 等人使用监督学习而非 RL 进行物理模拟角色的动作追踪。这提供了一种更直接、可能更高效的学习范式，适合特定轨迹复现任务。

[13] H. Yao, Z. Song, Y. Zhou, T. Ao, B. Chen, and L. Liu. MoConVQ: Unified physics-based motion control via scalable discrete representations. ACM Transactions on Graphics (TOG), 43(4):1–21, 2024. doi:10.1145/3658137.



> 💡 **离散表示 MoConVQ**: Yao 等人提出通过可扩展的离散表示（VQ-VAE 类）进行统一运动控制。离散化有助于压缩状态空间，提升长程规划的效率和稳定性。

[14] Y. Lei, Z. Luo, T. He, J. Cao, G. Shi, and K. Kitani. Scalable humanoid whole-body control via differentiable neural network dynamics. In ICLR 2025 Workshop on World Models: Understanding, Modelling and Scaling, 2025. URL https://openreview.net/pdf/b303199 0328dd52267a571b265c15328bf6d6120.pdf.



> 💡 **可微分动力学**: Lei 等人在 ICLR 2025 Workshop 探讨通过可微神经网络动力学实现可扩展的人形全身控制。可微仿真允许端到端梯度传播，加速训练收敛。

[15] D. Hafner, T. Lillicrap, J. Ba, and M. Norouzi. Dream to control: Learning behaviors by latent imagination. In International Conference on Learning Representations, 2020. URL https://openreview.net/forum?id=S1lOTC4tDS.



> 💡 **Dream to Control**: Hafner 等人的 Dreamer 系列早期作品，通过潜在想象学习行为。这是基于模型 RL（MBRL）的代表作，核心是用世界模型替代环境交互。

[16] D. Hafner, J. Pasukonis, J. Ba, and T. Lillicrap. Mastering diverse control tasks through world models. Nature, 640(8059):647–653, 2025. doi:10.1038/s41586-025-08744-2.



> 💡 **Nature MBRL 突破**: Hafner 等人 2025 年在 Nature 发表的工作，证明世界模型能掌握多样化控制任务。这标志着 MBRL 在复杂性和通用性上的重大进步，极具参考价值。

[17] N. Hansen, X. Wang, and H. Su. Temporal difference learning for model predictive control. In K. Chaudhuri, S. Jegelka, L. Song, C. Szepesvari, G. Niu, and S. Sabato, editors, Proceedings of the 39th International Conference on Machine Learning, volume 162 of Proceedings of Machine Learning Research, pages 8387–8406. PMLR, 17–23 Jul 2022. URL https://pr oceedings.mlr.press/v162/hansen22a.html.



> 💡 **TD-MPC 混合方法**: Hansen 等人结合时序差分学习和模型预测控制。这种混合策略试图平衡无模型 RL 的灵活性和基于模型 MPC 的精确性，适用于高维控制。

[18] P. Wu, A. Escontrela, D. Hafner, K. Goldberg, and P. Abbeel. DayDreamer: World models for physical robot learning. In Proceedings of The 6th Conference on Robot Learning, volume 205 of Proceedings of Machine Learning Research, pages 2226–2240. PMLR, 2023. URL https://proceedings.mlr.press/v205/wu23c.html.



> 💡 **DayDreamer 实机学习**: Wu 等人让世界模型直接在真实机器人上在线学习，而不是先在仿真中训练再迁移。它证明的是真机上的采样效率，并非 sim-to-real 迁移的案例。

[19] J. Xu, V. Makoviychuk, Y. Narang, F. Ramos, W. Matusik, A. Garg, and M. Macklin. Accelerated policy learning with parallel differentiable simulation. In International Conference on Learning Representations, 2022. URL https://openreview.net/forum?id=ZSKRQMvt tc.



> 💡 **并行可微仿真**: Xu 等人利用并行可微仿真加速策略学习。这主要解决计算瓶颈，使得大规模并行训练成为可能，显著缩短实验周期。

[20] Y. Song, S. Kim, and D. Scaramuzza. Learning quadruped locomotion using differentiable simulation. In Proceedings of The 8th Conference on Robot Learning, volume 270 of Proceedings of Machine Learning Research, pages 258–271. PMLR, 2025. URL https: //proceedings.mlr.press/v270/song25a.html.



> 💡 **四足的可微仿真**: Song 等人的工作收于第八届 CoRL（PMLR 第 270 卷），用可微仿真学习四足运动。推断：把可微物理接进策略优化的做法对人形控制同样可借鉴，但对象是四足，结论不能直接外推。

[21] C. Li, A. Krause, and M. Hutter. Robotic world model: A neural network simulator for robust policy optimization in robotics. arXiv preprint arXiv:2501.10100, 2025. URL https://ar xiv.org/abs/2501.10100. Presented at the NeurIPS 2025 Workshop on Embodied World Models for Decision Making.



> 💡 **Robotic World Model**: Li 等人提出用于鲁棒策略优化的神经网络模拟器。强调了“鲁棒性”，暗示该模型在处理不确定性或分布外数据时表现更佳。

[22] C. Li, A. Krause, and M. Hutter. Uncertainty-aware robotic world model makes offline modelbased reinforcement learning work on real robots. arXiv preprint arXiv:2504.16680, 2025. URL https://arxiv.org/abs/2504.16680. Presented at the 2nd Workshop on World Models at ICLR 2026.



> 💡 **不确定性感知 WM**: Li 等人进一步引入不确定性感知的世界模型，使离线 MBRL 在真实机器人上生效。这解决了离线数据缺乏探索导致模型偏差大的关键痛点。

[23] R. S. Sutton. Dyna, an integrated architecture for learning, planning, and reacting. ACM SIGART Bulletin, 2(4):160–163, 1991. doi:10.1145/122344.122377.



> 💡 **Dyna 架构起源**: Sutton 1991 年的 Dyna 是基于模型 RL 的开山之作。它将学习、规划和反应整合在一个架构中，奠定了现代 MBRL 的理论基础。

[24] M. Janner, J. Fu, M. Zhang, and S. Levine. When to trust your model: Model-based policy optimization. In Advances in Neural Information Processing Systems, volume 32, 2019. URL https://papers.nips.cc/paper\_files/paper/2019/hash/5faf461eff3099671ad 63c6f3f094f7f-Abstract.html.



> 💡 **信任模型边界**: Janner 等人探讨何时信任模型进行策略优化。这是 MBRL 的核心难题之一，即如何权衡模型误差带来的风险与规划带来的收益。

[25] J. Schulman, P. Moritz, S. Levine, M. I. Jordan, and P. Abbeel. High-dimensional continuous control using generalized advantage estimation. In International Conference on Learning Representations, 2016. URL https://arxiv.org/abs/1506.02438.



> 💡 **GAE 优势估计**: Schulman 等人的广义优势估计在偏差与方差之间做权衡，是 PPO 等策略梯度方法中稳定价值学习的常用组件。本文若用 PPO 类算法，GAE 很可能是其优势估计的实现方式。

[26] E. Todorov, T. Erez, and Y. Tassa. MuJoCo: A physics engine for model-based control. In IEEE/RSJ International Conference on Intelligent Robots and Systems, pages 5026–5033, 2012.



> 💡 **MuJoCo 仿真器**: Todorov 等人的 MuJoCo 是物理仿真领域的标准工具。如果本文实验基于 MuJoCo，需考虑其接触动力学特性对控制策略学习的影响。

[27] Bones Studio. BONES-SEED: Skeletal everyday embodiment dataset, 2026. URL https: //huggingface.co/datasets/bones-studio/seed.



> 💡 **数据集引用**: 引用 Bones Studio 的 BONES-SEED（Skeletal everyday embodiment dataset，2026 年），附 HuggingFace 数据集地址。该文献条目本身不含用法信息；这套数据在本文中是用于训练、评测还是仅作对照，需回到正文相应位置确认。

[28] F. G. Harvey, M. Yurick, D. Nowrouzezahrai, and C. Pal. Robust motion in-betweening. ACM Transactions on Graphics (Proceedings of ACM SIGGRAPH), 39(4):60:1–60:12, 2020. doi: 10.1145/3386569.3392480.



> 💡 **方法来源**: 引用 Harvey 等 2020 年发表于 ACM TOG（Proceedings of ACM SIGGRAPH）的 Robust Motion In-betweening，卷期页码为 39(4):60:1–60:12，DOI 10.1145/3386569.3392480。推断：作者很可能将这项工作作为运动中间帧生成或插值的相关工作、对比对象，但文献条目本身并未说明它在本文中具体承担什么角色。

