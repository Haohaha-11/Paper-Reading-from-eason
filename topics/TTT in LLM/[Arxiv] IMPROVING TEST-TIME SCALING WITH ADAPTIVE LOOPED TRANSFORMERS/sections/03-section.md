[← 返回 README](../README.md)

## How to post-train looped LLMs to outperform non-looped LLMs at the same test-time compute?

Fixed-depth iteration spends extra compute on every token, although many tokens gain little or even get worse (Section 3.2). Ouro’s adaptive exit gate improves compute efficiency, but not enough to surpass Standard. We introduce TaH2 to improve test-time scaling by learning which tokens benefit from additional iterations and allocating depth accordingly. The backbone and an iteration decider are jointly post-trained through lookahead depth supervision: depth labels are derived online from changes in prediction loss, and a cost-sensitive loss supervises each depth decision (Figure 2). Unlike TaH’s staged training with offline mismatch labels (Fu et al., 2025b), TaH2 supervises depth decisions using actual iteration gains measured on the current backbone throughout training. In addition, an updater provides input injection between iterations (Geiping et al., 2025a), and stopping probabilities weight the predictions across executed depths (Zeng et al., 2026).



> 💡 **机制拆解**: 本段是 TaH2 的总体介绍，动机链条为：固定深度迭代对每个 token 都用相同额外算力，但很多 token 收益很小、甚至变差（指向 Section 3.2）；Ouro 的自适应退出 gate 虽提升算力效率，但仍不足以超过 Standard。TaH2 的路径是学习「哪些 token 值得继续迭代」并据此分配深度。
> 
> 三个组件按证据可拆为：(1) backbone 与 iteration decider 通过 lookahead depth supervision 联合后训练——深度标签在线地由 prediction loss 的变化导出，再用 cost-sensitive loss 监督每次深度决策（对应 Figure 2）；(2) updater 在迭代之间提供 input injection（Geiping et al., 2025a）；(3) stopping probabilities 对已执行各深度的预测做加权（Zeng et al., 2026）。
> 
> 与 TaH（Fu et al., 2025b）的差别被原文明确限定为监督信号的来源：TaH 用离线 mismatch 标签做分阶段训练，TaH2 则在整个训练过程中用「当前 backbone 上实测的迭代增益」来监督深度决策。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: TaH2 与 TaH 的核心差别是什么？
> > - A: 不是网络结构，而是深度标签从「离线错配标签」变为「训练中在当 前 backbone 上在线测得的真实迭代增益」，因而监督信号与骨干同步演化。原文仅在本段给出这一对比，未在本节展开标签的具体计算方式。

We post-train Qwen3-Base models (Yang et al., 2025) on general-domain data and evaluate them on math, code, QA and tool use benchmarks. On AIME24–26 at 1.7B, TaH2 improves the accuracy– compute slope by 53% over the non-looped baseline (2.74 vs. 1.79 points per doubling of decoding FLOPs). With evaluation extended to 32K tokens, TaH2 exceeds Standard’s peak accuracy by about 3.4 points at matched decoding FLOPs (Figure 4b). Existing looped models largely plateau as iteration depth increases, while TaH2’s gain over the non-looped baseline grows from +2.8 points at M = 2 to +3.9 points at M = 8 (Figure 1b). TaH2’s gains persist at larger scales (4B and 8B) and generalise beyond math to code, QA and tool use (Table 3).



> 💡 **实验证据与关键数字**: 实验对象为 Qwen3-Base（Yang et al., 2025），在通用域数据上后训练，评测覆盖数学、代码、QA 与工具使用。
> 
> 可核对的具体数字：
> - 1.7B 规模、AIME24–26 上，TaH2 相对非循环 baseline 把 accuracy–compute 斜率提升 53%（2.74 vs. 1.79 points per doubling of decoding FLOPs）。注意该数字的度量单位是「解码 FLOPs 每翻倍所获得的点数」。
> - 约 3.4 点的超额是**带条件的**：原文限定为「With evaluation extended to 32K tokens」且在 matched decoding FLOPs 下、比较对象是 Standard 的峰值精度（Figure 4b）。批读时不能省略 32K token 这一评测条件。
> - 深度维度的对比：现有 looped 模型随迭代深度增加大多趋于平台（largely plateau），而 TaH2 相对**非循环 baseline（Standard）**的增益从 M = 2 的 +2.8 点增长到 M = 8 的 +3.9 点（Figure 1b）。这里比较基准是 Standard，不是「其他 looped 模型」。
> - 更大规模（4B、8B）与数学之外的代码、QA、工具任务上增益仍存在（Table 3）。
> 
> > 💡 **推断标注**: 本段只给出上述点估计，未报告方差、随机种子或显著性检验；「增益持续扩大」的可信度取决于 Figure 1b/4b 与 Table 3 的误差棒，本节文本本身不足以判定统计显著性。

We summarise our contributions as follows.



> 💡 **结构提示**: 这是贡献列表的引导句（"We summarise our contributions as follows."），本身不含技术主张。其后三条贡献分别对应：循环模型的测试时缩放现象（block 4）、TaH2 的自适应循环后训练方法（block 5）、测试时与深度缩放上的量化收益（block 6）。批读时把它当作列表入口，不必在此处做机制解读。

• Test-Time Scaling of Looped Models. We study how looping changes accuracy–compute scaling and find that, although post-trained looped models often yield steeper slopes, they underperform Standard at matched test-time compute.



> 💡 **问题动机与核心矛盾**: 第一条贡献界定的现象是——后训练过的 looped 模型在 accuracy–compute 上往往给出更陡的斜率（steeper slopes），但在 matched test-time compute 下仍不如 Standard。也就是说「斜率更陡」与「同算力下更强」是两件被原文分开陈述的事，本段并未给出两者不一致的原因解释。
> 
> > 💡 **推断标注**: 初稿在此处补了一句「这揭示了单纯依靠更多迭代并不等于更高效，从而引出需要智能决定何时停止迭代的必要性」。这一因果链在本节文字中没有被明写，只能视为对后续方法动机的合理推断，而非原文论断；机制归因需回到 Section 3.2 与 Figure 1/4 的正文证据。

• Adaptive Looped Post-training. We propose TaH2, which jointly post-trains the backbone and an iteration decider, directly supervising depth decisions with online labels indicating whether further iteration improves prediction.



> 💡 **机制拆解**: 第二条贡献陈述 TaH2 的方法骨架——联合后训练 backbone 与 iteration decider，并用 online labels 直接监督深度决策，标签的语义是「进一步迭代是否改善预测」。这确立了它属于「可学习深度分配 / 动态早停」路线，而非固定深度或纯退出 gate 路线。
> 
> > 💡 **修正标注**: 初稿把 TaH2 展开为「Test-time Adaptive Heuristic/Hub?」，并带问号猜测缩写。本节原文并未给出 TaH2 的字母展开，因此这只是无依据的猜测，应从批注中删除；缩写含义属于待证内容。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 「online labels」中的 online 具体指什么？
> > - A: 结合 block 1，指标签在训练过程中、在当前 backbone 的预测损失变化上实时导出，而非事先离线生成；本节未给出标签的计算公式，需看方法章节与 Figure 2。

• Improved Test-Time and Depth Scaling. On challenging AIME benchmarks, TaH2 yields a steeper accuracy–compute slope than Standard and achieves about 3.4 points higher accuracy at matched compute when Standard plateaus. As the maximum iteration depth increases from 2 to 8, its gain over Standard grows from +2.8 points to +3.9 points.



> 💡 **实验证据与量化收益**: 第三条贡献把收益按两个维度总结：
> - 测试时维度：在 AIME 基准上，TaH2 相对 Standard 的 accuracy–compute 斜率更陡；在 Standard 出现平台（plateaus）的区间，以 matched compute 计约有 3.4 点更高精度。
> - 深度维度：最大迭代深度从 2 增至 8 时，相对 Standard 的增益由 +2.8 点增长到 +3.9 点。
> 
> > 💡 **一致性核对**: 这里的 +2.8/+3.9 与 block 2 的 Figure 1b 数字一致，且两处都明确是「相对 Standard（非循环基线）」的增益，而非相对其他 looped 模型；block 2 的 3.4 点在原文中还附带「评测延长到 32K tokens」这一条件，本条表述为「Standard plateaus 时」，两处口径需要合读。
> 
> > 💡 **批注**: 本节给出了三点点的「约 3.4」为近似值（about），批读引用时应保留「约」字，避免当作精确值传播。

