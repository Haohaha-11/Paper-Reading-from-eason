[← 返回 README](../README.md)

## The queue:

• Queue items are for required implementation, repair of a demonstrated defect, or a concrete unanswered question needed to implement the solution. Do not create separate verification, testing, review, reproduction, or solution-checker assignments, including at a peer’s request. This rule applies to queue items created with publish or reserve. Diagnosis and measurements needed to choose or repair an implementation remain allowed. A checker that is itself the requested product is implementation.



> 💡 **队列准入**: 队列项只允许三类：必需实现、已证实缺陷的修复、实现所需的未决问题；明确禁止另立验证、测试、审查、复现或方案检查类条目，即使同伴要求也不行。为选方案或修缺陷而做的诊断与测量仍允许；“检查器本身就是交付物”时按实现处理。

• Name each item’s output, available inputs, shared interface and first usable dependency. Prefer complementary outputs that both workers can implement independently. Preserve required public behavior, interfaces and side effects. Reuse compatible components. Pursue a different approach to resolve a concrete limitation, sharing compatible parts. Keep related shared-file edits together. Do not invent work just to occupy a peer.



> 💡 **拆分与复用**: 每个条目须写明输出、可用输入、共享接口和首个可用依赖，优先设计两名 worker 能各自独立完成的互补输出。还要求保留既有的公共行为、接口与副作用并复用兼容组件；遇到具体限制可换方法但共享兼容部分，禁止为占住同伴而造任务。

• After the initial handoff, claim an existing item when it fits; otherwise reserve a distinct required implementation scope. Respect the returned owner. Ownership covers an output, not a fixed algorithm. Publish a needed component when a peer can implement it independently, stating its interface, inputs and nonoverlapping scope while continuing your part. Keep small local adjustments under the current claim. Routine local integration and necessary checking need no separate item or active claim. Respect ownership and interfaces while the owner is running. Report concrete defects through scoped FAIL notes. To hand off incomplete work, close the claim with its remaining scope and reusable files. An ended worker’s claim stays with it, but cannot block you from completing required work. Keep completed items closed. Do not assign verification roles through notes or status. Requests for existing evidence and interface clarification remain useful. Initial planning does not make either worker a permanent coordinator or checker. There is no global plan-closure step.



> 💡 **所有权与交接**: 认领后所有权绑定的是输出而非固定算法；同伴能独立实现时，可发布该组件并写明其接口、输入与不重叠范围，自己继续做其余部分。常规本地集成与必要检查无需另立条目，缺陷用有范围的 FAIL 记录上报；未完成工作以关闭声明加剩余范围交接，已结束 worker 的声明不妨碍你完成必需工作，也不存在全局计划收尾步骤。

<table><tr><td>DeLM Prompt (n = 2, Part 4): Validation and Completion</td></tr><tr><td>[DeLM AGENT A] Validation and completion:</td></tr><tr><td>• Derive necessary checks from the original task, not the queue summary or the current im- plementation. Preserve its measurement domain and apply each stated exception only to the requirement it modifies. Assess your own and peer evidence against those requirements. Reuse applicable results; adoption alone does not require repeating a check. Evidence must</td></tr><tr><td>match the relevant code, inputs, configuration and environment of the final local output. • For a new comparison, obtain matched candidate and reference observations before collecting repeats. Repeat as required by the task or where uncertainty could change the implementation or acceptance decision, preserving the required starting conditions. Preserve inputs, expected results and reference measurements. After candidate-only changes, rerun affected candi-</td></tr><tr><td>date checks; reuse reference measurements only under matching inputs, reference version, procedure and operating conditions. • Publish an observed relevant failure promptly as FAIL with its conditions. Keep it unresolved until corrected code or a task-based explanation addresses it; changing the sample or averaging it with unrelated cases is not a repair. When ground truth is unavailable, use available</td></tr><tr><td>references, convergence or invariants and state what remains unmeasured. Never turn that limitation into a claim of measured success. Submission.</td></tr><tr><td>to delay. Prevent exploratory work from changing the submitted output. Close any claim you still hold with done. If your item is already done, publish required corrections in a note rather</td></tr><tr><td>Before submitting, read the board and address published corrections relevant to your final local solution. A required repair is a dependency even if an earlier artifact is usable. Do not finish with a known unresolved failure of a required behavior or performance target. Once applicable evidence supports satisfying all requirements and no relevant failure remains, preserve that candidate and submit. An optional stronger result or an already-running search is not a reason</td></tr></table>



> 💡 **Agent A 验证规范**: 检查项必须从原始任务推导、沿用其测量域、只对相应需求套用例外，且证据须匹配最终本地输出的代码、输入、配置与环境；已有的适用结果可直接复用。新比较要先拿到匹配的候选与参考观测再重复，候选侧改动后重跑受影响的候选检查。无真值时改用参考、收敛性或不变量并说明未测量部分，不得把它说成已测成功。

Four-agent configuration. For n = 4, the two additional agents initially identify reusable components and interfaces, and resolve a concrete design uncertainty. These directions yield to available implementation work. The prompt also clarifies cross-author file conflicts, interface changes, local state and artifact reuse, container resource limits, and measurement intervals. Shared instructions and the first two startup prompts also contain wording changes.

> 💡 **四代理差异**: n=4 时多出的两名代理初期负责识别可复用组件与接口、并解决一个具体的设计不确定性，但这些探索方向要让位于可用的实现工作。提示词另补充了跨作者文件冲突、接口变更、本地状态与产物复用、容器资源上限和测量间隔等细节，共享说明与前两个启动提示词也有措辞改动。

