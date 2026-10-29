[← 返回 README](../README.md)

## C.1 Combining DELM with RLM

Recursive Language Models (RLMs) (Zhang et al., 2025) process long contexts by recursively selecting context segments, issuing sub-calls, and composing the resulting partial answers. Crucially, RLM performs this process through a code-mediated Read-Eval-Print Loop (REPL): the long input is stored as an external variable that the model can inspect, parse, and aggregate programmatically. This makes RLM a natural fit for OOLONG (Bertsch et al., 2025), an aggregation-heavy benchmark where each sample consists of timestamped, user-attributed entries and answering a question often requires classifying many entries before computing a distributional answer.



> 💡 **RLM机制与适配性**: RLM通过代码介导的REPL循环处理长文本，将输入存为外部变量供程序解析。这种结构化操作特性使其天然契合OOLONG这类需要分类和聚合大量时间戳条目的基准测试。

We first evaluate DELM and RLM separately on both LongBench-v2 Multi-Doc QA and OOLONG using GPT-5 with medium reasoning (RLM uses GPT-5-mini for sub-calls and recursion depth=1). As shown in Table 9, the two methods have complementary strengths. On LongBench-v2, DELM achieves higher average accuracy than RLM at roughly the same cost per task. On OOLONG, however, DELM underperforms RLM in both average accuracy and cost. This is because OOLONG is closer to a structured data-processing benchmark than a conversational reasoning benchmark (such as LongBench-v2): many questions require exact counting, filtering, comparison, and tie handling. In this setting, the natural-language shared context used by DELM is not reliable. RLM performs better because its core interface is code-mediated: through a REPL environment, the model can inspect, parse, transform, and aggregate the input using executable programs and programmatic sub-calls. However, RLM still relies on centralized coordination, where recursive calls report back to a root process that must reconcile and aggregate their outputs. This centralized aggregation is less suitable for natural-language multi-document reasoning, helping explain RLM’s weaker LongBench-v2 performance. These results motivate a hybrid design that combines RLM’s precise REPL-based execution with DELM’s decentralized coordination.



> 💡 **互补性与条件**: 两者均在GPT-5（medium reasoning）下评测，RLM的子调用与递归使用GPT-5-mini、递归深度为1；LongBench-v2上DELM平均准确率略高（57.9% vs 55.8%），OOLONG上RLM反超（56.0%/$0.43 vs 53.3%/$0.47）。作者把DELM在结构化计数类任务上的失利归因于自然语言共享上下文不可靠，把RLM在多文档推理上的劣势归因于中心化聚合；后一条是解释性归因，原文未给单独消融。

Table 9: Comparison of RLM, DELM, and their hybrid on LongBench-v2 Multi-Doc QA and OOLONG using GPT-5.
<table><tr><td>Method</td><td>Acc.</td><td>Cost/Task</td></tr><tr><td>OOLONG</td><td></td><td></td></tr><tr><td>RLM</td><td>56.0%</td><td>$0.43</td></tr><tr><td>DELM</td><td>53.3%</td><td>$0.47</td></tr><tr><td>DELM+RLM</td><td>64.0%</td><td>$0.40</td></tr><tr><td>LongBench-v2 Multi-Doc QA</td><td></td><td></td></tr><tr><td>RLM</td><td>55.8%</td><td>$0.29</td></tr><tr><td>DELM</td><td>57.9%</td><td>$0.30</td></tr><tr><td>DELM+RLM</td><td>60.3%</td><td>$0.24</td></tr></table>



> 💡 **混合模型性能**: Table 9显示DELM+RLM混合方法在两个基准上均取得最佳表现。在OOLONG上准确率提升至64.0%且成本降至$0.40；在LongBench-v2上准确率达60.3%，成本仅$0.24，验证了结合两者优势的有效性。

We therefore combine the two methods by retaining RLM as the underlying reasoner while adding two DELM components: a shared context and a decentralized task queue. In this hybrid setting, there is no main-agent/sub-agent hierarchy; all agents are equally ranked RLM instances that coordinate through shared state. An initial RLM call produces a compact work plan, after which subtasks are placed into the task queue and claimed by parallel RLM workers. Each worker’s output is admitted into the shared context, and the system then generates any additional subtasks from the current problem state, and computes the final answer from the shared context once no further subtasks are needed. This design preserves RLM’s strength in precise code-mediated execution while adding DELM’s decentralized coordination. As shown in Table 9, the combined method achieves the best accuracy and lowest cost on both benchmarks, indicating that RLM and DELM are complementary rather than competing approaches.



> 💡 **去中心化协作架构**: 混合设计摒弃主从层级，采用平等的RLM实例通过共享上下文和去中心化任务队列协作。初始规划生成子任务后，并行工作节点产出结果汇入共享状态，既保留RLM的代码执行精度，又引入DELM的去中心化协调优势。

