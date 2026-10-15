[← 返回 README](../README.md)

## 5. Conclusion

In this work, we identify Active Spatial Mentalization (ASM) as a key bottleneck in LVLM spatial reasoning. To address this issue, we propose SpatialASM, a novel post-training framework that internalizes ASM capabilities into LVLMs through training-time spatial simulation. Experiments across seven spatial reasoning benchmarks and four model architectures demonstrate that SpatialASM consistently improves spatial reasoning while preserving general visual capabilities. More importantly, our framework provides a scalable approach to constructing CoT supervision for existing spatial datasets, addressing the common lack of intermediate reasoning processes in open-source spatial data. In future work, we plan to internalize other forms of tool-assisted reasoning and extend ASM to dynamic real-world scenes, embodied interaction, and longer-horizon 3D reasoning.



> 💡 **结论与展望**: 结论段重申两条主张：ASM 是 LVLM 空间推理的关键瓶颈，SpatialASM 通过训练时空间模拟把该能力内化，在 7 个空间基准、4 种模型架构上提升空间推理且不损通用视觉能力——这些都是前文已给的结论，本身不含新证据。作者另指出该框架可为现有空间数据集规模化构建 CoT 监督，以应对开源空间数据普遍缺乏中间推理过程的问题；未来计划把工具辅助推理也内化，并把 ASM 扩展到动态场景、具身交互和更长视野的 3D 推理。

