[← 返回 README](../README.md)

# OPENWAM: An Open Framework for Composable World-Action Models

Heng Yu<sup>∗</sup>, David D. Yuan<sup>∗</sup>, Juze Zhang<sup>∗</sup>, Changan Chen, Yao Feng Michelle Baldonado, Steve Cousins, Li Fei-Fei, Jiajun Wu, Ehsan Adeli



> 💡 **作者团队**: 共 10 位作者，Heng Yu、David D. Yuan、Juze Zhang 三人带 ∗ 标注，对应下段的 Equal contribution；名单中包括 Li Fei-Fei 与 Jiajun Wu。OCR 中“Yao Feng Michelle Baldonado”之间疑缺一个逗号。

Stanford University <sup>\*</sup>Equal contribution



> 💡 **机构与标注**: 全篇只给出 Stanford University 一个机构，`*` 表示共同一作（Equal contribution），即前三位作者贡献均等。

World–action models (WAMs) couple future prediction with robot control, yet existing systems often vary the video backbone, interaction structure, supervision, and inference procedure simultaneously, making their design choices difficult to compare. We introduce OPENWAM, an open world–action modeling framework built around a common causal robot-video foundation and configurable video–action interaction. Starting from Wan2.2-5B, we perform causal robot-video pretraining on over 10,000 hours of video, then integrate an action expert through a shared Mixture-of-Transformers architecture that supports joint, video-then-action, action-then-video, and decoupled generation. OPENWAM achieves high success rates on four LIBERO suites and real-world bimanual tasks; robotvideo training with causal adaptation improves VTA success on LIBERO-Long from 68.4% to 97.8%. The same configurable architecture naturally extends to inverse and forward dynamics, allowing us to study how counterfactual transitions improve independently trained dynamics models beyond demonstrations alone. When only the video predictor is adapted to a new task, a frozen local-context inverse dynamics model trained on counterfactual data and demonstrations achieves 84.0% mean success across four held-out LIBERO-90 tasks, compared with 47.0% for a full-context inverse model and 21.5% for a local-context model trained only on demonstrations. For forward dynamics, counterfactual supervision reduces RGB prediction error by 34.5% and raises outcome identification from 21.1% to 71.3% among 16 same-state outcomes. OPENWAM provides a common testbed for comparing WAM interaction designs and for studying dynamics learning from video data beyond successful demonstrations.



> 💡 **核心贡献与结果**: 针对现有 WAM 同时改动视频骨干、交互结构、监督与推理流程导致设计难以比较的问题，OPENWAM 用统一的因果机器人视频底座加可配置交互来做对照。主数字：因果适应把 VTA 在 LIBERO-Long 上的成功率从 68.4% 提到 97.8%；只适配视频预测器时，用反事实数据＋演示训练的冻结局部上下文逆动力学模型在四个 held-out LIBERO-90 任务上平均成功率 84.0%，对比全上下文逆模型 47.0%、仅用演示的局部上下文模型 21.5%。前向动力学一侧，反事实监督把 RGB 预测误差降低 34.5%，并把 16 个同状态结果的结果识别率从 21.1% 提到 71.3%。

Project Page: https://openwam.stanford.edu Code: https://github.com/OpenWAM/OpenWAM Release: Project page & code: June 4, 2026 Paper: October 6, 2026



> 💡 **资源与时间**: 给出项目主页 openwam.stanford.edu 与代码库 github.com/OpenWAM/OpenWAM；原文标注项目页和代码于 2026 年 6 月 4 日发布、论文于 2026 年 10 月 6 日发布。

![](../images/f39f42659df8160c50f2aec23f17649c9f63a95a17cd2c244e505a5e8a3d9b66.jpg)



> 💡 **插图说明**: 此段只插入一张图片（文件名为哈希串），本次没有获得图片的视觉内容，也没有 caption，因此无法描述其具体内容；推断为框架总览或 teaser 图。

