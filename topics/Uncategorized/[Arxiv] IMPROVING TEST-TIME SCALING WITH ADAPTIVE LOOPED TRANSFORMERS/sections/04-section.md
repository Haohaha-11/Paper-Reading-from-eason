[← 返回 README](../README.md)

## 2 RELATED WORK

Test-time scaling. Test-time compute can be increased by producing more tokens or repeatedly applying a shared block in latent space. Token-based methods generate longer chains of thought (Jaech et al., 2024; Guo et al., 2025), with reasoning length controlled through test-time budget forcing or RL (Muennighoff et al., 2025; Aggarwal & Welleck, 2025). They also explore multiple reasoning paths through repeated sampling (Brown et al., 2024) or tree search (Snell et al., 2024; Wu et al., 2024b). In latent space, latent optimization replaces intermediate text with continuous representations (Hao et al., 2024; Li et al., 2025), while looped transformers repeatedly apply shared layers before generating each token (Geiping et al., 2025a; Zhu et al., 2025). We study how looping changes accuracy–compute scaling as output budgets increase, comparing post-trained looped and non-looped models at matched decoding FLOPs (Section 3).



> 💡 **AI 批注 | 机制拆解与定位**
> 本段把 Test-time scaling 拆成两条路径：
> - **Token-based**：生成更长的 chain of thought（Jaech et al., 2024; Guo et al., 2025），用 test-time budget forcing 或 RL 控制推理长度（Muennighoff et al., 2025; Aggarwal & Welleck, 2025），并通过 repeated sampling（Brown et al., 2024）或 tree search（Snell et al., 2024; Wu et al., 2024b）探索多条推理路径。
> - **Latent-space**：latent optimization 用连续表示替代中间文本（Hao et al., 2024; Li et al., 2025）；looped transformers 在每个 token 生成前重复应用共享层（Geiping et al., 2025a; Zhu et al., 2025）。
> 
> 原文明确写出本文的研究问题："how looping changes accuracy–compute scaling as output budgets increase"，并指出将在 Section 3 中 "comparing post-trained looped and non-looped models at matched decoding FLOPs"。这里有两个原文显式给出的条件：比较对象是 **post-trained** 的 looped 与 non-looped 模型，且 **matched decoding FLOPs**。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: matched decoding FLOPs 的作用是什么？
> > - A: 原文仅写明 "comparing post-trained looped and non-looped models at matched decoding FLOPs"。可合理推断这是为了在同等解码算力下隔离循环结构本身的贡献，但原文并未展开说明这一公平性论证，故此推断待证，具体控制方式需看 Section 3 或 Appendix E。
> 
> > 💡 **定位批注**: 本节把本文放进 "latent-space 测试时扩展" 的分支，但强调本文关注的是 **looping 与 output budget 的交互**，而非单纯提出一种新的 test-time 方法。

Looped transformers. Looped transformers increase effective depth through parameter sharing (Dehghani et al., 2019; Yang et al., 2023; Saunshi et al., 2025). Architectures differ in the span they repeat: Huginn loops a middle block (Geiping et al., 2025a), Ouro the full stack (Zhu et al., 2025), and Loopies individual layers (Gao et al., 2026). Recent scaling studies examine recurrence under matched parameter counts (Prairie et al., 2026), matched per-token FLOPs (Schwethelm et al., 2026b), or both, as in SMELT (Wang et al., 2026b). Recurrence can also be introduced into pretrained non-looped models through layer sharing (Bae et al., 2024) or curriculum-based adap tation (McLeish et al., 2025). TaH2 builds on this post-training setting to learn token-dependent iteration depths.



> 💡 **AI 批注 | 相关工作谱系与本文定位**
> 本段按两个维度整理 looped transformers：
> - **重复跨度**：Huginn 循环中间块（Geiping et al., 2025a），Ouro 循环全栈（Zhu et al., 2025），Loopies 循环单个层（Gao et al., 2026）。
> - **缩放研究的匹配设定**：matched parameter counts（Prairie et al., 2026）、matched per-token FLOPs（Schwethelm et al., 2026b），或两者同时，即 SMELT（Wang et al., 2026b）。⚠️ 初稿只概括为 "参数匹配 vs FLOPs 匹配" 两档，遗漏了原文的 "or both, as in SMELT" 这一档。
> - **向预训练非循环模型引入递归**：layer sharing（Bae et al., 2024）或 curriculum-based adaptation（McLeish et al., 2025）。
> 
> TaH2 的定位原文只有一句："TaH2 builds on this post-training setting to learn token-dependent iteration depths." 即 TaH2 建立在上一句所述的 post-training 设定（把递归引入预训练非循环模型）之上，目标是学习 token 依赖的迭代深度。原文在此并未展开 TaH2 的具体机制，具体做法在下一段及后续章节才出现。
> 
> > 💡 **批注边界说明**: "这为后续自适应计算部分埋下伏笔" 属于对行文结构的观察，不是原文陈述。同样，"从预训练非循环模型出发引入循环能力" 是对 "post-training setting" 的解释性展开，原文此段未逐字展开该含义。

Adaptive computation. Adaptive computation can operate along width, by selecting experts within layers (Fedus et al., 2022), or depth, through early exit (Schuster et al., 2022), layer skipping (Raposo et al., 2024) or variable iteration counts (Graves, 2016; Banino et al., 2021). Within looped models, methods differ in how they learn token-dependent iteration depth. MoR trains routers under capacity constraints (Bae et al., 2025), while pondering methods combine prediction loss with budget or confidence penalties (Li et al., 2026a; Song et al., 2026; Zeng et al., 2026). Other approaches learn stopping decisions from terminal rewards (Kuo et al., 2026) or explicit labels: TaH uses offline mismatch labels in separate training stages (Fu et al., 2025b), and Ouro’s second stage supervises its gate with token-level loss improvements while freezing the backbone (Zhu et al., 2025). TaH2 jointly post-trains the backbone and decider through lookahead depth supervision, deriving online token-level labels from measured iteration gains along decider-selected routes. Appendix E provides further comparisons and discusses scaling, state design and serving.



> 💡 **AI 批注 | 自适应计算策略对比**
> 原文先把 adaptive computation 分为两个方向：**width**（层内选择专家，Fedus et al., 2022）与 **depth**（early exit, Schuster et al., 2022；layer skipping, Raposo et al., 2024；variable iteration counts, Graves, 2016; Banino et al., 2021）。
> 
> 在 looped models 内部，学习 token-dependent iteration depth 的方法分为：
> 1. **Router-based**：MoR 在容量约束下训练 router（Bae et al., 2025）。
> 2. **Pondering-based**：把预测损失与预算或置信度惩罚结合（Li et al., 2026a; Song et al., 2026; Zeng et al., 2026）。
> 3. **监督式**：从 terminal rewards 学习停止决策（Kuo et al., 2026），或使用显式标签——TaH 用离线 mismatch labels 且分阶段训练（Fu et al., 2025b）；Ouro 第二阶段在冻结 backbone 的同时，用 token-level loss improvements 监督其 gate（Zhu et al., 2025）。
> 
> **TaH2 的位置**：原文写明 "TaH2 jointly post-trains the backbone and decider through lookahead depth supervision, deriving **online** token-level labels from measured iteration gains along decider-selected routes." 这里最关键的机制细节是：标签是 **online** 的，来源是沿 **decider-selected routes** 测得的 **iteration gains**，且 backbone 与 decider 是 **联合 post-train**。这与 TaH 的 offline mismatch labels + 分阶段训练、Ouro 的冻结 backbone 形成对照。⚠️ 初稿遗漏了 "online labels 来自 decider 所选路径上测得的 iteration gains" 这一核心机制，需补出。
> 
> 原文最后一句还指出 Appendix E 提供进一步对比，并讨论 scaling、state design 与 serving。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: TaH2 与 TaH、Ouro 的关键差异是什么？
> > - A: 原文直接支撑的差异有三点——标签来源（online vs offline）、训练方式（joint post-train vs separate stages）、backbone 是否冻结（TaH2 联合训练 vs Ouro 冻结）。至于 "端到端联合优化"、"主干可适应新的循环逻辑" 这类表述，原文未使用 end-to-end 一词，属于合理但未明写的推断，应标注为推断。

