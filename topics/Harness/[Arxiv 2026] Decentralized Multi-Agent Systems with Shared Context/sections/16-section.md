[← 返回 README](../README.md)

## E Prompts

DeLM Prompt (n = 2, Part 1): Initial Instructions   
[DeLM AGENT A]   
Workspace.   
Your task workspace is {workspace}. Do not spawn extra agents.   
Role.   
You are {worker}, one of two agents solving this task in parallel. Roster: {roster}.   
Objective.   
Minimize elapsed time to a correct, complete submission. Start useful work promptly, unblock   
your peer with usable results, and reuse applicable evidence.   
Coordination.   
• Divide work and exchange findings and text files through the board. It is the only connection   
between workers. Each worker has a separate container, so edits and installs are local. Every   
worker submits a complete solution from its own workspace.   
• The board is ready immediately. The first worker creates the initial queue: it reserves its own   
scope, then publishes actionable peer work or states the concrete dependency preventing a   
split. After that handoff, both can publish, reserve, and claim distinct work. If the first worker   
ends before the handoff, the other takes over. Both can share findings immediately.   
Task requirements.   
The original task remains authoritative for both workers. Queue items divide work; their   
summaries never replace or weaken its requirements. Preserve every required observable effect,   
including state changes and nonvisual side effects.   
Performance.   
• For performance-constrained work, choose the complete first-call path before fixing compo  
nent interfaces. In an existing initial item or FACT, state what the producer prepares, what the



> 💡 **DeLM 初始指令**: 定义双智能体并行协作框架。核心机制是通过“board”同步进度与文件，要求 Agent A 优先启动工作并解锁同伴，强调在隔离容器中各自提交完整解法以最小化总耗时。

consumer receives directly, and what substantial work remains on the first required invocation. Retained state is not directly usable if that invocation must rebuild another representation. Design preparation and updates within the task’s required lifecycle, preserving fresh-start, mutation and lifetime semantics.



> 💡 **性能约束策略**: 针对性能受限任务，要求在固定组件接口前先确定完整的首次调用路径，并写明生产者准备什么、消费者直接拿到什么、首次必需调用还剩多少实质工作；若该次调用必须重建另一种表示，保留的状态就不算可直接使用。另要求在任务规定的生命周期内设计准备与更新，保持 fresh-start、mutation、lifetime 语义。

• For decision or search operations, consider cheap sufficient conditions for exact acceptance and rejection before general preparation or search. An early answer must satisfy every required condition; a heuristic or necessary condition is not enough. Use the complete method when no such condition establishes an answer.



> 💡 **搜索剪枝原则**: 在决策或搜索操作中，优先使用能精确接受或拒绝的廉价充分条件进行快速判定。启发式或必要条件不足以作为早期答案，仅在无法通过简单条件确定时才执行完整搜索。

• Share the interface early and implement independent parts in parallel. When a specific interface cost could prevent a required target, resolve that concern with existing evidence or a small probe before expanding code dependent on that choice. Keep unaffected work moving. Address a measured bottleneck before extending an expensive comparison; reuse compatible components.



> 💡 **接口成本与并行**: 尽早共享接口以并行推进独立部分；当某项接口成本可能挡住目标时，先用已有证据或小探针确认，再扩展依赖该选择的代码，并让不受影响的工作继续。遇到瓶颈先处理已实测的那一个、再扩大昂贵的比较，并复用兼容组件。

