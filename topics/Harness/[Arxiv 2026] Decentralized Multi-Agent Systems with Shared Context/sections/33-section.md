[← 返回 README](../README.md)

## Long commands.

Run necessary long diagnostics asynchronously when useful independent work remains, exposing intermediate results. Preserve their inputs and code; results apply to those versions. Read new output when it can change a decision instead of repeatedly polling. If a peer update could make a tool wait unnecessary, read the board before blocking, not after the wait in the same call. Avoid competing workload during timing measurements. Wait only for evidence still required for completion.



> 💡 **长命令执行规范**: 本节给出异步执行长诊断的操作准则：只有在还有独立工作可做时，才异步运行长诊断，并及时暴露中间结果；同时要保存当时的输入和代码，因为这些结果只对那个版本成立。只有在新的输出可能改变决策时才读取输出，不要反复轮询；如果同伴的更新可能让当前等待变得多余，应在阻塞前查看状态板，而不是在同一个调用里等到结束之后；计时测量期间要避免并发负载；只等待任务完成仍必需的证据。

