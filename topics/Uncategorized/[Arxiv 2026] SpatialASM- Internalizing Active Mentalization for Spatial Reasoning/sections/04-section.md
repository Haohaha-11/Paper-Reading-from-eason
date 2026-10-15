[← 返回 README](../README.md)

## 4. Experiments

Dataset construction. To train the NVG in Sec. 3.1, we leverage DL3DV [40], a public dataset which provides 10k diverse scenes captured from multiple viewpoints together with the corresponding camera matrices, to construct our training set SpatialASM-edit140k, containing both single-image and multi-image inputs. To synthesize ASM trajectories introduced in Sec. 3.2, we source various challenging spatial-QA samples from



> 💡 **数据构建**: 训练集 SpatialASM-edit140k 基于 DL3DV 数据集（10k 场景）构建，包含单图和多图输入。为合成 ASM 轨迹，作者从多个空间 QA 数据集中采样困难样本进行生成。

Table 1: Performance comparison on 7 spatial reasoning and 3 general-VQA benchmarks. We report the average accuracy over all benchmarks within each category. The performance of several spatial models are also provided (7B variants on Qwen2.5-VL-7B; 8B variants on Qwen3-VL-8B).
<table><tr><td rowspan="2">Models</td><td colspan="7">Spatial Reasoning</td><td>Gnr.-VQA</td></tr><tr><td></td><td>Mindcube MMSI Spa4576d</td><td></td><td>Viewspa 3DSR EmbSpa</td><td></td><td></td><td>ESI</td><td>Avg. Avg.</td></tr><tr><td colspan="9">Representative Spatial Models</td></tr><tr><td>SpaceR-7B</td><td>36.6</td><td>28.8</td><td>45.1</td><td>38.4</td><td>54.5 37.3</td><td></td><td>40.2 40.1</td><td>59.4</td></tr><tr><td>VST-RL-7B</td><td>39.3</td><td>35.1</td><td>43.4</td><td>47.0</td><td>60.1 72.0</td><td>40.1</td><td>48.1</td><td>62.1</td></tr><tr><td>HoliSpatial-8B</td><td>52.0</td><td>30.6</td><td>30.5</td><td>50.1</td><td>49.7 73.8</td><td>41.8</td><td>46.9</td><td>63.1</td></tr><tr><td>SenseNova-8B</td><td>50.4</td><td>29.6</td><td>41.3</td><td>46.4</td><td>55.2 70.8</td><td>36.5</td><td>47.2</td><td>63.7</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-4B)</td></tr><tr><td>Qwen3-VL-4B</td><td>33.9</td><td>28.0</td><td>37.2</td><td>43.2</td><td>55.8 72.3</td><td></td><td>36.5 43.8</td><td>63.7</td></tr><tr><td>SpatialASM-4B</td><td>39.1</td><td>29.2</td><td>42.8</td><td>46.2</td><td>56.4</td><td>76.5 40.6</td><td>47.3</td><td>63.1</td></tr><tr><td>Improvement</td><td>+5.2</td><td>+1.2</td><td>+5.6</td><td>+3.0</td><td>+0.6 +4.2</td><td></td><td>+4.1 +3.5</td><td>-0.6</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-8B)</td></tr><tr><td>Qwen3-VL-8B</td><td>40.4</td><td>27.8</td><td>34.8</td><td>45.3</td><td>57.2</td><td>79.5</td><td>38.4 46.2</td><td>64.4</td></tr><tr><td>SpatialASM-8B</td><td>43.9</td><td>31.1</td><td>43.5</td><td>46.8</td><td>58.2</td><td>80.2</td><td>40.2 49.1</td><td>65.0</td></tr><tr><td>Improvement</td><td>+3.5</td><td>+3.3</td><td>+8.7</td><td>+1.5</td><td>+1.0</td><td>+0.7</td><td>+1.8 +2.9</td><td>+0.6</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-30BA3B)</td></tr><tr><td>Qwen3-VL-30BA3B</td><td>36.7</td><td>25.2</td><td>41.9</td><td>43.9</td><td>54.5 73.4</td><td></td><td>37.2 44.7</td><td>69.4</td></tr><tr><td>SpatialASM-30BA3B</td><td>37.9</td><td>30.8</td><td>42.1</td><td>45.6</td><td>58.8</td><td>75.2</td><td>40.3 47.2</td><td>68.9</td></tr><tr><td>Improvement</td><td>+1.2</td><td>+5.6</td><td>+0.2</td><td>+1.7</td><td>+4.3 +1.8</td><td></td><td>+3.1 +2.5</td><td>-0.5</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3.5-9B)</td></tr><tr><td>Qwen3.5-9B</td><td>50.8</td><td>33.5</td><td>41.4</td><td>47.3</td><td>64.4</td><td>81.4</td><td>39.1 51.1</td><td>78.1</td></tr><tr><td>SpatialASM-9B</td><td>54.7</td><td>35.2</td><td>43.5</td><td>47.9</td><td>63.5</td><td>82.3</td><td>39.2 52.3</td><td>78.5</td></tr><tr><td>Improvement</td><td>+3.9</td><td>+1.7</td><td>+2.1</td><td>+0.6</td><td>-0.9</td><td>+0.9</td><td>+0.1 +1.2</td><td>+0.4</td></tr></table>



> 💡 **Table 1 解读**: 四个基座的 7 项空间推理平均分都提升：4B 43.8→47.3（+3.5，增益最大）、8B 46.2→49.1（+2.9）、30B-A3B 44.7→47.2（+2.5）、9B 51.1→52.3（+1.2）；代价在通用 VQA，4B 与 30B-A3B 各降 0.6 和 0.5。所以“只有中等规模才受益”不成立，Qwen3.5-9B 的 SR 平均 52.3 为全表最高。

Holi-spatial, VST-500k, and Mindcube-train [26, 60, 71] and generate a dataset with 128k reasoning trajectories, denoted SpatialASM-128k. In practice, we use the subset SpatialASM-66k constructed exclusively from Holispatial for tuning LVLMs. For RL (Sec. 3.3), we only use the original 2D observations collected from HoliSpatial and DL3DV, and automatically construct SpatialASM-RL-58k by employing the NVG. More details are provided in Appendix B.



> 💡 **数据集细分**: 共生成 128k 轨迹（SpatialASM-128k），但实际微调仅使用源自 HoliSpatial 的 66k 子集（SpatialASM-66k）。RL 阶段则利用 NVG 自动构建了 58k 数据的 SpatialASM-RL-58k。

Training setup. To internalize ASM capabilities into LVLMs, we adopt the two-stage post-training strategy: first SFT then GRPO. During SFT, we train four models from both the Qwen3-VL and the Qwen3.5 series [3, 48] on SpatialASM-66k for 10 epochs with the learning rate of 5e-6. For GRPO, we use a global batch size of 128 and a learning rate of 1e-6, and train for 300 steps. We fully fine-tune Qwen3-VL-4B and Qwen3-VL-8B, while applying LoRA (rank=32) to Qwen3-VL-30B-A3B and Qwen3.5-9B. Training details are provided in Appendix C.1.



> 💡 **训练策略**: 采用 SFT+GRPO 两阶段后训练。SFT 使用 SpatialASM-66k 训练 10 轮；GRPO 训练 300 步。大模型（30B/9B）使用 LoRA (rank=32)，小模型全量微调。

Evaluation setup. We evaluate all models on seven Spatial Reasoning (SR) benchmarks [22, 29, 35, 45, 60, 63, 72] and three general-VQA benchmarks [12, 65, 75]. All benchmarks except ESI-Bench are evaluated using VLMEvalKit [23]. For ESI-Bench, we adopt the passive-single setting. We ask the models to produce reasoning content first on the SR tasks, and to answer directly on the general-VQA tasks. Benchmark and evaluation details are provided in Appendix C.2 and Appendix C.3.



> 💡 **评估设置**: 涵盖 7 个空间推理和 3 个通用 VQA 基准。除 ESI-Bench 外均用 VLMEvalKit。空间任务要求先输出推理过程再作答，通用任务直接作答。

## 4.1. Preliminary Analysis of Explicit ASM

Before internalizing ASM into LVLMs, we first wonder whether ASM is an efective reasoning paradigm and whether existing LVLMs lack this capability. Like previous studies in Fig. 2(b), we conduct a preliminary experiment on the external ASM settings, where LVLMs first plan question-relevant viewpoints, invoke the NVG to synthesize observations from these viewpoints, and then reason jointly over the original and generated images. We sample 200 questions from each of the Among and Around subtasks of MindCube and report the results of three reasoning methods: (i) Vanilla: LVLMs directly reason over the original observations; (ii)



> 💡 **初步分析动机**: 为验证显式 ASM 是否有效、现有 LVLM 是否缺这项能力，作者在 MindCube 的 Among 与 Around 上各采样 200 题，比三种外部 ASM 用法：Vanilla、Random-Plan、VLM-Plan，作为内化 ASM 之前的对照。

Table 2: Accuracy and average inference time of diferent methods on benchmark subtasks. (i) Vanilla: the LVLM reasons directly from the input observations alone, and the reasoning duration is reported; (ii) + External ASM: Similar to Sec. 4.1, the LVLM externally invokes SpatialASM-NVG to help reasoning. The time to synthesize novel-view observations is reported.
<table><tr><td rowspan="3">Subtask</td><td colspan="4">Qwen3-VL-8B</td><td colspan="3">SpatialASM-8B</td></tr><tr><td>Vanilla</td><td></td><td>+ External ASM</td><td></td><td>Vanilla</td><td>+ External ASM</td></tr><tr><td>|Accuracy T(s)</td><td></td><td>|Accuracy TNvG (s)</td><td></td><td>|Accuracy T(s)</td><td>|Accuracy TnvG(s)</td></tr><tr><td>MindcubeAmong</td><td>33.0</td><td>12.6</td><td>42.0</td><td>57.6</td><td>39.5</td><td>45.0</td></tr><tr><td>MindcubeAround</td><td>52.0</td><td>7.5</td><td>45.5 38.1</td><td>58.0</td><td>18.6 7.2</td><td>56.8 61.5 46.8</td></tr></table>



> 💡 **Table 2 解读**: 该表对比 Vanilla 与外部调用 SpatialASM-NVG（+External ASM）的准确率和耗时，并对已内化的 SpatialASM-8B 做同样对比，NVG 合成时间单独报告。可确读的一例是 Qwen3-VL-8B 在 MindCube Among 上 33.0→42.0；但表体在 OCR 中存在列错位（Around 行的 52.0、7.5、45.5、38.1 等数字无法可靠归列），引用具体数值前应回原文表格核对。

Random-Plan: randomly selected target viewpoints are passed to SpatialASM-NVG, and (iii) VLM-Plan: LVLMs are explicitly prompted to plan question-relevant viewpoints before invoking SpatialASM-NVG.



> 💡 **方法定义**: 这里补全第 4.1 节的对照设定：(ii) Random-Plan 随机选取目标视角交给 SpatialASM-NVG，(iii) VLM-Plan 先让 LVLM 显式规划与问题相关的视角再调用 NVG。加上 (i) Vanilla 直接推理，构成外部 ASM 的三档对照。

By comparing Vanilla with the other two settings in Fig. 4, the planning-based external ASM paradigm generally improves accuracy over vanilla reasoning, which demonstrates that viewpoint planning and imagination constitute a generally efective paradigm for complex spatial reasoning. Moreover, LVLM-based planning consistently matches or outperforms random planning, indicating that existing LVLMs already possess a certain ability to identify informative viewpoints when explicitly prompted. Nevertheless, this ability is not spontaneously activated during direct LVLM reasoning. In other words, existing LVLMs are able to execute view-planning only under an explicitly-prompted inference pipeline. ASM has not yet become an intrinsic reasoning capability that they can autonomously invoke when needed for certain complicated spatial tasks.



> 💡 **外部ASM范式有效性**: 对比 Fig.4 的三个设置可见，规划式外部 ASM 总体优于 vanilla 直接推理，说明视点规划加想象是一条普遍有效的空间推理路径。但 LVLM 只在显式提示的推理流程里才执行视点规划，随机规划与 LVLM 规划结果相当也说明这一能力尚未被自发调用。

Furthermore, Qwen3-VL-8B drops under both random and LVLM-based planning, suggesting that relatively small models still lag behind in both robust planning for diverse spatial tasks and long-context multimodal reasoning. It further motivates internalizing ASM through training so that models can learn when and how to employ this reasoning paradigm more reliably.



> 💡 **小模型为何掉点**: Qwen3-VL-8B 在随机规划和 LVLM 规划下都出现性能下降，作者据此推断小模型在鲁棒规划与长上下文多模态推理两方面都还不够。这一现象支持（而非证明）通过训练把 ASM 内化的动机。

## 4.2. Main Results

Overall SR performance gains. As shown in Table 1, SpatialASM improves spatial reasoning on both Qwen3-VL and Qwen3.5 architectures. The Qwen3-VL models of three diferent scales, including dense models and an mix-of-experts (MoE) model, yield consistent performance gains on all seven SR benchmarks after applying our approach. In particular, the 4B model achieves an average improvement of +3.5%, with notable gains on Mindcube (+5.2%) and Spatial457 (+5.6%). In contrast, prior spatial models exhibit uneven performance across benchmarks. For example, Sensenova-8B improves significantly on Mindcube (+10.0%), but exhibits drops by 8.7% on EmbSpa. This suggests that their gains may depend more on benchmark-specific task formats, whereas SpatialASM elicits a more universal and transferable spatial capability.



> 💡 **主结果提升**: Table 1 显示 SpatialASM 在 Qwen3-VL 各规模（含 dense 与 MoE）上七个 SR 基准一致提升，4B 模型平均 +3.5%，Mindcube +5.2%、Spatial457 +5.6%。对照的 Sensenova-8B 在 Mindcube 上 +10.0% 却在 EmbSpa 掉 8.7%，作者据此认为其增益更依赖特定基准格式，而 SpatialASM 激发的空间能力更通用、可迁移。

Analysis of spatial capabilities. The largest gains generally occur on more challenging benchmarks (e.g., MindCube, MMSI, and Spatial457 ) which require the comprehensive use of multiple reasoning capabilities, such as spatial mental modeling, cross-view correspondence, complex 6D reasoning, etc. These tasks benefit from the learned ability to plan informative viewpoints and imagine the corresponding observations. SpatialASM also yields modest gains on relatively perception-centric SR benchmarks (e.g., 3DSR, Embspa), indicating that the knowledge learned from ASM reasoning trajectories and the RL tasks improves the general understanding of the 3D space.



> 💡 **增益分布**: 最大增益集中在 MindCube、MMSI、Spatial457 这类需要空间心理建模、跨视角对应、6D 推理等综合能力的困难基准，作者归因于学到的视点规划与想象能力。3DSR、Embspa 等偏感知的基准也有小幅提升，说明 ASM 轨迹与 RL 任务带来的知识改善了对 3D 空间的一般理解。

General Visual Capabilities. SpatialASM preserves the general visual capabilities of LVLMs, with changes in the average general-VQA accuracy remaining within 0.6% across all four backbones. Importantly, SpatialASM-8B outperforms the two 8B counterparts in representative spatial models, where HoliSpatial experiences an average degredation of 1.3% on all three benchmarks after SFT. We attribute this result partly to view-imagination RL, which exposes LVLMs to diverse visual observations and high-quality, geometry-filtered NVG outputs. Consequently, the model learns from rich visual supervision rather than merely memorizing the reasoning patterns of the SFT trajectories. The models’ performance on each benchmark is shown in Appendix C.4.



> 💡 **通用视觉保持**: 四个骨干上通用 VQA 平均准确率变化都在 0.6% 以内，且 SpatialASM-8B 优于两个 8B 对照模型——HoliSpatial 在 SFT 后平均退化 1.3%。作者把这一结果部分归因于 view-imagination RL 提供了多样视觉观测与几何过滤后的 NVG 输出，使模型学到丰富视觉监督而非只记住 SFT 轨迹的推理模式。

Comparison with external ASM. As shown in Table 2, external ASM is not always beneficial for the baseline model. Introducing redundant visual evidence or unnecessary view-planning CoT may degrade performance (52.0% to 45.5% on Around). In contrast, after internalization, SpatialASM-8B improves under



> 💡 **外部 ASM 的负面例子**: Table 2 显示外部 ASM 对基线模型并非总有益，冗余视觉证据或不必要的 view-planning CoT 会造成退化，Around 上从 52.0% 降到 45.5%。本段后半句只说到内部化后的 SpatialASM-8B 在 vanilla 推理下反超基线，具体数字在下一段给出。

Table 3: Comparison of diferent novel-view generators under external ASM paradigm.
<table><tr><td>Methods</td><td> $| \mathsf { A c c } _ { \mathsf { a m o n g } } \ ^ { \cdot }$  个</td><td> $\mathtt { A c c } _ { \mathrm { a r o u n d } }$ </td><td>↑GeoP↓</td></tr><tr><td>Qwen3.5-35BA3B</td><td>42.5</td><td>66.0</td><td>/</td></tr><tr><td>+GPT-Image2</td><td>42.0</td><td>69.0</td><td>0.141</td></tr><tr><td>+SpatialEdit</td><td>46.0</td><td>68.0</td><td>0.110</td></tr><tr><td>+SVC</td><td>48.5</td><td>68.5</td><td>0.087</td></tr><tr><td>+SpatialASM-NVG</td><td>55.5</td><td>70.5</td><td>0.118</td></tr><tr><td>Kimi-k2.5</td><td>52.5</td><td>69.0</td><td>/</td></tr><tr><td>+GPT-Image2</td><td>52.0</td><td>71.0</td><td>0.157</td></tr><tr><td>+SpatialEdit</td><td>52.5</td><td>69.0</td><td>0.138</td></tr><tr><td>+SVC</td><td>52.5</td><td>74.0</td><td>0.072</td></tr><tr><td>+SpatialASM-NVG</td><td>68.0</td><td>80.5</td><td>0.099</td></tr></table>



> 💡 **Table 3 解读**: 在外部 ASM 范式下替换不同的新视角生成器，SpatialASM-NVG 在两个骨干（Qwen3.5-35B-A3B、Kimi-k2.5）的 Among 与 Around 上准确率均为最高，如 Kimi 下 68.0%/80.5%。GeoP（越低越好）上它优于 GPT-Image2 与 SpatialEdit，但高于 SVC 的 0.087/0.072，也就是几何精度并非全面最优，不能把该表概括为准确率与 GeoP 双双领先。

Table 4: Ablations on training strategies and GeoPbased data filtering strategies.
<table><tr><td colspan="5">Methods |Mindcube EmbSpa ViewSpa ESI Avg.</td></tr><tr><td>Qwen3-VL-4B</td><td>33.9</td><td>72.3</td><td>43.2</td><td>36.5 46.5</td></tr><tr><td colspan="5">SFT with ASM trajectories</td></tr><tr><td>w/o filter</td><td>36.4</td><td>75.0</td><td>45.3</td><td>36.8 48.4</td></tr><tr><td>with filter</td><td>38.7</td><td>74.6</td><td>46.2</td><td>40.2 49.9</td></tr><tr><td colspan="5">View-imagination RL</td></tr><tr><td>w/o filter with filter</td><td>34.2 35.1</td><td>75.7 76.3</td><td>43.8 43.6</td><td>39.2 48.2 40.1 48.8</td></tr><tr><td colspan="5">SFT + RL</td></tr><tr><td>SpatialASM-4B|</td><td>39.1</td><td>76.5</td><td>46.2</td><td>40.6 50.6</td></tr></table>



> 💡 **消融实验表**: Table 4 对比不同训练策略，SFT+RL 的 SpatialASM-4B 平均分最高（50.6），优于单独 SFT（with filter 49.9）和单独 RL（with filter 48.8）。GeoP 数据过滤在两种训练方式下都提升了平均分，但个别指标会略降，例如 SFT 的 EmbSpa 74.6 对 75.0。

vanilla reasoning on both subtasks without noticeable increase on the inference duration. Most importantly, combining SpatialASM-8B with external ASM yields the highest accuracy on both Among (45.0%) and Around (61.5%). SpatialASM strengthens the model’s intrinsic ability to plan viewpoints and reason about spatial evidence, while external NVG provides additional informative views. Employing them together achieves higher accuracy than either approach alone, suggesting that internalization and external tool-use provide complementary gains.



> 💡 **互补增益结论**: 把 SpatialASM-8B 与外部 ASM 结合后在 Among（45.0%）和 Around（61.5%）上都拿到最高准确率，且相对 vanilla 推理未带来明显的推理耗时增加。作者据此认为内部化的视点规划能力与外部 NVG 提供额外信息视图是互补的，合起来比任一单独方案都强。

## 4.3. Ablation Studies

Comparison of novel-view generators. Following the experimental design in Sec. 4.1, we execute the external ASM reasoning paradigm with diferent novel-view generators and evaluate on the same Mindcube subsets as in Sec. 4.1. The accuracy as well as the median GeoP value of all generated images are reported. As shown in Table 3, compared with other world models, SpatialASM-NVG achieves the highest accuracy for both backbone LVLMs on both Mindcube subsets.



> 💡 **NVG 对比设置**: 沿用 Sec. 4.1 的实验设计，在相同 Mindcube 子集上比较不同新视角生成器，并同时报告准确率与所生成图像 GeoP 的中位数。Table 3 的结论是 SpatialASM-NVG 在两个骨干 LVLM、两个子集上准确率最高，优于其他世界模型；GeoP 方面原文未宣称最优，表中该数值高于 SVC。

Interestingly, SpatialASM-NVG does not achieve the best GeoP as SVC obtains lower GeoP values for both LVLMs, but it consistently yields substantially higher reasoning accuracy. This observation suggests that geometric consistency alone does not fully determine the utility of generated views for downstream spatial reasoning. We attribute this to our allocentric form of viewpoint representation. Rather than describing the target viewpoint through a sequence of quantified egocentric motions, LVLMs can designate the viewpoint through a single query, and the corresponding location of the viewpoint is explicitly marked in the original input image. It makes the spatial correspondence between the original scene and the generated observation easier for LVLMs to understand during final multi-image reasoning. Therefore, our allocentric-view generation provides not only reliable novel views, but also auxiliary evidence that is more directly aligned with the reasoning objective.



> 💡 **几何一致性与效用**: 注意 GeoP 越低越好——SVC 在两个 LVLM 上 GeoP 更低，但 SpatialASM-NVG 的推理准确率明显更高，说明几何一致性并不能完全决定生成视图对下游空间推理的价值。作者把这一点归因于其 allocentric（环境中心式，区别于 egocentric 自我中心）视角表示：LVLM 用单次查询指定视角，且该视角对应位置直接标在原输入图上，比一串量化的自我中心运动更容易建立原始场景与生成视图的空间对应，因而提供的是更贴近推理目标的辅助证据。

Training and Data recipe. Table 4 compares the independent efect of each training stage and evaluates the filtering strategy for data preparation. All of the four single-stage variants are initialized from the baseline LVLM. For both SFT and RL, applying GeoP-based filtering improves the average performance on the four SR benchmarks, indicating that GeoP efectively removes geometrically distorted novel views and prevents unreliable visual evidence from being incorporated into ASM reasoning trajectories or RL supervision.



> 💡 **数据过滤有效性**: Table 4 验证了 GeoP 过滤策略在 SFT 和 RL 阶段均能提升四个基准的平均性能。这表明该机制有效剔除了几何畸变的生成视图，防止不可靠的视觉证据污染推理轨迹或强化学习监督信号。

The two training stages also exhibit distinct but complementary efects. SFT with SpatialASM-66k (with filter) produces strong gains on tasks requiring explicit viewpoint transformation and multi-step spatial reasoning, such as MindCube (+4.8%) and ViewSpa (+3.0%), which verifies the high quality of our ASM trajectories and the reliability of the proposed CoT synthesis engine from answer-only spatial QAs. In contrast, view-imagination RL is especially efective on the more perception-oriented EmbSpa benchmark and outperforms the setting of applying SFT alone. Combining SFT and RL achieves the best overall accuracy, suggesting a clear synergy where SFT primarily teaches the structured ASM reasoning pattern while RL further strengthens viewpoint-conditioned spatial perception and imagination. Some detailed design is given in Appendix C.5.



> 💡 **SFT与RL协同效应**: SFT 侧重结构化推理模式，显著提升需显式视角变换的任务（如 MindCube +4.8%）；RL 则增强感知导向任务中的视角想象能力（如 EmbSpa）。两者结合达到最佳整体准确率，证实了分工互补的有效性。

