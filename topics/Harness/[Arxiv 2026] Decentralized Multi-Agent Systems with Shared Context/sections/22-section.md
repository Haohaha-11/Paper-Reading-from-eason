[← 返回 README](../README.md)

## • claim --item ID

First claim wins; a loss names the owner. One unfinished claim per worker.



> 💡 **claim 规则**: 该段给出三条判定：先声明者取得该项；声明失败时返回的信息指出当前持有者是谁；每个 worker 同时只保留一个未完成的声明。原文仅此一句，未交代冲突检测、超时回收等细节，因此推断：这更像任务队列 claim 命令的语义约定，而非完整的分布式锁设计。

