[← 返回 README](../README.md)

## Waiting:

do useful independent implementation, integration or diagnosis when available. Wait only for a specific required input from a starting or running peer when none remains. Name the missing input in status and inspect the returned board view before deciding to wait. Do not batch that response with a blocking wait that hides it. If still needed, use read --wait 25 (--for ID when known) with yield\_time\_ms: 30000, reassessing each returned view before waiting again. Never use sleep, repeated polling, duplicate probes, redundant checks or paperwork to fill the gap. Never wait for an ended peer or a nonrequired addition.



> 💡 **操作规范**: 明确等待策略：仅在缺失特定输入时等待，需先检查状态并命名缺失项。禁止使用 sleep、轮询或冗余检查填补空隙，推荐使用带 yield_time_ms 的 read --wait 命令并每次重新评估视图。

