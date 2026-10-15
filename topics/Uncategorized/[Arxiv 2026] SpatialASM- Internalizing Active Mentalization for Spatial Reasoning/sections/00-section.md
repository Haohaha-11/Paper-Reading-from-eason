[← 返回 README](../README.md)

# SpatialASM: Internalizing Active Mentalization for Spatial Reasoning

Yuhong Liu<sup>\*1,2</sup>, Shenxi Wu<sup>\*1,2</sup>, Junyi Chi<sup>2,3</sup>, Beichen Zhang<sup>1,2</sup>, Xuanlang Dai<sup>2,3</sup>, Jiaqi Wang<sup>†4</sup>, Yuhang Zang<sup>†2</sup> and Dahua Lin<sup>†1,2,5</sup>



> 💡 **作者与通讯**: 第一作者 Yuhong Liu 与 Shenxi Wu 标注了同等贡献（\*），Jiaqi Wang、Yuhang Zang、Dahua Lin 三人带 † 号，按惯例为通讯作者。作者单位编号显示团队主要来自香港中文大学（1）与上海 AI Lab（2），另有复旦等单位参与。

<sup>1</sup>The Chinese University of Hong Kong, <sup>2</sup>Shanghai AI Laboratory, <sup>3</sup>Fudan University, <sup>4</sup>Shanghai Innovation Institute, <sup>5</sup>CPII under InnoHK



> 💡 **机构构成**: 单位依次为香港中文大学、上海 AI Lab、复旦大学、上海创新研究院（Shanghai Innovation Institute）与 InnoHK 下的 CPII，属高校与实验室联合。推断：这种组合通常意味着较充足的工程与算力资源，但原文未说明具体资源投入。

When solving complex spatial reasoning tasks, ordinary humans naturally revisit the scenes from task-relevant viewpoints and mentally reconstruct the spatial layouts from those viewpoints. But can Large Vision-Language Models (LVLMs) proactively think in the same way? We term this ability Active Spatial Mentalization (ASM), an intrinsic component of visual intelligence that remains substantially underdeveloped in current LVLMs. To bridge this gap, we introduce SpatialASM, a scalable framework to internalize ASM into LVLMs through training-time spatial simulation. SpatialASM employs an allocentric novel-view generator to ground hypothetical viewpoints in visual evidence and produces two complementary forms of supervision: i) explicit ASM trajectories that teach LVLMs when and how to adaptively invoke view planning and imagination during spatial reasoning, and ii) automatically-constructed perspective-taking tasks that further strengthen viewpoint-conditioned scene imagination. On seven benchmarks spanning relatively perception-oriented spatial understanding to more challenging tasks requiring multi-image, cross-view and viewpoint-transformation reasoning, SpatialASM yields performance gain on LVLMs of four LVLM backbones in spatial reasoning while retaining their original general vision capabilities. The experimental results demonstrate that our approach improves average accuracy across the seven spatial benchmarks. And the synthesis of ASM trajectories provides a scalable pathway to alleviate the scarcity of spatial Chain-of-Thought (CoT) supervision for eliciting active viewpoint exploration and mental scene simulation.



> 💡 **核心贡献**: 提出 SpatialASM，把“主动空间心理化”（ASM）通过训练时的空间模拟内化进 LVLM。方法用异中心新视角生成器（allocentric novel-view generator）把假设视角锚定到视觉证据，并给出两类互补监督：显式 ASM 轨迹（教模型何时、如何调用视角规划与想象）与自动构建的视角采择任务；摘要只说在七个基准、四种骨干上取得平均准确率提升，未给出具体数字。

