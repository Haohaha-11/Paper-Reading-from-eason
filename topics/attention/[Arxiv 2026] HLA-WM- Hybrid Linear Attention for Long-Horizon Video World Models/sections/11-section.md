[← 返回 README](../README.md)

## A.1.2 Turn-Aware Recent Context

To preserve local continuity under abrupt trajectory changes, we adapt the recent-history budget based on the motion within the current query chunk. For a regular 24-frame camera chunk with translation vectors $\{ \mathbf { p } _ { 0 } , \ldots , \mathbf { p } _ { 2 3 } \}$ , we define



> 💡 AI 批注 | 问题动机与机制拆解：本段提出 Turn-Aware Recent Context（转向感知的近期上下文）机制，要解决的问题是轨迹发生突变时局部连续性被破坏——若近期历史窗口固定不变，急转后的运动状态可能无法被正确延续。做法是基于“当前 query chunk 内部”的运动来自适应调整 recent-history budget，而不是全局固定。输入为规则 24 帧相机 chunk 的平移向量序列 $\{ \mathbf{p}_0, \ldots, \mathbf{p}_{23} \}$；本段只给出输入与动机，具体的判别量与预算规则由后文 Eq. 22–24 定义。

$$
\mathbf { d } _ { \mathrm { e a r l y } } = \mathbf { p } _ { 7 } - \mathbf { p } _ { 0 } , \qquad \mathbf { d } _ { \mathrm { l a t e } } = \mathbf { p } _ { 2 3 } - \mathbf { p } _ { 1 6 } ,\tag{22}
$$



> 💡 AI 批注 | 公式批读 (Eq. 22)：定义两个差分向量作为运动方向的两个观测点：$\mathbf{d}_{\mathrm{early}} = \mathbf{p}_7 - \mathbf{p}_0$（第 0→7 帧的净位移）、$\mathbf{d}_{\mathrm{late}} = \mathbf{p}_{23} - \mathbf{p}_{16}$（第 16→23 帧的净位移）。需要注意的是：原文只说用这两个向量比较方向，并未明说它们是“前半段/后半段”的代表，把它们称为 chunk 的前段与后段属合理推断（跨度各为 8 帧），不是原文显式表述；两者之间的第 8–15 帧被跳过，原文没有解释为何取这四帧端点，属待证细节。图片化建议：GitHub MathJax 不支持 `\tag{}`，需在渲染前移除，且注意 OCR 中 `\mathrm{e a r l y}` 一类字母间空格需整理为可读形式。

and compute their angular diference as



> 💡 AI 批注 | 过渡说明：本句把上述两个位移向量接入下一步计算——求它们之间的夹角。原文拼写为 “angular diference”（OCR 漏字），保持原样不改写。此处的“夹角”既不是位置差也不是速度差，而是两个位移方向之间的偏离程度，这是后文判定“是否转向”的唯一依据。

$$
\theta _ { c } = \operatorname { a r c c o s } \frac { \mathbf { d } _ { \mathrm { e a r l y } } ^ { \top } \mathbf { d } _ { \mathrm { l a t e } } } { \| \mathbf { d } _ { \mathrm { e a r l y } } \| _ { 2 } \| \mathbf { d } _ { \mathrm { l a t e } } \| _ { 2 } } .\tag{23}
$$



> 💡 AI 批注 | 公式批读 (Eq. 23)：$\theta_c = \arccos\!\big(\mathbf{d}_{\mathrm{early}}^{\top}\mathbf{d}_{\mathrm{late}} / (\|\mathbf{d}_{\mathrm{early}}\|_2 \|\mathbf{d}_{\mathrm{late}}\|_2)\big)$，即以归一化点积的 arccos 求出两个位移方向的夹角。更准确的说法是“用余弦值反解夹角”，而非初稿所称的“余弦相似度公式计算角度差”。边界情况需注意：当 $\mathbf{d}_{\mathrm{early}}$ 或 $\mathbf{d}_{\mathrm{late}}$ 模长为 0（静止不动）时分母为 0，原文未讨论该奇异情形，属未覆盖条件。$\theta_c$ 越小表示前后方向越一致，越大表示转向越剧烈或接近反向，这是把“轨迹是否突变”量化为单一标量的做法。

The recent-history budget is then set to



> 💡 AI 批注 | 逻辑引入：本句是条件分支的入口——把上一步算出的角度 $\theta_c$ 映射为近期历史预算 $r_c$ 的取值。原文没有在此说明 $r_c$ 计量的到底是帧数、chunk 数还是 token 数，只称其为 “recent-history budget”，因此 $r_c=3$ 与 $r_c=1$ 的具体物理含义在本节内未定义，须结合正文对 recent context 的构造方式才能确定。

$$
r _ { c } = { \left\{ \begin{array} { l l } { 3 , } & { \theta _ { c } \geq \tau _ { \mathrm { t u r n } } , } \\ { 1 , } & { \mathrm { o t h e r w i s e } , } \end{array} \right. }\tag{24}
$$



> 💡 AI 批注 | 公式批读 (Eq. 24)：分段规则 $r_c = 3$ 若 $\theta_c \geq \tau_{\mathrm{turn}}$，否则 $r_c = 1$，即检测到足够大的方向偏转时把近期历史预算从 1 扩大到 3。可确证的是“转向时预算更大、直行时更小”这一方向性；不可确证的是 $r_c$ 的单位与“3 具体多带来多少帧”的换算——原文未给，初稿中“保留更多过去帧的信息”属合理推断而非原文陈述。另外原文并未说明 $r_c=1$ 就是“默认预算”，只是下文说未触发时“revert to the default budget”，可推出 1 为默认值。

where $\tau _ { \mathrm { t u r n } } = 2 5 ^ { \circ }$ . The expanded recent context is applied only to the current chunk; subsequent chunks revert to the default budget unless the criterion is triggered again.



> 💡 AI 批注 | 参数与作用范围（初稿此处有误，已修正）：原文明确给出阈值 $\tau_{\mathrm{turn}} = 25^{\circ}$，并非“未在此处给出、可能在实验设置中”——这是本段最关键的数值，必须写对。作用范围上，原文强调扩展后的近期上下文“只作用于当前 chunk”，后续 chunk 会回落到默认预算，除非再次触发判据；这意味该机制是逐 chunk 的瞬时重判，而不是一旦触发就长期保持的大窗口。据此，本段可确证的完整逻辑是：chunk 内方向偏转 $\geq 25^{\circ}$ → 当前 chunk 用 $r_c=3$，否则用 $r_c=1$，且该决定不向后跨 chunk 继承。

