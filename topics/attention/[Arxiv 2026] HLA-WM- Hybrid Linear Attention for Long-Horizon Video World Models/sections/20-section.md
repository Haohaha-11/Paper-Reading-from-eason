[← 返回 README](../README.md)

## A.8.1 SANA-WM-Bench Category Results

SANA-WM-Bench contains four scene categories—Game Style, Indoor, Outdoor City, and Outdoor Nature—with 20 scenes per category in each trajectory split. Tables 8–10 report category-level results under Stage 1, causal AR refinement, and bidirectional refinement. The gains are broadly distributed across scene categories, with Stage 1 showing the most consistent improvements and downstream refinement introducing several category-dependent trade-ofs.



> 💡 【AI 批注】事实核对与要点：本段交代 SANA-WM-Bench 的构成与表格归属——四个场景类别（Game Style、Indoor、Outdoor City、Outdoor Nature），每个类别在每条 trajectory split 中各 20 个场景；表 8–10 分别汇报 Stage 1、causal AR refinement、bidirectional refinement 三种设置下的类别级结果。原文给出的结论是：增益广泛分布于各场景类别，其中 Stage 1 的改进最一致，而下游 refinement 会引入若干依赖类别的 trade-offs（原文 OCR 写作 trade-ofs）。批注边界：把这一拆分解读为“检验泛化能力/鲁棒性”属合理推断，原文并未出现泛化、鲁棒性字样，也未给出类别难度分级标准，不应当作原文主张。

Overall, the category-level results show that the improvements are not concentrated in a single scene type. Stage 1 provides the most consistent gains, while downstream refinement introduces several category-specific trade-ofs. At the aggregate level, HLA-WM improves all three revisit-consistency metrics under each evaluated inference mode.



> 💡 【AI 批注】结论边界与命名核对：本段是类别级结果的收束——提升未集中在单一场景类型；Stage 1 增益最一致，下游 refinement 存在类别特定的 trade-offs；在聚合层面，HLA-WM 在每种被评估的推理模式下都提升了全部三项 revisit-consistency 指标。两点需注意：(1) 主体名是模型 HLA-WM，SANA-WM-Bench 是基准名，不可混用；(2) 原文只断言“三项 revisit-consistency 指标全面提升”，并未说明每个场景类别内部是否同步提升，也未提及任何“物理规律遵循”类维度——相关追问只能标为推断或待证，需回到表 8–10 逐类别核对。

