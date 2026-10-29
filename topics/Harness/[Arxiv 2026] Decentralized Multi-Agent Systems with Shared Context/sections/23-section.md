[← 返回 README](../README.md)

## • reserve --subject TEXT --detail TEXT

Atomically create and claim one scope if your claim slot is free. Matches reuse open items, report claimed owners, or remain done. Subjects match ignoring case and extra whitespace. Different titles can still overlap, so respect visible scopes.



> 💡 **机制拆解**: 当 claim slot 空闲时，该命令会原子性地创建并占用一个 scope。若与已有项匹配，则复用未关闭项、报告已声明的 owner，或保持已完成状态。subject 匹配会忽略大小写与多余空白，但标题不同仍可能区域重叠，因此需尊重可见 scope 的范围。

