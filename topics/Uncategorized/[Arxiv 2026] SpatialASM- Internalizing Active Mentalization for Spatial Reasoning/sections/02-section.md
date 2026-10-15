[← 返回 README](../README.md)

## 2. Related Work

We thoroughly discuss more related work here in Appendix A.



> 💡 **附录指引**: 提示读者若需更详尽的文献综述，可参考论文附录 A，正文仅保留核心对比。

Proactive spatial intelligence. Current LVLMs achieve reasonable performance on static spatial perception [31, 62] but still struggle with tasks requiring cross-view association, spatial mental modeling, and active evidence acquisition [51, 72, 77]. ESI-Bench [29] recasts spatial intelligence as a perception-action loop and shows that active exploration improves accuracy over passive observation, though its exploration relies on physical interaction with an embodied environment rather than internalized reasoning. The two components of ASM have also been studied separately. For view imagination, APC [34] is a training-free pipeline that builds a 3D scene abstraction with vision foundation models and re-projects it to a perspective specified in the question. For view planning, recent work trains LVLMs to choose camera viewpoints, either to localize a given target view [59] or to answer a question [33], but the chosen views are then rendered from a 3D environment rather than imagined. GeoThinker [36] internalizes a form of active reasoning at the feature-retrieval level by selectively querying geometric features, but such geometry-enhanced approaches [36, 81] require architecturespecific fusion modules that limit portability across LVLM architectures. In contrast, ASM couples view planning and view imagination in a single question-conditioned reasoning trajectory that operates at the viewpoint and observation level and can be internalized through standard post-training without architectural modification.



> 💡 **方法定位**: ASM 将视角规划与想象耦合于单一推理轨迹，无需架构修改即可通过标准后训练内化，优于依赖外部渲染或特定融合模块的前人工作。

Spatial data synthesis. Many studies enhance spatial reasoning by synthesizing large-scale datasets (Fig. 2(a)). Early approaches build on public scene datasets (e.g., ScanNet [18], ARKitScenes [4]) with 3D annotations [37, 47], or employ expert models (e.g., segmentation, depth estimation) for data curation [11, 15, 20]. Recent data engines scale further: HoliSpatial [26] renders scenes through 3D Gaussian Splatting, and VST [71] uses simulators to generate novel observations. Despite their efectiveness, the dominant supervision remains answer-only, providing the final answer but little guidance on how to actively acquire or mentally construct missing evidence. VST-R [71] takes a step towards reasoning supervision with explicit step-by-step traces, but its trajectories teach the model to derive spatial conclusions from provided evidence rather than to plan informative viewpoints and imagine unobserved perspectives. SpatialASM addresses this gap by augmenting existing spatial QA pairs with ASM reasoning trajectories that couple question-conditioned view planning, viewpoint-conditioned imagination, and evidence-based reasoning.



> 💡 **数据缺口**: 现有合成数据多仅提供答案或被动证据推导步骤，SpatialASM 通过注入包含主动规划与想象的完整推理轨迹，填补了监督信号中“如何获取证据”的空白。

World-model-assisted spatial reasoning. Drawing on generative world models [1, 7, 13, 55, 82], several approaches equip LVLMs with external spatial simulators during test-time reasoning (Fig. 2(b)). MindJourney [73] and SpatialDreamer [10] use video-generation models to synthesize observations under egocentric motions in a multi-round think-imagine-reason loop, while Think3D [80] allows the LVLM to manipulate a virtual camera inside a reconstructed 3D scene. Astra [84] and AVIC [74] further train the LVLM to decide when and how much to imagine, but the imagined views are still produced by an external simulator at inference time, and all of these methods reach a target viewpoint through egocentric action sequences. A second line uses world models or renderers only at training time. World2VLM [78] distills motion-conditioned view association from a world model, with trajectories sampled independently of any question and without reasoning traces; GeoWorld-VLM [27] aligns the vision encoder with world-model features under a frozen LLM; and Imaginative Perception Tokens [5] train a unified model to generate latent views of a viewpoint specified in the question, supervised by simulator renders. SpatialASM belongs to this second line. Its diference lies not in using a world model at training time but in what is distilled: question-conditioned view planning and viewpoint-conditioned imagination, written out as explicit reasoning trajectories, so that the LVLM learns when and where to imagine rather than only how a view changes under a given motion. Test-time simulators and training-time internalization are complementary and could in principle be combined (Tab. 2).



> 💡 **范式差异**: SpatialASM 属于训练时内化路线，蒸馏的是问题条件下的规划与想象能力而非单纯运动变化，使模型学会“何时何地”想象，区别于依赖测试时外部模拟器的方法。

