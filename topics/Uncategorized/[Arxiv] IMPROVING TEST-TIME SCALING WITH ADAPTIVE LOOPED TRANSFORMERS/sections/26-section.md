[← 返回 README](../README.md)

## B.1 TEACHER SELECTION

We compare Qwen3-8B, Qwen3-32B and Qwen3-235B-A22B as teachers, using responses generated from the same prompts. We fine-tune Qwen3-1.7B-Base (Standard) on each response set with an identical training recipe and evaluate with a 32K output limit, temperature 0.6, top-p 0.95 and topk 20. The 8B teacher yields the highest mean student accuracy on AIME24–26 (Table 11), exceeding the 32B and 235B teachers by 1.63 and 1.80 points, respectively. We therefore use Qwen3-8B as the teacher for all main experiments.



> 💡 **机制拆解与反直觉发现**: 本段是一个教师规模消融：教师候选为 Qwen3-8B / Qwen3-32B / Qwen3-235B-A22B，三组**response 来自同一批 prompt**；学生统一是 Qwen3-1.7B-Base (Standard)，且使用**完全相同的 training recipe**；评估解码参数为 32K 输出上限、temperature 0.6、top-p 0.95、topk 20（初稿漏写了 topk 20，已补）。结论是 8B 教师的平均学生准确率最高，分别比 32B、235B 高 **1.63 与 1.80 个百分点（points，不是 1.63%/1.80% 的相对百分比）**，因此主实验统一采用 Qwen3-8B。
> 
> > 💡 **证据边界**: 原文只给出“8B 教师 > 32B/235B 教师”的观测差值，**没有报告显著性检验、方差或置信区间**，也没有解释原因。因此初稿中“统计显著性意义”和“过大模型的输出分布过于复杂或与学生容量不匹配”均属**推断，非原文结论**，此处明确标注为待证。（另一处“挑战了更大教师更好的直觉”是同向的外部常识性解读，同样属于推断，不能当作本文主张引用。）
> 
> > 💡 **Q&A 批注记录**:
> > - Q: 这个消融真正控制了哪些变量？
> > - A: prompt 集（同一 prompts 生成 response）、训练配方、学生底座与规模、解码参数（32K/0.6/0.95/topk 20）四组都固定，唯一变动量是教师模型；但评估只报了 AIME24–26 的 avg@32 均值，属于单点证据。

Table 11: AIME accuracy (%) for Qwen3-1.7B-Base students trained on responses from different teachers. All evaluations use avg@32 and a 32K output-token limit.
<table><tr><td>Teacher</td><td>AIME24</td><td>AIME25</td><td>AIME26</td><td>Mean</td></tr><tr><td>Qwen3-8B</td><td>11.04</td><td>13.02</td><td>11.87</td><td>11.98</td></tr><tr><td>Qwen3-32B</td><td>9.69</td><td>11.98</td><td>9.38</td><td>10.35</td></tr><tr><td>Qwen3-235B-A22B</td><td>10.42</td><td>9.90</td><td>10.21</td><td>10.18</td></tr></table>



> 💡 **Table 11 批读**: 表格给出三个教师的学生在 AIME24/AIME25/AIME26 上的准确率与 Mean。逐行核对均值算术：8B 为 (11.04+13.02+11.87)/3 = 11.98；32B 为 (9.69+11.98+9.38)/3 = 10.35；235B-A22B 为 (10.42+9.90+10.21)/3 = 10.18，与表中 Mean 一致；正文的 1.63 / 1.80 也对应 11.98−10.35 与 11.98−10.18，**数字自洽**。三档教师的排序是 8B(11.98) > 32B(10.35) > 235B(10.18)，即 235B 教师的学生最差。caption 明确“All evaluations use avg@32 and a 32K output-token limit”，故 avg@32 与 32K 上限有原文依据。
> 
> > 💡 **证据边界**: 初稿中“确保长思维链推理不被截断”是**推断**——原文只说明设了 32K 输出上限，未说明该上限是否足以避免截断，也未报告截断率；同时“提升幅度虽小但具有统计显著性意义”同样**无原文支撑**（未做显著性检验）。
> 
> > 💡 **支撑的 claim**: 该表支撑的唯一 claim 是“在相同 recipe 下，8B 教师给 1.7B 学生带来的 AIME 平均准确率最高”，据此选择 8B 作主实验教师；它**不**支撑“更大教师一定更差”的一般性结论（只覆盖 3 个教师、3 个 AIME 年份）。

