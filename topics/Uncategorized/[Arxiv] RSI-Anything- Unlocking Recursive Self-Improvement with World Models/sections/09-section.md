[← 返回 README](../README.md)

## References

[1] Karl Cobbe, Vineet Kosaraju, Mohammad Bavarian, Mark Chen, Heewoo Jun, Lukasz Kaiser, Matthias Plappert, Jerry Tworek, Jacob Hilton, Reiichiro Nakano, Christopher Hesse, and John Schulman. Training verifiers to solve math word problems. arXiv preprint arXiv:2110.14168, 2021. URL https://arxiv.org/abs/2110.14168.



> 💡 **引用定位**: [1] Cobbe et al. 2021, "Training verifiers to solve math word problems" (arXiv:2110.14168)。这是 GSM8K 与答案验证器（verifier）的工作，验证器的典型用法是对候选解做**结果级**重排序/打分。
> 
> > 💡 **纠正（初稿超范围）**: 初稿称其指向"Process Reward Model（过程奖励模型）"，与条目所能支持的内容不符——该标题与摘要语境指向结果级验证而非逐步过程监督，已改为"结果级验证器"。
> 
> > 💡 **推断界限**: 该引用只能说明数学推理与验证是本文的背景/评测来源之一；本文是否使用 PRM 或过程监督，属**待正文确认**，不能由参考文献推出。

[2] Noah Shinn, Federico Cassano, Edward Berman, Ashwin Gopinath, Karthik Narasimhan, and Shunyu Yao. Reflexion: Language agents with verbal reinforcement learning. arXiv preprint arXiv:2303.11366, 2023. URL https://arxiv.org/abs/2303.11366.



> 💡 **引用定位**: [2] Reflexion (arXiv:2303.11366) —— 语言智能体用**语言形式的口头反馈**做自我反思，并把反思写入情节记忆供后续试验复用。
> 
> > 💡 **纠正（初稿用词）**: 初稿写"经验回放或错误修正循环"，其中"经验回放（experience replay）"是 RL 中的参数化缓冲机制，Reflexion 并不以该机制为核心，已改为"自我反思 + 情节记忆反馈"。
> 
> > 💡 **推断界限**: 本文是否包含类似反思循环，属**推断**，需正文/附录确认。

[3] Caglar Gulcehre, Tom Le Paine, Srivatsan Srinivasan, Ksenia Konyushkova, Lotte Weerts, Abhishek Sharma, Aditya Siddhant, Alex Ahern, Miaosen Wang, Chenjie Gu, Wolfgang Macherey, Arnaud Doucet, Orhan Firat, and Nando de Freitas. Reinforced self-training (ReST) for language modeling. arXiv preprint arXiv:2308.08998, 2023. URL https://arxiv.org/abs/2308.08998.



> 💡 **引用定位**: [3] ReST (arXiv:2308.08998) —— Reinforced Self-Training，通过"生成→过滤→再训练"的迭代自训练提升语言模型，含 growing-batch 的离线 RL 阶段。
> 
> > 💡 **纠正（简化）**: 初稿称其为"离线 RL 微调技术"，方向对但过度简化：ReST 是**迭代式**流程，包含数据过滤与多轮再训练，不只是单次离线微调。
> 
> > 💡 **推断界限**: "本文可能采用自训练策略"为**推断**，参考文献本身不构成证据。

[4] Rafael Rafailov, Archit Sharma, Eric Mitchell, Stefano Ermon, Christopher D. Manning, and Chelsea Finn. Direct preference optimization: Your language model is secretly a reward model. In Advances in Neural Information Processing Systems, volume 36, 2023.



> 💡 **引用定位**: [4] DPO (NeurIPS 2023) —— 用偏好对直接优化策略，绕开显式奖励模型，损失形式具有二分类式结构。
> 
> > 💡 **纠正（措辞精度）**: 初稿"将偏好对齐转化为分类问题"是可接受的通俗说法，但更准确的说法是"把带 KL 约束的 RLHF 目标重参数化为可直接在偏好对上优化的目标"。
> 
> > 💡 **推断界限**: "本文可能使用 DPO 而非 PPO"属**推断**，需正文训练细节佐证。

[5] Eric Zelikman, Yuhuai Wu, Jesse Mu, and Noah D. Goodman. STaR: Bootstrapping reasoning with reasoning. In Advances in Neural Information Processing Systems, volume 35, pages 15476–15488, 2022.



> 💡 **引用定位**: [5] STaR (NeurIPS 2022) —— 以模型自生成、经答案筛选的正确推理链做自举（bootstrapping），并可对失败样本补充合理化推理后继续微调。
> 
> > 💡 **核对结论**: 初稿"利用正确推理链自举生成更多数据"与条目一致，无夸大。
> 
> > 💡 **推断界限**: 本文是否在 CoT 数据合成上借鉴该思想，属**推断**。

[6] David Ha and Jürgen Schmidhuber. World Models. arXiv preprint arXiv:1803.10122, 2018. URL https://arxiv.org/abs/1803.10122.



> 💡 **引用定位**: [6] Ha & Schmidhuber 2018, "World Models" (arXiv:1803.10122) —— 提出"在学到的世界模型内部训练策略"的范式（VAE 编码 + 时序动力学 + 控制器）。
> 
> > 💡 **纠正（无支持的强断言）**: 初稿称其为"本文的理论基石之一"，这是**无证据的推断**——仅凭被引用无法判断依赖深度，也可能只是背景性引用。已改为"世界模型脉络的背景引用，具体角色待正文确认"。

[7] Danijar Hafner, Jurgis Pasukonis, Jimmy Ba, and Timothy Lillicrap. Mastering diverse control tasks through world models. Nature, 640:647–653, 2025. doi: 10.1038/s41586-025-08744-2.



> 💡 **引用定位**: [7] Hafner et al., Nature 640:647–653, 2025，"Mastering diverse control tasks through world models" —— 该文通常对应 Dreamer 系列的最新版本。
> 
> > 💡 **名称映射标注**: 条目本身未出现"DreamerV3"字样，初稿直接写 DreamerV3 属**名称映射推断**（依据公开常识），已明确标注为推断而非条目原文。
> 
> > 💡 **数值核对**: 卷号 640、页码 647–653、DOI 10.1038/s41586-025-08744-2 与条目一致。

[8] Julian Schrittwieser, Ioannis Antonoglou, Thomas Hubert, et al. Mastering atari, go, chess and shogi by planning with a learned model. Nature, 588:604–609, 2020. doi: 10.1038/s41586-020-03051-4.



> 💡 **引用定位**: [8] Schrittwieser et al., Nature 588:604–609, 2020，"Mastering atari, go, chess and shogi by planning with a learned model" —— 这是 **MuZero**，核心是学习隐式环境动力学模型 + MCTS 规划。
> 
> > 💡 **纠正（硬性误归属）**: 初稿写成"AlphaZero 展示结合 MCTS 与神经网络的价值"**错误**。AlphaZero 对应的是"Mastering chess and shogi by self-play with a general reinforcement learning algorithm"（无学习模型、依赖完美模拟器）；本条的关键差异恰恰在于**用学到的模型替代模拟器**。已修正为 MuZero。
> 
> > 💡 **数值核对**: 卷 588、页 604–609、DOI 10.1038/s41586-020-03051-4 与条目一致。

[9] Taoyong Cui, Zhongyao Wang, Xinyue Xu, Weiyang Liu, Zhaochen Yu, Yuying Zhang, Qiang Gao, Mengyue Yang, Wanli Ouyang, Pheng Ann Heng, Yingcheng Wu, Zhenfei Yin, and Ling Yang. JEPA-Anything: Learning predictive models across diferent worlds. arXiv preprint arXiv:2609.20800, 2026. URL https://arxiv.org/abs/2609.20800.



> 💡 **引用定位**: [9] JEPA-Anything (arXiv:2609.20800, 2026) —— 从标题看是把预测式联合嵌入思路推广到多"世界"/多域。
> 
> > 💡 **OCR 提示**: 条目中 "diferent" 疑为 "different" 的解析拼写，引用时保留原文即可。
> 
> > 💡 **作者线索**: 作者含 Taoyong Cui、Ling Yang、Zhenfei Yin、Yingcheng Wu 等，与条目 [13] 高度重叠，提示同一研究群体的系列工作；这只能作为**引用线索**，不能作为本文方法归属的证据。
> 
> > 💡 **推断界限**: "本文关注通用世界模型构建能力"为推断。

[10] Mahmoud Assran, Quentin Duval, Ishan Misra, Piotr Bojanowski, Pascal Vincent, Michael Rabbat, Yann LeCun, and Nicolas Ballas. Self-supervised learning from images with a joint-embedding predictive architecture. arXiv preprint arXiv:2301.08243, 2023. URL https://arxiv.org/abs/ 2301.08243.



> 💡 **引用定位**: [10] Assran et al. 2023 (arXiv:2301.08243) —— 即 I-JEPA，图像上的联合嵌入预测架构，属**非对比式**自监督，作者列表含 Yann LeCun。
> 
> > 💡 **纠正（措辞）**: 初稿"LeCun 提出的非对比式自监督学习范式"应改为"LeCun **参与署名**的"，避免把集体工作归为单人提出；"非对比式"判断与条目主题一致。
> 
> > 💡 **推断界限**: "本文视觉/状态表征的核心参考"属**未证实推断**，需正文方法佐证。

[11] Adrien Bardes, Quentin Garrido, Jean Ponce, Xinlei Chen, Michael Rabbat, Yann LeCun, Mahmoud Assran, and Nicolas Ballas. Revisiting feature prediction for learning visual representations from video. arXiv preprint arXiv:2404.08471, 2024. URL https://arxiv.org/abs/2404.08471.



> 💡 **引用定位**: [11] Bardes et al. 2024 (arXiv:2404.08471) —— 从视频中通过特征预测学习视觉表征，是 V-JEPA 线的相关工作。
> 
> > 💡 **核对结论**: 初稿"V-JEPA 前身工作，聚焦从视频学习特征预测"方向正确，但"前身"的时序关系属**推断**，条目未自述与本文的关系。
> 
> > 💡 **推断界限**: "支撑本文动态环境状态建模"为推断。

[12] Mido Assran, Adrien Bardes, David Fan, et al. V-JEPA 2: Self-supervised video models enable understanding, prediction and planning. arXiv preprint arXiv:2506.09985, 2025. URL https: //arxiv.org/abs/2506.09985.



> 💡 **引用定位**: [12] V-JEPA 2 (arXiv:2506.09985, 2025) —— 标题明确含 understanding / prediction / planning。
> 
> > 💡 **纠正（过度强的推断）**: 初稿"极可能是本文方法论的**直接前身或主要对比对象**"超出参考文献所能支持的范围。被引用既可能是前身、基线，也可能只是背景。已降级为"待正文方法/实验部分确认的候选前身或基线"。

[13] Taoyong Cui, Xi Wang, Zonghang Li, Jinchao Ding, Lingsen You, Yuzhi Xu, Wanghan Xu, Fang Wu, Kejun Ying, Wanli Ouyang, Pheng Ann Heng, Ling Yang, Zhenfei Yin, and Yingcheng Wu. An immune world model for multiscale forecasting and therapeutic hypothesis generation. arXiv preprint arXiv:2609.14709, 2026. doi: 10.48550/arXiv.2609.14709. URL https://arxiv.org/abs/ 2609.14709.



> 💡 **引用定位**: [13] "An immune world model for multiscale forecasting and therapeutic hypothesis generation" (arXiv:2609.14709, 2026) —— 世界模型在免疫学/多尺度预测与治疗假设生成上的应用。
> 
> > 💡 **作者线索**: 与 [9] 大量共同作者（Taoyong Cui、Ling Yang、Zhenfei Yin、Yingcheng Wu 等），为同一群体的世界模型系列。
> 
> > 💡 **纠正（评价性推断）**: 初稿"展示世界模型在垂直领域的高价值应用潜力"是**评价性推断**，条目本身只提供标题与出处；已改为"提示世界模型被用于科学领域，本文用途待确认"。
> 
> > 💡 **数值核对**: DOI 10.48550/arXiv.2609.14709 与条目一致。

[14] Yinjie Wang, Ling Yang, Ye Tian, Ke Shen, and Mengdi Wang. Co-evolving LLM coder and unit tester via reinforcement learning. arXiv preprint arXiv:2506.03136, 2025. doi: 10.48550/arXiv.2506.03136. URL https://arxiv.org/abs/2506.03136.



> 💡 **引用定位**: [14] "Co-evolving LLM coder and unit tester via reinforcement learning" (arXiv:2506.03136, 2025) —— 代码生成与单元测试生成器通过 RL 共同演化。
> 
> > 💡 **推断界限**: 初稿"显示本文可能涉及代码生成领域的 RL 反馈闭环设计"为**推断**。该条目也可能仅作为"用自动反馈信号训练"的通用先例被引用，不必然意味着本文做代码任务。
> 
> > 💡 **数值核对**: DOI 10.48550/arXiv.2506.03136 与条目一致。

[15] Yujia Li, David Choi, Junyoung Chung, Nate Kushman, Julian Schrittwieser, Rémi Leblond, Tom Eccles, James Keeling, Felix Gimeno, Agustin Dal Lago, et al. Competition-level code generation with AlphaCode. Science, 378(6624):1092–1097, 2022. doi: 10.1126/science.abq1158.



> 💡 **引用定位**: [15] AlphaCode, Science 378(6624):1092–1097, 2022，DOI 10.1126/science.abq1158。
> 
> > 💡 **核对结论**: 卷期页与 DOI 与条目完全一致。初稿"定义了竞赛级代码生成的标准"是对该文地位的**评价**，方向合理但属主观表述，建议保留为"常被用作竞赛级代码生成的参照"。
> 
> > 💡 **推断界限**: 本文是否以竞赛级编程为主评测，需实验节确认。

[16] Glenn W. Brier. Verification of forecasts expressed in terms of probability. Monthly Weather Review, 78(1):1–3, 1950. doi: 10.1175/1520-0493(1950)078<0001:VOFEIT>2.0.CO;2.



> 💡 **引用定位**: [16] Brier 1950, Monthly Weather Review 78(1):1–3 —— Brier score，对概率预报做均方误差型打分，属**结果级概率校准**指标。（条目 DOI 含 `<`/`>` 字符，批注引用时需转义。）
> 
> > 💡 **纠正（因果链条不成立）**: 初稿称其"暗示本文不仅关注最终结果，还重视**中间步骤**的不确定性量化"——Brier score 本质是对最终概率预测的评分，与"中间步骤不确定性"没有必然联系，已改为"若正文使用该指标，应理解为对概率预测的校准/评分；是否用于中间步骤属**待证**"。

[17] Dan Hendrycks, Steven Basart, Saurav Kadavath, Mantas Mazeika, Akul Arora, Ethan Guo, Collin Burns, Samir Puranik, Horace He, Dawn Song, and Jacob Steinhardt. Measuring coding challenge competence with APPS. arXiv preprint arXiv:2105.09938, 2021. doi: 10.48550/arXiv.2105.09938. URL https://arxiv.org/abs/2105.09938.



> 💡 **引用定位**: [17] Hendrycks et al. 2021, APPS (arXiv:2105.09938) —— 编程挑战数据集，含入门到竞赛级难度。
> 
> > 💡 **核对结论**: 初稿"本文实验部分将在真实编码任务上评估"属**推断**（被引用亦可仅为评测背景介绍）。条目本身正确，DOI 10.48550/arXiv.2105.09938 一致。

[18] Naman Jain, King Han, Alex Gu, Wen-Ding Li, Fanjia Yan, Tianjun Zhang, Sida Wang, Armando Solar-Lezama, Koushik Sen, and Ion Stoica. LiveCodeBench: Holistic and contamination free evaluation of large language models for code. arXiv preprint arXiv:2403.07974, 2024. doi: 10.48550/arXiv.2403. 07974. URL https://arxiv.org/abs/2403.07974.



> 💡 **引用定位**: [18] LiveCodeBench (arXiv:2403.07974) —— 强调抗污染、按时间切分的代码评测。
> 
> > 💡 **推断标注**: 初稿"反映作者对当前 LLM 代码评测可信度的关注"是**推断**，方向合理但不能断言为作者明确立场；已标注为推断。
> 
> > 💡 **OCR 提示**: 条目中 "arXiv:2403. 07974"（数字间空格）与 "https://arxiv.org/abs/2403.07974" 为换行解析产物，原样保留。

[19] Evan Zheran Liu, Kelvin Guu, Panupong Pasupat, Tianlin Shi, and Percy Liang. Reinforcement learning on web interfaces using workflow-guided exploration. In International Conference on Learning Representations, 2018. URL https://arxiv.org/abs/1802.08802.



> 💡 **引用定位**: [19] Liu et al., ICLR 2018 —— 在 Web 界面上用工作流引导的探索做强化学习，属早期 Web 智能体工作。
> 
> > 💡 **推断界限**: 初稿"可能与本文在多步交互环境中的动作空间定义有关"为**推断**，需正文确认；亦可能仅为 Web/交互式智能体背景引用。

[20] Tianbao Xie, Danyang Zhang, Jixuan Chen, Xiaochuan Li, Siheng Zhao, Ruisheng Cao, Toh Jing Hua, Zhoujun Cheng, Dongchan Shin, Fangyu Lei, Yitao Liu, Yiheng Xu, Shuyan Zhou, Silvio Savarese, Caiming Xiong, Victor Zhong, and Tao Yu. OSWorld: Benchmarking multimodal agents for open-ended tasks in real computer environments. arXiv preprint arXiv:2404.07972, 2024. doi: 10.48550/arXiv.2404.07972. URL https://arxiv.org/abs/2404.07972.



> 💡 **引用定位**: [20] OSWorld (arXiv:2404.07972) —— 在真实计算机环境中评测多模态智能体的开放式任务基准。
> 
> > 💡 **纠正（推断被写成结论）**: 初稿"指向本文可能**具备系统级操作能力**"——基准被引用不等于本文具备该能力，也可能是"现有评测与真实 OS 任务仍有差距"这类动机性引用。已改为"待正文确认其为评测基准还是动机引用"。

[21] Yinjie Wang, Tianbao Xie, Ke Shen, Mengdi Wang, and Ling Yang. RLAnything: Forge environment, policy, and reward model in completely dynamic RL system. arXiv preprint arXiv:2602.02488, 2026. doi: 10.48550/arXiv.2602.02488. URL https://arxiv.org/abs/2602.02488.



> 💡 **引用定位**: [21] RLAnything (arXiv:2602.02488, 2026) —— 标题显示为在完全动态 RL 系统中同时构造环境、策略与奖励模型。
> 
> > 💡 **推断界限**: 初稿"可能是本文解决环境变化或非平稳性的理论依据"为**推断**；条目仅提供标题与出处，不能据此确认本文的技术依赖。
> 
> > 💡 **数值核对**: DOI 10.48550/arXiv.2602.02488 与条目一致。

[22] Stanford Cong Lab. Pushing the limits of laboratory 3D perception and long-horizon planning via protocol-aligned action prediction. Public benchmark repository, 2026. URL https://github. com/Stanford-CongLab/LabHorizon. Accessed September 30, 2026.



> 💡 **引用定位**: [22] Stanford Cong Lab 的公开基准仓库 LabHorizon，面向实验室 3D 感知与长视野规划，访问日期 2026-09-30（按条目原文照录，未做外部核验）。
> 
> > 💡 **推断界限**: 初稿"暗示本文应用场景可能延伸至科学实验自动化"为**推断**；该条目为基准仓库而非论文，引用也可能只是数据/评测来源。
> 
> > 💡 **格式提示**: 条目中 "github. com/Stanford-CongLab/LabHorizon" 的断行空格为解析产物，原样保留。

[23] xAI. Grok 4.3. xAI model documentation, 2026. URL https://docs.x.ai/developers/ models/grok-4.3. Accessed September 30, 2026.



> 💡 **引用定位**: [23] xAI 的 Grok 4.3 模型文档（2026，访问日期 2026-09-30）。
> 
> > 💡 **纠正（用途被默认）**: 初稿"作为商业 SOTA 模型被引用，通常用于大模型能力的横向对比"把常见惯例当成事实。条目本身未说明用途，已改为"若出现在实验节则可能为对比基线，**待正文确认**"。
> 
> > 💡 **核验界限**: 模型版本号与发布日期**未做外部检索核验**，按条目原文照录。

[24] Google. Gemini 3.1 Pro. Oficial model announcement, February 2026. URL https: //blog.google/innovation-and-ai/models-and-research/gemini-models/ gemini-3-1-pro/.



> 💡 **引用定位**: [24] Google 的 Gemini 3.1 Pro 模型公告（2026-02）。
> 
> > 💡 **OCR 提示**: 条目中 "Oficial" 疑为 "Official" 的解析拼写；URL 中 "https: //blog.google/..." 的断行亦为解析产物。
> 
> > 💡 **纠正（无支持断言）**: 初稿"体现本文旨在超越当前顶级闭源模型的性能"是**无支持推断**，已降级为"可能作为对比基线，待实验节确认"。

[25] OpenAI. Introducing GPT-5.5. OpenAI product release, April 2026. URL https://openai.com/ index/introducing-gpt-5-5/.



> 💡 **引用定位**: [25] OpenAI 的 GPT-5.5 产品发布（2026-04）。
> 
> > 💡 **纠正**: 初稿"进一步确认了本文对标的是下一代前沿通用人工智能系统"属于把惯例读成事实的强断言，已改为"用途待正文实验节确认"。版本号与日期按条目照录，未外部核验。

[26] Moonshot AI. Kimi K2.6. Hugging Face model card, 2026. URL https://huggingface.co/ moonshotai/Kimi-K2.6. Accessed September 30, 2026.



> 💡 **引用定位**: [26] Moonshot AI 的 Kimi K2.6 Hugging Face 模型卡（2026，访问日期 2026-09-30）。
> 
> > 💡 **纠正（推断）**: 初稿"用于证明本文方法在开源生态中的竞争力"为**推断**；被引用也可能只是作为可复现基座模型。已标注为待正文确认。

[27] Qwen Team. Qwen3.6-35B-A3B. Hugging Face model card, 2026. URL https://huggingface. co/Qwen/Qwen3.6-35B-A3B. Accessed September 30, 2026.



> 💡 **引用定位**: [27] Qwen3.6-35B-A3B Hugging Face 模型卡（2026，访问日期 2026-09-30）。
> 
> > 💡 **推断标注**: 命名中的 "35B-A3B" 通常表示总参数 35B、激活约 3B 的 MoE 结构，初稿"作为 MoE 架构代表"是**基于命名惯例的推断**而非条目自述，需正文确认。"用于效率或规模对比"同样属推断。

[28] Benjamin J. Shields, Jason Stevens, Jun Li, et al. Bayesian reaction optimization as a tool for chemical synthesis. Nature, 590:89–96, 2021. doi: 10.1038/s41586-021-03213-y.



> 💡 **引用定位**: [28] Shields et al., Nature 590:89–96, 2021，"Bayesian reaction optimization as a tool for chemical synthesis"，DOI 10.1038/s41586-021-03213-y。
> 
> > 💡 **纠正（过度推断）**: 初稿"强烈暗示本文涉及通过世界模型指导实验设计或分子合成"——该引用常被用作"用贝叶斯优化做闭环实验设计"的通用先例，未必意味着本文做化学/分子任务。已降级为"若正文涉及实验设计/主动优化，则为该方向的先例引用；否则仅为背景，**待正文确认**"。
> 
> > 💡 **数值核对**: 卷 590、页 89–96 与条目一致。

[29] Maxime Robeyns, Martin Szummer, and Laurence Aitchison. A self-improving coding agent. arXiv preprint arXiv:2504.15228, 2025. doi: 10.48550/arXiv.2504.15228. URL https://arxiv.org/ abs/2504.15228.



> 💡 **书目信息**: Robeyns, Szummer, Aitchison, “A self-improving coding agent”, arXiv:2504.15228 (2025)。条目内部的 arXiv 编号与 URL 一致，可自洽核对。
> 
> > 💡 **边界说明**: 初稿称该文献“用于对比本文提出的递归自我改进机制在代码生成领域的早期探索”，这属于对正文引用意图的推断。References 本身不含引用位置与论述语境，该对比关系**待正文核实**，不应作为已证事实。

[30] Jenny Zhang, Shengran Hu, Cong Lu, Robert Lange, and Jef Clune. Darwin gödel machine: Openended evolution of self-improving agents. arXiv preprint arXiv:2505.22954, 2025. doi: 10.48550/ arXiv.2505.22954. URL https://arxiv.org/abs/2505.22954.



> 💡 **书目信息**: Zhang, Hu, Lu, Lange, Clune, “Darwin gödel machine: Openended evolution of self-improving agents”, arXiv:2505.22954 (2025)。
> 
> > 💡 **勘误提示**: 标题中 `Openended` 与作者 `Jef Clune` 疑为 MinerU/OCR 造成的断词与拼写问题（常见写法为 Open-ended、Jeff Clune）。批读保留原文原样，仅标注疑似排版错误，不改写正文。初稿直接断言“本文可能借鉴其变异-选择思想”属推断，**待正文证据**。

[31] Dhruv Srikanth, Bingchen Zhao, Dixing Xu, Yuxiang Wu, and Zhengyao Jiang. Recursive selfimprovement of AI research agents. arXiv preprint arXiv:2609.26457, 2026. doi: 10.48550/arXiv. 2609.26457. URL https://arxiv.org/abs/2609.26457.



> 💡 **书目信息**: Srikanth, Zhao, Xu, Wu, Jiang, “Recursive self-improvement of AI research agents”, arXiv:2609.26457 (2026)。
> 
> > 💡 **主题邻近性**: 标题与本文主题词高度重合，是**最需要在正文中区分**的一类文献。但“最直接的对标或基础文献”只能由正文引用处的论述强度决定，仅凭本条无法判定；若摘要/正文未明确讨论二者差异，应记为**待证**而非既成结论。

[32] Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, and Anima Anandkumar. Voyager: An open-ended embodied agent with large language models. arXiv preprint arXiv:2305.16291, 2023. doi: 10.48550/arXiv.2305.16291. URL https://arxiv.org/ abs/2305.16291.



> 💡 **书目信息**: Wang 等, “Voyager: An open-ended embodied agent with large language models”, arXiv:2305.16291 (2023)。
> 
> > 💡 **批注边界**: 初稿的“引用它旨在说明‘自主探索+技能库积累’范式对科研代理的启示”是推测引用动机。可核验的只有条目本身（标题显示其为开放式具身代理工作），引用动机**需回正文定位**。

[33] Jiacheng Guo, Ling Yang, Peter Chen, Qixin Xiao, Yinjie Wang, Xinzhe Juan, Jiahao Qiu, Ke Shen, and Mengdi Wang. GenEnv: Dificulty-aligned co-evolution between LLM agents and environment simulators. arXiv preprint arXiv:2512.19682, 2025. doi: 10.48550/arXiv.2512.19682. URL https: //arxiv.org/abs/2512.19682.



> 💡 **书目信息**: Guo 等, “GenEnv: Dificulty-aligned co-evolution between LLM agents and environment simulators”, arXiv:2512.19682 (2025)。
> 
> > 💡 **勘误与边界**: 标题 `Dificulty` 应为 `Difficulty`（OCR 漏字）。初稿“本文若涉及模拟实验环境或动态任务难度调整，此文献提供了方法论参考”为条件式推断——前提是否成立取决于正文，**待证**。

[34] Peng Xia, Kaide Zeng, Jiaqi Liu, Can Qin, Fang Wu, Yiyang Zhou, Caiming Xiong, and Huaxiu Yao. Agent0: Unleashing self-evolving agents from zero data via tool-integrated reasoning. arXiv preprint arXiv:2511.16043, 2025. doi: 10.48550/arXiv.2511.16043. URL https://arxiv.org/abs/ 2511.16043.



> 💡 **书目信息**: Xia 等, “Agent0: Unleashing self-evolving agents from zero data via tool-integrated reasoning”, arXiv:2511.16043 (2025)。
> 
> > 💡 **批注边界**: 标题可支持“零数据 + 工具集成推理”这一表述本身；但初稿进一步推断“暗示本文可能采用类似的无监督/弱监督自举策略”**超出本段证据**，需在方法章节找到对应设计后才可成立。

[35] Yinjie Wang, Xuyang Chen, Xiaolong Jin, Mengdi Wang, and Ling Yang. OpenClaw-RL: Train any agent simply by talking. arXiv preprint arXiv:2603.10165, 2026. doi: 10.48550/arXiv.2603.10165. URL https://arxiv.org/abs/2603.10165.



> 💡 **书目信息**: Wang 等, “OpenClaw-RL: Train any agent simply by talking”, arXiv:2603.10165 (2026)。
> 
> > 💡 **批注边界**: “通过对话训练代理”可由标题支撑；初稿“表明本文可能探讨了自然语言反馈作为强化学习信号的有效性”属于对本文内容的推断，**待正文核实**。

[36] Zhaochen Yu, Yingcheng Wu, Zhenfei Yin, Kaiyuan Chen, Zhe Zhao, Mengdi Wang, Shuicheng Yan, and Ling Yang. Recursive experiential-working memory evolution for long-horizon agent harnesses. arXiv preprint arXiv:2608.24876, 2026. doi: 10.48550/arXiv.2608.24876. URL https: //arxiv.org/abs/2608.24876.



> 💡 **书目信息**: Yu 等, “Recursive experiential-working memory evolution for long-horizon agent harnesses”, arXiv:2608.24876 (2026)。
> 
> > 💡 **批注边界**: 本条主题可归入长时程代理的记忆/经验复用路线；初稿“本文若处理多步科研规划，此文献支撑了其状态持久化的必要性”是条件推断，不能当作本文设计依据，**待证**。

[37] Shuhan Xue, Jianyuan Zhong, Ziyuan Nan, Wenbin Li, Zhaochen Yu, Jinchao Ding, Qiang Gao, Pengyu Zhan, Yuntong Zhang, Tian Cheng, Zhenfei Yin, Yingcheng Wu, and Ling Yang. Science-Buddy: Recursive-in-recursive self-improvement for interactive scientific agents. arXiv preprint arXiv:2609.17523, 2026. doi: 10.48550/arXiv.2609.17523. URL https://arxiv.org/abs/ 2609.17523.



> 💡 **书目信息**: Xue 等, “Science-Buddy: Recursive-in-recursive self-improvement for interactive scientific agents”, arXiv:2609.17523 (2026)。
> 
> > 💡 **批注边界**: 该条属于“递归自改进 × 科学代理”的交叉方向；初稿称其为本文“在特定垂直领域的直接前驱或并行工作”是较强判断，References 无法证实引用语境，**需回正文**确认是作为 baseline、动机还是相关工作列举。

[38] Jake Bruce, Michael Dennis, Ashley Edwards, et al. Genie: Generative interactive environments. arXiv preprint arXiv:2402.15391, 2024. URL https://arxiv.org/abs/2402.15391.



> 💡 **书目信息**: Bruce, Dennis, Edwards 等, “Genie: Generative interactive environments”, arXiv:2402.15391 (2024)。注：本条无 DOI，仅有 URL。
> 
> > 💡 **批注边界**: 初稿“引用它可能为了论证构建虚拟实验室或仿真环境的可行性”是推测。本条只能确认其主题为生成式交互环境，引用用途**待正文**。

[39] Anthony Brohan, Noah Brown, Justice Carbajal, et al. RT-2: Vision-language-action models transfer web knowledge to robotic control. arXiv preprint arXiv:2307.15818, 2023. URL https://arxiv. org/abs/2307.15818.



> 💡 **书目信息**: Brohan, Brown, Carbajal 等, “RT-2: Vision-language-action models transfer web knowledge to robotic control”, arXiv:2307.15818 (2023)。
> 
> > 💡 **批注边界**: 标题支持“VLA 模型将网络知识迁移到机器人控制”；初稿延伸出的“用于对比纯数字代理的局限”属推断，**待证**。

[40] Open X-Embodiment Collaboration, Abby O’Neill, Abdul Rehman, et al. Open X-Embodiment: Robotic learning datasets and RT-X models. arXiv preprint arXiv:2310.08864, 2023. URL https: //arxiv.org/abs/2310.08864.



> 💡 **书目信息**: Open X-Embodiment Collaboration 等, “Open X-Embodiment: Robotic learning datasets and RT-X models”, arXiv:2310.08864 (2023)。
> 
> > 💡 **批注边界**: 初稿称“反衬科研代理在数据稀缺下的挑战”，这是把本条用作论证支点的推断；正文是否如此使用，**需定位引用句**。

[41] Alexander Khazatsky, Karl Pertsch, Suraj Nair, et al. DROID: A large-scale in-the-wild robot manipulation dataset. arXiv preprint arXiv:2403.12945, 2024. URL https://arxiv.org/abs/2403. 12945.



> 💡 **书目信息**: Khazatsky, Pertsch, Nair 等, “DROID: A large-scale in-the-wild robot manipulation dataset”, arXiv:2403.12945 (2024)。
> 
> > 💡 **批注边界**: 初稿“支持本文关于仿真优于实体操作的论点”属**强推断**：即便正文确有“仿真 vs 实体”讨论，也需引用句或实验设置佐证，不能由参考文献条目推定。

[42] Daniil A. Boiko, Robert MacKnight, Ben Kline, and Gabe Gomes. Autonomous chemical research with large language models. Nature, 624:570–578, 2023. doi: 10.1038/s41586-023-06792-0.



> 💡 **书目信息**: Boiko, MacKnight, Kline, Gomes, “Autonomous chemical research with large language models”, Nature 624:570–578 (2023)，带 DOI。
> 
> > 💡 **评价性表述纠正**: 初稿“首个展示 LLM 驱动自主化学研究的里程碑”是外部评价（含“首个”“里程碑”），超出本段证据，亦非本段所载信息；应删除或明确标注为**未经核验的评价**。可确证的仅为期刊、卷页与标题。

[43] Andres M. Bran, Sam Cox, Oliver Schilter, et al. Augmenting large language models with chemistry tools. Nature Machine Intelligence, 6:525–535, 2024. doi: 10.1038/s42256-024-00832-8.



> 💡 **书目信息**: Bran, Cox, Schilter 等, “Augmenting large language models with chemistry tools”, Nature Machine Intelligence 6:525–535 (2024)，带 DOI。
> 
> > 💡 **批注边界**: 标题可支持“LLM 结合化学工具”这一方向；初稿“说明单纯 LLM 不够，必须通过外部工具弥补幻觉和精度不足，本文可能扩展了工具集”中，“必须”“本文可能扩展”均为推断，**待正文核实**。

[44] Philippe Schwaller, Alain C. Vaucher, Teodoro Laino, and Jean-Louis Reymond. Prediction of chemical reaction yields using deep learning. Machine Learning: Science and Technology, 2:015016, 2021. doi: 10.1088/2632-2153/abc81d.



> 💡 **书目信息**: Schwaller, Vaucher, Laino, Reymond, “Prediction of chemical reaction yields using deep learning”, Machine Learning: Science and Technology 2:015016 (2021)，带 DOI。
> 
> > 💡 **批注边界**: 本条是反应产率预测的具体方法文献；初稿“本文可能将其作为代理调用的专家模型之一，或作为评估基准”属两种并行猜测，均**待正文**确认，二选一不可先定。

[45] Benjamin Burger, Phillip M. Mafettone, Vladimir V. Gusev, et al. A mobile robotic chemist. Nature, 583:237–241, 2020. doi: 10.1038/s41586-020-2442-2.



> 💡 **书目信息**: Burger, Mafettone, Gusev 等, “A mobile robotic chemist”, Nature 583:237–241 (2020)，带 DOI。
> 
> > 💡 **勘误提示**: 作者 `Phillip M. Mafettone` 疑为 `Maffettone` 的 OCR/拼写错误，批读保留原文并标注。初稿“对比软件代理与硬件代理的成本和灵活性差异”为推断性框架，**需正文引用句支撑**。

[46] Nathan J. Szymanski, Bernardus Rendy, Yuxing Fei, et al. An autonomous laboratory for the accelerated synthesis of inorganic materials. Nature, 624:86–91, 2023. doi: 10.1038/s41586-023-06734-w.



> 💡 **书目信息**: Szymanski, Rendy, Fei 等, “An autonomous laboratory for the accelerated synthesis of inorganic materials”, Nature 624:86–91 (2023)，带 DOI。
> 
> > 💡 **批注边界**: 标题支持“无机材料自主合成自动化实验室”；初稿“为本文代理的实验设计模块提供现实参照”属推断，**待证**。

[47] Tatsuya Sagawa and Ryosuke Kojima. ReactionT5: A pre-trained transformer model for accurate chemical reaction prediction with limited data. Journal of Cheminformatics, 17:126, 2025. doi: 10.1186/s13321-025-01075-4.



> 💡 **书目信息**: Sagawa, Kojima, “ReactionT5: A pre-trained transformer model for accurate chemical reaction prediction with limited data”, Journal of Cheminformatics 17:126 (2025)，带 DOI。
> 
> > 💡 **批注边界**: 标题含 “limited data” 可支持小样本反应预测的定位；但初稿由此推出“本文代理的策略比从头训练更有效”属**超出证据的结论**，应改为待证假设。

[48] Haotian Cui, Chloe Wang, Hassaan Maan, et al. scGPT: Toward building a foundation model for single-cell multi-omics using generative AI. Nature Methods, 21:1470–1480, 2024. doi: 10.1038/ s41592-024-02201-0.



> 💡 **书目信息**: Cui, Wang, Maan 等, “scGPT: Toward building a foundation model for single-cell multi-omics using generative AI”, Nature Methods 21:1470–1480 (2024)，带 DOI。
> 
> > 💡 **批注边界**: 本条属生物组学基础模型；初稿“本文若涉及多模态或序列建模，此文献提供架构灵感”是条件推断，不能与本文自身架构混为一谈，**待正文**。

[49] Christina V. Theodoris, Ling Xiao, Anant Chopra, et al. Transfer learning enables predictions in network biology. Nature, 618:616–624, 2023. doi: 10.1038/s41586-023-06139-9.



> 💡 **书目信息**: Theodoris, Xiao, Chopra 等, “Transfer learning enables predictions in network biology”, Nature 618:616–624 (2023)，带 DOI。
> 
> > 💡 **批注边界**: 标题支持“迁移学习用于网络生物学预测”；初稿“支持本文代理利用已有科学知识加速假设生成的逻辑”是外推，**待证**。

[50] Yusuf Roohani, Kexin Huang, and Jure Leskovec. Predicting transcriptional outcomes of novel multigene perturbations with GEARS. Nature Biotechnology, 42:927–935, 2024. doi: 10.1038/ s41587-023-01905-6.



> 💡 **书目信息**: Roohani, Huang, Leskovec, “Predicting transcriptional outcomes of novel multigene perturbations with GEARS”, Nature Biotechnology 42:927–935 (2024)，带 DOI。
> 
> > 💡 **批注边界**: 标题明确为基因扰动的转录结果预测；初稿“对应代理的推理模块”是映射式猜测，**需在正文找到对应模块**方可成立。

[51] Charlotte Bunne, Stefan G. Stark, Gabriele Gut, et al. Learning single-cell perturbation responses using neural optimal transport. Nature Methods, 20:1759–1768, 2023. doi: 10.1038/s41592-023-01969-x.



> 💡 **书目信息**: Bunne, Stark, Gut 等, “Learning single-cell perturbation responses using neural optimal transport”, Nature Methods 20:1759–1768 (2023)，带 DOI。
> 
> > 💡 **批注边界**: 标题支持“神经最优传输用于单细胞扰动响应”；初稿“暗示本文代理可能在优化实验设计时使用类似统计方法”属**无证据推断**，应标为待证或删除。

[52] Binyuan Hui, Jian Yang, Zeyu Cui, Jiaxi Yang, Dayiheng Liu, Lei Zhang, Tianyu Liu, Jiajun Zhang, Bowen Yu, et al. Qwen2.5-Coder technical report. arXiv preprint arXiv:2409.12186, 2024. doi: 10.48550/arXiv.2409.12186. URL https://arxiv.org/abs/2409.12186.



> 💡 **书目信息**: Hui 等, “Qwen2.5-Coder technical report”, arXiv:2409.12186 (2024)，带 DOI 与 URL。
> 
> > 💡 **关键纠正**: 初稿断言“明确本文使用的底层大语言模型版本”，但参考文献条目**无法证明本文以 Qwen2.5-Coder 为基座**——这只是可能的推断。若正文“Implementation/Setup”确写明基座型号，才是事实；否则应写成 **待正文确认**，不得在此充当已证事实。

[53] Edward J. Hu, Yelong Shen, Phillip Wallis, Zeyuan Allen-Zhu, Yuanzhi Li, Shean Wang, Lu Wang, and Weizhu Chen. LoRA: Low-rank adaptation of large language models. In International Conference on Learning Representations, 2022.



> 💡 **书目信息**: Hu, Shen, Wallis, Allen-Zhu, Li, Wang, Wang, Chen, “LoRA: Low-rank adaptation of large language models”, ICLR 2022（无 DOI）。
> 
> > 💡 **关键纠正**: 初稿“引用它表明本文可能对基座模型进行了轻量级适配”仍是推断。LoRA 是通用微调方法，本文是否实际采用**必须由训练设置章节证明**；本条本身只提供 LoRA 的出处，**待证**。

