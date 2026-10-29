[← 返回 README](../README.md)

## 3 Experiments

Benchmarks. We evaluate DELM on four agentic coding benchmarks. (1) Terminal-Bench 4.0 (Merrill et al., 2026). The benchmark consists of tasks that agents solve in live terminal environments, such as configuring systems, installing dependencies, and debugging failures. The tasks require long sequences of shell commands and agents benefit from sharing discovered environment state and failed attempts. (2) DeepSWE v1.1 (Huang and Jiang, 2026). The benchmark contains long-horizon software-engineering tasks. Agents must localize faults, understand interdependent modules, and debug across large codebases. It tests whether agents can reuse each other’s localization and debugging progress instead of repeating it. (3) SWE-bench Verified (Jimenez et al., 2024). The benchmark is built from real-world GitHub issues, with human-verified task specifications and tests. Agents inspect the repository, identify the relevant files, implement a fix, and validate it through tests. (4) ProgramBench (Yang et al., 2026a). Each task gives the agent a compiled executable and its documentation, and asks it to re-implement the program from scratch. Submissions are scored by hidden behavioral tests that compare the candidate against the original program. We test ProgramBench on two hard tasks, ctags and pandoc, and track the test pass rate over a fixed 120-minute budget.



> 💡 **四基准设置**: 覆盖 Terminal-Bench 4.0（终端环境中配置系统、装依赖、调试等长序列命令）、DeepSWE v1.1（大代码库跨模块故障定位）、SWE-bench Verified（真实 GitHub issue 修复）与 ProgramBench（依据可执行文件和文档从零重实现，作者只取 ctags 和 pandoc 两题，限时 120 分钟）。

Because DELM targets long-horizon tasks where coordination matters, we evaluate on a subset of long-running tasks from Terminal-Bench 4.0 and DeepSWE v1.1. We select tasks based on the wall-clock latency of Codex with GPT-6-Astra: for Terminal-Bench 4.0, we select 10 tasks with latency between 15 minutes and 1 hour; for DeepSWE v1.1, we select 10 tasks with latency of at least 10 minutes. The full list of selected tasks is provided in Appendix B.



> 💡 **任务筛选标准**: 针对协调需求高的长时任务，依据 Codex+GPT-6-Astra 的运行延迟（Terminal-Bench 15-60 分钟，DeepSWE ≥10 分钟）各选出 10 个代表性任务，完整清单在附录 B。

Baselines. We compare DELM with production coding agent harnesses, both in their default mode and with their native subagents enabled, as well as with a research centralized multi-agent system. Codex (v0.153.4) (OpenAI, 2025) and Claude Code (v2.1.280) (Anthropic, 2026) are agentic coding harnesses that enable language models to work on coding tasks using tools for file editing and shell execution. Codex (subagent) and Claude Code (subagent) use the same harnesses, with one addition: the task prompt ends with the instruction “Use [Codex/Claude Code]’s native subagents to help solve this task.” This lets the main agent spawn and coordinate sub-agents through each harness’s built-in mechanism. AOrchestra (Ruan et al., 2026) is a centralized multi-agent system. Its main orchestrator creates specialized sub-agents on demand and instantiates each one as a tuple of instruction, context, tools, and model. On SWE-bench Verified, we also compare with mini-SWEagent (Yang et al., 2024), a lightweight software-engineering agent that uses a simple linear loop with Bash as its main interface.



> 💡 **基线对比设置**: 对比生产级工具（Codex v0.153.4 / Claude Code v2.1.280）及其原生子代理模式（子代理模式仅在任务提示末尾加一句“用原生子代理帮忙”）、集中式多智能体系统 AOrchestra，以及 SWE-bench Verified 上的轻量单代理 mini-SWEagent。

Models and implementation. On Terminal-Bench 4.0 and DeepSWE v1.1, we use two frontier base models: GPT-6-Astra (OpenAI, 2026b) and Claude Opus 5.5 (Anthropic, 2026), both with xhigh reasoning effort. We build DELM on top of Codex for GPT-6-Astra and on top of Claude Code for Claude Opus 5.5. Every baseline uses the same base model as DELM in its setting, so the only difference between them is how agents coordinate. On SWE-bench Verified, all methods use Gemini 3 Flash (Doshi, 2025), accessed through OpenRouter with default reasoning and sampling parameters. For this setting, we route the Claude Code CLI through OpenRouter to support the non-Anthropic model. For every task, we evaluate DELM with $n \in \{ 2 , 4 \}$ parallel agents.



> 💡 **模型与并行配置**: Terminal-Bench 与 DeepSWE 用 GPT-6-Astra 或 Claude Opus 5.5（xhigh 推理），DELM 分别搭在 Codex 与 Claude Code 上，基线使用同一 base model，以把差异限定在协调方式；SWE-bench 统一用 Gemini 3 Flash（经 OpenRouter）。注意 $n\in\{2,4\}$ 只针对 DELM，并非所有方法都跑多代理。

Metrics. We report task accuracy, wall-clock latency, and cost. Wall-clock latency is the time from the first API call to the last generated token. We use two cost measures: Cost/Task is the total API cost of all agents in a single run, and Cost/Submission divides this total by the number of agents, since each DELM agent produces its own submission that is scored independently. All results are averaged over three runs and reported as mean ± standard deviation.



> 💡 **评估指标定义**: 报告准确率、墙钟延迟（首次 API 调用到最后生成 token）及两种成本：Cost/Task 为单次运行全部代理的 API 总花费，Cost/Submission 为总花费除以代理数（每个 DELM 代理各自独立提交并计分）。结果取三次运行均值±标准差。

## 3.1 Terminal-Bench 4.0

We evaluate on the 10 long-horizon Terminal-Bench 4.0 tasks described in Appendix B. Table 2 shows that DELM improves both accuracy and latency under both base models. With GPT-6-Astra, DELM (n = 2) reaches 85.00% accuracy, 13.3 points higher than the strongest baseline (Codex and Codex with subagents, both 71.67%), and runs 1.54× faster than Codex, at a higher Cost/Task (\$13.65 vs. \$9.01) but a lower Cost/Submission (\$6.83). With Claude Opus 5.5, DELM $( n = 2 )$ reaches 93.33%, 11.7 points above Claude Code, while cutting latency from 130.75 to 64.77 minutes (2.02×) at a lower Cost/Task (\$17.91 vs. \$20.31).



> 💡 **Terminal-Bench 主结果**: DELM ($n=2$) 在两个模型上都同时改善准确率与延迟。GPT-6-Astra 下准确率 85.00%，比最强基线（Codex 与 Codex 子代理同为 71.67%）高 13.3 个百分点，运行速度快 1.54×，Cost/Task 更高（$13.65 vs $9.01）但 Cost/Submission 更低（$6.83）；Claude Opus 5.5 下 93.33%，比 Claude Code 高 11.7 个百分点，延迟从 130.75 降到 64.77 分钟（2.02×），总成本也更低（$17.91 vs $20.31）。

Increasing to n=4 gives the largest speedups (2.05× and 2.49×): more work proceeds in parallel, and findings reach peers earlier. It also gives the lowest Cost/Submission, since each agent does a smaller share of the work and total cost grows more slowly than the number of submissions. Accuracy drops by 3–4 points relative to $n { = } 2 ,$ , because each agent still submits a complete solution while building less of it itself. More of each solution is assembled from peers’ components, which raises the risk of integration errors and lets a defect in one shared result affect several submissions. n=4 nonetheless remains above every baseline.



> 💡 **并行度影响分析**: $n=4$ 带来最大加速（2.05× 与 2.49×）和最低 Cost/Submission，代价是准确率较 $n=2$ 下降 3–4 个百分点——每个代理仍要提交完整方案，但自己完成的部分更少，更多组件来自同伴，集成错误和单点缺陷扩散的风险上升。即便如此 $n=4$ 仍高于所有基线。

Native subagent baselines give model-dependent trade-offs in accuracy and latency, but they consistently raise cost. Codex (subagent) matches vanilla Codex in accuracy and runs 1.34× faster, but at 2.9× the cost (\$25.80 vs. \$9.01). Claude Code with subagents lowers accuracy from 81.67% to 80.00% while increasing latency and raising cost by 1.5× (\$30.88 vs. \$20.31). The trajectories show that delegation adds work rather than dividing it. Subagents unaware of one another repeat parts of the same investigation; and the main agent adds further rounds of testing and review while remaining responsible for integration and final delivery. In contrast, DELM $\scriptstyle ( n = 2 )$ costs less per task than either subagent baseline (\$13.65 and \$17.91) because it keeps communication cheap and makes knowledge reuse direct.



> 💡 **原生子代理缺陷**: 现有 Harness 的子代理模式在准确率和延迟上随模型而异（Codex 子代理准确率持平、快 1.34×，Claude Code 加子代理反而从 81.67% 降到 80.00% 且更慢），但成本一律上升（2.9× 与 1.5×）。作者从轨迹看出的原因是委派增加了工作，而非分摊工作：彼此不知情的子代理重复同一调查，主代理还要多做测试与审查并负责集成。

Table 2: Comparison on Terminal-Bench 4.0 across two base models. Best mean values within each model are bolded. Cost/Task sums the costs of all agents in a single run. Cost/Submission divides this total by the number of agents, as each agent produces one submission that is scored independently.
<table><tr><td>Method</td><td>Avg. Acc (%)</td><td>Avg. Latency</td><td>Cost/Task</td><td>Cost/Submission</td></tr><tr><td colspan="5">GPT-6-Astra</td></tr><tr><td>Codex</td><td> $7 1 . 6 7 \pm 1 1 . 5 5$ </td><td> $2 6 . 9 1 \pm 0 . 9 1 \mathrm { m i n } \left( 1 . 0 0 \times \right)$ </td><td> $\$ 9.01\pm \$ 90.71$ </td><td> $\$ 9.01\pm \$ 90.71$ </td></tr><tr><td>Codex (subagent)</td><td> $7 1 . 6 7 \pm 1 1 . 6 9$ </td><td> $2 0 . 0 4 \pm 1 . 9 4 \operatorname* { m i n } { \left( 1 . 3 4 \times \right) }$ </td><td> $\$ 25.80\pm \$ 92.21$ </td><td> $\$ 25.80\pm \$ 92.21$ </td></tr><tr><td>AOrchestra</td><td> $7 0 . 0 0 \pm 5 . 0 0$ </td><td> $2 5 . 7 8 \pm 1 . 1 5 \operatorname* { m i n } { \left( 1 . 0 4 \times \right) }$ </td><td> $\$ 123,456,7$ </td><td> $\$ 123,456,7$ </td></tr><tr><td>DELM (n = 2)</td><td> ${ \bf 8 5 . 0 0 \pm 1 3 . 2 3 }$ </td><td> $1 7 . 4 7 \pm 1 . 8 2$  min (1.54×)</td><td> $\$ 13.45\pm91.49$ </td><td> $\$ 6.83\pm \$ 90.74$ </td></tr><tr><td>DELM (n = 4)</td><td> $8 1 . 6 7 \pm 1 2 . 3 3$ </td><td> ${ \bf 1 3 . 1 6 \pm 1 . 4 3 }$  min (2.05×)</td><td> $\$ 23.90\pm \$ 92.27$ </td><td> $\$ 5.97\pm90.57$ </td></tr><tr><td colspan="5">Claude Opus 5.5</td></tr><tr><td>Claude Code</td><td> $8 1 . 6 7 \pm 2 . 8 9$ </td><td> $1 3 0 . 7 5 \pm 6 . 2 7 \mathrm { m i n } \left( 1 . 0 0 \times \right)$ </td><td> $\$ 20.31\pm \$ 90.23$ </td><td> $\$ 20.31\pm \$ 90.23$ </td></tr><tr><td>Claude Code (subagent)</td><td> $8 0 . 0 0 \pm 5 . 0 0$ </td><td> $1 3 8 . 5 8 \pm 1 0 . 1 7 \operatorname* { m i n } { ( 0 . 9 4 \times ) }$ </td><td> $\$ 30.88\pm \$ 3.17$ </td><td> $\$ 30.88\pm \$ 3.17$ </td></tr><tr><td>AOrchestra</td><td> $7 6 . 6 7 \pm 5 . 7 7$ </td><td> $1 5 7 . 2 1 \pm 9 . 0 3 \mathrm { m i n } ( 0 . 8 3 \times )$ </td><td> $\$ 24.22\pm \$ 1.05$ </td><td> $\$ 24.22\pm \$ 1.05$ </td></tr><tr><td>DELM (n = 2)</td><td> ${ \bf 9 3 . 3 3 \pm 3 . 3 3 }$ </td><td> $6 4 . 7 7 \pm 5 . 0 3 \mathrm { m i n } \ : ( 2 . 0 2 \times )$ </td><td> $\$ 123,456,7$ </td><td> $\$ 8.96\pm \$ 50.59$ </td></tr><tr><td> $\mathbf { D } \mathbf { E } \mathbf { L } \mathbf { M } \left( n = 4 \right)$ </td><td> $8 9 . 1 7 \pm 5 . 2 0$ </td><td> ${ \bf 5 2 . 5 1 \pm 4 . 4 3 \operatorname* { m i n } { ( { \bf 2 . 4 9 \times } ) } }$ </td><td> $\$ 29.98\pm \$ 90.91$ </td><td> $\$ 7.50\pm \$ 90.23$ </td></tr></table>



> 💡 **Table 2 解读**: 在 Terminal-Bench 4.0 上，DELM 方法（n=2/4）在 GPT-6-Astra 和 Claude Opus 5.5 两个基座模型下均取得最高平均准确率（如 GPT-6 下 85.00%），且延迟显著降低（最快达 13.16 min）。尽管部分成本数据存在格式异常（如 $123,456,7），但总体显示 DELM 在保持高准确率的同时优化了时效性。

## 3.2 DeepSWE v1.1

We evaluate on the 10 long-horizon DeepSWE v1.1 tasks listed in Appendix B. Table 3 shows that DELM again achieves the best accuracy and latency under both base models. With GPT-6-Astra, DELM (n=2) reaches 98.33% accuracy, 8.3 points above the strongest baseline, Codex with subagents (90.00%), and runs 1.25× faster than Codex. It also costs less per task than Codex with subagents (\$16.83 vs. \$24.97). With Claude Opus 5.5, the gains are larger: DELM (n=4) reaches 90.83%, 17.5 points above the strongest baseline, AOrchestra (73.33%), and runs 1.57× faster than Claude Code. DELM $\scriptstyle ( n = 2 )$ reaches 88.33% at roughly the same Cost/Task as Claude Code (\$12.15 vs. \$12.01).



> 💡 **DeepSWE 结果**: 在 10 个长周期 DeepSWE v1.1 任务（Appendix B）上，GPT-6-Astra 下 DELM (n=2) 达 98.33%，比最强基线 Codex with subagents（90.00%）高 8.3 个百分点，且比 Codex 快 1.25×、单任务成本更低（\$16.83 vs. \$24.97）；Claude Opus 5.5 下 DELM (n=4) 达 90.83%，比 AOrchestra（73.33%）高 17.5 个百分点，比 Claude Code 快 1.57×。作者据此称 DELM 在两个基座模型下都取得最优准确率与延迟。

Native subagents hurt Claude Code most: accuracy falls from 71.67% to 50.00%, while latency and cost increase by 15.9% and 65.1%, respectively. Trajectory analysis shows that most of its failed runs ended with an empty patch or a benchmark timeout, often because the main agent held the final commit while waiting on subagents that were cut off by the CLI’s idle limit<sup>1</sup> or that prolonged integration with extra review rounds. By comparison, DELM (n=2) achieves higher accuracy and lower latency and cost than the corresponding native subagent baselines. Its shared context makes recorded findings and failed attempts directly accessible to peer agents. These results highlight that the value of parallel work depends on how effectively it is reused and converted into completed submissions, rather than on delegation alone.



> 💡 **子代理失效分析**: 这组 71.67%→50.00% 的下降出自紧随本节讨论的 DeepSWE v1.1（Table 3）；Table 2 里 Claude Code 的子代理版本是 81.67%→80.00%，不要把两处混在一起。作者的归因是主代理为等子代理而推迟最终提交，子代理或被 CLI 空闲上限截断、或因额外评审轮次拖长集成，失败轨迹多收在空 patch 或超时。DELM 的共享上下文让已记录的发现和失败尝试对同级 agent 直接可见，这是它比原生子代理更准、更省时的机制解释。

Table 3: Comparison on DeepSWE v1.1 across two base models. Best mean values within each model are bolded. Cost/Task sums the costs of all agents in a single run. Cost/Submission divides this total by the number of agents, as each agent produces one submission that is scored independently.
<table><tr><td>Method</td><td>Avg. Acc (%)</td><td>Avg. Latency</td><td>Cost/Task</td><td>Cost/Submission</td></tr><tr><td colspan="5">GPT-6-Astra</td></tr><tr><td>Codex</td><td> $8 8 . 3 3 \pm 2 . 8 9$ </td><td> $1 6 . 5 7 \pm 0 . 3 9 \operatorname* { m i n } { \left( 1 . 0 0 \times \right) }$ </td><td> $\$ 9.40\pm \$ 90.18$ </td><td> $\$ 9.40\pm \$ 90.18$ </td></tr><tr><td>Codex (subagent)</td><td> $9 0 . 0 0 \pm 0 . 0 0$ </td><td> $1 5 . 7 6 \pm 2 . 0 5 \operatorname* { m i n } { \left( 1 . 0 5 \times \right) }$ </td><td> $\$ 24.97\pm \$ 3.15$ </td><td> $\$ 24.97\pm \$ 3.15$ </td></tr><tr><td>AOrchestra</td><td> $8 3 . 3 3 \pm 7 . 6 4$ </td><td> $1 8 . 3 1 \pm 1 . 0 8 \operatorname* { m i n } { ( 0 . 9 1 \times ) }$ </td><td> $\$ 10.74\pm \$ 90.97$ </td><td> $\$ 10.74\pm \$ 90.97$ </td></tr><tr><td>DELM (n = 2)</td><td> ${ \bf 9 8 . 3 3 \pm 2 . 8 9 }$ </td><td> $1 3 . 2 9 \pm 0 . 9 8 \operatorname* { m i n } { \left( 1 . 2 5 \times \right) }$ </td><td> $\$ 16.83\pm \$ 1.65$ </td><td> $\$ 8.42\pm \$ 82$ </td></tr><tr><td>DELM (n = 4)</td><td> $9 0 . 0 0 \pm 1 0 . 0 0$ </td><td> ${ \bf 1 2 . 3 7 \pm 0 . 6 6 \operatorname* { m i n } { ( { \bf 1 . 3 4 } \times ) } }$ </td><td> $\$ 30.87\pm \$ 51.09$ </td><td> $\$ 72$ </td></tr><tr><td colspan="5">Claude Opus 5.5</td></tr><tr><td>Claude Code</td><td> $7 1 . 6 7 \pm 2 . 8 9$ </td><td> $4 9 . 5 8 \pm 3 . 1 1 \mathrm { m i n } \left( 1 . 0 0 \times \right)$ </td><td> $\$ 123,456,7$ </td><td> $\$ 12.01\pm \$ 90.60$ </td></tr><tr><td>Claude Code (subagent)</td><td> $5 0 . 0 0 \pm 1 0 . 0 0$ </td><td> $5 7 . 4 6 \pm 2 . 1 6 \operatorname* { m i n } { ( 0 . 8 6 \times ) }$ </td><td> $\$ 19.83\pm \ S 0.99$ </td><td> $\$ 19.83\pm \$ 80.99$ </td></tr><tr><td>AOrchestra</td><td> $7 3 . 3 3 \pm 5 . 7 7$ </td><td> $4 7 . 3 2 \pm 2 . 8 6 \operatorname* { m i n } { \left( 1 . 0 5 \times \right) }$ </td><td> $\$ 12.11\pm90.52$ </td><td> $\$ 12.11\pm90.52$ </td></tr><tr><td> $\mathbf { D } \mathbf { E } \mathbf { L } \mathbf { M } \left( n = 2 \right)$ </td><td> $8 8 . 3 3 \pm 2 . 8 9$ </td><td> $3 2 . 9 8 \pm 2 . 4 7 \operatorname* { m i n } \left( 1 . 5 0 \times \right)$ </td><td> $\$ 12.15\pm \$ 50.57$ </td><td> $\$ 6.07\pm \$ \$ 90.28$ </td></tr><tr><td> $\mathbf { D } \mathbf { E } \mathbf { L } \mathbf { M } \left( n = 4 \right)$ </td><td> ${ \bf 9 0 . 8 3 \pm 8 . 7 8 }$ </td><td> $\mathbf { 3 1 . 6 1 \pm 0 . 9 6 } \operatorname* { m i n } \left( \mathbf { 1 . 5 7 } \times \right)$ </td><td> $\$ 24.49\pm \$ 80.67$ </td><td> $\$ 6.12\pm \$ 90.17$ </td></tr></table>



> 💡 **Table 3 解读**: GPT-6-Astra 上 DELM (n=2) 以 98.33% 超过 Codex 的 88.33%；Claude Opus 5.5 上 DELM (n=4) 达 90.83%（同表基线 Claude Code 71.67%）。代价是 Cost/Task 上升（$9.40→$16.83/$30.87，Claude 组约 $12→$12.15/$24.49），但 n=2 的 Cost/Submission 更低（$8.42、$6.07），延迟也更快（GPT 组 n=4 为 12.37 min，1.34×；Claude 组 31.61 min，1.57×）。

Coordination with a low-cost model. We additionally evaluate DeepSeek-V4.1-Flash (DeepSeek-AI, 2026b) with Codex on the same 10 long-horizon DeepSWE tasks. Table 4 shows that DELM $( n = 2 )$ improves mean accuracy from 76.67% to 88.33% (+11.7 points) and reduces mean latency from 27.67 to 22.25 minutes (1.24× faster). Notably, DELM with DeepSeek-V4.1-Flash matches the mean accuracy of vanilla Codex with GPT-6-Astra (88.33%) at approximately 1/28 of its total cost per task (\$0.33 vs. \$9.40), though it remains slower (22.25 vs. 16.57 minutes), consistent with the more frequent revisions and failed attempts observed in its trajectories. These results suggest that decentralized coordination can make a low-cost model more competitive on long-horizon tasks.



> 💡 **低成本模型验证**: DeepSeek-V4.1-Flash 上 DELM (n=2) 把准确率从 76.67% 提到 88.33%（+11.7 点），延迟 27.67→22.25 min。作者强调它以约 1/28 的每任务成本（$0.33 vs $9.40）追平 GPT-6-Astra 下 Codex 的 88.33%，但更慢（22.25 vs 16.57 min）；这里的 $9.40 是 GPT-6-Astra 组的 Cost/Task，比较口径需留意。

Table 4: Comparison of Codex and DELM with DeepSeek-V4.1-Flash on the same 10 long-horizon DeepSWE v1.1 tasks. Results are mean ± standard deviation across three repeats.
<table><tr><td>Method</td><td>Avg. Acc (%)</td><td>Avg. Latency</td><td>Cost/Task</td><td>Cost/Submission</td></tr><tr><td>DeepSeek-V4.1-Flash</td><td></td><td></td><td></td><td></td></tr><tr><td>Codex</td><td> $7 6 . 6 7 \pm 1 1 . 5 5$ </td><td> $2 7 . 6 7 \pm 2 . 1 7 \operatorname* { m i n } { \left( 1 . 0 0 \times \right) }$ </td><td> $\$ 19\pm \$ 0.01$ </td><td> $\$ 0.19\pm \ S 0.01$ </td></tr><tr><td> $\mathbf { D } \mathbf { E } \mathbf { L } \mathbf { M } \left( n = 2 \right)$ </td><td> ${ \bf 8 8 . 3 3 \pm 7 . 6 4 }$  </td><td> $\mathbf { 2 2 . 2 5 \pm 0 . 9 0 \operatorname* { m i n } { ( \mathbf { 1 . 2 4 } \times } ) }$ </td><td> $\$ 0.33\pm \ S 0.01$ </td><td> $\$ 17\pm \$ 0.01$  </td></tr></table>



> 💡 **Table 4 数据确认**: 10 个长程任务、三次重复下，DELM (n=2) 准确率 88.33±7.64%、延迟 22.25±0.90 min（1.24×）；Cost/Task $0.33 约为 Codex $0.19 的 1.7 倍，但按提交平摊的 Cost/Submission $0.17 低于 $0.19，即总开销上升、单份提交更便宜。

Table 5: Comparison on SWE-bench Verified with Gemini 3 Flash. Best mean values within each model are bolded. Cost/Task sums the costs of all agents in a single run. Cost/Submission divides this total by the number of agents, as each agent produces one submission that is scored independently.
<table><tr><td>Method</td><td>Avg. Acc (%)</td><td>Avg. Latency</td><td>Cost/Task</td><td>Cost/Submission</td></tr><tr><td>Gemini 3 Flash</td><td></td><td></td><td></td><td></td></tr><tr><td>Claude Code</td><td> $4 9 . 3 2 \pm 1 . 9 8$ </td><td> $1 2 9 . 1 3 \pm 5 . 1 2 \mathrm { s } ( 1 . 0 0 \times )$ </td><td> $\$ 1.00\pm \$ 90.00^ { a }$ </td><td> $\$ 1.00\pm \$ 90.00^ { a }$ </td></tr><tr><td>mini-SWE-agent</td><td> $5 4 . 7 3 \pm 2 . 3 7$ </td><td> $9 1 . 7 8 \pm 3 . 5 3 \mathrm { s } ( 1 . 4 1 \times )$ </td><td> $\$ 0.26\pm \$ 90.07$ </td><td> $\$ 0.26\pm \$ 90.07$ </td></tr><tr><td>AOrchestra</td><td> $5 5 . 2 6 \pm 2 . 0 3$ </td><td> $8 9 . 9 4 \pm 3 . 0 7 \mathrm { s } \left( 1 . 4 4 \times \right)$ </td><td> $\$ 0.24\pm \$ 90.04$ </td><td> $\$ 0.24\pm \$ 90.04$ </td></tr><tr><td>DELM (n = 2)</td><td> $6 3 . 7 2 \pm 2 . 2 9$ </td><td> $7 1 . 8 6 \pm 2 . 8 8 \mathrm { s } ( 1 . 8 0 \times )$ </td><td> $\$ 0.24\pm \$ \$ 0.05$ </td><td> $\$ 0.12\pm \$ 90.03$ </td></tr><tr><td>DELM (n = 4)</td><td> ${ \bf 6 6 . 0 8 \pm 1 . 8 6 }$ </td><td> $6 1 . 0 1 \pm 2 . 6 3 \mathrm { ~ s ~ } ( \mathbf { 2 . 1 2 } \times )$ </td><td> $\$ 0.47\pm \$ 90.07$ </td><td> $\$ 0.12\pm \$ 90.02$ </td></tr></table>



> 💡 **Table 5 跨基准表现**: Gemini 3 Flash 上 DELM (n=4) 准确率最高 66.08%（Claude Code 49.32%），延迟 61.01 s，相对基线 129.13 s 约 2.12× 加速；Cost/Submission 为 $0.12，低于 mini-SWE-agent 的 $0.26 与 AOrchestra 的 $0.24，但 n=4 的 Cost/Task $0.47 约是 n=2 的两倍。

<sup>a</sup> The Claude Code CLI sends cache\_control blocks in the Anthropic API format, so cache reuse only works if the upstream provider honors those blocks. For Gemini-3-Flash the real cost without cache reuse is therefore around \$1 per task.



> 💡 **成本计算说明**: 脚注 a 解释 Claude Code 以 Anthropic 格式发送 cache_control 块，只有上游服务商支持时缓存才生效；Gemini-3-Flash 不复用这些缓存，故该行真实成本约 $1/task（表中以 $1.00^a 标出），远高于其余方法。

## 3.3 SWE-bench Verified

SWE-bench Verified tests whether DELM still helps on a widely used software-engineering benchmark with a lightweight base model. We build DELM based on AOrchestra’s harness and compare it with Claude Code, mini-SWE-agent, and AOrchestra, all using Gemini 3 Flash. Table 5 shows that DELM outperforms all baselines in both accuracy and latency. With n = 4, DELM reaches 66.08% accuracy, 10.8 points higher than the strongest baseline, AOrchestra (55.26%). It is also 2.12× faster than Claude Code and 1.47× faster than AOrchestra. With n = 2, DELM still improves accuracy by 8.5 points over AOrchestra at the same Cost/Task (\$0.24), which halves the cost per submission. Going from n = 2 to n = 4 roughly doubles Cost/Task but leaves Cost/Submission unchanged at \$0.12, and it further improves both accuracy and latency.



> 💡 **SWE-bench 结果**: 所有系统共用 Gemini 3 Flash，DELM 建在 AOrchestra 的 harness 上；n=4 时准确率 66.08%，比最强基线 AOrchestra（55.26%）高 10.8 点，比 Claude Code 快 2.12×、比 AOrchestra 快 1.47×。n=2 时在相同 Cost/Task（$0.24）下高出 8.5 点，并因每次提交成本减半到 $0.12 而在成本上更划算。

These results show that multiple agents help even on largely sequential tasks. A single agent rarely completes every step correctly on its first attempt. It often inspects irrelevant files, pursues a wrong root cause, or submits a patch that fails the tests before it recovers. Each such detour costs time, and a single agent can only discover it by paying that cost itself. In DELM, agents explore different paths in parallel and record what they learn in the shared context. Once one agent has hit a dead end, the others can skip it, and once one has localized a fault, the others can build on that finding. This shortens the path to a correct fix, which explains why DELM is both faster and more accurate. A more detailed analysis is provided in Appendix D.



> 💡 **并行探索机制**: 解释了 DELM 为何更快更准：单智能体常因试错（如查错文件、修错补丁）浪费大量时间；DELM 通过并行探索不同路径并共享上下文，使其他智能体能跳过死胡同或基于已有发现继续，从而缩短解决路径。

## 3.4 ProgramBench

ProgramBench asks agents to rebuild an entire program from its compiled executable and documentation. There is no natural stopping point: the pass rate keeps rising for as long as agents keep implementing. We therefore use it to ask a different question from the preceding sections: given a fixed wall-clock budget, how much progress does each system make, and how quickly? We run Claude Code and DELM, built on top of Claude Code, with Claude Opus 5.5 (xhigh reasoning effort) on two hard tasks, ctags and pandoc. Each run has a 120-minute budget, and we track the hidden-test pass rate over time (Figure 5).



> 💡 **ProgramBench 设定**: 针对无自然终止点的程序重建任务，评估固定 120 分钟预算下的进度。对比 Claude Code 与基于其构建的 DELM（使用 Claude Opus 5.5），重点考察系统在硬任务 ctags 和 pandoc 上的测试通过率随时间的变化。

![](../images/a4baac7652a2c920c55ba126d581583cc6b111619edad5a971d10cafb1246596.jpg)



> 💡 **图片位置**: 这里的图片没有图号，也未获得它的视觉信息，无法描述曲线细节。本节数字请以正文和 Figure 5 caption 为准。

![](../images/2bc563cdedeb95d67c2024492a281996dc73909c60d839837081b5674029de91.jpg)  
Figure 5: DELM makes more progress within the same time budget. Test pass rate over time on two ProgramBench tasks with Claude Opus 5.5 under a 120-minute budget. DELM uses Claude Code as its per-agent harness. From 60 minutes onward, the curves stay ordered (n=4 above n=2 above Claude Code), so DELM reaches any given pass rate sooner and ends the budget higher.



> 💡 **Figure 5 解读**: 在两条 ProgramBench 任务上，通过率排序为 DELM(n=4) > DELM(n=2) > Claude Code，但 caption 把它限定在 60 分钟之后才稳定；也就是说 DELM 更早达到任一给定通过率，预算结束时也更高。

Higher pass rates under the same budget. At the end of the budget, DELM (n=2) improves over Claude Code by 6.73 points on ctags (41.05% vs. 34.32%) and by 8.17 points on pandoc (38.26% vs. 30.09%). With $n { = } 4 ,$ the gains grow to 18.96 and 19.91 points (53.28% and 50.00%). Each DELM agent runs the same Claude Code harness and model as the baseline. The gains therefore come from the coordination layer, not from a stronger individual agent.



> 💡 **最终性能对比**: 在预算结束时，DELM (n=2) 在 ctags 和 pandoc 上分别比 Claude Code 高 6.73 和 8.17 个百分点；n=4 时优势扩大至约 19 个百分点。由于底层模型和工具一致，增益完全归因于 DELM 的协调机制而非单体能力增强。

Faster progress toward the same result. The advantage is not limited to the final checkpoint. From 60 minutes onward, the curves stay ordered on both tasks, with $n { = } 4$ above $n { = } 2$ above Claude Code, so DELM reaches any given pass rate sooner. On ctags, Claude Code reaches its final 34.32% only at the end of the budget. DELM matches it after about 95 minutes with $n { = } 2$ and after about 60 minutes with $n { = } 4$ , which is half the budget. Decentralized coordination thus turns each additional agent into faster progress, not only a better final result.



> 💡 **收敛速度分析**: DELM 的优势体现在全过程。例如在 ctags 任务中，Claude Code 需跑满 120 分钟才达到的 34.32% 通过率，DELM (n=4) 仅需约 60 分钟即可达成。这证明去中心化协调能将额外智能体转化为更快的迭代速度，而不仅仅是更高的上限。

## 3.5 Ablation Study

To isolate the contribution of each coordination mechanism in Table 1, we remove one mechanism at a time from DELM $( n = 2 )$ on Terminal-Bench 4.0 and keep everything else fixed. w/o early sharing lets agents publish results only after they complete their claimed subtask. w/o peer-status hides item owners and session status, so agents cannot see what their peers are currently working on. w/o shared context removes the shared context entirely, and agents coordinate by sending messages directly to each other. Figure 6 shows the results.



> 💡 **消融实验设计**: 在 Terminal-Bench 4.0 上逐一移除 DELM 的核心协调机制：早期共享（w/o early sharing）、对等状态感知（w/o peer-status）和共享上下文（w/o shared context）。目的是隔离各组件对整体性能的独立贡献。

The most striking effect is on latency. With the full system, the two agents run 1.54× (GPT-6-Astra) and 2.02× (Claude Opus 5.5) faster than the single-agent harness, whereas every ablation falls to



> 💡 **延迟关键发现**: 完整系统能实现 1.54x-2.02x 的速度提升，但移除任一协调机制后，加速比降至 0.82-1.09x（即无加速甚至变慢）。这说明并行本身不带来收益，有效的协调机制才是速度提升的根本来源。

0.82–1.09×. Two parallel agents are thus no faster than one unless they coordinate effectively, so the speedup of DELM comes from coordination rather than from parallelism alone.



> 💡 **协调必要性**: 缺少有效协调时，两个并行智能体的加速比只有 0.82–1.09×，并不优于单个智能体；作者据此把 DELM 的加速归因于协调机制，而非并行本身。

Removing peer-status awareness causes the largest slowdown under both models (0.87× and 0.82×), and with Claude Opus 5.5 it also causes the largest accuracy drop (−3.3 points). Without knowing what their peers are working on, agents tend to claim overlapping work and discover the duplication only after both have spent time on it. Removing early sharing brings the speedup back to about 1× (0.98× and 0.93×), because an agent whose work depends on a peer’s output must wait until the peer finishes its entire subtask instead of starting from partial results. Replacing the shared context with direct messaging retains slightly more speed than the other ablations (1.09× and 1.03×) but causes the largest accuracy drop with GPT-6-Astra (−3.0 points). Direct messages still let agents exchange information, but they do not accumulate into a persistent record that agents can revisit, build on, or correct, so findings and fixes are more easily missed.



> 💡 **各组件影响**: 移除 peer-status 在两个模型上减速最大（0.87×、0.82×），且在 Claude Opus 5.5 上精度跌幅也最大（−3.3 点），原因是智能体容易认领重叠工作；移除 early sharing 后加速回到约 1×（0.98×、0.93×），因为依赖方必须等对方做完整个子任务；用直接消息替代共享上下文保留略多加速（1.09×、1.03×），但在 GPT-6-Astra 上精度跌幅最大（−3.0 点），因为消息不会沉淀为可复用的记录。

![](../images/d71a6f25727c21a15dc288ccd3d159e411d79c38a108931d07bbdc7623508f01.jpg)  
Figure 6: Ablation on Terminal-Bench 4.0. Arrows point from DELM (n = 2) to each variant with one mechanism removed.



> 💡 **Figure 6 阅读要点**: caption 说明箭头从 DELM(n=2) 指向每个只去掉一个机制的变体。定量比较（延迟加速比 1.54×/2.02× 对比 0.82–1.09×，以及精度跌幅）在正文，未获得图像视觉信息，不做曲线描述。

