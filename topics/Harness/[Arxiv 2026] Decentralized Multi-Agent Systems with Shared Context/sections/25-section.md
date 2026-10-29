[← 返回 README](../README.md)

## • apply --id ID [--only PATH...]

Copy attachments over original or registered peer versions. Unknown local edits refuse the selected batch; inspect and reconcile locally. Identical files and newer peer versions are kept.



> 💡 **覆盖与拒绝规则**: 该命令把附件复制覆盖到原始版本或已注册的对等版本之上；内容完全相同的文件和对端更新的版本保持不变，即不会回退或重复写入。遇到无法识别的本地编辑时，被拒绝的是整个 selected batch，而非只跳过单条覆盖；配合 `--only PATH` 可分选子集，需在本地 inspect 后自行 reconcile。

