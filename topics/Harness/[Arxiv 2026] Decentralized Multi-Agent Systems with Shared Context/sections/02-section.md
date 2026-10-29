[← 返回 README](../README.md)

## 1 Introduction

Multi-agent systems (MAS) offer a natural way to scale large language model reasoning at test time: instead of solving a complex task in a single trajectory, they decompose it into subtasks, dispatch agents in parallel, and aggregate their progress (Anthropic, 2026, Feng et al., 2026, Hong et al., 2023, Kimi et al., 2026, OpenAI, 2025, Ruan et al., 2026, Zhang et al., 2025). This paradigm is increasingly important in agentic coding, both in repository-level software engineering (e.g., SWE-bench (Jimenez et al., 2024), DeepSWE (Huang and Jiang, 2026)), where agents can investigate different files, root causes, or fixes concurrently, and in interactive terminal tasks (e.g., Terminal-Bench (Merrill et al., 2026)), where agents can explore alternative solution paths while sharing what they learn about the environment. In both settings, the value of MAS lies not in issuing more model calls, but in converting additional test-time compute into useful parallel progress, reducing wall-clock time and improving success rates without a proportional increase in cost.



> 💡 **问题动机**: 多智能体系统 (MAS) 的核心价值在于将测试时算力转化为并行进度，而非单纯增加调用次数。通过分解子任务并行执行，旨在降低墙钟时间并提升成功率，尤其适用于代码仓库级工程和交互式终端任务。

Realizing this potential requires keeping every agent productive. Borrowing a term from pipeline parallelism (Huang et al., 2019), we call agent time that does not advance the solution a bubble: time an agent spends waiting on others, or redoing work a peer has already done. Bubbles waste compute and stretch wall-clock time, and each of the three dominant families of MAS creates them in its own way.



> 💡 **概念定义**: 引入“气泡 (bubble)”概念指代不推进解的等待或重复工作时间。这是实现高效 MAS 的关键障碍，三类主流架构（独立、对等通信、集中式）各自以不同方式产生此类资源浪费。

Independent agents run in parallel and select among their outputs, as in self-consistency (Wang et al., 2022), repeated sampling (Brown et al., 2024), and best-of-n sampling (Cobbe et al., 2021, Lightman et al., 2024). They never wait, but they share nothing while they run, so each agent must rediscover the same faults, fixes, and dead ends on its own. Their bubbles are redundant work: adding agents adds compute, but much of it repeats exploration another agent has already done.



> 💡 **机制缺陷**: 独立代理模式虽无等待，但因缺乏信息共享导致严重冗余。每个代理需独自重新发现错误和死胡同，新增算力主要被用于重复探索，而非有效扩展搜索空间。

Peer-communicating agents exchange responses over synchronous rounds, as in multi-agent debate (Chen et al., 2024, Du et al., 2023, Liang et al., 2023). Seeing peers’ answers lets agents correct one another, but each round is a barrier: its duration is set by the slowest agent, so agents that finish early sit idle. These barrier waits are the straggler problem familiar from bulk-synchronous parallel computing (Dean and Ghemawat, 2008, Valiant, 1990). Communication cost also grows with the number of agents: in the fully connected setting (Du et al., 2023), each agent ingests all n − 1 peer responses every round, for n(n − 1) messages per round, so each agent’s context fills with peer responses it must read and reconcile on its own.



> 💡 **同步瓶颈**: 对等通信代理受限于同步轮次，最快完成的代理必须等待最慢者（掉队者问题）。此外，全连接模式下消息复杂度为 $O(n^2)$，上下文迅速膨胀且需自行协调，通信成本高昂。

![](../images/b7a6c1db33458737a3598cd04040c763f94c1e4b05a891fa8740f360169d6d56.jpg)  
Figure 3: Centralized orchestration creates bubbles; DELM squeezes them out. Execution timelines on Terminal-Bench 4.0’s retro-console-soc task with Claude Opus 5.5 and four agents. With Claude Code’s centralized native subagents, the main agent spends 73% of its time waiting on delegated work (yellow), and 44% of all agent time is spent waiting. DELM agents build on one another’s intermediate results, spend 2% of their time waiting, and finish 5.5× sooner.



> 💡 **Figure 3 解读**: 图示对比显示 Claude Code 原生子代理中主代理 73% 时间在等待，总等待占比 44%；而 DELM 通过利用中间结果，将等待降至 2%，完成速度提升 5.5 倍。

Centralized orchestration underlies structured reasoning methods (Besta et al., 2024, Ning et al., 2024, Yao et al., 2023), SPRINT (Biju et al., 2026), the native subagents of Claude Code (Anthropic, 2026) and Codex (OpenAI, 2025), AOrchestra (Ruan et al., 2026), and blackboard-style systems with a control agent (Han and Zhang, 2025, Salemi et al., 2025). A main agent decomposes the task, dispatches subtasks, waits for their outputs, and integrates the results (Figure 2, left). This parallelizes sub-agent execution but not the coordination around it, creating relay bubbles: the main agent idles while delegated work runs, and sub-agents, unaware of one another, cannot build on each other’s progress until the main agent relays it. On Terminal-Bench 4.0’s retro-console-soc task (Figure 3), Claude Code’s main agent spends 73% of its time waiting on its subagents. Relaying can also lose information. Because progress lives in the main agent’s context, details can be dropped in summarization, and correcting an error that has already propagated requires another round of delegation.



> 💡 **中继损耗**: 集中式编排存在“中继气泡”：主代理在分发和整合间空闲，子代理因互不可见无法基于彼此进展工作。信息经主代理中转易丢失，纠错需额外委托轮次，效率低下。

We propose Decentralized Language Models (DELM), a MAS framework that squeezes out these bubbles by replacing the main agent with a shared problem state (Figure 2, right). DELM consists of parallel agents, a shared context, and a task queue, and each component targets one kind of bubble. Agents claim tasks from the queue asynchronously, so no agent waits at a round barrier or for a main agent to assign work. They publish compact findings to the shared context as soon as results are available, so peers build on intermediate progress instead of waiting for a relay. The queue and shared context also show what each agent is working on and which approaches have failed, so agents avoid redoing a peer’s work. Finally, corrections are appended as new entries rather than relayed through a main agent, so a fix reaches every agent without another round of delegation. Table 1 contrasts these mechanisms with those of the systems we compare against. On Terminal-Bench 4.0’s retro-console-soc task (Figure 3), four DELM agents spend only 2% of their time waiting and finish 5.5× sooner than Claude Code with native subagents.



> 💡 **方法概述**: DELM 用共享状态取代主代理，包含异步任务队列和共享上下文。代理即时发布紧凑发现，避免重复劳动；修正作为新条目追加而非中继，显著减少等待并加速收敛。

Table 1: Collaboration and coordination mechanisms in the compared multi-agent systems. A checkmark means the system provides the mechanism while a cross means that it does not.
<table><tr><td rowspan="2">System</td><td colspan="3">General collaboration capabilities</td><td colspan="3">Explicit coordination mechanisms</td></tr><tr><td>Parallel subtask execution</td><td>Cross-agent work reuse</td><td>Feedback &amp; correction</td><td>Intermediate-results sharing</td><td>Peer-status awareness</td><td>Stateful shared context</td></tr><tr><td>Isolated agents</td><td>X</td><td>X</td><td>X</td><td>X</td><td>X</td><td>X</td></tr><tr><td>AOrchestra</td><td>X</td><td>√</td><td>√</td><td>X</td><td>X</td><td>X</td></tr><tr><td>Codex</td><td>√</td><td>√</td><td>√</td><td>√</td><td>X</td><td>X</td></tr><tr><td>Claude Code</td><td>√</td><td>√</td><td>√</td><td>√</td><td>X</td><td>X</td></tr><tr><td>DELM</td><td>√</td><td>J</td><td>√</td><td>√</td><td>√</td><td>√</td></tr></table>



> 💡 **Table 1 解读**: 表格对比表明现有系统均缺失部分显式协调机制。DELM 是唯一同时具备并行执行、跨代理复用、反馈修正、中间结果共享、同伴状态感知及有状态共享上下文的系统。

Key idea. Existing MAS waste parallelism in bubbles because of how their agents communicate: independent agents redo one another’s work, peer-communicating agents wait at synchronous rounds, and centralized systems wait on a main agent to relay progress. DELM squeezes out these bubbles by letting agents coordinate asynchronously through a shared context and a task queue, with no main agent in the loop.



> 💡 **核心思想**: 现有 MAS 的低效源于通信模式导致的同步或冗余。DELM 的关键创新在于去中心化异步协调：通过共享上下文和任务队列消除主代理瓶颈，让代理直接基于最新状态协作。

We evaluate DELM on four agentic coding benchmarks: Terminal-Bench 4.0 (Merrill et al., 2026), where agents share discovered environment state and failed attempts in live terminals; DeepSWE v1.1 (Huang and Jiang, 2026), where agents reuse each other’s debugging progress on long-horizon software engineering tasks; SWE-bench Verified (Jimenez et al., 2024), where agents explore alternative root causes and fixes for real GitHub issues; and ProgramBench (Yang et al., 2026a), where agents rebuild entire programs from scratch. We compare against Codex and Claude Code, with and without native subagents, AOrchestra (Ruan et al., 2026), and, on SWE-bench Verified, mini-SWE-agent (Yang et al., 2024). Our key findings are:



> 💡 **评估设置**: 明确实验范围：四个代理编码基准（Terminal-Bench 4.0、DeepSWE v1.1、SWE-bench Verified、ProgramBench），对比 Codex、Claude Code（含/不含原生 subagent）、AOrchestra，SWE-bench Verified 上另加 mini-SWE-agent。

• DELM is both faster and more accurate on long-horizon coding. Across two frontier models, DELM reaches up to 93.33% on 10 long-horizon tasks of Terminal-Bench 4.0 and 98.33% on 10 long-horizon tasks of DeepSWE v1.1, improving over the strongest baseline by up to 17.5 points while running up to 2.49× faster than the harness it builds on (Figure 1, §3.1, §3.2).



> 💡 **核心结果 1**: 长周期任务上精度与速度同时提升。两个前沿模型下，Terminal-Bench 4.0 的 10 个长周期任务最高 93.33%，DeepSWE v1.1 最高 98.33%，较最强基线最多高 17.5 分，同时比它搭建其上的 harness 最多快 2.49×（§3.1、§3.2）。

• The gains also hold with a lightweight model on a standard benchmark. On SWE-bench Verified with Gemini 3 Flash, DELM reaches 66.08%, 10.8 points above the strongest baseline, while running 2.12× faster than Claude Code at half of AOrchestra’s cost per submission (§3.3).



> 💡 **核心结果 2**: 换轻量模型结论仍成立。SWE-bench Verified 上用 Gemini 3 Flash 达 66.08%，比最强基线高 10.8 分，比 Claude Code 快 2.12×，且单次提交成本仅为 AOrchestra 的一半（§3.3）。

• DELM provides an effective way for scaling agents, enabling more agents to make more progress under a fixed time budget. On ProgramBench, where agents rebuild entire programs from scratch, DELM ends a 120-minute budget up to 19.91 points above Claude Code, and matches Claude Code’s final pass rate in half the time (§3.4).



> 💡 **核心结果 3**: 固定时间预算下的扩展性。ProgramBench 从零重建程序，120 分钟预算结束时 DELM 最多比 Claude Code 高 19.91 分，并用一半时间追平其最终通过率（§3.4）。

• The gains come from coordination, not parallelism alone. Removing any single coordination mechanism brings two agents back to roughly single-agent speed (§3.5).



> 💡 **机制归因**: 作者称增益来自协调而非单纯并行，证据是消融中移除任一协调机制，双代理就回落到接近单代理的速度（§3.5）。这条证据只覆盖速度；各机制对精度的贡献，推断：原文未拆开给出。

![](../images/077923f74c01d6b9dc0da26c40a58d2b3e6146e797d87b06b95b4b7b0ebf8fd1.jpg)  
Figure 4: Overview of DELM. The shared task queue is initialized with a set of predefined, general preparation subtasks. Parallel agents asynchronously claim subtasks $\{ T _ { i } \}$ , read the shared context, and implement $\left\{ T _ { i } \right\}$ locally. Each agent’s update is compressed and appended to the shared context as a gist $G _ { i } ,$ making reusable progress visible to all agents. There is no central coordinator or reviewer: any agent may add required work at any time, and each agent submits its own complete solution once its evidence covers all task requirements.



> 💡 **Figure 4 读法**: 据 caption，DELM 无中央协调者或审查者：共享任务队列预置通用准备子任务，并行代理异步领取、读共享上下文并本地实现，再把更新压缩成 gist 追加回共享上下文；任一代理可随时补充工作，证据覆盖全部要求后各自提交完整解。未获得图片视觉信息，图示细节以上述 caption 为准。

