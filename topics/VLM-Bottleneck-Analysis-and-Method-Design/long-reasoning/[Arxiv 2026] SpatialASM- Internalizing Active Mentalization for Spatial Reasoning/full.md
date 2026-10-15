# SpatialASM: Internalizing Active Mentalization for Spatial Reasoning

Yuhong Liu<sup>\*1,2</sup>, Shenxi Wu<sup>\*1,2</sup>, Junyi Chi<sup>2,3</sup>, Beichen Zhang<sup>1,2</sup>, Xuanlang Dai<sup>2,3</sup>, Jiaqi Wang<sup>†4</sup>, Yuhang Zang<sup>†2</sup> and Dahua Lin<sup>†1,2,5</sup>

<sup>1</sup>The Chinese University of Hong Kong, <sup>2</sup>Shanghai AI Laboratory, <sup>3</sup>Fudan University, <sup>4</sup>Shanghai Innovation Institute, <sup>5</sup>CPII under InnoHK

When solving complex spatial reasoning tasks, ordinary humans naturally revisit the scenes from task-relevant viewpoints and mentally reconstruct the spatial layouts from those viewpoints. But can Large Vision-Language Models (LVLMs) proactively think in the same way? We term this ability Active Spatial Mentalization (ASM), an intrinsic component of visual intelligence that remains substantially underdeveloped in current LVLMs. To bridge this gap, we introduce SpatialASM, a scalable framework to internalize ASM into LVLMs through training-time spatial simulation. SpatialASM employs an allocentric novel-view generator to ground hypothetical viewpoints in visual evidence and produces two complementary forms of supervision: i) explicit ASM trajectories that teach LVLMs when and how to adaptively invoke view planning and imagination during spatial reasoning, and ii) automatically-constructed perspective-taking tasks that further strengthen viewpoint-conditioned scene imagination. On seven benchmarks spanning relatively perception-oriented spatial understanding to more challenging tasks requiring multi-image, cross-view and viewpoint-transformation reasoning, SpatialASM yields performance gain on LVLMs of four LVLM backbones in spatial reasoning while retaining their original general vision capabilities. The experimental results demonstrate that our approach improves average accuracy across the seven spatial benchmarks. And the synthesis of ASM trajectories provides a scalable pathway to alleviate the scarcity of spatial Chain-of-Thought (CoT) supervision for eliciting active viewpoint exploration and mental scene simulation.

## 1. Introduction

Spatial reasoning is a foundational capability for Large Vision-Language Models (LVLMs), underpinning applications from autonomous driving and embodied navigation to robotic manipulation [11, 15, 37]. While LVLMs achieve strong performance on many visual tasks [2, 43, 70], they still fail on complex spatial problems that involve viewpoint transformation, cross-view correspondence, and reasoning about unobserved regions [25, 68, 69, 72]. These tasks go beyond passively extracting relations from the given observations, as they require the model to reason about the three-dimensional scene from perspectives that the input images do not directly provide.

We argue that a core capability missing from current LVLMs is the ability to proactively determine which alternative viewpoints would reveal useful evidence and to construct what the scene looks like from those viewpoints mentally [54]. We term this joint capability Active Spatial Mentalization (ASM), comprising two complementary skills: view planning, which selects task-relevant viewpoints given the question and current observations, and view imagination, which mentally constructs the spatial layout expected from the selected viewpoints (Fig. 2(c)). Teaching ASM to LVLMs is dificult. Recent data-centric eforts have expanded spatial supervision through large-scale datasets built from 3D annotations and videos [26, 41, 71], but these datasets provide answer-only QA pairs rather than Chain-of-Thought (CoT) trajectories that show how to select informative viewpoints and imagine the corresponding observations (Fig. 2(a)). Current LVLMs are unreliable at bootstrapping this supervision themselves, as they often produce incorrect viewpoint plans and hallucinated scenes when asked to generate ASM trajectories from scratch.

![](images/35134933ea2cb431a1fe7c3157ef9ad5d7a3f4a587af41f3fc33d017d566016b.jpg)  
Figure 1: SpatialASM improves LVLM spatial reasoning by eliciting active exploration and imagination. (a) The qualitative example shows that the baseline LVLM (red) fails to associate objects across views, whereas our model (green) uses the internal viewpoint-planning skill and reasons over the imagined observation to reach the correct answer. (b) Quantitative experiments demonstrate that SpatialASM delivers consistent performance gains on seven benchmarks.

An alternative is to attach an external world model or spatial tool at inference time to generate supplementary observations [10, 73, 80] (Fig. 2(b)). While this provides useful visual evidence, it ties spatial reasoning to an external simulator at inference time, even when the LVLM learns when to invoke it [74, 84]. Tool distillation [38, 83] removes this dependence, but targets perceptual tools such as depth and cognitive maps rather than viewpoint planning and view imagination. Most such methods also adopt an egocentric interface that reaches a target viewpoint through a sequence of camera-centric actions, so each query requires a multi-step rollout [10, 73]. Closer to our setting, World2VLM [78] uses a world model at training time, but it supervises egocentric view association without producing any reasoning trajectory.

We introduce SpatialASM, a framework that equips LVLMs with ASM capabilities by using an allocentric novel-view generator (NVG) as a training-time spatial simulation engine rather than an inference-time tool (Fig. 2(c)). Given a spatial QA sample, the NVG renders views from question-conditioned viewpoints planned by the LVLM, grounding hypothetical perspectives in concrete visual evidence. SpatialASM transforms these into two complementary forms of supervision: explicit ASM reasoning trajectories for supervised fine-tuning (SFT) and automatically verifiable perspective-taking tasks for reinforcement learning with GRPO [53]. The rendered views serve only as temporary scafolding: in the final SFT data, they are replaced by text captions that describe the imagined observations, so the LVLM learns to construct spatial layouts internally from the original input images alone (Fig. 1(a)).

The design of SpatialASM is guided by two key mechanisms (Fig. 3). The allocentric viewpoint formulation specifies a target view by its location and direction in a single step, avoiding the multi-step rollouts of egocentric interfaces. A Geometry Perturbation (GeoP) metric provides a model-based proxy for the geometric consistency of rendered views; together with an answer-consistency check, it admits only samples that pass pre-defined thresholds into the training data.

Across four backbones from the Qwen3-VL and Qwen3.5 series [3, 48], SpatialASM improves average accuracy over seven spatial benchmarks (Tab. 1), with average gains ranging from +1.2 to +3.5 points (Fig. 1(b)). The largest per-benchmark gains appear on tasks that demand spatial mental modeling and viewpoint transformation, reaching +8.7 on Spatial457-6d and +5.2 on MindCube. General-VQA performance remains within 0.6 points of the baselines across all four backbones.

Our contributions are threefold. 1) We formulate Active Spatial Mentalization as the joint capability of proactively planning task-relevant viewpoints and imagining corresponding observations, and argue that the absence of ASM is a key bottleneck of spatial reasoning in current LVLMs. 2) We introduce SpatialASM, a scalable framework built on an allocentric novel-view generator and GeoP-based filtering that converts existing spatial QA data into explicit ASM trajectories for SFT and perspective-taking tasks for RL. 3) SpatialASM improves average spatial accuracy on four LVLM backbones over seven benchmarks while keeping general-VQA performance within 0.6 points.

![](images/be739ff00991c141819068140b7ec44823f1f7d2c725247d438ba43df3b764e5.jpg)  
Figure 2: (a) Prior data-centric approaches mostly provide answer-only supervision. (b) Reasoning with external calls of world models adds unnecessary intermediate observations and extra inference time. (c) SpatialASM enables LVLMs to intrinsically produce plan-imagine-reason CoT.

## 2. Related Work

We thoroughly discuss more related work here in Appendix A.

Proactive spatial intelligence. Current LVLMs achieve reasonable performance on static spatial perception [31, 62] but still struggle with tasks requiring cross-view association, spatial mental modeling, and active evidence acquisition [51, 72, 77]. ESI-Bench [29] recasts spatial intelligence as a perception-action loop and shows that active exploration improves accuracy over passive observation, though its exploration relies on physical interaction with an embodied environment rather than internalized reasoning. The two components of ASM have also been studied separately. For view imagination, APC [34] is a training-free pipeline that builds a 3D scene abstraction with vision foundation models and re-projects it to a perspective specified in the question. For view planning, recent work trains LVLMs to choose camera viewpoints, either to localize a given target view [59] or to answer a question [33], but the chosen views are then rendered from a 3D environment rather than imagined. GeoThinker [36] internalizes a form of active reasoning at the feature-retrieval level by selectively querying geometric features, but such geometry-enhanced approaches [36, 81] require architecturespecific fusion modules that limit portability across LVLM architectures. In contrast, ASM couples view planning and view imagination in a single question-conditioned reasoning trajectory that operates at the viewpoint and observation level and can be internalized through standard post-training without architectural modification.

Spatial data synthesis. Many studies enhance spatial reasoning by synthesizing large-scale datasets (Fig. 2(a)). Early approaches build on public scene datasets (e.g., ScanNet [18], ARKitScenes [4]) with 3D annotations [37, 47], or employ expert models (e.g., segmentation, depth estimation) for data curation [11, 15, 20]. Recent data engines scale further: HoliSpatial [26] renders scenes through 3D Gaussian Splatting, and VST [71] uses simulators to generate novel observations. Despite their efectiveness, the dominant supervision remains answer-only, providing the final answer but little guidance on how to actively acquire or mentally construct missing evidence. VST-R [71] takes a step towards reasoning supervision with explicit step-by-step traces, but its trajectories teach the model to derive spatial conclusions from provided evidence rather than to plan informative viewpoints and imagine unobserved perspectives. SpatialASM addresses this gap by augmenting existing spatial QA pairs with ASM reasoning trajectories that couple question-conditioned view planning, viewpoint-conditioned imagination, and evidence-based reasoning.

World-model-assisted spatial reasoning. Drawing on generative world models [1, 7, 13, 55, 82], several approaches equip LVLMs with external spatial simulators during test-time reasoning (Fig. 2(b)). MindJourney [73] and SpatialDreamer [10] use video-generation models to synthesize observations under egocentric motions in a multi-round think-imagine-reason loop, while Think3D [80] allows the LVLM to manipulate a virtual camera inside a reconstructed 3D scene. Astra [84] and AVIC [74] further train the LVLM to decide when and how much to imagine, but the imagined views are still produced by an external simulator at inference time, and all of these methods reach a target viewpoint through egocentric action sequences. A second line uses world models or renderers only at training time. World2VLM [78] distills motion-conditioned view association from a world model, with trajectories sampled independently of any question and without reasoning traces; GeoWorld-VLM [27] aligns the vision encoder with world-model features under a frozen LLM; and Imaginative Perception Tokens [5] train a unified model to generate latent views of a viewpoint specified in the question, supervised by simulator renders. SpatialASM belongs to this second line. Its diference lies not in using a world model at training time but in what is distilled: question-conditioned view planning and viewpoint-conditioned imagination, written out as explicit reasoning trajectories, so that the LVLM learns when and where to imagine rather than only how a view changes under a given motion. Test-time simulators and training-time internalization are complementary and could in principle be combined (Tab. 2).

## 3. SpatialASM

SpatialASM (Fig. 3) uses an allocentric novel-view generator (NVG) � as a training-time spatial simulator that converts answer-only spatial QA data into two complementary forms of supervision. First, � is fine-tuned to render the observation from a queried allocentric viewpoint, and we introduce a Geometry Perturbation (GeoP) metric to assess the geometric consistency of its outputs (Sec. 3.1). We then synthesize explicit ASM trajectories for SFT, in which a pretrained LVLM plans task-relevant viewpoints, � renders the corresponding views, and the rendered views are then replaced by textual captions so that the model learns to imagine, rather than receive, the missing observations (Sec. 3.2). Finally, we construct automatically verifiable perspective-taking questions for reinforcement learning (RL), which expose the model to observations from diverse viewpoints and reinforce the association between a viewpoint description and its observation (Sec. 3.3). The trajectories supervise the full plan-imagine-reason procedure, while the RL tasks strengthen viewpoint-conditioned imagination.

## 3.1. Allocentric Novel-View Generator

Allocentric conditioning. We obtain SpatialASM-NVG (denoted as �) by fine-tuning a pretrained image-editing model (FLUX.2-klein-9B [6] in our implementation) to render the observed scene from a queried viewpoint (Fig. 3(1a)). Most existing world models act as egocentric simulators: a viewpoint change is expressed as a sequence of ego-motion commands with quantified translations and rotations. In contrast, � is conditioned directly on the target viewpoint, so that a task-relevant perspective can be queried in a single step without specifying the camera motion that leads to it.

Given a set of images $\mathcal { T } = \{ I _ { 1 } , \ldots , I _ { n } \}$ of the same scene, we define an allocentric viewpoint as $v =$ $( i , x , y , d )$ , where $( x , y )$ is a pixel coordinate on the anchor image $I _ { i }$ that specifies the target observation location and � is the viewing direction at that location. The anchor index � is required because a pixel coordinate is only meaningful relative to one image, and providing the full set ℐ gives � additional scene context beyond the anchor view. Given ℐ and $v , G$ renders the observation from the queried viewpoint:

$$
I _ { \mathrm { e d i t } } = G ( { \mathbb T } , v ) , \quad v = ( i , x , y , d ) .\tag{1}
$$

Each training sample consists of the image set $\mathcal { T } ,$ an allocentric viewpoint $v = ( i , x , y , d )$ , and the groundtruth observation $I _ { \mathrm { g t } }$ from that viewpoint. In practice, $( x , y )$ is provided to � as a visual marker drawn on $I _ { i } ,$ , and $d$ as a textual prompt. We build such samples from public scene datasets that provide multiple posed observations of the same scene. For a pair of frames from one scene, we take one frame as the anchor $I _ { i }$ and the other as $I _ { \mathrm { { g t } } } ;$ the relative camera pose between them determines where the camera of $I _ { \mathrm { g t } }$ lies in $I _ { i }$ and which direction it faces, which yields $( x , y , d )$

Training. We fine-tune the image-editing model on these samples, supervising the rendered view $I _ { \mathrm { e d i t } } =$ $G ( \mathcal { T } , v )$ with the ground-truth observation $I _ { \mathrm { g t } }$ . This supervision asks � to preserve the scene content of $\mathcal { T }$ while changing the perspective as specified by �; whether the rendered views are geometrically consistent with the scene is assessed with the metric introduced next. Details of the data construction are given in the Appendix B.1.

![](images/893cc1da6ed0faebaa5848706c5d87afcfa94b213e0f1bd4fda9a563adb8e460.jpg)  
Figure 3: Overview of SpatialASM. (1) An allocentric novel-view generator (NVG, �) renders the scene from a queried viewpoint, and GeoP measures how much a rendered view perturbs the 3D reconstruction of the inputs. (2) An LVLM plans viewpoints, � renders them, and filtered trajectories are converted to captions for SFT. (3) GeoP-filtered candidate views form verifiable perspective-taking questions for RL.

Geometry Perturbation (GeoP) metric. Standard novel-view synthesis metrics such as PSNR, SSIM, and LPIPS measure image-level similarity to a reference view and therefore do not capture whether a rendered view is geometrically consistent with the scene under large viewpoint changes.

To assess the geometric plausibility of views rendered by $G ,$ we introduce GeoP, a model-based metric that measures how much a rendered view perturbs the 3D geometry inferred from the original observations (Fig. 3(1b)). Given the image set ℐ of a scene, we first reconstruct the scene with a pretrained feed-forward 3D reconstruction model $F ,$ , obtaining a point cloud $\mathcal { M } _ { \mathrm { o r i g } } = F ( \mathcal { T } )$ from the original observations alone. We then add the rendered view $I _ { \mathrm { e d i t } } = G ( \mathbb { Z } , v )$ to the observations and run the same reconstruction:

$$
\begin{array} { r } { T ^ { + } = \mathcal { T } \cup \{ I _ { \mathrm { e d i t } } \} , \quad \mathcal { M } _ { \mathrm { a u g } } = F ( \mathcal { T } ^ { + } ) . } \end{array}\tag{2}
$$

Both point clouds are produced by � in the same world coordinate system from its predicted depth maps, camera intrinsics, and camera poses, so they can be compared directly. We define $\mathrm { G e o P } ( \mathcal { T } , I _ { \mathrm { e d i t } } )$ as the Chamfer distance between $\mathcal { M } _ { \mathrm { o r i g } }$ and $\mathcal { M } _ { \mathrm { a u g } } , i . e .$ , the average squared nearest-neighbor distance between the two point sets in both directions:

$$
\begin{array} { r } { \mathrm { G e o P } ( \mathcal { T } , I _ { \mathrm { e d i t } } ) = \displaystyle \frac { 1 } { | \mathcal { M } _ { \mathrm { o r i g } } | } \sum _ { \mathbf { p } \in \mathcal { M } _ { \mathrm { o r i g } } } \operatorname* { m i n } _ { \mathbf { q } \in \mathcal { M } _ { \mathrm { a u g } } } \| \mathbf { p } - \mathbf { q } \| _ { 2 } ^ { 2 } } \\ { + \displaystyle \frac { 1 } { | \mathcal { M } _ { \mathrm { a u g } } | } \sum _ { \mathbf { q } \in \mathcal { M } _ { \mathrm { a u g } } } \operatorname* { m i n } _ { \mathbf { p } \in \mathcal { M } _ { \mathrm { o r i g } } } \| \mathbf { q } - \mathbf { p } \| _ { 2 } ^ { 2 } . } \end{array}\tag{3}
$$

A low GeoP means that adding $I _ { \mathrm { e d i t } }$ changes the reconstructed geometry little, i.e., � can explain the rendered view as another observation of the same scene. A high GeoP means that the rendered view forces � to alter its scene-level geometry, which we take as a sign of geometric inconsistency. GeoP is therefore a proxy that depends on $F$ rather than a ground-truth measure; we use it to filter rendered views with thresholds (Sec. 3.2 and Sec. 3.3) and to compare generators in our experiments.

## 3.2. ASM Trajectory Synthesis

Motivation. Existing spatial datasets ofer large-scale supervision, but most contain only question-answer (QA) pairs without intermediate reasoning. Such answer-only supervision does not teach a model how to perform ASM on spatial tasks that require reasoning beyond the given observations. Meanwhile, current LVLMs often select uninformative viewpoints and imagine novel views unreliably (Sec. 4.1). Directly prompting an LVLM to write ASM trajectories therefore risks inaccurate or hallucinated intermediate steps, which would yield misleading supervision.

Trajectory synthesis. We therefore synthesize ASM trajectories automatically, using � as an external spatial simulator during data construction only (Fig. 3(2)). From large-scale open-source spatial datasets, we select subtasks that require active perspective exploration beyond direct recognition, $e . g .$ , reasoning about camera-object relationships and spatial configurations, associating multiple input views, and inferring what is seen from a viewpoint other than the original ones.

Each QA sample from the selected subtasks is a triplet $( q , T , a )$ , where � is the question, ℐ is the set of input images, and � is the ground-truth answer from the source dataset. For each sample that � fails to answer from ℐ alone (see Filtering below), we prompt � to produce a view plan with two parts: a reasoning trace $r _ { \mathrm { p l a n } }$ that infers which viewpoints would help answer $q ,$ and � allocentric viewpoints $\{ v _ { k } \} _ { k = 1 } ^ { K }$ , each of which can be used directly as the condition of �:

$$
( r _ { \mathrm { p l a n } } , \{ v _ { k } \} _ { k = 1 } ^ { K } ) = M ( q , \mathbb { Z } ) , \quad v _ { k } = ( i _ { k } , x _ { k } , y _ { k } , d _ { k } ) .\tag{4}
$$

The planned viewpoints $\{ v _ { k } \}$ specify where and in which direction the model should observe the scene to answer �. � then renders a view for each of them:

$$
I _ { \mathrm { e d i t } , k } = G ( { \mathbb T } , v _ { k } ) , \quad k = 1 , \ldots , K .\tag{5}
$$

The rendered views are then returned to � together with a textual description $t _ { \mathrm { d e s } }$ of the planned viewpoints, and � answers the question again, producing a reasoning trace $r _ { \mathrm { r e a s o n } }$ and an answer �ˆ:

$$
\begin{array} { r } { \left( r _ { \mathrm { r e a s o n } } , \hat { a } \right) = M \left( q , t _ { \mathrm { d e s } } , \mathcal { T } , \{ I _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K } \right) . } \end{array}\tag{6}
$$

Filtering. We retain a trajectory only if the rendered views are both useful and geometrically consistent: � must answer incorrectly from ℐ alone but correctly after seeing the rendered views, and every rendered view must have a GeoP below a threshold �. Let $\hat { a } _ { 0 }$ denote the answer of � from ℐ alone. It is checked first, so that questions � already answers correctly are discarded before any viewpoint is planned or rendered (Fig. 3(2)). The set of retained image-form trajectories for a question $( q , { \mathcal { T } } )$ is

$$
\begin{array} { r } { \mathcal { T } _ { \mathrm { i m g } } = \big \{ \big ( r _ { \mathrm { p l a n } } , \{ I _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K } , r _ { \mathrm { r e a s o n } } , \hat { a } \big ) \big | \hat { a } _ { 0 } \ne a , \hat { a } = a , \operatorname* { m a x } _ { k } \mathrm { G e o P } ( \mathcal { L } , I _ { \mathrm { e d i t } , k } ) < \theta \big \} , } \end{array}\tag{7}
$$

where � is a pre-defined threshold. The first two conditions select cases in which the rendered views change the outcome, so that the supervision reflects useful perspective exploration rather than redundant visual input; the third removes geometrically inconsistent views.

Caption substitution. To internalize view imagination rather than view consumption, we replace each rendered view in a retained trajectory with a textual caption. A captioning model $M _ { \mathrm { c a p } }$ describes $I _ { \mathrm { e d i t } , k }$ as $C _ { \mathrm { e d i t } , k } = M _ { \mathrm { c a p } } ( I _ { \mathrm { e d i t } , k } )$ and is prompted to focus on the 2D layout and relative positions of objects in the view. The final ASM trajectories for a question $( q , { \mathcal { T } } )$ are

$$
\mathcal { T } _ { \mathrm { A S M } } = \left\{ \left( r _ { \mathrm { p l a n } } , \left\{ C _ { \mathrm { e d i t } , k } \right\} _ { k = 1 } ^ { K } , r _ { \mathrm { r e a s o n } } , \hat { a } \right) \Big | \left( r _ { \mathrm { p l a n } } , \left\{ I _ { \mathrm { e d i t } , k } \right\} _ { k = 1 } ^ { K } , r _ { \mathrm { r e a s o n } } , \hat { a } \right) \in \mathcal { T } _ { \mathrm { i m g } } \right\} .\tag{8}
$$

Each trajectory thus supervises the model to plan informative viewpoints for the task, to imagine the observation and spatial layout from each planned viewpoint in text, and to reason with the imagined views. Because the rendered images never appear in the supervision, the model learns to carry out this procedure from the original observations alone and does not call � at inference time. This supervision targets multi-step spatial tasks that require information beyond the original observations; its efect is examined in our experiments.

![](images/3257fc3ff4a5db99cfa01bc2f618271d9fe5bd0da0f3b99c02994d017f3fa1f5.jpg)

![](images/a77c4cdec3be775015f4e11e1845b8ad738c5e761a0a8ee61be54d5558889047.jpg)  
Figure 4: Evaluation of external ASM. We evaluate vanilla VLM reasoning, and two external ASM strategies with diferent viewpoint-planning methods. Both planning-based settings invoke SpatialASM-NVG and condition the final reasoning on the original and generated observations.

## 3.3. View-Imagination Reinforcement Learning

Motivation. To further strengthen the ability of LVLMs to imagine a scene from a designated allocentric viewpoint, we construct automatically verifiable multiple-choice questions and train the SFT model from Sec. 3.2 on them with GRPO [53] (Fig. 3(3)). These questions train the model to associate an allocentric viewpoint query with its observation, without manually annotated reasoning traces.

Candidate views. For an image set ℐ of a scene, we randomly sample four allocentric viewpoints $\{ v _ { k } =$ $( 1 , x _ { k } , y _ { k } , d _ { k } ) \} _ { k = 1 } ^ { 4 }$ anchored on $I _ { 1 }$ and render one candidate view for each with �:

$$
\mathcal { V } = \{ I _ { \mathrm { e d i t } , 1 } , \dots , I _ { \mathrm { e d i t } , 4 } \} , \quad I _ { \mathrm { e d i t } , k } = G ( \mathcal { T } , v _ { k } ) .\tag{9}
$$

From each candidate set � we build two complementary multiple-choice tasks. 1) Generated-view localization. We overlay four numbered markers on the anchor image $I _ { 1 }$ at the sampled locations $\{ ( x _ { k } , y _ { k } ) \}$ to obtain $I _ { \mathrm { m a r k } }$ and pick one candidate $I _ { \mathrm { e d i t } , j }$ . Given $( I _ { \mathrm { m a r k } } , \mathcal { T } , I _ { \mathrm { e d i t } , j } )$ , the model must determine at which numbered marker the candidate view was captured and the answer is $j .$ . 2) Pose-conditioned view selection. This task is the inverse: given a textual target pose, the model must pick the matching candidate. For a chosen index $j$ (selected as described below), we use $M _ { \mathrm { c a p } }$ to describe the location $( x _ { j } , y _ { j } )$ ) on $I _ { 1 }$ and combine this description with the viewing direction $d _ { j }$ into a target-pose specification $t _ { j }$ . Given $t _ { j } , \mathcal { I }$ , and the four candidates �, the model must select the candidate that depicts the scene from the described location and direction and the answer is �.

Answer selection. The ground-truth candidate $I _ { \mathrm { e d i t } , j }$ must be geometrically consistent with ℐ for the reward to be meaningful, so we select � for each candidate set � with GeoP:

$$
\begin{array} { r } { K = \{ k \mid \mathrm { G e o P } ( \overline { { L } } , I _ { \mathrm { e d i t } , k } ) < \theta _ { \mathrm { R L } } \} , \quad j = \left\{ \begin{array} { l l } { \mathrm { a n y } k \in \mathcal { K } , } & { K \neq \emptyset , } \\ { \mathrm { d i s c a r d } \mathrm { t h e } s \mathrm { a m p l e } , } & { K = \emptyset , } \end{array} \right. } \end{array}\tag{10}
$$

where $\theta _ { \mathrm { R L } }$ is a pre-defined threshold. The two tasks provide complementary supervision: generated-view localization learns the inverse mapping from an observation to its allocentric viewpoint, whereas pose-conditioned view selection learns the forward mapping from a textual target pose to its expected observation. Both answers are derived automatically without human annotation, and the two tasks are intended to improve the ability of LVLMs to imagine and reason about scene layout and object configuration from viewpoints other than the observed ones.

## 4. Experiments

Dataset construction. To train the NVG in Sec. 3.1, we leverage DL3DV [40], a public dataset which provides 10k diverse scenes captured from multiple viewpoints together with the corresponding camera matrices, to construct our training set SpatialASM-edit140k, containing both single-image and multi-image inputs. To synthesize ASM trajectories introduced in Sec. 3.2, we source various challenging spatial-QA samples from

Table 1: Performance comparison on 7 spatial reasoning and 3 general-VQA benchmarks. We report the average accuracy over all benchmarks within each category. The performance of several spatial models are also provided (7B variants on Qwen2.5-VL-7B; 8B variants on Qwen3-VL-8B).
<table><tr><td rowspan="2">Models</td><td colspan="7">Spatial Reasoning</td><td>Gnr.-VQA</td></tr><tr><td></td><td>Mindcube MMSI Spa4576d</td><td></td><td>Viewspa 3DSR EmbSpa</td><td></td><td></td><td>ESI</td><td>Avg. Avg.</td></tr><tr><td colspan="9">Representative Spatial Models</td></tr><tr><td>SpaceR-7B</td><td>36.6</td><td>28.8</td><td>45.1</td><td>38.4</td><td>54.5 37.3</td><td></td><td>40.2 40.1</td><td>59.4</td></tr><tr><td>VST-RL-7B</td><td>39.3</td><td>35.1</td><td>43.4</td><td>47.0</td><td>60.1 72.0</td><td>40.1</td><td>48.1</td><td>62.1</td></tr><tr><td>HoliSpatial-8B</td><td>52.0</td><td>30.6</td><td>30.5</td><td>50.1</td><td>49.7 73.8</td><td>41.8</td><td>46.9</td><td>63.1</td></tr><tr><td>SenseNova-8B</td><td>50.4</td><td>29.6</td><td>41.3</td><td>46.4</td><td>55.2 70.8</td><td>36.5</td><td>47.2</td><td>63.7</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-4B)</td></tr><tr><td>Qwen3-VL-4B</td><td>33.9</td><td>28.0</td><td>37.2</td><td>43.2</td><td>55.8 72.3</td><td></td><td>36.5 43.8</td><td>63.7</td></tr><tr><td>SpatialASM-4B</td><td>39.1</td><td>29.2</td><td>42.8</td><td>46.2</td><td>56.4</td><td>76.5 40.6</td><td>47.3</td><td>63.1</td></tr><tr><td>Improvement</td><td>+5.2</td><td>+1.2</td><td>+5.6</td><td>+3.0</td><td>+0.6 +4.2</td><td></td><td>+4.1 +3.5</td><td>-0.6</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-8B)</td></tr><tr><td>Qwen3-VL-8B</td><td>40.4</td><td>27.8</td><td>34.8</td><td>45.3</td><td>57.2</td><td>79.5</td><td>38.4 46.2</td><td>64.4</td></tr><tr><td>SpatialASM-8B</td><td>43.9</td><td>31.1</td><td>43.5</td><td>46.8</td><td>58.2</td><td>80.2</td><td>40.2 49.1</td><td>65.0</td></tr><tr><td>Improvement</td><td>+3.5</td><td>+3.3</td><td>+8.7</td><td>+1.5</td><td>+1.0</td><td>+0.7</td><td>+1.8 +2.9</td><td>+0.6</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3-VL-30BA3B)</td></tr><tr><td>Qwen3-VL-30BA3B</td><td>36.7</td><td>25.2</td><td>41.9</td><td>43.9</td><td>54.5 73.4</td><td></td><td>37.2 44.7</td><td>69.4</td></tr><tr><td>SpatialASM-30BA3B</td><td>37.9</td><td>30.8</td><td>42.1</td><td>45.6</td><td>58.8</td><td>75.2</td><td>40.3 47.2</td><td>68.9</td></tr><tr><td>Improvement</td><td>+1.2</td><td>+5.6</td><td>+0.2</td><td>+1.7</td><td>+4.3 +1.8</td><td></td><td>+3.1 +2.5</td><td>-0.5</td></tr><tr><td colspan="9">Baselines &amp; Our Model (Qwen3.5-9B)</td></tr><tr><td>Qwen3.5-9B</td><td>50.8</td><td>33.5</td><td>41.4</td><td>47.3</td><td>64.4</td><td>81.4</td><td>39.1 51.1</td><td>78.1</td></tr><tr><td>SpatialASM-9B</td><td>54.7</td><td>35.2</td><td>43.5</td><td>47.9</td><td>63.5</td><td>82.3</td><td>39.2 52.3</td><td>78.5</td></tr><tr><td>Improvement</td><td>+3.9</td><td>+1.7</td><td>+2.1</td><td>+0.6</td><td>-0.9</td><td>+0.9</td><td>+0.1 +1.2</td><td>+0.4</td></tr></table>

Holi-spatial, VST-500k, and Mindcube-train [26, 60, 71] and generate a dataset with 128k reasoning trajectories, denoted SpatialASM-128k. In practice, we use the subset SpatialASM-66k constructed exclusively from Holispatial for tuning LVLMs. For RL (Sec. 3.3), we only use the original 2D observations collected from HoliSpatial and DL3DV, and automatically construct SpatialASM-RL-58k by employing the NVG. More details are provided in Appendix B.

Training setup. To internalize ASM capabilities into LVLMs, we adopt the two-stage post-training strategy: first SFT then GRPO. During SFT, we train four models from both the Qwen3-VL and the Qwen3.5 series [3, 48] on SpatialASM-66k for 10 epochs with the learning rate of 5e-6. For GRPO, we use a global batch size of 128 and a learning rate of 1e-6, and train for 300 steps. We fully fine-tune Qwen3-VL-4B and Qwen3-VL-8B, while applying LoRA (rank=32) to Qwen3-VL-30B-A3B and Qwen3.5-9B. Training details are provided in Appendix C.1.

Evaluation setup. We evaluate all models on seven Spatial Reasoning (SR) benchmarks [22, 29, 35, 45, 60, 63, 72] and three general-VQA benchmarks [12, 65, 75]. All benchmarks except ESI-Bench are evaluated using VLMEvalKit [23]. For ESI-Bench, we adopt the passive-single setting. We ask the models to produce reasoning content first on the SR tasks, and to answer directly on the general-VQA tasks. Benchmark and evaluation details are provided in Appendix C.2 and Appendix C.3.

## 4.1. Preliminary Analysis of Explicit ASM

Before internalizing ASM into LVLMs, we first wonder whether ASM is an efective reasoning paradigm and whether existing LVLMs lack this capability. Like previous studies in Fig. 2(b), we conduct a preliminary experiment on the external ASM settings, where LVLMs first plan question-relevant viewpoints, invoke the NVG to synthesize observations from these viewpoints, and then reason jointly over the original and generated images. We sample 200 questions from each of the Among and Around subtasks of MindCube and report the results of three reasoning methods: (i) Vanilla: LVLMs directly reason over the original observations; (ii)

Table 2: Accuracy and average inference time of diferent methods on benchmark subtasks. (i) Vanilla: the LVLM reasons directly from the input observations alone, and the reasoning duration is reported; (ii) + External ASM: Similar to Sec. 4.1, the LVLM externally invokes SpatialASM-NVG to help reasoning. The time to synthesize novel-view observations is reported.
<table><tr><td rowspan="3">Subtask</td><td colspan="4">Qwen3-VL-8B</td><td colspan="3">SpatialASM-8B</td></tr><tr><td>Vanilla</td><td></td><td>+ External ASM</td><td></td><td>Vanilla</td><td>+ External ASM</td></tr><tr><td>|Accuracy T(s)</td><td></td><td>|Accuracy TNvG (s)</td><td></td><td>|Accuracy T(s)</td><td>|Accuracy TnvG(s)</td></tr><tr><td>MindcubeAmong</td><td>33.0</td><td>12.6</td><td>42.0</td><td>57.6</td><td>39.5</td><td>45.0</td></tr><tr><td>MindcubeAround</td><td>52.0</td><td>7.5</td><td>45.5 38.1</td><td>58.0</td><td>18.6 7.2</td><td>56.8 61.5 46.8</td></tr></table>

Random-Plan: randomly selected target viewpoints are passed to SpatialASM-NVG, and (iii) VLM-Plan: LVLMs are explicitly prompted to plan question-relevant viewpoints before invoking SpatialASM-NVG.

By comparing Vanilla with the other two settings in Fig. 4, the planning-based external ASM paradigm generally improves accuracy over vanilla reasoning, which demonstrates that viewpoint planning and imagination constitute a generally efective paradigm for complex spatial reasoning. Moreover, LVLM-based planning consistently matches or outperforms random planning, indicating that existing LVLMs already possess a certain ability to identify informative viewpoints when explicitly prompted. Nevertheless, this ability is not spontaneously activated during direct LVLM reasoning. In other words, existing LVLMs are able to execute view-planning only under an explicitly-prompted inference pipeline. ASM has not yet become an intrinsic reasoning capability that they can autonomously invoke when needed for certain complicated spatial tasks.

Furthermore, Qwen3-VL-8B drops under both random and LVLM-based planning, suggesting that relatively small models still lag behind in both robust planning for diverse spatial tasks and long-context multimodal reasoning. It further motivates internalizing ASM through training so that models can learn when and how to employ this reasoning paradigm more reliably.

## 4.2. Main Results

Overall SR performance gains. As shown in Table 1, SpatialASM improves spatial reasoning on both Qwen3-VL and Qwen3.5 architectures. The Qwen3-VL models of three diferent scales, including dense models and an mix-of-experts (MoE) model, yield consistent performance gains on all seven SR benchmarks after applying our approach. In particular, the 4B model achieves an average improvement of +3.5%, with notable gains on Mindcube (+5.2%) and Spatial457 (+5.6%). In contrast, prior spatial models exhibit uneven performance across benchmarks. For example, Sensenova-8B improves significantly on Mindcube (+10.0%), but exhibits drops by 8.7% on EmbSpa. This suggests that their gains may depend more on benchmark-specific task formats, whereas SpatialASM elicits a more universal and transferable spatial capability.

Analysis of spatial capabilities. The largest gains generally occur on more challenging benchmarks (e.g., MindCube, MMSI, and Spatial457 ) which require the comprehensive use of multiple reasoning capabilities, such as spatial mental modeling, cross-view correspondence, complex 6D reasoning, etc. These tasks benefit from the learned ability to plan informative viewpoints and imagine the corresponding observations. SpatialASM also yields modest gains on relatively perception-centric SR benchmarks (e.g., 3DSR, Embspa), indicating that the knowledge learned from ASM reasoning trajectories and the RL tasks improves the general understanding of the 3D space.

General Visual Capabilities. SpatialASM preserves the general visual capabilities of LVLMs, with changes in the average general-VQA accuracy remaining within 0.6% across all four backbones. Importantly, SpatialASM-8B outperforms the two 8B counterparts in representative spatial models, where HoliSpatial experiences an average degredation of 1.3% on all three benchmarks after SFT. We attribute this result partly to view-imagination RL, which exposes LVLMs to diverse visual observations and high-quality, geometry-filtered NVG outputs. Consequently, the model learns from rich visual supervision rather than merely memorizing the reasoning patterns of the SFT trajectories. The models’ performance on each benchmark is shown in Appendix C.4.

Comparison with external ASM. As shown in Table 2, external ASM is not always beneficial for the baseline model. Introducing redundant visual evidence or unnecessary view-planning CoT may degrade performance (52.0% to 45.5% on Around). In contrast, after internalization, SpatialASM-8B improves under

Table 3: Comparison of diferent novel-view generators under external ASM paradigm.
<table><tr><td>Methods</td><td> $| \mathsf { A c c } _ { \mathsf { a m o n g } } \ ^ { \cdot }$  个</td><td> $\mathtt { A c c } _ { \mathrm { a r o u n d } }$ </td><td>↑GeoP↓</td></tr><tr><td>Qwen3.5-35BA3B</td><td>42.5</td><td>66.0</td><td>/</td></tr><tr><td>+GPT-Image2</td><td>42.0</td><td>69.0</td><td>0.141</td></tr><tr><td>+SpatialEdit</td><td>46.0</td><td>68.0</td><td>0.110</td></tr><tr><td>+SVC</td><td>48.5</td><td>68.5</td><td>0.087</td></tr><tr><td>+SpatialASM-NVG</td><td>55.5</td><td>70.5</td><td>0.118</td></tr><tr><td>Kimi-k2.5</td><td>52.5</td><td>69.0</td><td>/</td></tr><tr><td>+GPT-Image2</td><td>52.0</td><td>71.0</td><td>0.157</td></tr><tr><td>+SpatialEdit</td><td>52.5</td><td>69.0</td><td>0.138</td></tr><tr><td>+SVC</td><td>52.5</td><td>74.0</td><td>0.072</td></tr><tr><td>+SpatialASM-NVG</td><td>68.0</td><td>80.5</td><td>0.099</td></tr></table>

Table 4: Ablations on training strategies and GeoPbased data filtering strategies.
<table><tr><td colspan="5">Methods |Mindcube EmbSpa ViewSpa ESI Avg.</td></tr><tr><td>Qwen3-VL-4B</td><td>33.9</td><td>72.3</td><td>43.2</td><td>36.5 46.5</td></tr><tr><td colspan="5">SFT with ASM trajectories</td></tr><tr><td>w/o filter</td><td>36.4</td><td>75.0</td><td>45.3</td><td>36.8 48.4</td></tr><tr><td>with filter</td><td>38.7</td><td>74.6</td><td>46.2</td><td>40.2 49.9</td></tr><tr><td colspan="5">View-imagination RL</td></tr><tr><td>w/o filter with filter</td><td>34.2 35.1</td><td>75.7 76.3</td><td>43.8 43.6</td><td>39.2 48.2 40.1 48.8</td></tr><tr><td colspan="5">SFT + RL</td></tr><tr><td>SpatialASM-4B|</td><td>39.1</td><td>76.5</td><td>46.2</td><td>40.6 50.6</td></tr></table>

vanilla reasoning on both subtasks without noticeable increase on the inference duration. Most importantly, combining SpatialASM-8B with external ASM yields the highest accuracy on both Among (45.0%) and Around (61.5%). SpatialASM strengthens the model’s intrinsic ability to plan viewpoints and reason about spatial evidence, while external NVG provides additional informative views. Employing them together achieves higher accuracy than either approach alone, suggesting that internalization and external tool-use provide complementary gains.

## 4.3. Ablation Studies

Comparison of novel-view generators. Following the experimental design in Sec. 4.1, we execute the external ASM reasoning paradigm with diferent novel-view generators and evaluate on the same Mindcube subsets as in Sec. 4.1. The accuracy as well as the median GeoP value of all generated images are reported. As shown in Table 3, compared with other world models, SpatialASM-NVG achieves the highest accuracy for both backbone LVLMs on both Mindcube subsets.

Interestingly, SpatialASM-NVG does not achieve the best GeoP as SVC obtains lower GeoP values for both LVLMs, but it consistently yields substantially higher reasoning accuracy. This observation suggests that geometric consistency alone does not fully determine the utility of generated views for downstream spatial reasoning. We attribute this to our allocentric form of viewpoint representation. Rather than describing the target viewpoint through a sequence of quantified egocentric motions, LVLMs can designate the viewpoint through a single query, and the corresponding location of the viewpoint is explicitly marked in the original input image. It makes the spatial correspondence between the original scene and the generated observation easier for LVLMs to understand during final multi-image reasoning. Therefore, our allocentric-view generation provides not only reliable novel views, but also auxiliary evidence that is more directly aligned with the reasoning objective.

Training and Data recipe. Table 4 compares the independent efect of each training stage and evaluates the filtering strategy for data preparation. All of the four single-stage variants are initialized from the baseline LVLM. For both SFT and RL, applying GeoP-based filtering improves the average performance on the four SR benchmarks, indicating that GeoP efectively removes geometrically distorted novel views and prevents unreliable visual evidence from being incorporated into ASM reasoning trajectories or RL supervision.

The two training stages also exhibit distinct but complementary efects. SFT with SpatialASM-66k (with filter) produces strong gains on tasks requiring explicit viewpoint transformation and multi-step spatial reasoning, such as MindCube (+4.8%) and ViewSpa (+3.0%), which verifies the high quality of our ASM trajectories and the reliability of the proposed CoT synthesis engine from answer-only spatial QAs. In contrast, view-imagination RL is especially efective on the more perception-oriented EmbSpa benchmark and outperforms the setting of applying SFT alone. Combining SFT and RL achieves the best overall accuracy, suggesting a clear synergy where SFT primarily teaches the structured ASM reasoning pattern while RL further strengthens viewpoint-conditioned spatial perception and imagination. Some detailed design is given in Appendix C.5.

## 5. Conclusion

In this work, we identify Active Spatial Mentalization (ASM) as a key bottleneck in LVLM spatial reasoning. To address this issue, we propose SpatialASM, a novel post-training framework that internalizes ASM capabilities into LVLMs through training-time spatial simulation. Experiments across seven spatial reasoning benchmarks and four model architectures demonstrate that SpatialASM consistently improves spatial reasoning while preserving general visual capabilities. More importantly, our framework provides a scalable approach to constructing CoT supervision for existing spatial datasets, addressing the common lack of intermediate reasoning processes in open-source spatial data. In future work, we plan to internalize other forms of tool-assisted reasoning and extend ASM to dynamic real-world scenes, embodied interaction, and longer-horizon 3D reasoning.

## References

[1] Niket Agarwal, Arslan Ali, Jon Allen, Martin Antolini, Adeline Aubame, Alisson Azzolini, Junjie Bai, Maciej Bala, Yogesh Balaji, Josh Bapst, et al. Cosmos 3: Omnimodal world models for physical ai. arXiv preprint arXiv:2606.02800, 2026. 2

[2] Lei Bai, Jiaqi Cao, Chiyu Chen, Guanzhou Chen, Kai Chen, Guangran Cheng, Erfei Cui, Xuanlang Dai, Shengyuan Ding, Shangheng Du, et al. Intern-s2-preview: Scientific agentic foundation model. arXiv preprint arXiv:2608.13505, 2026. 1

[3] Shuai Bai, Yuxuan Cai, Ruizhe Chen, Keqin Chen, Xionghui Chen, Zesen Cheng, Lianghao Deng, Wei Ding, Chang Gao, Chunjiang Ge, et al. Qwen3-vl technical report. arXiv preprint arXiv:2511.21631, 2025. 1, 4, C.3, C.5

[4] Gilad Baruch, Zhuoyuan Chen, Afshin Dehghan, Tal Dimry, Yuri Feigin, Peter Fu, Thomas Gebauer, Brandon Jofe, Daniel Kurz, Arik Schwartz, et al. Arkitscenes: A diverse real-world dataset for 3d indoor scene understanding using mobile rgb-d data. arXiv preprint arXiv:2111.08897, 2021. 2

[5] Mahtab Bigverdi, Linjie Li, Weikai Huang, Yiming Liu, Jaemin Cho, Tuhin Kundu, Chris Dongjoo Kim, Zelun Luo, Jieyu Zhang, Linda Shapiro, et al. Imaginative perception tokens enhance spatial reasoning in multimodal language models. In European Conference on Computer Vision, pages 40–57. Springer, 2026. 2

[6] Black Forest Labs. FLUX.2: Frontier visual intelligence. https://bfl.ai/blog/flux-2, 2025. 3.1, C.1, D.1

[7] Jake Bruce, Michael D Dennis, Ashley Edwards, Jack Parker-Holder, Yuge Shi, Edward Hughes, Matthew Lai, Aditi Mavalankar, Richie Steigerwald, Chris Apps, et al. Genie: Generative interactive environments. In Forty-first international conference on machine learning, 2024. 2

[8] Wenxiao Cai, Iaroslav Ponomarenko, Jianhao Yuan, Xiaoqi Li, Wankou Yang, Hao Dong, and Bo Zhao. Spatialbot: Precise spatial understanding with vision language models. In 2025 IEEE International Conference on Robotics and Automation (ICRA), pages 9490–9498. IEEE, 2025. A.2

[9] Zhongang Cai, Ruisi Wang, Chenyang Gu, Fanyi Pu, Junxiang Xu, Yubo Wang, Wanqi Yin, Zhitao Yang, Chen Wei, Tongxi Zhou, et al. Scaling spatial intelligence with multimodal foundation models. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 7879–7890, 2026. A.2, C.3

[10] Meng Cao, Xingyu Li, Xue Liu, Ian Reid, and Xiaodan Liang. Spatialdreamer: Incentivizing spatial reasoning via active mental imagery. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 7176–7187, 2026. 1, 2

[11] Boyuan Chen, Zhuo Xu, Sean Kirmani, Brain Ichter, Dorsa Sadigh, Leonidas Guibas, and Fei Xia. Spatialvlm: Endowing vision-language models with spatial reasoning capabilities. In Proceedings of the IEEE/CVF conference on computer vision and pattern recognition, pages 14455–14465, 2024. 1, 2, A.2

[12] Lin Chen, Jinsong Li, Xiaoyi Dong, Pan Zhang, Yuhang Zang, Zehui Chen, Haodong Duan, Jiaqi Wang, Yu Qiao, Dahua Lin, et al. Are we on the right way for evaluating large vision-language models? Advances in Neural Information Processing Systems, 37:27056–27087, 2024. 4, C.2

[13] Xinyuan Chen, Haoyu Guo, Shi Guo, Bingqi Jiang, Chunhua Shen, Xing Shen, Tianfan Xue, Yufei Xue, Mulin Yu, Weinan Zhang, et al. A definition and roadmap for world models. arXiv preprint arXiv:2607.06401, 2026. 2

[14] Zhixun Chen, Ming Li, Yuxuan Huang, Yali Du, Meng Fang, and Tianyi Zhou. Atlas: Agent tuning via learning critical steps. In Findings of the Association for Computational Linguistics: ACL 2025, pages 25334–25349, 2025. A.3

[15] An-Chieh Cheng, Hongxu Yin, Yang Fu, Qiushan Guo, Ruihan Yang, Jan Kautz, Xiaolong Wang, and Sifei Liu. Spatialrgpt: Grounded spatial reasoning in vision-language models. Advances in Neural Information Processing Systems, 37:135062–135093, 2024. 1, 2, A.2

[16] Seokju Cho, Ryo Hachiuma, Abhishek Badki, Hang Su, Byung-Kwan Lee, Chan Hee Song, Sifei Liu, Subhashree Radhakrishnan, Seungryong Kim, Yu-Chiang Frank Wang, et al. Spatialclaw: Rethinking action interface for agentic spatial reasoning. arXiv preprint arXiv:2606.13673, 2026. A.3

[17] Amirhossein Dabiriaghdam, Shayan Vassef, Mohammadreza Bakhtiari, Yasamin Medghalchi, Ilker Haci haliloglu, Mesrob Ohannessian, Lele Wang, and Giuseppe Carenini. Vamps: Visual-assisted mathematical problem solving benchmark. arXiv preprint arXiv:2606.04244, 2026. A.1

[18] Angela Dai, Angel X Chang, Manolis Savva, Maciej Halber, Thomas Funkhouser, and Matthias Nießner. Scannet: Richly-annotated 3d reconstructions of indoor scenes. In 2017 IEEE conference on computer vision and pattern recognition (CVPR), pages 2432–2443. IEEE, 2017. 2

[19] Yalun Dai, Hao Li, Shulin Tian, Runmao Yao, Yuhao Dong, Fangzhou Hong, Zhaoxi Chen, Fangfu Liu, Baoliang Tian, Dingwen Zhang, et al. S-agent: Spatial tool-use elicits reasoning for spatial intelligence. arXiv preprint arXiv:2606.20515, 2026. A.3

[20] Nianchen Deng, Lixin Gu, Shenglong Ye, Yinan He, Zhe Chen, Songze Li, Haomin Wang, Jinhui Yin, Qi Wei, Tianshuo Yang, et al. Internspatial: A comprehensive dataset for spatial reasoning in vision-language models. In International Conference on Learning Representations, volume 2026, pages 50773–50814, 2026. 2, A.2

[21] Shuangrui Ding, Xuanlang Dai, Long Xing, Shengyuan Ding, Ziyu Liu, Yang JingYi, Penghui Yang, Zhixiong Zhang, Xilin Wei, Xinyu Fang, et al. Wildclawbench: A benchmark for real-world, long-horizon agent evaluation. arXiv preprint arXiv:2605.10912, 2026. A.1

[22] Mengfei Du, Binhao Wu, Zejun Li, Xuanjing Huang, and Zhongyu Wei. EmbSpatial-Bench: Benchmarking spatial understanding for embodied tasks with large vision-language models. In Proceedings of the 62nd Annual Meeting of the Association for Computational Linguistics (Volume 2: Short Papers), pages 346–355, 2024. 4, C.2

[23] Haodong Duan, Junming Yang, Yuxuan Qiao, Xinyu Fang, Lin Chen, Yuan Liu, Xiaoyi Dong, Yuhang Zang, Pan Zhang, Jiaqi Wang, et al. VLMEvalKit: An open-source toolkit for evaluating large multi-modality models. In Proceedings of the 32nd ACM international conference on multimedia, pages 11198–11201, 2024. 4

[24] Xiang Feng, Jiawei Zhou, Zhangfeng Huang, Kewei Wang, Shanshan Ye, Jinxin Hu, Zulong Chen, Yong Luo, and Jing Zhang. Docscope: Benchmarking verifiable reasoning for trustworthy long-document understanding. arXiv preprint arXiv:2605.08888, 2026. A.1

[25] Xingyu Fu, Yushi Hu, Bangzheng Li, Yu Feng, Haoyu Wang, Xudong Lin, Dan Roth, Noah A Smith, Wei-Chiu Ma, and Ranjay Krishna. Blink: Multimodal large language models can see but not perceive. In European Conference on Computer Vision, pages 148–166. Springer, 2024. 1

[26] Yuanyuan Gao, Hao Li, Yifei Liu, Xinhao Ji, Yuning Gong, Yuanjun Liao, Fangfu Liu, Manyuan Zhang, Yuchen Yang, Dan Xu, et al. Holi-spatial: Evolving video streams into holistic 3d spatial intelligence. arXiv preprint arXiv:2603.07660, 2026. 1, 2, 4, A.2, B.2.1, B.3, C.3

[27] Renjie Gu, Kaichen Zhou, Yan Luo, and Mengyu Wang. GeoWorld-VLM: Geometry from world models for vision-language models. arXiv preprint arXiv:2605.16713, 2026. 2

[28] Zefeng He, Xiaoye Qu, Yafu Li, Tong Zhu, Siyuan Huang, and Yu Cheng. Difthinker: Towards generative multimodal reasoning with difusion models. arXiv preprint arXiv:2512.24165, 2025. C.1

[29] Yining Hong, Jiageng Liu, Han Yin, Manling Li, Leonidas Guibas, Li Fei-Fei, Jiajun Wu, and Yejin Choi. Esi-bench: Towards embodied spatial intelligence that closes the perception-action loop. arXiv preprint arXiv:2605.18746, 2026. 2, 4, A.1, C.2

[30] Mu Hu, Wei Yin, Chi Zhang, Zhipeng Cai, Xiaoxiao Long, Hao Chen, Kaixuan Wang, Gang Yu, Chunhua Shen, and Shaojie Shen. Metric3d v2: A versatile monocular geometric foundation model for zero-shot metric depth and surface normal estimation. IEEE Transactions on Pattern Analysis and Machine Intelligence, 46(12):10579–10596, 2024. A.2

[31] Amita Kamath, Jack Hessel, and Kai-Wei Chang. What’s “up” with vision-language models? investigating their struggle with spatial reasoning. In Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing, pages 9161–9175, 2023. 2

[32] Nikhil Keetha, Norman Müller, Johannes Schönberger, Lorenzo Porzi, Yuchen Zhang, Tobias Fischer, Arno Knapitsch, Duncan Zauss, Ethan Weber, Nelson Antunes, et al. Mapanything: Universal feed-forward metric 3d reconstruction; map-anything. github. io. In 2026 International Conference on 3D Vision (3DV), pages 499–509. IEEE, 2026. A.3, C.1

[33] Juil Koo, Daehyeon Choi, Sangwoo Youn, Phillip Y Lee, and Minhyuk Sung. Toward ambulatory vision: Learning visually-grounded active view selection. arXiv preprint arXiv:2512.13250, 2025. 2

[34] Phillip Y Lee, Jihyeon Je, Chanho Park, Mikaela Angelina Uy, Leonidas Guibas, and Minhyuk Sung. Perspective-aware reasoning in vision-language models via mental imagery simulation. In Proceedings of the IEEE/CVF international conference on computer vision, pages 9241–9251, 2025. 2

[35] Dingming Li, Hongxing Li, Zixuan Wang, Yuchen Yan, Hang Zhang, Siqi Chen, Guiyang Hou, Shengpei Jiang, Wenqi Zhang, Yongliang Shen, et al. ViewSpatial-Bench: Evaluating multi-perspective spatial localization in vision-language models. In European Conference on Computer Vision, pages 95–111. Springer, 2026. 4, C.2

[36] Haoyuan Li, Qihang Cao, Tao Tang, Kun Xiang, Zihan Guo, Jianhua Han, JiaWang Bian, Hang Xu, and Xiaodan Liang. Thinking with geometry: Active geometry integration for spatial reasoning. arXiv preprint arXiv:2602.06037, 2026. 2, A.1

[37] Hongxing Li, Dingming Li, Zixuan Wang, Yuchen Yan, Hang Wu, Wenqi Zhang, Yongliang Shen, Weiming Lu, Jun Xiao, and Yueting Zhuang. Spatialladder: Progressive training for spatial reasoning in visionlanguage models. In International Conference on Learning Representations, volume 2026, pages 76566– 76592, 2026. 1, 2, A.2

[38] Xudong Li, Mengdan Zhang, Peixian Chen, Jiaxi Tan, Zihao Huang, Jingyuan Zheng, Yan Zhang, Xiawu Zheng, Xing Sun, and Rongrong Ji. OmniView-Space: Reinforcing spatial reasoning via multi-perspective spatial mapping. arXiv preprint arXiv:2607.00881, 2026. 1

[39] Haotong Lin, Sili Chen, Junhao Liew, Donny Y Chen, Zhenyu Li, Guang Shi, Jiashi Feng, and Bingyi Kang. Depth anything 3: Recovering the visual space from any views. arXiv preprint arXiv:2511.10647, 2025. A.3

[40] Lu Ling, Yichen Sheng, Zhi Tu, Wentian Zhao, Cheng Xin, Kun Wan, Lantao Yu, Qianyu Guo, Zixun Yu, Yawen Lu, et al. Dl3dv-10k: A large-scale scene dataset for deep learning-based 3d vision. In 2024 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), pages 22160–22169. IEEE, 2024. 4, B.1.1, B.3

[41] Jianhui Liu, Haoze Sun, Wenbo Li, Yanbing Zhang, Rui Yang, Zhiliang Zhu, Yijun Yang, Shenghe Zheng, Nan Jiang, Jiaxiu Jiang, et al. Openspatial: A principled data engine for empowering spatial intelligence. arXiv preprint arXiv:2604.07296, 2026. 1, A.2

[42] Shilong Liu, Zhaoyang Zeng, Tianhe Ren, Feng Li, Hao Zhang, Jie Yang, Qing Jiang, Chunyuan Li, Jianwei Yang, Hang Su, et al. Grounding dino: Marrying dino with grounded pre-training for open-set object detection. In European conference on computer vision, pages 38–55. Springer, 2024. A.2

[43] Yuan Liu, Haodong Duan, Yuanhan Zhang, Bo Li, Songyang Zhang, Wangbo Zhao, Yike Yuan, Jiaqi Wang, Conghui He, Ziwei Liu, et al. Mmbench: Is your multi-modal model an all-around player? In European conference on computer vision, pages 216–233. Springer, 2024. 1

[44] Yuhong Liu, Beichen Zhang, Yuhang Zang, Yuhang Cao, Long Xing, Xiaoyi Dong, Haodong Duan, Dahua Lin, and Jiaqi Wang. Spatial-ssrl: Enhancing spatial understanding via self-supervised reinforcement learning. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 9570–9581, 2026. A.2

[45] Wufei Ma, Haoyu Chen, Guofeng Zhang, Yu-Cheng Chou, Jieneng Chen, Celso De Melo, and Alan Yuille. 3DSRBench: A comprehensive 3d spatial reasoning benchmark. In 2025 IEEE/CVF International Conference on Computer Vision (ICCV), pages 6924–6934. IEEE, 2025. 4, C.2

[46] OpenAI. GPT Image 2 model, 2026. C.5

[47] Kun Ouyang, Yuanxin Liu, Haoning Wu, Yi Liu, Hao Zhou, Jie Zhou, Fandong Meng, and Xu Sun. Spacer: Reinforcing mllms in video spatial reasoning. arXiv preprint arXiv:2504.01805, 2025. 2, A.2, C.3

[48] Qwen Team. Qwen3.5: Towards native multimodal agents, February 2026. 1, 4

[49] Qwen Team. Qwen3.6-27B: Flagship-level coding in a 27b dense model, April 2026. B.2.2

[50] Qwen Team. Qwen3.6-35B-A3B: Agentic coding power, now open to all, April 2026. B.2.2, C.5

[51] Arijit Ray, Jiafei Duan, Ellis Brown, Reuben Tan, Dina Bashkirova, Rose Hendrix, Kiana Ehsani, Aniruddha Kembhavi, Bryan A Plummer, Ranjay Krishna, et al. Sat: Dynamic spatial aptitude training for multimodal language models. arXiv preprint arXiv:2412.07755, 2024. 2

[52] Juneyoung Ro, Namwoo Kim, and Yoonjin Yoon. How well do vision-language models understand cities? a comparative study on spatial reasoning from street-view images. In 2025 IEEE/CVF International Conference on Computer Vision Workshops (ICCVW), pages 6535–6544. IEEE, 2025. A.2

[53] Zhihong Shao, Peiyi Wang, Qihao Zhu, Runxin Xu, Junxiao Song, Xiao Bi, Haowei Zhang, Mingchuan Zhang, YK Li, Yang Wu, et al. Deepseekmath: Pushing the limits of mathematical reasoning in open language models. arXiv preprint arXiv:2402.03300, 2024. 1, 3.3

[54] Roger N Shepard and Jacqueline Metzler. Mental rotation of three-dimensional objects. Science, 1971. 1

[55] HunyuanWorld Team, Zhenwei Wang, Yuhao Liu, Junta Wu, Zixiao Gu, Haoyuan Wang, Xuhui Zuo, Tianyu Huang, Wenhuan Li, Sheng Zhang, et al. Hunyuanworld 1.0: Generating immersive, explorable, and interactive 3d worlds from words or pixels. arXiv preprint arXiv:2507.21809, 2025. 2

[56] Kimi Team, Tongtong Bai, Yifan Bai, Yiping Bao, Jianfeng Cai, Xinyuan Cai, Peizhou Cao, Yuxuan Cao, Ziwei Chai, Y Charles, et al. Kimi k3: Open frontier intelligence. arXiv preprint arXiv:2607.24653, 2026. A.1

[57] Kimi Team, Tongtong Bai, Yifan Bai, Yiping Bao, SH Cai, Yuan Cao, Ziwei Chai, Y Charles, HS Che, Cheng Chen, et al. Kimi k2. 5: Visual agentic intelligence. arXiv preprint arXiv:2602.02276, 2026. C.5

[58] Qwen Team. Qwen2.5-vl, January 2025. C.3

[59] Kangrui Wang, Linjie Li, Zhengyuan Yang, Shiqi Chen, Zihan Wang, Li Fei-Fei, Jiajun Wu, Leonidas Guibas, Lijuan Wang, and Manling Li. Planning with the views. arXiv preprint arXiv:2605.29563, 2026. 2

[60] Qineng Wang, Baiqiao Yin, Pingyue Zhang, Jianshu Zhang, Kangrui Wang, Zihan Wang, Jieyu Zhang, Keshigeyan Chandrasegaran, Han Liu, Ranjay Krishna, et al. Spatial mental modeling from limited views. In The Fourteenth International Conference on Learning Representations, 2025. 4, A.1, B.2.1, C.2

[61] Shuzhe Wang, Vincent Leroy, Yohann Cabon, Boris Chidlovskii, and Jerome Revaud. Dust3r: Geometric 3d vision made easy. In 2024 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), pages 20697–20709. IEEE, 2024. A.2

[62] Wenqi Wang, Reuben Tan, Pengyue Zhu, Jianwei Yang, Zhengyuan Yang, Lijuan Wang, Andrey Kolobov, Jianfeng Gao, and Boqing Gong. Site: towards spatial intelligence thorough evaluation. In 2025 IEEE/CVF International Conference on Computer Vision (ICCV), pages 9058–9069. IEEE, 2025. 2

[63] Xingrui Wang, Wufei Ma, Tiezheng Zhang, Celso M de Melo, Jieneng Chen, and Alan Yuille. Spatial457: A diagnostic benchmark for 6d spatial reasoning of large multimodal models. In 2025 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), pages 24669–24679. IEEE, 2025. 4, A.1, C.2

[64] Shenxi Wu, Yuhong Liu, Haosong Zhang, Tongjin Zou, Yanxun Zhang, Gaochang Chen, Dun Liang, Jiaqi Wang, Zhecan James Wang, Yuhang Zang, et al. Scidocbench: A workflow-centered benchmark and data pipeline for scientific document understanding. arXiv preprint arXiv:2609.05141, 2026. A.1

[65] xAI. Grok-1.5 vision preview. https://x.ai/news/grok-1.5v, 2024. 4, C.2

[66] Yicheng Xiao, Wenhu Zhang, Lin Song, Yukang Chen, Wenbo Li, Nan Jiang, Tianhe Ren, Haokun Lin, Wei Huang, Haoyang Huang, et al. Spatialedit: Benchmarking fine-grained image spatial editing. arXiv preprint arXiv:2604.04911, 2026. C.5

[67] Long Xing, Xiaoyi Dong, Yuhang Zang, Yuhang Cao, Jianze Liang, Qidong Huang, Jiaqi Wang, Feng Wu, and Dahua Lin. Caprl: Stimulating dense image caption capabilities via reinforcement learning. In International Conference on Learning Representations, volume 2026, pages 13066–13093, 2026. A.1

[68] Haotian Xu, Yue Hu, Zhengqiu Zhu, Chen Gao, Ziyou Wang, Junreng Rao, Wenhao Lu, Weishi Li, Quanjun Yin, and Yong Li. Citycube: Benchmarking cross-view spatial reasoning on vision-language models in urban environments. In Proceedings of the 64th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), pages 9190–9215, 2026. 1

[69] Jihan Yang, Shusheng Yang, Anjali W Gupta, Rilyn Han, Li Fei-Fei, and Saining Xie. Thinking in space: How multimodal large language models see, remember, and recall spaces. In 2025 IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR), pages 10632–10643. IEEE, 2025. 1

[70] Penghui Yang, Long Xing, Xiaoyi Dong, Yuhang Zang, Yuhang Cao, Yibin Wang, Yujie Zhou, Jiazi Bu, Jianze Liang, Qidong Huang, et al. Caprl++: Unified reinforcement learning with verifiable rewards for dense image and video captioning. arXiv preprint arXiv:2606.09393, 2026. 1, A.1

[71] Rui Yang, Ziyu Zhu, Yanwei Li, Jingjia Huang, Shen Yan, Siyuan Zhou, Zhe Liu, Xiangtai Li, Shuangye Li, Wenqian Wang, et al. Visual spatial tuning. In European Conference on Computer Vision, pages 192–211. Springer, 2026. 1, 2, 4, A.2, B.2.1, C.3

[72] Sihan Yang, Runsen Xu, Yiman Xie, Sizhe Yang, Mo Li, Jingli Lin, Chenming Zhu, Xiaochen Chen, Haodong Duan, Xiangyu Yue, et al. Mmsi-bench: A benchmark for multi-image spatial intelligence. In International Conference on Learning Representations, volume 2026, pages 157051–157088, 2026. 1, 2, 4, A.1, C.2

[73] Yuncong Yang, Jiageng Liu, Zheyuan Zhang, Siyuan Zhou, Reuben Tan, Jianwei Yang, Yilun Du, and Chuang Gan. Mindjourney: Test-time scaling with world models for spatial reasoning. Advances in Neural Information Processing Systems, 38:109855–109885, 2026. 1, 2, A.3

[74] Shoubin Yu, Yue Zhang, Zun Wang, Jaehong Yoon, Huaxiu Yao, Mingyu Ding, and Mohit Bansal. When and how much to imagine: Adaptive test-time scaling with world models for visual spatial reasoning. arXiv preprint arXiv:2602.08236, 2026. 1, 2, A.3

[75] Xiang Yue, Yuansheng Ni, Kai Zhang, Tianyu Zheng, Ruoqi Liu, Ge Zhang, Samuel Stevens, Dongfu Jiang, Weiming Ren, Yuxuan Sun, et al. MMMU: A massive multi-discipline multimodal understanding and reasoning benchmark for expert agi. In Proceedings of the IEEE/CVF conference on computer vision and pattern recognition, pages 9556–9567, 2024. 4, C.2

[76] Beichen Zhang, Yuhong Liu, Xiaoyi Dong, Yuhang Zang, Pan Zhang, Haodong Duan, Yuhang Cao, Dahua Lin, and Jiaqi Wang. Booststep: Boosting mathematical capability of large language models via improved single-step reasoning. arXiv preprint arXiv:2501.03226, 2025. A.1

[77] Jiahui Zhang, Yurui Chen, Yueming Xu, Ze Huang, Jilin Mei, Chunhui Chen, Yanpeng Zhou, Yu-Jie Yuan, Xinyue Cai, Guowei Huang, et al. From flatland to space: Teaching vision-language models to perceive and reason in 3d. Advances in Neural Information Processing Systems, 38, 2026. 2

[78] Wanyue Zhang, Wenxiang Wu, Wang Xu, Jiaxin Luo, Helu Zhi, Yibin Huang, Shuo Ren, Zitao Liu, and Jiajun Zhang. World2vlm: Distilling world model imagination into vlms for dynamic spatial reasoning. arXiv preprint arXiv:2604.26934, 2026. 1, 2, A.2

[79] Youcai Zhang, Xinyu Huang, Jinyu Ma, Zhaoyang Li, Zhaochuan Luo, Yanchun Xie, Yuzhuo Qin, Tong Luo, Yaqian Li, Shilong Liu, et al. Recognize anything: A strong image tagging model. In 2024 IEEE/CVF Conference on Computer Vision and Pattern Recognition Workshops (CVPRW), pages 1724–1732. IEEE, 2024. A.2

[80] Zaibin Zhang, Yuhan Wu, Lianjie Jia, Yifan Wang, Zhongbo Zhang, Yijiang Li, Binghao Ran, Fuxi Zhang, Zhuohan Sun, Zhenfei Yin, et al. Think3d: Thinking with space for spatial reasoning. arXiv preprint arXiv:2601.13029, 2026. 1, 2, A.3

[81] Duo Zheng, Yanyang Li, Liwei Wang, et al. Learning from videos for 3d world: Enhancing mllms with 3d vision geometry priors. Advances in neural information processing systems, 38:20560–20586, 2026. 2, A.1

[82] Jensen Zhou, Hang Gao, Vikram Voleti, Aaryaman Vasishta, Chun-Han Yao, Mark Boss, Philip Torr, Christian Rupprecht, and Varun Jampani. Stable virtual camera: Generative view synthesis with difusion models. In 2025 IEEE/CVF International Conference on Computer Vision (ICCV), pages 12405–12414. IEEE, 2025. 2, C.5, D.2

[83] Yang Zhou, Zixuan Huang, Sunzhu Li, Zhuo Yang, Chen Zhang, Shunian Chen, Caijun Yan, Jianyao Xu, Shunyu Liu, Weijie Fu, et al. Spatialcli: Learning to reason with spatial tools, then without them. arXiv preprint arXiv:2607.27703, 2026. 1, A.3

[84] Chenming Zhu, Jingli Lin, Yilin Long, Peizhou Cao, Tai Wang, Jiangmiao Pang, and Xihui Liu. Thinking with imagination: Agentic visual spatial reasoning with world simulators. arXiv preprint arXiv:2606.06476, 2026. 1, 2, A.3

## A. More Related Work

## A.1. Proactive Spatial Intelligence

LVLMs have exhibited outstanding performance on various tasks, such as image and video captioning [67, 70], math reasoning [17, 76], agent harness [21, 56], and document understanding [24, 64]. In spatial reasoning, with the progress of LVLMs, recent benchmarks are moving beyond static spatial recognition and understanding towards more demanding spatial reasoning tasks where LVLMs still perform significantly worse than ordinary human beings. For example, Spatial457 [63] progressively evaluates spatial understanding from object recognition and 2D localization to complex 6D reasoning, revealing a pronounced performance degradation as geometric complexity increases. MindCube [60] studies whether LVLMs can construct a coherent spatial mental model from only a few observations, which evaluates cognitive mapping, perspective taking, and mental simulation of hypothetical movements. Similarly, MMSI-Bench [72] targets multi-view spatial intelligence and identifies scene reconstruction, cross-view correspondence, and perspective-transformation reasoning as major failure modes of current LVLMs. These benchmarks suggest that successful spatial reasoning often requires more than extracting relations from the observations already provided: the model must reason about how the scene would be perceived under alternative unobserved viewpoints.

The recent ESI-Bench [29] delves deeper into the perception-action loop and treat it as a higher level of spatial intelligence. An agent can selectively employ locomotion, and manipulation to uncover information that cannot be resolved from the initial observation alone. By reasoning under the active multiple setting, where the models can proactively execute movements and interact with the simulation environments, the performance significantly outperforms that under the passive single setting. However, the accuracy of the active multiple setting lags far behind the GT passive setting, where the best ground-truth passive trajectories are provided. These results suggest that current LVLMs lack intrinsic proactive exploration capability when solving spatial problems, unless they are explicitly prompted to do so. And their trajectory-planning capability is still far from saturated.

To internalize the thinking pattern of ‘perception-active loop’ into LVLMs, Geothinker [36] proposes active geometry integration, in which semantic representations selectively query task-relevant geometric features. However, such geometry-enhanced approaches [36, 81] introduce an additional pretrained feature branch and architecture-specific fusion modules into the LVLMs, and require alignment between geometric and semantic representations, potentially limiting their adaptability across heterogeneous VLM architectures. Unlike their notion of ‘active’ at the feature-retrieval level, our ASM operates at the viewpoint and observation level. It reasons about where useful evidence should be acquired and what should be observed from the corresponding hypothetical viewpoint. And we focus on data-centric pipeline where such capability can be internalized through of-the-shelf post-training strategies without introducing geometry module or making architectural modification.

## A.2. Spatial data synthesis

A wide spectrum of previous work lay emphasis on large-scale high-quality data curation pipeline to improve spatial reasoning in LVLMs. A straightforward approach is to construct such supervision via human annotation [8], but it is hardly afordable to scale. Some other pipelines resort to proprietary/open-source large-language models [15, 52]. However, current models’ performance on relatively challenging spatial reasoning tasks, especially in multi-view or video modalities, remains far from satisfactory and may introduce abundant errors to curated QA pairs.

To formulate scalable spatial supervision with high accuracy and quality, various synthesis frameworks emerge with two predominate characteristics: leveraging public scene datasets with explicit geometric annotations, and extensively employing visual expert models to recover otherwise unavailable 3D information from ordinary images. i) Numerous approaches (e.g., SpatialRGPT, OpenSpatial) leverage open-source scene datasets (e.g., ScanNet) with diverse annotated scenes as their foundational data [37, 41, 47]. These datasets provide 3D annotations, such as object classification, semantic voxel labels, and 3D segmentations, which can be easily augmented to spatial QA tasks. ii) A complementary direction further adds necessary annotations for constructing spatial supervision through specialized visual models [11, 15, 20]. These models have grown relatively mature in the traditional field of computer vision and perform well in tasks such as segmentation [42], metric depth estimation [30], object detection [79], and pose estimation [61], thereby lifting 2D imagery to

3D context.

While these pipelines greatly expand the scale of spatial supervision, the resulting data are largely constrained by the expert models or the information already encoded in the source public datasets. Recent works therefore explore richer sources of supervision. For instance, VST [71] broadens the coverage of spatial supervision across single-image, multi-image, and video scenarios by leveraging a 3D scene simulator to generate novel observations. Spatial-SSRL [44] bypasses explicit spatial annotation by constructing five self-supervised pretext tasks which provides naturally verifiable rewards for spatial reinforcement learning. Holi-Spatial [26] turns raw video streams into geometrically optimized 3D Gaussian Splatting (3DGS) scenes and automatically derives multi-level supervision (e.g., 3D grounding instances, QA pairs). SenseNova-SI [9] scales spatial intelligence to a new level, curating millions of spatial training samples by incorporating and filtering QA pairs from an incredibly large variety of datasets.

Despite the increasing scale and diversity of these datasets, most of them only provide answer-only supervisory signals. Specifically, for those relatively challenging tasks (e.g., perspective-taking allocentric transformation, mental reconstruction), the answers remain centered on what spatial relation or camera motion should be predicted, rather than explicitly demonstrating how a model can approach the correct answer through proactively acquiring missing evidence during reasoning.

VST-R [71] constitutes an important step towards reasoning supervision by providing 135K curated sample with explicit step-by-step reasoning traces. However, its synthesis process requires ground-truth 3D bounding boxes in the original scene data to form intermediate Bird’s-Eye View (BEV) images. Moreover, its reasoning supervision primarily teaches the model to derive spatial conclusions from the provided visual evidence, rather than to execute the perception-action loop of proactive exploration. It is not yet publicly released as well. In contrast, SpatialASM grounds an explicit question-conditioned reasoning process of when additional viewpoint reasoning is needed, where the model should mentally observe, and what evidence should be expected from that viewpoint, which is more demanding and human-like. Importantly, SpatialASM can synthesize ASM reasoning trajectories for existing spatial QA data without requiring any prerequisite ground-truth 3D annotations.

World2VLM [78] is particularly relevant to our work in that it also employs a generative world model in its data engine and distills its spatial imagination into the LVLMs. World2VLM synthesizes future views based on given parameterized camera trajectories to teach LVLMs to associate multi-view observations with egocentric motion transitions. Though efective in enhancing view-imagination capabilities, World2VLM fails to teach how to initialize a thinking process with imaginary world-model-like motions when encountering spatial problems, nor does it provide any reasoning content in its supervision. Our objective is diferent: SpatialASM starts from the spatial question itself and teaches the model to determine which viewpoint would provide useful missing evidence, rather than starting from a predefined egocentric motion trajectory and learning the corresponding visual outcome. The generated views serve only as temporary scafolding for synthesizing SpatialASM-128k, whose final supervision contains long-CoT traces which explicitly couples question-conditioned view planning, viewpoint-conditioned imagination, and reasoning with the imagined evidence.

## A.3. Tool-Assisted Spatial Reasoning

Recent studies increasingly augment LVLMs with paradigms of external harness to improve spatial intelligence. The tools can be primarily categorized into three groups: specialized visual tools, 3D reconstruction tools, and generative world models.

Specialized visual tools are computer-vision models which provide more reliable low-level or metric spatial evidence, such as pose/depth estimation and segmentation. Multiple studies demonstrate that the accurate specialist perception substantially improves spatial reasoning when LVLMs are specifically optimized for determining which tools to invoke and how to integrate their outputs [16, 19, 83]. Pretrained 3D reconstruction models recover point clouds and camera poses from scene images [32, 39] so that LVLMs can actively manipulate the reconstructed scene and inspect spatial configurations from alternative viewpoints [80]. A third direction employs generative world models to synthesize observations that are not present in the original visual inputs [73, 74, 84]. These methods primarily enhance spatial reasoning by augmenting the external evidence, which introduce additional inference overhead due to external tool execution and LVLM-tool interaction. Moreover, specialized tool-use training on LVLMs may introduce an additional trade-of between tool proficiency and the original general capabilities [14].

SpatialASM targets a fundamentally diferent yet complementary dimension from the above tool-assisted approaches: strengthening the intrinsic spatial reasoning process itself. While tool-assisted methods improve the availability and precision of external spatial evidence, SpatialASM improves the model’s ability to reason about what spatial evidence is needed and how it should be used. The two paradigms can thus be naturally combined, improving spatial intelligence along complementary axes of intrinsic reasoning capability and external evidence accessibility.

## B. Dataset Details

## B.1. SpatialASM-edit140k

## B.1.1. Statistics

SpatialASM-edit140k consists of 139,730 samples, all automatically constructed from the large-scale public scene datasets DL3DV [40]. As illustrated in Sec. 3.1, each sample has the form of $( \mathcal { T } , v , I _ { \mathrm { g t } } )$ , containing the input images ℐ, the allocentric viewpoint $v ,$ and expected ground-truth novel-view observation. To enable � to generate novel views from multi-image inputs, we set the length of input images |ℐ| to diferent values, and the number of samples of each |ℐ| is provided in Tab. 5.

Table 5: The samples numbers of each input-image length.
<table><tr><td>Input Length | |Z| = 1 |Z| = 2 |Z| = 3</td></tr><tr><td></td></tr><tr><td>|I| = 4 Total 35,02634,83534,84635,023 |139,730</td></tr></table>

## B.1.2. Construction Method

Given a 3D scene video in $D L 3 D V ,$ we first select a few observations as $\mathcal { T } = \{ I _ { 1 } , . . . , I _ { n } \}$ , and another frame as $I _ { \mathrm { g t } }$ . Critically, $I _ { \mathrm { g t } }$ is excluded from $\mathcal { T }$ and the frame indices of any two images in ℐ difer by at least 30, ensuring a noticeable viewpoint diference between them.

Since the image-editing model cannot directly accept the viewpoint query $v = ( i , x , y , d )$ as its input, so we separate and integrate it into the image and prompt inputs. In practice, we lay a visual marker on the pixel (�, �) of the anchor image $I _ { i } ,$ and we transform � into a textual prompt. Here we will explain how we derive $( x , y )$ from the metadata provided in ��3��.

Assume that $I _ { 1 }$ is the anchor image we select, then we need to compute the viewpoint of $I _ { \mathrm { g t } }$ to derive (�, �) in the query based on the coordinate system of $I _ { 1 }$ . We can easily collect their corresponding camerato-world transformation matrices $T _ { 1 } , T _ { \mathrm { g t } } \in S E ( 3 )$ from the original dataset. ��(3) denotes the set of 3D rigid transformations, each represented by a 4 × 4 homogeneous matrix that combines a 3D rotation and a translation. The transformation from camera-gt to camera 1 is:

$$
T _ { g t  1 } = T _ { 1 } ^ { - 1 } T _ { g t } = [ \begin{array} { c c } { R _ { g t  1 } } & { \mathbf { t } _ { g t  1 } } \\ { \mathbf { 0 } ^ { \top } } & { 1 } \end{array} ] ,\tag{11}
$$

where $R _ { g t \to 1 } \in \mathbb { R } ^ { 3 \times 3 }$ is the relative rotation of camera-gt with respect to camera 1, and $\mathbf { t } _ { g t  1 } = [ t _ { x } , t _ { y } , t _ { z } ] ^ { T }$ is the position of camera-gt in the coordinate system of camera 1. This position is projected onto $I _ { 1 }$ as:

$$
x = f _ { x } \frac { t _ { x } } { t _ { z } } + c _ { x } , \qquad y = f _ { y } \frac { t _ { y } } { t _ { z } } + c _ { y } ,\tag{12}
$$

where $( f _ { x } , f _ { y } )$ are the focal lengths and $( c _ { x } , c _ { y } )$ is the principal point, also provided in ��3�� originally. A valid projection requires $t _ { z } > 0$ and the resulting pixel $( x , y )$ to lie inside the image boundary. Finally, we derive the allocentric viewpoint direction � based on the relative rotation around the �-axis, which can be computed from $R _ { g t  1 }$ and expressed as a pitch angle in degrees. And we assign � to the corresponding direction in {forward, left, back, right}.

## B.1.3. Training samples

We provide two examples with single and multiple image inputs in Fig. 5. As in some training samples, when the queried viewpoint reveals regions outside the views of the original visual inputs, we allow and encourage �

![](images/229128e974e588e2777e033ccade3b005e90f5cc1bb11c57f77edd49638f3de7.jpg)  
Figure 5: Examples in SpatialASM-edit140k. (Case 1, 2: |ℐ| = 1, Case 3: |ℐ| = 2)

to imagine objects not present in the input images, provided that these imagined objects are plausible in the real world (e.g., the patio umbrella and the window on the left wall in $I _ { \mathrm { g t } }$ of Case 2).

## B.2. SpatialASM-128k

In Sec. 3.2, we explain how to synthesize the ASM reasoning trajectories based on triplets (�, ℐ, �) from existing answer-only spatial datasets. We construct 128k ASM trajectory samples, and use a subset of 66k samples for our SFT training. Our approach provides a scalable and convenient pathway to synthesizing high-quality spatial CoTs as it does not require any other metadata (e.g., BEV image, bounding box). It helps address the scarcity of reasoning content in prior large-scale spatial supervision and elicit active task-conditioned planning and imagination capabilities in LVLMs from scratch.

## B.2.1. Statistics

All original spatial-QAs are sourced from Holi-Spatial [26], VST-500K [71], and the training set of Mindcube [60]. We give the number of samples from each original data source in SpatialASM-128k in Tab. 6. Importantly, to avoid distribution overlap between training data and our final evaluation on benchmarks, SpatialASM-66k does not contain any sample synthesized from Mindtube-train.

Table 6: The number of samples from each data source in SpatialASM-128k
<table><tr><td>Data Source</td><td>Holi-Spatial</td><td>VST-500k Mindcube-train</td><td>Total</td></tr><tr><td>Sample No.</td><td>91,648 34,741</td><td>1,965</td><td>128,354</td></tr></table>

## B.2.2. Synthesis Protocols

Some detailed implementations for ASM trajectory synthesis are given in this section. In Sec. 3.2, we have mentioned that we use two pretrained LVLMs, � and $M _ { c a p ; }$ , to generate certain parts in our CoT trajectories. � is prompted to produce planning trace $r _ { p l a n }$ and the final reasoning trace $r _ { r e a s o n . }$ , and $M _ { c a p }$ is used to transform $\{ \bar { I _ { \mathrm { e d i t } , k } } \bar  \} _ { k = 1 } ^ { K }$ into $\{ C _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K }$ . In practice, we employ Qwen3.6-35BA3B [50] as � and Qwen3.6-27B [49] as $M _ { c a p } .$ For filtering, � is used to discard samples whose $\{ I _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K }$ have high GeoP values. Since we also filter for reasoning correctness in SFT (Equation 7), we adopt a relatively lenient quality threshold for $\{ I _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K }$ . We set $\theta = 2 . 5$ during our filtering process.

Here we provide the system prompts of planning, reasoning, and captioning. The planning prompt is for producing $( r _ { \mathrm { p l a n } } \{ v _ { k } \} _ { k = 1 } ^ { K } )$ in Equation $4 ;$ the reasoning prompt is for producing $\left( r _ { r e a s o n } , \hat { a } \right)$ in Equation $6 ;$ the captioning prompt is for producing $\{ C _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K }$ in Equation 8.

![](images/9ff540b958199d05a93d04e75660f2e5252a25374a8f17675bd861c70eb7311e.jpg)  
The problem requires determining the direction of the rug relative to a person standing at the laptop and facing the towel. Based on the images, the laptop is on the bed (visible in Image $\mathsf { A l } ,$ and the towel (likely the white cloth on the table or pile of clothes) is in the foreground (visible in Image B). Image A shows the room looking towards the door, meaning the foreground (where the towel is) is behind the camera. Therefore, to face the towel from the laptop, the person would need to turn around to face the direction opposite to the door. This corresponds to a “Backwards” rotation relative to the viewpoint of Image A. I will generate a view from the laptop‘s position facing backwards to clarify the spatial layout of the rug, bed, and towel. Therefore, I need to imagine the views I will see in novel perspectives. Assume that all input images have the size (100, 100). Assume I add a marker ’1‘ at the position (38, 65) in Image A, I will get <image3>. At the marker '1' in <image3>, if I turn backwards. I will see <image4>:

In the immediate foreground on the left, a large blue unit sits next to a black frame. To its right, occupying the right foreground and extending into the mid-ground, is a bed covered with a light blue sheet and a crumpled beige blanket. To its left, a grey shag rug runs down the center of the floor, separating the blue unit on the left from the bed on the right. In the background on the left wall, white shelves are mounted high up, holding various small items. Below the shelves, further back in the room, is an orange armchair with a dark jacket draped over it. In the far background center, a desk area is cluttered with papers and objects, situated beneath three framed pictures hanging on the wall. A laptop is partially visible in the extreme bottom right corner.

![](images/917bea6982d916c32f176dd6fec7de22f699d335246cd93b9611d72f4517e719.jpg)

Now I will rethink the problems based on input images and the imagined images. Based on the provided images and the spatial reasoning task:\n1. The problem states: “You are positioned at laptop and face towel.” This means we are to imagine ourselves standing at the location of the laptop (on the bed, as seen in image A) and facing the towel (which is draped over a dark object, likely a chair or bag, near the foot of the bed in image B).\n2. From this vantage point (laptop position, facing towel), we need to determine the direction of the rug.\n3. Looking at image A, the rug is located on the floor, running from the foot of the bed towards the door. Since we are standing at the head of the bed (laptop position) and facing towards the foot of the bed (towel position), the rug is directly in front of us.\n4. Image 4 provides an alternative perspective. It shows the bed (with the laptop visible on the right side of the frame) and the rug to its left, running along the floor towards the back of the room. This confirms that from the perspective of the bed's head, the rug is in front-left.

Figure 6: Example in SpatialASM-128k. Note that $I _ { \mathrm { e d i t } , }$ <sub>1</sub> does not exist in the ASM trajectory.

• Planning prompt: I gave you several images and a spatial reasoning question. You can now use an image-editing model to generate images of the scene from new perspectives based on the input images. You do not need to answer the spatial reasoning question I gave you, but you need to reason based on the question and give commands to the image-editing model to provide more useful information for answering this question.

For each command, you need to first select a source image from the input images. Then you need to select an area on the image and assign a facing direction, so that the image-editing model will generate the scene viewed from this area and specific facing direction.

Provide your answer in the following format: a list containing Dict(s):

[{‘source\_img’: ‘<e.g. 1>’, ‘pixel\_x’: ’<e.g. 22>’, ‘pixel $\underline { { \boldsymbol { y } } } ^ { \prime } \colon ^ { \cdot } < \mathbf { e } . \mathbf { g } . \ 6 3 > ^ { \prime }$ , ’Direction’: $\mathrm { ^ { \circ } e . g . < R i g h t > ^ { \prime } } \} , \ldots ]$ ‘source\_img’ is the anchor image number (the first image is numbered 1, and so on). ‘pixel\_x’ and ’pixel\_y’ represent the assigned area, and we assume the width and height of the images are both 100 for simplicity. Direction is chosen from $\mathrm { \bar { L } e f t { \ell } }$ , ‘Backwards’, ‘Forward’ and ‘Right’.

You can output an empty list if you believe there is no need for providing a new perspective.

First think and then give the final answer. The final answer should contain a short summary of your reasoning process (no more than 200 words), which only focus on how to derive the command without giving the answer of the original question, and the final list.

The summary should be put within <summary> and </summary>, and then output the list within <answer> and </answer>.

• Reasoning prompt: First think and then give the final answer. Put the option letter within <answer> and </answer>.

• Captioning prompt: Describe the 3D location of the objects in the image. The location include left, right, top, bottom, background, foreground, and so on. You can describe the location of each object separately and the relative location between objects as well (e.g. Object A is to the right of Object B). The answer should be no more than 200 words.

## B.2.3. Data Sample

We present a sample of SpatialASM-128k in Fig. 6. The sample contains the original problem input $( \boldsymbol { \mathcal { T } } , \boldsymbol { q } )$ and the ASM trajectory $( r _ { \mathrm { p l a n } } , \{ C _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K } , r _ { \mathrm { r e a s o n } } , \hat { a } )$ . In this case, the number of auxiliary imagined view is $K = 1$ Critically, the generated novel views only act as scafolding for conversion into $\{ C _ { \mathrm { e d i t } , k } \} _ { k = 1 } ^ { K }$ , and do not really exist in the final dataset.

## B.3. SpatialASM-RL-58k

We construct the SpatialASM-RL-58k automatically by employing SpatialASM-NVG without any LVLM/human annotation. Only the original 2D images come from Holi-Spatial [26] and DL3DV [40]. SpatialASM-RL-58k consists of 58,346 training samples, with 28,594 from Holi-Spatial and 29,752 from DL3DV.

In Sec. 3.3 we introduce the answer selection strategy to reduce potential ambiguity in the two multi-choice tasks and ensure the quality of the rendered images. A pre-defined parameter $\theta _ { R L }$ is applied in this strategy. During the construction process, we find that generated images generally have higher GeoP values when the queried allocentric viewpoint is set to backwards than when it is set to other directions. This is because the backward direction is more likely to reveal regions not visible in the input images, requiring the NVG to synthesize more unobserved content. Therefore, even when the generated images exhibit similar levels of geometric consistency, those generated for the backward direction tend to receive higher GeoP. After manually inspecting a large number of generated images and their corresponding GeoP values, we set to $\theta _ { R L }$ to 1.2 for images whose corresponding target direction is backwards, and 0.6 for all other directions.

In Fig. 7, we give example VQAs of the two tasks we construct for RL: generated-view localization and pose-conditioned view selection. The formulation of both tasks are illustrated in Sec. 3.3.

## Generated-view localization

<image2>  
![](images/44be6236229bc3074af3f348e4c78b6375aebe16bf2e6fbcaa95769402b9f783.jpg)  
<image1>

![](images/9aa8c5d7dfe4ecbe2250cf88da4fdfa904ad9d193d8ba584aae9173d71a4bf5f.jpg)

![](images/be458e1ac0b7ddc7a5e3b989654c8c26fc3baf2d5e161fb55930381c34e22ad1.jpg)  
<image3>  
Q: 1 images is provided <image1>, depicting a real-world 3D scene. I select four places in <image1>, as shown in <image2>. The four locations are marked '1', '2', '3', and '4' respectively. From which marked location can I see Figure <image3>? A. 1 B. 2 C. 3 D. 4 GT: A

## Pose-conditioned view selection

![](images/9d3477ed6f8f2c8153150c393eeb036a816ca8bcc6b57b7bb45a3be5bfa2e861.jpg)  
<image1>

![](images/5239dc7fd8cb1339cdabcababaf8e6b3eac26cbcd4aec1f87fdc98b4709ee53f.jpg)  
<image2>: GeoP=0.04

![](images/2497ca590cce6b0c76386a7aed6dda0d521924720aba8c54b8cdbf4ff3332a61.jpg)  
<image3>: GeoP=0.97

Q: 1 image is provided <image1>, depicting a real-world 3D scene. From the viewpoint of <image1>, if I am positioned at the head of the bed, above the mattress, near the leftmost pillow. And I turn right. Which of the following images is the most similar as what I see?   
A. <image2> B. <image3> C. <image4> D. <image5>   
GT: A

![](images/f3f745a1425d51bc8773c8fd3326ca1afe881405a73392224cd4db9ec6c4212e.jpg)  
<image3>: GeoP=0.63

![](images/eeaea91788c65652e654226a10cf326e56b1435da1be125a6ecfd7804b7233d1.jpg)  
<image5>: GeoP=0.05

Figure 7: Examples of view-imagination RL tasks.

## C. Experiment Details

## C.1. Training Details

SpatialASM-NVG training. We finetune FLUX.2-klein-9B [6], an image-editing model for real-time textconditioned image generation, on SpatialASM-edit140k to derive our SpatialASM-NVG. Drawing on the training strategy in DifThinker [28], we use mean squared error (MSE) as our training objective and train for 3 epochs. We only finetune its Difusion Transformer (DiT) with Low-Rank Adaptation (LoRA) and we set LoRA rank to 512. For GeoP calculation, we use Mapanything [32] as the feed-forward 3D reconstruction model � to reconstruct the 3D point clouds.

View-imagination RL. During GRPO, we apply KL regularization with weight 1e-2. And we rollout 5 responses in each group with the temperature set to 1.0. Our reward is composed of two items: accuracy reward and format reward, with the weight of 0.9 and 0.1. Accuracy reward is a binary reward which is assigned to 1 only when the final option is correct. Format reward is also binary which requires the LVLMs to reason first and put the option letter within <answer> and </answer>.

Table 7: Performance comparison on three general-VQA benchmarks.
<table><tr><td colspan="4">Models MMMUvAL MMStar RealWorldQA|Average</td></tr><tr><td colspan="4">Representative Spatial Models</td></tr><tr><td>SpaceR-7B 51.8 VST-RL-7B 51.2 HoliSpatial-8B 56.0 SenseNova-8B</td><td>61.4 63.7 65.4 64.5</td><td>65.0 71.4 68.0 71.6</td><td>59.4 62.1 63.1 63.7</td></tr><tr><td colspan="4">55.1</td></tr><tr><td>Baselines &amp; Our Model (Qwen3-VL-4B) Qwen3-VL-4B 54.7</td><td>65.1 64.0</td><td>71.3 71.6</td><td>63.7 63.1</td></tr><tr><td colspan="4">SpatialASM-4B 53.8 Baselines &amp; Our Model (Qwen3-VL-8B)</td></tr><tr><td>Qwen3-VL-8B 54.1 SpatialASM-8B 56.1</td><td>66.0 67.1</td><td>73.2 71.8</td><td>64.4 65.0</td></tr><tr><td colspan="4">Baselines &amp; Our Model (Qwen3-VL-30BA3B)</td></tr><tr><td>Qwen3-VL-30BA3B SpatialASM-30BA3B</td><td>62.7 70.7 62.1 68.6</td><td>74.8 76.1</td><td>69.4 68.9</td></tr><tr><td colspan="4">Baselines &amp; Our Model (Qwen3.5-9B)</td></tr><tr><td>Qwen3.5-9B</td><td>73.3 78.6</td><td>82.4</td><td>78.1</td></tr><tr><td>SpatialASM-9B</td><td>75.3 78.5</td><td>81.7</td><td>78.5</td></tr></table>

Prompt. For both SFT and RL, we append a system prompt to the end of the original question, guiding LVLMs to output first reasoning content and then the final answer. The system prompt is: First think and then give the final answer. Put the option letter within <answer> and </answer>.

## C.2. Benchmark Overview

We evaluate the LVLMs on seven spatial reasoning (SR) benchmarks. For Spatial457 [63], we only select its most challenging 6d subtask with 1837 questions. ESI-Bench [29], MindCube [60], MMSI-Bench [72], and Spatial457-6d are the most dificult among the seven benchmarks, requiring LVLMs to go beyond directly reading spatial relations from the given observations and instead integrate incomplete evidence, mentally construct a 3D scene representation, and reason about unobserved or hypothetical viewpoints. ViewSpatial-Bench [35] represents an intermediate level of dificulty, with camera-perspective questions which can often be answered directly from the observed coordinate frame and person-perspective questions which require some basic perspective-taking capabilities. Finally, 3DSRBench [45] and EmbSpatial-Bench [22] are relatively perception-oriented, covering spatial properties such as height, orientation, location, and egocentric spatial relations. Taken together, these benchmarks provide a comprehensive evaluation of spatial reasoning across complementary dimensions, ranging from fundamental spatial perception and egocentric relation recognition to perspective transformation, multi-view scene reconstruction, and imagination-driven reasoning about unobserved spaces.

We additionally evaluate on three general-VQA benchmarks to examine whether SpatialASM preserves the broad visual capabilities of the base LVLMs. MMMU [75] evaluates expert-level multimodal understanding and reasoning across 6 major disciplines. MMStar [12] evaluates 18 detailed ability axes in 6 major categories, spanning fine-grained and coarse visual perception, instance-level reasoning, and science and technology, with an emphasis on genuine visual dependency. RealWorldQA [65] focuses on basic visual understanding in real-world scenes, including counting, object size comparison, relative direction, optical character recognition (OCR), etc. Together, these benchmarks measure whether the spatial improvements introduced by SpatialASM come at the expense of general visual understanding.

## C.3. Evaluation Details

Evaluation prompt. In Tab. 1, we evaluate the LVLMs on two major task categories: spatial reasoning and general-VQA (Gnr.-VQA). For spatial reasoning, we apply the same system prompt as training (in Appendix C.1) to let LVLMs reason before giving the answer. For general-VQA benchmarks, we apply another system prompt to enable direct responses of final answers from LVLMs: Directly output your answer without any reasoning. The correct option letter is:.

Representative spatial models. Four popular spatial LVLMs of previous studies are also evaluated on the benchmarks. SpaceR-7B [47] and VST-RL-7B [71] are trained upon Qwen2.5-VL-7B [58]. Their oficial repository provides system prompts. For the SR evaluation, we follow them exactly to guide the models to produce structured reasoning and final answers. Holi-Spatial [26] and SenseNova-SI-1.1-Qwen3-VL-8B [9] are trained upon Qwen3-VL-8B [3], similar to out SpatialASM-8B. Since the two models do not provide oficial system prompt, we follow our system prompt during evaluation.

## C.4. General VQA Results

In Tab. 1, we only report the average score on the three general-VQA benchmarks. We provide the detailed accuracy of all LVLMs on each benchmark in Tab. 7.

## C.5. Ablation Details

In Fig. 4 and Tab. 3, we employ the reasoning paradigm of external ASM: LVLMs execute question-conditioned view-planning, then invoke the NVG to synthesize corresponding observations, and finally reason based on the previous auxiliary information. Three diferent LVLMs are evaluated in Fig. 4: Qwen3-VL-8B [3], Qwen3.5-35BA3B [50], and Kimi-k2.5 [57]. And four diferent rendering models are compared in Tab. 3: GPT-Image-2 [46], SpatialEdit [66], SVC [82], and our SpatialASM-NVG.

In Tab. 3, GeoP is used for comparing the synthesis quality of diferent NVGs. For each setting, we report the median GeoP of the images generated by the NVG rather than the average. This is because most generated images have low GeoP values (below 1), while a small number of poor-quality images have much higher values (above 10). These outliers can disproportionately raise the average GeoP, making it less representative of the geometric consistency of a typical novel-view observation generated by a certain NVG/World-Model.

Tab. 4 ablates training and data-filtering strategies described in Sec. 3.2 and Sec. 3.3. For SFT, w/ofilter means omitting the trajectory filtering step in Sec. 3.2 and using all synthesized ASM trajectories for training. For RL, w/o filter means omitting the GeoP-based criterion in the answer selection step of Sec. 3.3: no sample is discarded because its candidate views exceed the GeoP threshold, and the ground-truth candidates are selected from the full candidate set. Importantly, the final SFT+RL setting in Tab. 4 as well as all our models shown in Tab. 1 experience both training stages and all training data experience the filtering strategy.

## D. More Cases

## D.1. SpatialASM-NVG Rendering

We obtain SpatialASM-NVG after training FLUX.2-klein-9B [6] on SpatialASM-edit140k. Here we provide three output examples rendered by SpatialASM-NVG in Fig. 8.

Case 1: Turning right at the target location reveals an area that was originally outside the input image’s field of view (beyond the sofa on the far right). SpatialASM-NVG successfully reconstructs the missing content in this region while retaining the objects in the original input (e.g., blue chair, white box).

Case 2: The generated view is not only geometrically consistent with the input but also faithfully reproduces the lighting on the brown panel on the right and the shadows cast on the floor.

Case 3: This case is indeed rather tricky, as the relative camera poses of the two input images are not immediately apparent. Nevertheless, our model correctly infers their spatial relationship and seamlessly integrates the tactile paving from Image 1 (invisible in Image 2) with the white panel on the inner left side of the bus stop from Image 2 (not visible in Image 1) into a single rendered image.

## D.2. Results of Novel-view Generators

To showcase qualitative examples of the rendering results of diferent novel-view generators, we select some generated observations from the experiment in Tab. 3 with Kimi-k2.5 as the LVLM for planning and reasoning.

![](images/94b9a10d327914a614926b3cb46abc8f6a95170e98a059d5c27b6c34a086e376.jpg)  
Figure 8: Examples of novel-view observations rendered by SpatialASM-NVG.

The generated images are shown in Fig. 9 and Fig. 10. Evidently, the formulation of target viewpoints varies greatly. Prior approaches(e.g., SVC [82]) use ego-centric motions from the camera position of an anchor image, whereas SpatialASM-NVG defines the target location directly by marking a pixel on an image. Note that in both figures we assume that the input images all have the size (100, 100).

Case 1 (Fig. 9): The query target viewpoints of the two novel-view generators are approximately the same. SpatialASM-NVG has a much lower GeoP value because the red fire-fighting unit and the black disinfectant unit in the output image more closely resemble their counterparts in the input images.

Case 2 (Fig. 10): Although SVC’s output image is more geometrically consistent with the visual inputs (lower GeoP), its egocentric formulation results in relatively small camera motions and its output remains highly similar to the anchor image, providing little additional information for further LVLM reasoning. In contrast, our allocentric formulation induces larger viewpoint changes and provides more informative output observations, which could also possibly help the LVLM better understand the relative camera poses across input images and the spatial layout of the 3D scene.

## D.3. Examples of LVLM Internalization

We have trained several LVLMs to internalize ASM capabilities and improved their overall spatial reasoning performance (Tab. 1). Here, we provide four qualitative comparisons between SpatialASM-8B and its baseline, Qwen3-VL-8B. These examples show that SpatialASM-8B can adaptively decide whether to employ the ASM reasoning pattern in its output according to the task type and dificulty, rather than simply overfitting to the ASM reasoning trajectories. In fact, we find that the model does not explicitly follow the ASM pattern in most cases, yet its overall spatial reasoning accuracy still improves substantially (Tab 1). This suggests that our training internalizes generalizable spatial reasoning capabilities instead of imposing a rigid reasoning template.

Fig. 11 and Fig. 12 show two problems targeting the viewpoint of person and camera respectively. Though

Target Viewpoint Anchor Image: Image 3

Input Images

![](images/b3bc642905aa84fb6ca0815783ebb1da54c1eaaf3f821e63439fac7c68dae5f8.jpg)

![](images/a20fa43617881cbc216ba11e6f5256741159bdaa83e7ed12d8adfc64d1959a0e.jpg)

![](images/543e5726b81a89e326e77b2a34f7339dd5f5be618b04923bbbc36555aa1593dc.jpg)

## SVC Output

Target Viewpoint Anchor Image: Image 3 Ego-centric Command: Move forward for 0.25m

![](images/ebae505f7733be469450a91fce88097a31206ec4bf697c4a96aefe2073d7b795.jpg)  
GeoP=0.2407  
SpatialASM-NVG Output  
Target Viewpoint  
Anchor Image: Image 1  
Target Location: (85, 60)  
Direction: Left

![](images/4a1d0c887a023b1e6c37e0048f6fc1245c47257315ed36a85d820e86cd736152.jpg)  
GeoP=0.0599  
Input Images

Figure 9: Case 1: Observations generated by diferent novel-view generators.  
![](images/6585aa3ba54fffc032e29009a3f27eb586631220bfe795e5f79d705d96a177f1.jpg)

![](images/9b2ca6c0a2c23a5f4f0995f7aea458feaf46abc01a1a5c05df91978cddf5145a.jpg)

![](images/2910b70fea8a890de304ac126dfe056d56012489135ce2451b03ad244539abdc.jpg)

## SVC Output

Move forward for 0.25m

![](images/3edbed8732a639c56faffee892a626c418dde3a1ae434ff2c95d21eb840073b6.jpg)  
GeoP=0.1266  
SpatialASM-NVG Output  
Target Location: (85, 50)  
Direction: Left

![](images/97940387d13852137bb0214f725e23637f2af08bd83db71ae7fca90c3ef01696.jpg)  
GeoP=0.1284

Figure 10: Case 2: Observations generated by diferent novel-view generators.

SpatialASM-8B does not produce the ASM reasoning pattern explicitly, it accurately interprets the 3D spatial configuration from diferent perspectives and correctly answers both questions. Fig. 13 and Fig. 14 are more challenging tasks where SpatialASM-8B generates CoT of active spatial planning and imagination to help reach the correct answer.

![](images/438aab46d57f162da2e7f200db66c905220c4fc54d52e61533687cff4c3d842e.jpg)  
Figure 11: Qualitative comparison of Qwen3-VL-8B and SpatialASM-8B.

![](images/ce287f6e3274b9e3183343940557de0ca05bf3180010013109fea44daf0438fb.jpg)  
Figure 12: Qualitative comparison of Qwen3-VL-8B and SpatialASM-8B.

![](images/ccaecd00778cdea3ac6ac551ee10873c51a66078355597efd477760b6dac6fdd.jpg)  
Figure 13: Qualitative comparison of Qwen3-VL-8B and SpatialASM-8B.

![](images/10b3578a236f14c58a74971472a65e2dd9f83a5e389524a913c91a5777922cda.jpg)  
Figure 14: Qualitative comparison of Qwen3-VL-8B and SpatialASM-8B.