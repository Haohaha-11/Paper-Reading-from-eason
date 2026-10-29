[← 返回 README](../README.md)

## 6 Discussion and Limitations

Conclusion. We introduced DELM, a decentralized multi-agent framework in which agents coordinate through a shared context and a task queue rather than through a main agent. By letting agents claim work asynchronously, publish findings as soon as they are usable, and see what every peer is doing, DELM squeezes out the bubbles that existing designs create: redundant work among independent agents, barrier waits among peer-communicating agents, and relay waits under centralized orchestration. Across Terminal-Bench 4.0, DeepSWE v1.1, and SWE-bench Verified, DELM is both more accurate and faster than single-agent harnesses, their native subagents, and AOrchestra, and on ProgramBench it makes more progress than Claude Code under the same time budget. Our ablation shows that these gains come from coordination rather than parallelism alone: removing any single mechanism brings two agents back to roughly single-agent speed. These results suggest that scaling multi-agent systems requires not only more agents, but a coordination substrate that keeps every agent productive while keeping the cost of communication among them low.



> 💡 **结论与证据**: 在 Terminal-Bench 4.0、DeepSWE v1.1、SWE-bench Verified 上，DELM 同时比单智能体 harness、其原生 subagent 和 AOrchestra 更准更快；ProgramBench 上则是在同一时间预算内进展超过 Claude Code（只比较进展，不比较准确率）。消融实验显示，移除任一机制都会让两个智能体大致退回单智能体速度，因此作者把增益归因于协调而非单纯并行。

Limitations. In certain settings, lower latency and Cost/Submission for DELM come with higher Cost/Task. A natural-language shared context is also less suited to exact aggregation over structured data, where DELM can be enhanced when combined with code-mediated execution (Appendix C.1).



> 💡 **局限性分析**: 指出DELM虽降低延迟和单次提交成本，但可能增加单任务总成本；自然语言共享上下文不擅长结构化数据精确聚合，建议结合代码执行以增强处理能力。

