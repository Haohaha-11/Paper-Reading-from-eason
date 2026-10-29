[← 返回 README](../README.md)

## D Why DELM Is Both Accurate and Cost-Efficient on SWE-bench

Three trace-level mechanisms explain why DELM improves both accuracy and cost efficiency. In the DELM traces below, each shared entry is one thread’s note, tagged with its thread id (t0, t1, . . . ) and a type such as FACT, FAIL, or PATCH\_SUMMARY.



> 💡 **机制总览**: 本节通过三个 trace-level 机制解释 DELM 在 SWE-bench 上兼顾准确率与成本效率的原因。每条共享记录包含线程 ID（如 t0）和类型标签（FACT/FAIL/PATCH_SUMMARY），用于区分事实、失败与补丁摘要。

(1) Parallel agents complement each other by sharing failures. The first mechanism is that failed hypotheses become reusable state rather than private dead ends. In isolated forks, a negative result remains local to one trajectory, so other attempts may spend their own budget rediscovering the same failure. In DELM, once such a failure is admitted to the shared context, later agents can treat it as a constraint and redirect their search.



> 💡 **机制一：失败共享**: 并行代理通过共享失败互补。传统隔离分支中，负面结果仅局部有效导致重复搜索；DELM 将失败写入共享上下文，使后续代理将其视为约束条件，避免浪费预算重蹈覆辙。

This mechanism appears in the trace below. The task fixes lambdify for single-element tuples. The natural guess, the Python printer layer, is a red herring; the real bug is on a separate tuple-building path in sympy/utilities/lambdify.py:



> 💡 **案例引入**: 以修复单元素元组 lambdify 为例，指出 Python printer 层是红鲱鱼，真实缺陷位于 sympy/utilities/lambdify.py 的独立元组构建路径。此段为后续 trace 提供具体 bug 背景。

```ini
[t0/FACT] AbstractPythonCodePrinter change did not affect lambdify output
[t1/FACT] sympy/utilities/lambdify.py:964 _recursive_to_string manual tuple join
bypasses printer fix
[t0/FACT] sympy/utilities/lambdify.py:964 manual join for tuples lacks trailing comma
logic
```



> 💡 **Trace 证据**: t0 确认修改 printer 无效；t1 定位到 _recursive_to_string 绕过 printer；t0 进一步记录该函数缺失尾逗号逻辑。三条 FACT 展示了从排除错误假设到锁定具体代码行的推理链。

The decisive update is the negative result in the first line: thread t0 shows that changing the printer does not affect the output. After reading this failure, t1 avoids repeating the same detour and localizes the actual bypass in \_recursive\_to\_string; t0 then records the concrete defect, the missing trailing comma. Thus, DELM turns a failed hypothesis into shared progress, improving later agents’ search efficiency.



> 💡 **机制解析**: 首行负面结果是关键转折点。t1 读取 t0 的失败记录后跳过 printer 弯路，直接定位 bypass；t0 随后补充具体缺陷。DELM 由此将失败假设转化为共享进度，提升搜索效率。

(2) Admitted constraints remain binding shared state. The second mechanism is that DELM preserves important constraints as shared state rather than routing them through a central controller. In centralized coordination, a main agent may soften, omit, or reopen constraints discovered by sub-agents before they can guide later work.



> 💡 **机制二：约束绑定**: DELM 将重要约束作为绑定共享状态保留，而非经中央控制器路由。集中式协调常因主代理软化或遗漏子代理发现的约束而导致失败，DELM 则确保约束持久有效。

This mechanism appears in the trace below. Folding multiple search filters into one .filter() call is unsafe for multi-valued relations, where .filter(A).filter(B) and .filter(A, B) differ. In AOrchestra, a sub-agent finds exactly this danger, but the constraint must pass through the main agent, which reopens the optimization and softens it into a tradeoff about reducing joins “for many use cases”; the run fails. The relevant fact was found, but not preserved as binding state. In contrast, DELM keeps the constraint explicit and reusable:



> 💡 **对比 AOrchestra**: Django ORM 多值关系中 .filter(A,B) 与 .filter(A).filter(B) 语义不同。AOrchestra 中主代理将该安全约束软化为权衡选项致任务失败；DELM 则显式保留该约束供复用。

```ini
[t3/FAIL] django/contrib/admin/options.py:1041 single .filter() breaks M2M multi-term
search
[t3/FACT] Django ORM .filter(Q1, Q2) vs .filter(Q1).filter(Q2) differs for multi
valued relations
[t3/FACT] lookup_spawns_duplicates determines if single .filter() is safe
[t1/FACT] lookup_spawns_duplicates preserves M2M search semantics while optimizing
FKs
```  
Thread t3 records the unsafe case (FAIL), the semantic reason, and the predicate lookup\_spawns\_duplicates, which determines when the optimization is valid. Thread t1 then builds on this state to preserve M2M semantics while still optimizing foreign-key cases. Thus, later threads inherit the binding constraint rather than reopening a globally invalid simplification.



> 💡 **Trace 证据**: t3 记录 FAIL 及语义原因，并提取 lookup_spawns_duplicates 谓词判断优化安全性；t1 基于此状态在保留 M2M 语义的同时优化 FK。后续线程继承绑定约束而非重新打开无效简化。

(3) Compact patch summaries carry discoveries. The third mechanism is compact sharing, which lowers cost by preventing peer communication from becoming another long raw transcript. Sharing full traces would preserve information, but would also expose every worker to command history, file dumps, failed edits, and intermediate reasoning. DELM instead shares a compressed version of the useful discovery, so later workers can reuse the result without reading the entire trajectory.



> 💡 **机制三：紧凑摘要**: 通过共享压缩版发现降低通信成本。全量轨迹虽保信息但暴露冗长命令历史与中间推理；DELM 仅分享有用发现的精简版本，使后续 worker 无需阅读完整轨迹即可复用结果。

This mechanism appears in the trace below. Both the shared and no-share settings solve the task, but compact sharing reduces the cost from \$0.399 to \$0.125. The difference is not that DELM shares more context; rather, it shares a compressed version of the useful search result (labeled as



> 💡 **成本数据**: 同一任务下，紧凑共享将成本从 $0.399 降至 $0.125。差异源于共享内容形态：DELM 并非共享更多上下文，而是传递标记为 PATCH_SUMMARY 的压缩搜索结果。

PATCH\_SUMMARY). One thread first rules out the ordinary tuple-printer path and localizes the actual bypass in lambdify.py:



> 💡 **Trace 背景**: 仍是 lambdify 单元素元组这一任务：某个线程先排除普通 tuple-printer 路径，把真正的 bypass 定位到 lambdify.py，为下一条 PATCH_SUMMARY 的生成做铺垫。

```ini
[t1/FACT] StrPrinter._print_tuple at sympy/printing/str.py:868 already includes
trailing comma logic
[t1/FACT] sympy/utilities/lambdify.py:964 lacks trailing comma for single-element
tuples
[t1/PATCH_SUMMARY] files=sympy/utilities/lambdify.py | idea=Modify
_recursive_to_string to add a trailing comma for single-element tuples | evidence=
reproduce_issue.py PASSED
```



> 💡 **摘要结构**: t1 的 PATCH_SUMMARY 包含 files、idea 和 evidence 字段，浓缩了“修改 _recursive_to_string 添加尾逗号”这一核心方案及测试通过证据，形成短小且有据可依的交接文档。

This patch summary turns a multi-step debugging trajectory into a short, evidence-backed handoff. Later workers do not need the full command history, file dumps, failed edits, or intermediate reasoning. Thus, compact sharing preserves the reusable discovery while avoiding the cost of exposing every peer to the raw trace, explaining why DELM solves the task at much lower cost.



> 💡 **机制解析**: 补丁摘要将多步调试轨迹转化为简短交接。后续 worker 无需处理原始命令历史或失败编辑，既保留了可复用发现，又避免了向所有 peer 暴露 raw trace 的高昂 token 成本。

Together, these examples explain the trend in Table 5: DELM keeps reusable facts, failures, constraints, and patch summaries visible to all peers, while avoiding both redundant isolated search and the high token cost of raw trace sharing. This improves per-thread success while reducing cost.



> 💡 **总结呼应**: 三个机制共同解释 Table 5 趋势：DELM 保持事实、失败、约束和摘要对所有 peer 可见，同时规避孤立冗余搜索与原始轨迹共享的高成本，从而提升单线程成功率并降低成本。

