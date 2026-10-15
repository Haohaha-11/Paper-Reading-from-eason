[← 返回 README](../README.md)

## C.5 CROSS-ITERATION ATTENTION

We examine three representative heads of TaH2 (M = 8) on 100 randomly sampled validation sequences under teacher forcing, retaining the decider’s actual routes. For queries at iteration 8, Figure 12 shows distinct preferences for first-iteration states, later-iteration states, or both. Figure 13 visualises these patterns over the first 128 response positions with the original context preserved.



> 💡 **分析设置批读（纠正初稿）**:
> - 这一段的对象是**观察性分析**，不是新模块设计：原文说 “We examine three representative heads of TaH2 (M = 8)”，即从 TaH2 在 $M = 8$ 时挑出三个有代表性的注意力头做检查，本身不提出任何机制。
> - 采样与运行条件：**100 条随机抽样的验证序列**，在 **teacher forcing** 下运行，并且 “retaining the decider’s actual routes”——分析时沿用 decider 自己实际走的路由，没有把它替换成强制路由。这一点初稿完全没提，属于漏读的关键控制条件。
> - 观察焦点在 **iteration 8 的 query**：它们对**第一迭代状态**、**后续迭代状态**、或**两者兼有**表现出不同偏好（Figure 12）；Figure 13 进一步把这些模式画在**前 128 个 response 位置**上，并保留原始上下文。
> - 因此本段支撑的是“跨迭代注意力偏好存在头间差异、且与迭代位置有关”这一**现象性 claim**，而不是“该模块解决迭代间信息丢失/提升一致性”这类机理性 claim。
> 
> > 💡 **机制拆解（证据边界）**: 初稿写的“引入 Cross-Iteration Attention，允许当前迭代 Query 关注历史迭代 Key/Value”“解决依赖断裂、稳定生成过程”“打破同一时间步内注意力的限制”等表述，在本段原文中**均无对应文字**，属于把“观察到跨迭代偏好”倒推成“设计了一个跨迭代模块”。按原文严格读，本段只做到：**给定三个头、给定采样条件，展示它们在 iteration 8 对历史迭代状态的偏好分化**。
> 
> > 💡 **待证/推断**: “decider” 指什么、它输出的 “routes” 是什么粒度、TaH2 中 $M = 8$ 代表迭代步数还是别的维度、以及被选中的三个头是按何标准“代表性”——这些本段都未交代，需依赖正文对应章节与 Figure 12/13 的 caption 才能定论，此处只能标为**推断待证**。另外，本段只给出定性观察（“distinct preferences”），**没有任何数值、比例或显著性统计**，不能据此声称偏好差异在总体上有统计意义。
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 为什么要特别标注 “retaining the decider’s actual routes”？
> > - A: 因为如果分析时改用强制路由，观察到的注意力偏好就不再反映模型真实推理路径；保留实际路由让 Figure 12/13 的偏好差异对应到**实际发生的迭代轨迹**上，这是解读该图的前提条件（依据本段原文的 “under teacher forcing, retaining the decider’s actual routes”）。

