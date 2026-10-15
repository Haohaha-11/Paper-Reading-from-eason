[← 返回 README](../README.md)

## A.4 COMPUTE ACCOUNTING

This section defines the decoding FLOPs reported throughout the paper and the corresponding training cost. Section A.4.1 fixes the conventions and per-call costs, and Sections A.4.2 and A.4.3 give compact forms of decoding and training FLOPs before expanding each term.



> 💡 **Section 概览**: 本段是 A.4 的路线图，明确了三件事的归属：(1) 全篇引用的 decoding FLOPs 与对应 training cost 的定义放在本节；(2) A.4.1 负责固定约定（conventions）以及每次调用（per-call）的开销；(3) A.4.2 与 A.4.3 先给出 decoding FLOPs 和 training FLOPs 的紧凑形式（compact forms），再逐项展开（before expanding each term）。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 本节是否已经给出了具体公式或数值？
> > - A: 没有。原文本段只做结构与职责划分，未出现任何公式、符号或数字；具体公式在 A.4.1–A.4.3 中给出，此处不下结论。
> > - Q: “per-call costs” 指的是什么粒度？
> > - A: 原文只写“conventions and per-call costs”，未展开粒度定义（如 per-token / per-forward / per-decode-step）。属于**待证**，需结合 A.4.1 正文确认，不能从本段推断。
> 
> > 💡 **批注校准（相对初稿）**: 初稿称“该段落仅包含标题，无实质正文内容”，与原文不符——block 1 有实质内容，且明确交代了 A.4.1/A.4.2/A.4.3 的分工。初稿关于“通常此类章节会定义硬件利用率假设”等表述属于**推断**，原文未提及硬件利用率、吞吐或步数计数，故在本批注中降级为推测，不作为事实陈述。

