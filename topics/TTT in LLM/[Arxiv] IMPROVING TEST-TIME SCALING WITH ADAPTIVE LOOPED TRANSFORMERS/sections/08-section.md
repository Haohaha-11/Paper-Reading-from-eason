[← 返回 README](../README.md)

## 6 CONCLUSION

In this paper, we study the test-time scaling of looped transformers introduced through post-training. Existing looped models yield steeper accuracy–compute slopes than their non-looped baseline, yet remain less accurate at matched compute. We therefore introduce TaH2, which post-trains adaptive looped transformers by jointly training the backbone and an iteration decider through lookahead depth supervision. On AIME, TaH2 improves the slope by 53% and exceeds Standard’s peak accuracy by about 3.4 points at matched compute. Its gain over Standard grows from 2.8 to 3.9 points as the maximum iteration depth increases from 2 to 8, and extends to larger models (4B and 8B) and other domains (code, QA and tool use).



> 💡 **核心贡献与性能提升**: 本段是结论段对全文主张的收束。问题设定：现有 looped transformers 虽经后训练获得测试时扩展能力，但其准确率–计算量斜率比非循环基线更陡，却在匹配算力下精度更低（原文区分了 slope 与 matched-compute accuracy 两件事）。方法：TaH2 通过 lookahead depth supervision 联合训练骨干网络与迭代决策器，对自适应循环 Transformer 做后训练。关键数据核对无误：AIME 上斜率提升 53%，匹配算力下峰值精度超过 Standard 约 3.4 点；相对 Standard 的增益随最大迭代深度从 2 增至 8 而由 2.8 点升到 3.9 点。需注意：原文对 4B/8B 和其他领域用的是 “extends to”，属于范围扩展声明，不能直接改写为“泛化能力已被严格验证”；此处保留为“扩展到”，不额外拔高为泛化证明。

Limitations. (1) TaH2 incurs more training FLOPs than standard SFT (Appendix A.4.3). But note that post-training requires substantially less compute than pretraining, making TaH2 a practical way to add adaptive depth to existing models. (2) Our method is studied only under SFT; we leave its extension to on-policy distillation and reinforcement learning for future work.



> 💡 **局限性与实用性权衡**: 本段列出两点限制，且带条件与出处。(1) TaH2 训练 FLOPs 高于标准 SFT，原文指向 Appendix A.4.3；作者随后补充 post-training 所需计算量远低于 pretraining，据此论证 TaH2 是“给现有模型添加自适应深度的一种实用途径”（a practical way to add adaptive depth to existing models）。这里应限定为作者基于后训练/预训练计算量对比的可行性论证，而非对所有部署场景的普适承诺。(2) 方法仅在 SFT 下研究，扩展到 on-policy distillation 与 reinforcement learning 被明确留作 future work。两点均为原文直接陈述，没有数值推断空间。

