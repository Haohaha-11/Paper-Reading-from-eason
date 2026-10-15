[← 返回 README](../README.md)

## A.1.2 TRAINING DATA



> 💡 **小节定位**: 本小节（A.1.2 TRAINING DATA）只交代训练数据来源与规模配置，用于支撑正文 Section 5.2（1.7B）与 Section 5.3（4B/8B）的实验可复现性，不涉及方法结构本身的说明。（AI 批注）

Main experiments. For the 1.7B experiments (Section 5.2), we regenerate responses to the AM-Qwen3-Distilled and Nemotron-Agentic-v1 prompts (a-m-team, 2025; NVIDIA, 2025) with Qwen3-8B. We choose this teacher because it gives the most accurate student among the teachers we compare (Appendix B.1).



> 💡 **机制拆解**: 本段说明 1.7B 实验（Section 5.2）的训练数据生成方式：针对 AM-Qwen3-Distilled 与 Nemotron-Agentic-v1 两类提示，用 Qwen3-8B 重新生成回答（regenerate responses）。原文并未出现“蒸馏范式”一词，用教师模型重生成回答只能视为作者隐含的教师-学生式数据生成，属推断而非原文表述。（AI 批注）
> 
> 💡 **问题动机 / 证据边界**: 选择 Qwen3-8B 的原文依据是“it gives the most accurate student among the teachers we compare”，即在与作者对比过的教师中，它训出的学生最准确，详细对比见 Appendix B.1。这是本段给出的唯一理由。注意两点校正：其一，本段并未说明 AM-Qwen3 / Nemotron-Agentic 各自覆盖哪些领域（“数学/代码/QA + 工具使用”来自下一段 block 2 的描述，不能前移）；其二，“旨在让 1.7B 学生具备多任务能力”是初稿补出的目的，原文未写，已标为推断。（AI 批注）

Additional scales. For the experiments in Section 5.3, we train the 4B and 8B backbones on the original AM-Qwen3-Distilled responses from Qwen3-235B-A22B (a-m-team, 2025), covering math, code and QA, together with tool use samples from Nemotron-Agentic-v1 (NVIDIA, 2025). We exclude samples whose combined prompt and response exceed 16,384 tokens. The 4B and 8B models are trained on 8B and 16B tokens, respectively. At each size, TaH2 uses $M \ : = \ : 2$ and the same training recipe as Standard.



> 💡 **实验设置与约束**: 本段描述 Section 5.3 中 4B/8B 骨干的训练配置，可拆为四组事实：
> - **数据来源**：直接使用 Qwen3-235B-A22B 生成的原始（original）AM-Qwen3-Distilled 回答，覆盖 math、code、QA，另加 Nemotron-Agentic-v1 的工具使用样本。原文用词是“original ... responses”，即复用已有数据，而非像 1.7B 那样重新生成。
> - **长度过滤**：排除 prompt 与 response 合计超过 16,384 tokens 的样本。原文只给规则、未给原因，“适配上下文窗口 / 优化训练效率”属推断。
> - **训练量**：4B 用 8B tokens，8B 用 16B tokens。
> - **方法配置**：每个规模下 TaH2 均取 $M := 2$，训练配方与 Standard 相同。
> 
> 💡 **推断边界校正**: 初稿称 Qwen3-235B-A22B 为“更强的教师模型”、并称“学生变大后直接复用最强模型数据更有效 / 为节省成本”，这两点原文均未说明——本段只交代数据来自哪个模型，没有比较教师强弱，也没有给出选择动机，故应标为推断/待证；同样，“符合 scaling law 趋势”与“体现跨尺度通用性”也不是原文表述，是读者层面的解读。可确证的只有：数据来源、领域覆盖、16,384 阈值、8B/16B tokens、$M := 2$ 与同配方这五项。（AI 批注）

