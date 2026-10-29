[← 返回 README](../README.md)

## 2 Decentralized Language Models (DELM)

Given a task D, DELM runs n agents concurrently, each with its own persistent session and workspace. Agents coordinate through two shared structures, a shared context C and a task queue T, and each agent produces its own complete solution.



> 💡 **DELM 总体结构**: 给定任务 D，DELM 会并发运行 $n$ 个 Agent，每个 Agent 都有自己独立且持久的会话和工作区；它们通过共享上下文 $C$ 和任务队列 $T$ 协调，并各自产出完整解。推断：这种设计把协调成本放在两个共享结构上，而不是依赖集中调度器。

Figure 4 and Algorithm 1 summarize five operations: 1 initialize the task queue, 2 execute subtasks in parallel, 3 summarize and publish intermediate results, 4 update the task queue as work progresses, and 5 finalize each agent’s solution. These operations proceed asynchronously rather than in lockstep: while one agent publishes a result, others keep working and can reuse it as soon as it appears.



> 💡 **异步协作机制**: 五步分别是初始化任务队列、并行执行子任务、总结并发布中间结果、随进展更新队列、各 Agent 最终定稿自己的解。关键特征是“非锁步”异步运行：某个 Agent 发布结果时其他 Agent 继续工作，并能立即复用该结果，不必等待全局同步。

## 2.1 Shared Context

The shared context C is an append-only log of reusable findings and contributions from all agents. Agents write to it through compact entries $G _ { i }$ of a declared type: a FACT records a finding supported by observations, a FAIL records an approach or hypothesis that was contradicted, and a completion summary records the result of a finished subtask. FACT and FAIL entries carry explicit scope and evidence fields; agents are instructed to keep entries brief and to state unfinished parts and known limitations, so that other agents can judge whether an entry applies to their own work. Any entry may attach files, which peers can inspect as diffs or merge into their own workspaces with conflict protection. Agents publish an entry as soon as a result is usable and then continue with their own work.



> 💡 **共享上下文类型**: $C$ 是一个只追加、不修改的日志，包含三类条目：FACT 记录有观察支持的事实，FAIL 记录被证伪的假设或方法，以及完成摘要；FACT 和 FAIL 都带有明确的范围和证据字段。条目要求简短并写明未完成部分与已知局限，以便其他 Agent 判断是否适用；任意条目都可以附加文件，其他 Agent 可以将这些文件以 diff 形式查看，或在冲突保护下合并到自己的工作区。

Summaries with files by reference. An entry can attach files, which are stored separately from its summary and snapshotted at publication time. Agents inspect these files with expand, which shows them as diffs against the original task files, and copy selected files into their own workspace with apply. apply refuses to overwrite local changes it cannot account for, leaving reconciliation to the receiving agent. A read of C shows only what is new since that agent’s last read, namely new entries and changed work status, while older entries remain retrievable on demand. This design resembles demand paging in an operating system: agents follow progress through compact summaries and fetch detailed content from backing storage only when they need it (Appendix A.1).



> 💡 **按需读取设计**: 条目正文与附件文件分开存储，附件在发布时生成快照；Agent 用 `expand` 以相对原始任务文件的 diff 形式查看附件，再用 `apply` 把它拷入自己的工作区。`apply` 会拒绝覆盖无法解释的本地改动，冲突留给接收方处理。每次读取 $C$ 时，只返回该 Agent 上次读取以来的新条目与状态变化，旧条目按需再取，作者把这一点类比为操作系统的按需分页。

Distributed verification and correction. Verification is left to the agents rather than to a central checker: each agent derives the checks for its own solution from the original task rather than from subtask summaries, reuses applicable evidence from its peers without repeating it, and directs additional checks at requirements and component interactions that existing evidence does not cover. Because entries are immutable, an agent that finds a defect in a shared result does not edit it. Instead, it appends a FAIL entry stating the conditions under which the defect occurs, and a correction follows as a new entry or a repair subtask. A FAIL stays open until it is addressed, and before submitting, each agent reads the shared context and resolves any published failure relevant to its own solution.



> 💡 **分布式纠错**: 系统没有中央校验器；每个 Agent 都从原始任务推导自己的检查项，而不是依据子任务摘要；它复用同伴已有的证据，避免重复检查，并重点检查现有证据未覆盖的需求以及组件之间的交互。条目不可变，发现缺陷时 Agent 会追加 FAIL 来说明触发条件，修正以新条目或修复子任务的方式跟进；提交前，每个 Agent 必须读取共享上下文，并解决与自己方案相关的未决 FAIL。

## 2.2 Task Queue

The task queue T divides the work among agents. Each item consists of a subject and a free-text detail, in which agents are instructed to state the intended output, its available inputs, the interface it shares with other work, and the earliest partial result it depends on. Agents are instructed to create items only for required implementation, repair of a demonstrated defect, or a concrete open question the solution depends on. At startup, the first agent reserves its own scope and publishes complementary items for its peers, or states the dependency that prevents a split; if it ends before this handoff, the next running agent takes over. After that, any agent can add items or claim open ones at any time. There is no privileged planner and no plan-closure step.



> 💡 **任务队列机制**: 每个条目包含 subject 和自由文本 detail，并要求写清预期输出、可用输入、与其他工作的共享接口，以及所依赖的最早部分结果；Agent 只能为必要实现、已证实缺陷的修复，或方案所依赖的具体开放问题创建条目。启动时，第一个 Agent 先划范围并为同伴发布互补条目，或声明无法拆分的原因；如果它在交接前结束，就由下一个运行中的 Agent 接管。此后任何 Agent 都可以添加条目或认领开放条目，不存在特权规划者，也没有计划收尾步骤。

Claims and status. The queue enforces exclusive claims: the first claim wins, and each agent holds at most one unfinished claim. Every item is marked open, claimed, or done, together with its owner, so each agent can see who is responsible for what and how much remains. Each read also shows every agent’s session state (starting, running, or ended), tracked by the framework rather than reported by the agents. Agents use this state to choose complementary work and to handle dependencies. If a dependency’s owner is starting or running, an agent continues with independent work and waits only when nothing else remains, using a bounded wait that returns as soon as a peer publishes or ends. If the owner of an item has ended without completing it, this does not block progress: an agent that needs the result builds it in its own workspace, reusing anything the owner already shared, instead of waiting.



> 💡 **认领与状态可见**: 队列实行排他认领，先到先得，每个 Agent 同时最多持有一个未完成认领；条目会标记为 open/claimed/done 并注明 owner。框架自动追踪各 Agent 的会话状态（starting/running/ended），不是由 Agent 自行上报；如果依赖方仍在 starting 或 running，Agent 可以先推进独立工作，只有在没有其他可做的事时才进行有界等待；如果依赖方已经结束，Agent 就在自己的工作区中自行构建所需结果，不阻塞进度。

When agents publish and read. Apart from session state, which the framework tracks automatically, all content in the shared context and task queue comes from the agents themselves: the framework never publishes on an agent’s behalf. Agents decide when to publish and read under the instructions in Appendix E. An agent publishes when a result can unblock a peer, without needing to run new checks first, and avoids routine status updates and repeated evidence. It reads the shared context at startup, before starting a new subproblem or diagnostic hypothesis, before final integration, and before submitting. How often an agent publishes and reads is therefore driven mainly by its own progress rather than by the number of peers.



> 💡 **读写节奏由 Agent 自己定**: 除会话状态由框架自动追踪外，共享上下文和任务队列里的内容全部来自 Agent 本身，框架不会代 Agent 发布。Agent 在结果能解锁同伴时就发布（不必先跑新检查），避免例行状态更新和重复证据；读取发生在启动、开始新子问题或诊断假设前、最终集成前、提交前。推断：因此读写频率主要由自身进度而非同伴数量决定。

