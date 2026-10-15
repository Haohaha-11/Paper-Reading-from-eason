[← 返回 README](../README.md)

## C.4 TOKEN-LEVEL DEPTH ALLOCATION

Figure 11 shows iteration depths in three sampled correct responses. The math and code examples show a clear shift from deeper natural-language reasoning to shallower mathematical expressions and final code. Mean depth falls from 2.46 to 1.29 in OlympiadBench and from 3.31 to 1.04 in HumanEval across the displayed spans. The GPQA example retains substantial depth through its concluding explanation. These cases suggest that depth allocation reflects local response content and the stage of reasoning.



> 💡 **Figure 11 批读与证据边界**：本段只报告了 Figure 11 中三条被采样正确响应的迭代深度轨迹。可直接核对的事实有三点：(1) 数学与代码样例呈现从“较深的自然语言推理”向“较浅的数学表达式与最终代码”的偏移；(2) 数字仅限所展示区间（across the displayed spans），OlympiadBench 平均深度由 2.46 降至 1.29，HumanEval 由 3.31 降至 1.04；(3) GPQA 样例在收尾解释中仍保持较大深度。原文结论措辞是 These cases suggest，属于基于个例的提示性推断，而非全局规律或因果证明；样本也仅是 three sampled correct responses，不含错误响应或失败案例对照。
> 
> **需修正的初稿表述**：(a) 初稿称该现象“证实了深度分配与推理阶段密切相关”，强度过高，原文为 suggest，且未做统计检验；(b) 初稿把自然语言阶段解释为“抽象规划/高层规划”，并称其“消耗更多计算资源”、符号操作“更轻量”——原文只说深度（iteration depth）更深或更浅，未把深度等同于规划层级或计算资源开销，这是超出证据的机制推断；(c) 初稿称“该规律主要适用于结构化推导而非开放式知识总结”，原文没有做这种适用范围的划分，GPQA 只是一个保持深度的反例式观察。
> 
> **可保留的解释**：深度分配与“局部响应内容 + 所处推理阶段”相关，这是原文自身的措辞；若要沿用“规划—执行”的说法，应明确标注为推断。

