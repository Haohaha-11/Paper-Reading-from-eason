[← 返回 README](../README.md)

## C.1. Training Details

SpatialASM-NVG training. We finetune FLUX.2-klein-9B [6], an image-editing model for real-time textconditioned image generation, on SpatialASM-edit140k to derive our SpatialASM-NVG. Drawing on the training strategy in DifThinker [28], we use mean squared error (MSE) as our training objective and train for 3 epochs. We only finetune its Difusion Transformer (DiT) with Low-Rank Adaptation (LoRA) and we set LoRA rank to 512. For GeoP calculation, we use Mapanything [32] as the feed-forward 3D reconstruction model � to reconstruct the 3D point clouds.



> 💡 **训练配置**: SpatialASM-NVG基于FLUX.2-klein-9B微调，仅对DiT部分应用LoRA（rank=512），使用MSE损失训练3个epoch。GeoP计算依赖Mapanything进行3D点云重建。

View-imagination RL. During GRPO, we apply KL regularization with weight 1e-2. And we rollout 5 responses in each group with the temperature set to 1.0. Our reward is composed of two items: accuracy reward and format reward, with the weight of 0.9 and 0.1. Accuracy reward is a binary reward which is assigned to 1 only when the final option is correct. Format reward is also binary which requires the LVLMs to reason first and put the option letter within <answer> and </answer>.



> 💡 **RL奖励机制**: GRPO训练中采用KL正则化（权重1e-2），每组采样5次（温度1.0）。奖励由准确率（权重0.9，二元）和格式合规性（权重0.1，要求先推理后在标签内输出选项）组成。

Table 7: Performance comparison on three general-VQA benchmarks.
<table><tr><td colspan="4">Models MMMUvAL MMStar RealWorldQA|Average</td></tr><tr><td colspan="4">Representative Spatial Models</td></tr><tr><td>SpaceR-7B 51.8 VST-RL-7B 51.2 HoliSpatial-8B 56.0 SenseNova-8B</td><td>61.4 63.7 65.4 64.5</td><td>65.0 71.4 68.0 71.6</td><td>59.4 62.1 63.1 63.7</td></tr><tr><td colspan="4">55.1</td></tr><tr><td>Baselines &amp; Our Model (Qwen3-VL-4B) Qwen3-VL-4B 54.7</td><td>65.1 64.0</td><td>71.3 71.6</td><td>63.7 63.1</td></tr><tr><td colspan="4">SpatialASM-4B 53.8 Baselines &amp; Our Model (Qwen3-VL-8B)</td></tr><tr><td>Qwen3-VL-8B 54.1 SpatialASM-8B 56.1</td><td>66.0 67.1</td><td>73.2 71.8</td><td>64.4 65.0</td></tr><tr><td colspan="4">Baselines &amp; Our Model (Qwen3-VL-30BA3B)</td></tr><tr><td>Qwen3-VL-30BA3B SpatialASM-30BA3B</td><td>62.7 70.7 62.1 68.6</td><td>74.8 76.1</td><td>69.4 68.9</td></tr><tr><td colspan="4">Baselines &amp; Our Model (Qwen3.5-9B)</td></tr><tr><td>Qwen3.5-9B</td><td>73.3 78.6</td><td>82.4</td><td>78.1</td></tr><tr><td>SpatialASM-9B</td><td>75.3 78.5</td><td>81.7</td><td>78.5</td></tr></table>



> 💡 **Table 7 解读**: 在 MMMUval、MMStar、RealWorldQA 三个通用 VQA 基准上按 4B/8B/30B/9B 四档与同尺寸 Qwen3-VL 对比：8B 档平均 65.0 vs 基线 64.4、9B 档 78.5 vs 78.1，略有提升；但 4B 档平均 63.1 反而低于基线 63.7。提升随规模并不一致，30B 行的数值在所给表格文本中排列错乱，无法逐项核对。

Prompt. For both SFT and RL, we append a system prompt to the end of the original question, guiding LVLMs to output first reasoning content and then the final answer. The system prompt is: First think and then give the final answer. Put the option letter within <answer> and </answer>.



> 💡 **提示词设计**: SFT和RL阶段均追加特定系统提示，强制LVLM遵循“先推理、后答案”且答案需包裹在<answer>标签内的格式规范，确保输出可解析。

