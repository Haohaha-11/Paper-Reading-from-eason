[← 返回 README](../README.md)

## B Selected Tasks

DELM is designed for long-horizon tasks, where multiple agents have enough time to share progress and split up the remaining work. On short tasks, a single agent often finishes before any coordination can pay off. We therefore evaluate on a subset of long-running tasks from Terminal-Bench 4.0 and DeepSWE v1.1. We first ran the Codex baseline with GPT-6-Astra (xhigh reasoning effort) on each benchmark and recorded the wall-clock latency of every task. From Terminal-Bench 4.0, we selected 10 tasks whose latency fell between 15 minutes and 1 hour. The upper bound excludes tasks that run so long that repeated evaluation would be impractical. From DeepSWE v1.1, we selected 10 tasks with latency of at least 10 minutes. The selection criterion is based only on the baseline’s latency. Table 7 lists the selected tasks together with their Codex latencies.



> 💡 **任务筛选逻辑**: 本段先给理由再给筛选标准——DELM 面向需要多智能体共享进度、拆分剩余工作的长时程任务；在短任务中，单智能体往往在协作见效前就已完成，因此不纳入评估。作者只用 Codex（GPT-6-Astra，xhigh）基线的墙钟延迟筛选：从 Terminal-Bench 4.0 取 10 个 15 分钟至 1 小时的任务，下限保证协作有收益空间，上限避免重复评估过于耗时；从 DeepSWE v1.1 取 10 个至少 10 分钟的任务。筛选与 DELM 自身表现无关，不构成按结果挑任务的循环。

Table 7: Selected tasks from Terminal-Bench 4.0 and DeepSWE v1.1. Baseline latency is the average execution time of the Codex baseline using GPT-6-Astra at xhigh reasoning effort.  
Terminal-Bench 4.0
<table><tr><td>Task</td><td>Codex Latency</td></tr><tr><td>ks-solver-cpp</td><td>21.00 min</td></tr><tr><td>telecom-entity-resolution</td><td>24.23 min</td></tr><tr><td>vf2-speedup-networkx</td><td>23.42 min</td></tr><tr><td>biped-contact-dynamics</td><td>21.56 min</td></tr><tr><td>cumulative-layout-shift</td><td>39.34 min</td></tr><tr><td>retro-console-soc</td><td>27.28 min</td></tr><tr><td>rs-archive-clone</td><td>21.92 min</td></tr><tr><td>wdm-design</td><td>39.27 min</td></tr><tr><td>lake-temp-glm</td><td>32.40 min</td></tr><tr><td>payments-pipeline-fix</td><td>18.67 min</td></tr><tr><td>Average</td><td>26.91 min</td></tr></table>



> 💡 **Table 7 批读（Terminal-Bench）**: 上半张表列出 10 个入选任务及各自基线耗时，最快 18.67 min（payments-pipeline-fix），最慢 39.34 min（cumulative-layout-shift），平均 26.91 min，均落在前文 15–60 分钟区间内。从任务名看，涵盖 C++ 求解、图算法加速、控制与仿真、前端布局等方向，读者可从任务类型判断是否与自己的场景接近。

DeepSWE v1.1
<table><tr><td>Task</td><td>Codex Latency</td></tr><tr><td>oxvg-structural-selector-preservation</td><td>19.71 min</td></tr><tr><td>wasmi-trap-coredumps</td><td>14.55 min</td></tr><tr><td>happy-dom-abort-pending-body-reads</td><td>12.52 min</td></tr><tr><td>scriggo-method-declarations</td><td>17.35 min</td></tr><tr><td>dynamodb-toolbox-lazy-recursive-schemas</td><td>22.19 min</td></tr><tr><td>dynamodb-toolbox-conditional-attribute</td><td>16.78 min</td></tr><tr><td>opa-template-string-reconstruction</td><td>13.99 min</td></tr><tr><td>boa-hierarchical-evaluation-cancellation</td><td>13.28 min</td></tr><tr><td>numba-stencil-boundary-modes</td><td>15.91 min</td></tr><tr><td>pebble-durability-wait-apis</td><td>19.38 min</td></tr><tr><td>Average</td><td>16.57 min</td></tr></table>



> 💡 **Table 7 批读（DeepSWE）**: 下半张表 10 个任务耗时在 12.52–22.19 min 之间，平均 16.57 min，均满足至少 10 分钟门槛。需注意延迟是入选的唯一标准，因此这组数字是筛选规则的直接结果，不能当作数据集本身“耗时偏长”的独立证据；DeepSWE 一侧不设上限，但整体仍比 Terminal-Bench 短约 10 分钟。

