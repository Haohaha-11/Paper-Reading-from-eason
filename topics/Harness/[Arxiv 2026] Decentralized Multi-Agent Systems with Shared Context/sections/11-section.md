[← 返回 README](../README.md)

## A.1 Selective Retrieval and Reuse

A compact entry $G _ { i }$ in the shared context C can reference files captured at publication time. These files remain available under the publication’s identifier, even as the contributing agent continues editing its own workspace. Agents access them through two operations, expand and apply, which separate inspection from incorporation into a local solution.



> 💡 **机制拆解**: 共享上下文 C 里只放紧凑条目 $G_i$，它指向“发布时刻”捕获的文件快照；即使贡献者此后继续改自己的工作区，这些文件仍按发布标识可取。`expand`（只看）与 `apply`（写入本地）把“检查”和“并入自己的方案”分成两步，读者可据此理解为什么共享上下文不会被附件正文撑爆。

Expansion (expand). The expand operation returns a publication’s attached files as tool output without changing the agent’s workspace. By default, it shows differences against the original task files, with full contents for new files or files whose originals are unavailable. Using --only, the agent can select particular files and view them in full, allowing it to examine the implementation details or supporting evidence relevant to its current work.



> 💡 **expand 语义**: `expand` 只把附件作为工具输出返回，不修改工作区；默认显示相对原始任务文件的差异，但新文件或找不到原件的文件直接给全文。用 `--only` 可指定文件看完整内容，便于核对实现细节或支撑证据。

Application (apply). The apply operation copies all or selected attachments into the receiving agent’s workspace. It transfers complete files rather than merging patches, checking local contents against the task originals and published versions before writing. If a selected file contains local changes it cannot account for, the import is refused and the agent must reconcile those changes. Expansion is optional: an agent can inspect a contribution before incorporating it or apply selected files directly.



> 💡 **apply 的冲突检查**: `apply` 把全部或选定附件复制进接收方工作区，传的是完整文件而非合并 patch；写入前会把本地内容与任务原始版、发布版对照，若选中文件带有系统无法解释的本地改动就拒绝导入，要求 agent 先解决冲突。`expand` 是可选的，也可以跳过检查直接 apply。

Expanded content enters the requesting agent’s session, while applied files become part of its local workspace. Neither operation inserts file contents into the compact shared-context view, so agents can inspect and reuse detailed contributions without requiring every other agent to read them.



> 💡 **数据流边界**: 展开的内容只进入发起请求的 agent 会话，应用的文件进入其本地工作区；两者都不回填到紧凑共享上下文视图，因此其他 agent 不必被动读取这些细节。这是该设计实现“选择性复用”的关键约束。

