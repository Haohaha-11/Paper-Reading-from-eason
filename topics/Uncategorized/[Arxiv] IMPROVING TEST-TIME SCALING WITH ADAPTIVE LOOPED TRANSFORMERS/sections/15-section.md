[← 返回 README](../README.md)

## A.1.1 HYPER-PARAMETERS

Table 5: Training hyper-parameters shared by all variants at a given scale.
<table><tr><td>Hyper-parameter</td><td>Value</td></tr><tr><td>global batch (samples)</td><td>128</td></tr><tr><td>sequence length</td><td>16384</td></tr><tr><td>learning rate</td><td> $4 \times 1 0 ^ { - 5 }$ </td></tr><tr><td>max gradient norm</td><td>1.0</td></tr><tr><td>epochs</td><td>3</td></tr><tr><td>warmup ratio</td><td>0.03</td></tr><tr><td>learning-rate scheduler</td><td>cosine</td></tr><tr><td>minimum learning-rate ratio</td><td>0.1</td></tr><tr><td>TPP</td><td>2</td></tr><tr><td>precision</td><td>bf16</td></tr><tr><td>coverage  $\rho / \alpha _ { D }$  / threshold  $\tau _ { \mathrm { e x i t } }$ </td><td>0.99 / 0.05 / 0.5</td></tr></table>



> 💡 **Table 5 批读（AI 批注）**: 该块是附录 A.1.1 的唯一内容——一张表，caption 原文为 “Training hyper-parameters shared by all variants at a given scale.”，即这些配置是同一规模下所有变体**共享**的，表中本身不含变体间的差异参数。可直接核对的数值：global batch = 128 samples、sequence length = 16384、learning rate = $4 \times 10^{-5}$、max gradient norm = 1.0、epochs = 3、warmup ratio = 0.03、learning-rate scheduler = cosine、minimum learning-rate ratio = 0.1、TPP = 2、precision = bf16，以及 coverage $\rho$ / $\alpha_D$ / threshold $\tau_{\mathrm{exit}}$ = 0.99 / 0.05 / 0.5。
> 
> > 💡 **证据边界说明**: 初稿中“序列长度 16384 表明模型处理长上下文能力”属**推断**（长序列长度与长上下文能力相关，但本表未说明任务输入长度或外推设计）；“这些参数直接关联到论文核心机制中的动态退出或稀疏化策略”同样是**推断/待证**——本块只有表格，没有给出 TPP、$\rho$、$\alpha_D$、$\tau_{\mathrm{exit}}$ 的定义与使用位置，需回到方法/正文相应小节确认后才可断言其与动态退出或稀疏化的具体绑定关系。
> 
> > 💡 **复现提示**: 超参数分两类——标准优化项（batch、lr、调度、精度）与论文自定义项（TPP、coverage/threshold 三元组）；后者是与方法实现强耦合的，复现时须结合正文确定其生效阶段（训练期统计、推理期退出判据等，**待证**）。

