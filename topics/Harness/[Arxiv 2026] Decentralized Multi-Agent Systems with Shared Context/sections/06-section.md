[← 返回 README](../README.md)

## 5 Related Work

Multi-agent coordination. Recent LLM-based multi-agent systems improve performance by decomposing tasks across specialized or interacting agents. CAMEL (Li et al., 2023) instantiates role-playing agents to study autonomous cooperation, while MetaGPT (Hong et al., 2023) and Chat-Dev (Qian et al., 2024) organize collaboration through software-engineering roles and standardized workflows. More recent systems make orchestration adaptive. AOrchestra (Ruan et al., 2026) creates task-specific sub-agents on demand, ToolOrchestra (Su et al., 2025) trains a lightweight orchestrator to coordinate models and tools, Squeeze Evolve (Maheswaran et al., 2026) routes the stages of evolutionary inference to models of different cost and capability, and RecursiveMAS (Yang et al., 2026b) replaces text-level communication with recursive latent-state transfer. These systems coordinate through either a fixed workflow or a central orchestrator that decides what each agent sees and when progress is merged. Combee (Li et al., 2026) likewise identifies centralized aggregation as a bottleneck under high parallelism, but addresses it for parallel prompt learning rather than agent coordination.



> 💡 **多智能体综述**: 梳理了从固定角色（CAMEL/MetaGPT）到自适应编排（AOrchestra/ToolOrchestra）的演进，指出当前系统主要依赖中心调度器或固定工作流，而 Combee 虽关注聚合瓶颈但场景不同。

Blackboard and shared-memory architectures. Blackboard architectures coordinate problem solvers through a shared workspace (Erman et al., 1980, Nii, 1986). Recent LLM variants use a blackboard on which agents post messages for general reasoning (Han and Zhang, 2025) or to which agents volunteer information in response to requests for data discovery (Salemi et al., 2025). However, these systems still rely on a control agent to select which agent acts or which request is posted, and they treat the board mainly as a message channel. DELM removes the controller from the loop entirely. Agents claim work asynchronously from a task queue, see what their peers are working on, and build on or correct earlier findings by appending to a stateful shared context (Table 1). We further target long-horizon agentic coding, where shared progress includes repository and environment state rather than only textual messages.



> 💡 **黑板架构对比**: 回顾传统及 LLM 版黑板模型，强调其仍依赖控制代理且仅将板作为消息通道。DELM 的核心差异在于移除控制器，通过异步任务队列和状态共享实现去中心化协作。

Agentic coding harnesses. Language models become far more capable when coupled with executable environments that let them inspect state, invoke tools, and act on feedback. SWE-agent (Yang et al., 2024) and its lightweight variant mini-SWE-agent show that a well-designed agent-computer interface substantially improves software-engineering performance. Production harnesses such as Claude Code (Anthropic, 2026) and Codex (OpenAI, 2025) let models read repositories, edit files, execute commands, and increasingly delegate work to native subagents. Recursive Language Models (Zhang et al., 2025) apply a similar code-mediated loop to long-context reasoning by treating the prompt as an external environment. DELM complements these harnesses: rather than replacing the single-agent loop, it adds a coordination layer on top of it, and we instantiate it on both Codex and Claude Code.



> 💡 **编码框架定位**: 列举 SWE-agent、Claude Code 等增强单智能体能力的执行环境，说明 DELM 并非替代这些框架，而是作为协调层叠加在现有单智能体循环之上以支持并行协作。

