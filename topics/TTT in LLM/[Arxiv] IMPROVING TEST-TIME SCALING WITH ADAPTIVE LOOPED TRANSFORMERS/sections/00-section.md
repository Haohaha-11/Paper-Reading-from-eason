[← 返回 README](../README.md)

# IMPROVING TEST-TIME SCALING WITH ADAPTIVE LOOPED TRANSFORMERS

Yichen You<sup>∗1</sup>, Tianyu Fu<sup>∗1</sup>, Aosong Feng<sup>∗2</sup>, Xingtai Lv<sup>1</sup>, Xuefei Ning<sup>†1</sup>, Ning Ding<sup>†1</sup>, Yu Wang<sup>†1</sup>



> 💡 **作者与贡献标记批注**：本段列出 7 位作者。对照上标可确认：带 ∗ 的是 Yichen You、Tianyu Fu、Aosong Feng 三人；带 † 的是 Xuefei Ning、Ning Ding、Yu Wang 三人；Xingtai Lv 无特殊标记。需注意：原文只给出符号标记，**并未说明 ∗ 与 † 的确切含义**（例如 ∗ 是否代表 equal contribution、† 是否代表 corresponding author，均需看脚注或正文说明）。因此“三位共同一作 + 三位通讯作者”属于按学术排版惯例的**推断**，不是原文陈述，待正文/脚注确认。此外，初稿中“这种配置通常暗示工作由多个子模块并行推进”属于对作者分工的**外部推测**，原文无任何依据，已删除；仅凭作者列表无法判断模型架构设计与测试时扩展策略如何结合。

<sup>1</sup>Tsinghua University <sup>2</sup>Yale University



> 💡 **机构信息批读**：原文给出两个机构编号：1 为清华大学（Tsinghua University），2 为耶鲁大学（Yale University）。结合上一段的编号可做一次**可从原文推出的对应**：三位 ∗ 标记作者中，Yichen You、Tianyu Fu 属机构 1（清华），Aosong Feng 属机构 2（耶鲁）；三位 † 标记作者（Xuefei Ning、Ning Ding、Yu Wang）均为机构 1（清华）。注意：这只是上标编号的机械对应，原文未写明各人的具体贡献或负责方向。初稿中“清华团队在系统优化与大模型推理效率方面常有积累”“耶鲁团队可能在算法理论提供补充视角”属于**未获证据支持的外部背景断言**，本批注不予采用，已删除；跨校合作的具体分工在此阶段属于待证事项。

