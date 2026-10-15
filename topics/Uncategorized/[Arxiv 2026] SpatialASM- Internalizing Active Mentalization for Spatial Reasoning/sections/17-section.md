[← 返回 README](../README.md)

## B.2.1. Statistics

All original spatial-QAs are sourced from Holi-Spatial [26], VST-500K [71], and the training set of Mindcube [60]. We give the number of samples from each original data source in SpatialASM-128k in Tab. 6. Importantly, to avoid distribution overlap between training data and our final evaluation on benchmarks, SpatialASM-66k does not contain any sample synthesized from Mindtube-train.



> 💡 **数据来源与隔离**: SpatialASM-128k 由 Holi-Spatial、VST-500K 和 Mindcube-train 聚合而成。关键防泄漏措施是：用于最终评估的 SpatialASM-66k 严格剔除了所有源自 Mindcube-train 的合成样本，确保训练集与评测基准无分布重叠。

Table 6: The number of samples from each data source in SpatialASM-128k
<table><tr><td>Data Source</td><td>Holi-Spatial</td><td>VST-500k Mindcube-train</td><td>Total</td></tr><tr><td>Sample No.</td><td>91,648 34,741</td><td>1,965</td><td>128,354</td></tr></table>



> 💡 **Table 6 解读**: SpatialASM-128k 的构成是 Holi-Spatial 91,648 条、VST-500K 34,741 条、Mindcube-train 1,965 条，合计 128,354 条，三个分项之和与表中 Total 相符。注意表头把 VST-500k 与 Mindcube-train 挤在同一单元格，是排版错位，读数时应对应三个来源三列。推断：数据量高度不均衡（Holi-Spatial 约占 71%），若要分析数据配比的影响，这一项最值得优先检查。

