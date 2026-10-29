[← 返回 README](../README.md)

## A Method Details

Algorithm 1 DELM pipeline. Agents coordinate concurrent work through a shared context C and task queue T. They publish intermediate results for others to reuse, update the queue as needed, and return their solutions independently.



> 💡 **协作机制**: Algorithm 1 的说明段交代了整体协议：$n$ 个智能体并行工作，通过共享上下文 $\mathcal{C}$ 和任务队列 $\mathcal{T}$ 互通；各自发布中间结果供他人复用、按需更新队列，最后各自独立返回解。协作靠共享状态完成，并不预先固定划分任务。

Input: Task $D ;$ number of agents n   
Output: Agent solutions $\{ Y _ { i } \} _ { i = 1 } ^ { n }$   
1: $\begin{array} { r l } { { \mathcal { C } } \gets \emptyset ; } & { { } { \mathcal { T } } \gets \{ T _ { i } \} _ { i = 1 } ^ { n } } \end{array}$   
2: for all agents $i = 1 , \ldots , n$ in parallel do   
3: $r _ { i } \gets \emptyset$ ▷ accumulated local work   
4: repeat   
5: r<sub>i</sub> ← CONTINUEWORK(D, r<sub>i</sub>, T, C) 2   
6: if useful intermediate results or corrections are available then   
7: $G _ { i }  \mathsf { S } \mathrm { { L } }$ MMARIZE(r<sub>i</sub>) 3   
8: PUBLISH $( \mathcal { C } , G _ { i } )$ ▷ references to sharedfiles   
9: end if   
10: UPDATESUBTASKS $( T , r _ { i } , { \mathcal { C } } )$ ▷ complete or add work 4   
11: until READYTOSUBMIT $( D , r _ { i } , \mathcal { C } )$   
12: Y<sub>i</sub> ← FINALIZE(D, r<sub>i</sub>, C) 5   
13: end for   
14: return $\{ Y _ { i } \} _ { i = 1 } ^ { n }$



> 💡 **伪代码拆解**: 输入任务 $D$ 和智能体数 $n$，输出 $n$ 个独立解 $Y_i$；初始化 $\mathcal{C}=\emptyset$、队列初始含 $n$ 个子任务。每轮先 CONTINUEWORK 推进本地工作 $r_i$，有可用中间结果或修正时 SUMMARIZE 成 $G_i$ 并 PUBLISH 到共享上下文（注释写明发布的是指向共享文件的引用，而非全文），再 UPDATESUBTASKS 完成或新增子任务，READYTOSUBMIT 为真后 FINALIZE 得到 $Y_i$；各辅助函数内部实现本节未给出。

