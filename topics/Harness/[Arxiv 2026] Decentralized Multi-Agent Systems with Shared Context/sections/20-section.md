[← 返回 README](../README.md)

## Board commands:

\`sh /tools/context.sh ACTION ...\` through your ordinary shell tool.



> 💡 **调用方式**: 与 board 的交互统一走普通 shell 工具执行 `sh /tools/context.sh ACTION ...`，即命令经由既有 shell 通道转发，而不是通过专用 API。

• read [--all] [--wait SECONDS] [--for ID]



> 💡 **read 的参数**: `read` 是本节给出的查询指令，`--all` 用于读取更早条目或补取遗漏的响应，`--wait SECONDS` 让读取阻塞等待，`--for ID` 指定所需的条目或发布。三种修饰对应不同的读取场景，具体语义由下一段界定。

The first view is complete. Later views show active work, new publications and changed statuses since your last served view. Unchanged active items show their identity and owner. Session states (starting/running/ended) are always shown. A session ending does not establish task correctness. Use read --all for older entries or a missed response. A waiting read returns on a peer publication or a peer ending, otherwise after at most 25 seconds. Status changes do not end a wait. --for names a needed item or publication and returns if it is available already.



> 💡 **增量视图与语义边界**: 首次读取是完整的，后续视图只显示自上次被服务以来的活跃工作、新发布和状态变更，未变化的活跃项只给标识与所有者。作者特别提醒会话结束不等于任务正确；`--wait` 在对方发布或结束会话时返回，否则最长等 25 秒，状态变化不会结束等待。

