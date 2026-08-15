![](images/c34127eff74a7d9a07b4c14cb1dcaa1359c4f129b22979d2e5f4717ac4f09a44.jpg)

# SVGThinker: Instruction-Aligned and Reasoning-Driven Text-to-SVG Generation

Hanqi Chen Shanghai Jiao Tong University SJTU Paris Elite Institute of Technology Shanghai, China cafinities@sjtu.edu.cn

Zhongyin Zhao Shanghai Jiao Tong University Shanghai, China zhao\_zhongyin@sjtu.edu.cn

Zhujin Liang PhiGent Robotics Beijing, China zhujin.liang@phigent.ai

Ye Chen Shanghai Jiao Tong University Shanghai, China chenye123@sjtu.edu.cn

Bingbing Ni<sup>∗</sup> Shanghai Jiao Tong University Shanghai, China nibingbing@sjtu.edu.cn

Figure 1: SVGThinker, our proposed text-to-SVG reasoning model, leverages Multimodal Large Language Model (MLLM) to construct sequential SVG textual annotations, creating both overall descriptions and step-by-step descriptions of visual changes in SVG primitives. By using this paired data, we enable the reasoning model to understand SVG primitives and their corresponding visual semantic information, thereby generating SVGs aligned with the instructions. Through modifying generation instructions, SVGThinker allows for precise and controllable editing of SVGs, showcasing capabilities that were previously unattainable by other models.

## Abstract

Scalable Vector Graphics (SVG) is a code structure used to represent visual information, and with the powerful capabilities of large language models, it holds significant research potential. Current text-to-SVG generation methods lack generalization capabilities and struggle with accurately adhering to input generation instructions. In this paper, we propose a novel approach for generating SVG using large language models, named SVGThinker, which in corporates a reasoning process to align the generation of SVG code with the visualization process, while supporting all SVG primitives. Through sequential rendering of SVG primitives, we first use a multimodal model to annotate the SVG, followed by sequential updates corresponding to the incremental additions of primitives. We then employ a supervised training framework based on Chain of-Thought reasoning, which enhances the model’s robustness and reduces the risk of errors or hallucinations. Through comparisons with state-of-the-art baseline models, our experiments show that our model generates more stable, high-quality, and editable SVG code. In contrast to image-based methods, our approach preserves the structural advantages of SVG and supports precise, hierarchical editing. We believe our work opens new directions for SVG genera tion, with potential applications in design, content creation, and automated SVG-based graphic generation.

## CCS Concepts

• Computing methodologies → Computer vision.

## Keywords

SVG Generation, Text-to-SVG, Chain-of-Thought Reasoning, Mul timodal Model

## ACM Reference Format:

Hanqi Chen, Zhongyin Zhao, Ye Chen, Zhujin Liang, and Bingbing Ni. 2025. SVGThinker: Instruction-Aligned and Reasoning-Driven Text-to-SVG Generation. In Proceedings of the 33rd ACM International Conference on Multimedia (MM ’25), October 27–31, 2025, Dublin, Ireland. ACM, New York, NY, USA, 9 pages. https://doi.org/10.1145/3746027.3755392

## 1 Introduction

Scalable Vector Graphics (SVG) is a powerful and widely used vector graphics format. It provides a flexible and scalable method for cre ating, editing, and distributing images through XML-based textual descriptions. Compared to raster images, SVG maintains clarity and sharpness at any resolution while occupying minimal storage space. These advantages make SVG highly applicable across various fields, particularly in design, art, creativity, and industrial applications. However, despite its widespread use, understanding its syntax or mastering its creation methods poses significant challenges for hu mans. The source code of SVG and the visual image representation and semantic information it encapsulates are complex, making it dificult for humans to comprehend. In recent years, generative models based on deep learning techniques [3] have partially ad dressed the dificulty of creating and using SVGs by facilitating the learning and understanding process. These methods can be categorized into two paradigms.

The first paradigm is optimization-based methods, which typically include image generation models [24] and diferentiable SVG renderers [15], such as VectorFusion [12], SVGDreamer [34], etc. This approach uses images as visual targets and optimizes the sim ilarity between the SVG and the original image. These methods typically represent images using paths, and often produce visual artifacts, which diminish some of the expressive advantages of SVGs, and generate redundant and chaotic primitives that cannot be easily edited or modified later. This approach leads to a loss of semantic understanding of the SVG code, creating a bottleneck for further optimization in generating high-quality SVGs.

The second paradigm is based on generative models [32], typically using image generation models [14] or autoregressive models to generate SVGs in a feedforward manner. Methods such as LayerTracer [25] use generated SVG-style images, which are then vectorized using tools like VTracer [7]. This approach also sufers from content redundancy, image flaws, and issues with unreadable or uneditable SVG source code. Another approach is to use autoregressive models based directly in the SVG source code space, like IconShop [32], or to use language models like StarVector [23], or GPT-4o [1]. These methods either limit the range of SVG primitives using their own SVG tokenizer, which weakens their expressive capabilities and fails to properly understand the language input information [32], or struggle to capture the semantic information contained in it due to the abstract nature of SVG source code representation [1], leading to challenges in instruction-aligned generalization and editing.

To address these challenges, we propose SVGThinker, a large language model-based text-to-SVG generation model, which supports all SVG primitives. Our approach is motivated by the inherent tree structure and visual-semantic information of SVG codes. Specifically, in contrast to general LLM-based pipelines which directly pair textual descriptions and SVG codes for training [33], we redesigned a novel textual annotation process based on parsing and reconstructing the tree structure of SVGs, ensuring a clear correspondence between the visual meaning and textual description corresponding to each SVG primitive. At the same time, we train a LLM integrated with a Chain-of-Thought (CoT) [30] reasoning process aligned with human cognitive understanding and the logical construction of SVG instructions. Through the proposed method, we make the generated SVG representations more concise and meet the high standards of human-made SVGs, while adhering to the way humans conceptualize SVGs.

Extensive comparisons with existing state-of-the-art models demonstrate that our method surpasses previous approaches by generating SVGs that are more aligned with textual descriptions, more readable, and more reasonable. Additionally, based on the input textual instructions, fine-grained editing is possible. Our experiments show that by accurately modifying parts of the input prompt, such as position, color, elements, and structure, the model can precisely adjust the generated SVG code and the corresponding visual output. This capability, derived from the model’s reasoning ability and the data annotation from prior work, is something that no previous method has been able to achieve.

## 2 Related Works

## 2.1 SVG Generation

Early SVG generation methods were primarily based on image vectorization techniques. After significant advancements in deep learning, SVG-VAE [18], based on variational autoencoders, began conditional generation of SVGs. Subsequently, methods like VectorFusion [12] and SVGDreamer [34] emerged, combining image generation models and vectorization techniques to achieve higherquality SVG generation. Additionally, many related SVG generation works [36] based on difusion models [10] have been proposed. As autoregressive generation models [1, 2] demonstrated strong generalization capabilities, IconShop [32] introduced an SVG tokenization approach that constrains the range and expression based on the structure of SVG instructions. Recent trends have further lever aged large language model-based methods, such as StarVector [23] and Chat2SVG [31]. These approaches exhibit higher potential and theoretically complete SVG support. However, existing methods have not explored the corresponding semantic information of SVG code, which limits their performance, motivating the development of SVGThinker.

## 2.2 Large Language Models

Large language models, such as GPT-4 [1], have revolutionized natural language processing and gradually expanded to other modalities. For example, through methods like LLaVA [17], image modality support has been integrated, further advancing large language models toward general intelligence. By increasing the model pa rameters and using transformer [29] architectures, language models have demonstrated scaling laws [13], and their high performance is becoming a foundational model [22] for other tasks, such as SVG generation. As model parameters have reached certain bot tlenecks, the focus of language models has gradually shifted to reasoning applications and more powerful task-solving capabili ties [35]. Through reinforcement learning, supervised learning, and new paradigms such as CoT [30], model performance has been further enhanced and aligns more closely with human thinking processes for corresponding tasks. The work presented in this paper also relies on the improved understanding and learning capabilities of models, enabling the model’s reasoning process to align with the SVG construction process, thereby demonstrating precise editing capabilities for the first time.

## 3 Methodology

In this section, we describe the entire process ofdataset creation and the corresponding model training workflow. First, we collect the data, followed by preprocessing the SVG data. We then annotate the SVG using a multimodal large language model, and subsequently train the large language model based on reasoning logic.

## 3.1 Data Preparation for Instruction Alignment

Data Collection and Curation. We collected a dataset on the Kag gle platform [5] consisting of 5,269 fine-grained categories, totaling more than 300,000 icon samples. From the SVGRepo platform [26], we gathered more than 100,000 vector graphic data points through classified retrieval, organizing them into 34 coarse-grained seman tic categories. The semantic matching accuracy of these data was manually calibrated to ensure comprehensive semantic coverage. Subsequently, we used the tools provided by SVGO to optimize all the SVGs and removed any samples with excessively long SVG.

SVG structure reconstruction for image generation. After com pleting the optimization step for the SVG files, we proceeded to generate intermediate-step images that are aligned with the instructions. To achieve this, we parsed the SVG files according to their hierarchical structure. The drawing process in SVG is exe cuted directly in the order of instructions. However, since SVG instructions are structured in an XML-like tree format, they are not processed in a simple sequential manner. In addition, many structures have attributes that afect the child nodes. To better align the SVG code with the rendered images, we developed a simple algorithm for reconstructing the SVG structure based on the visual changes observed during image rendering.

Through this approach, for each individual SVG file, we generate a series of images that guide the reconstruction process. The updates between each image are aligned with the corresponding instructions, helping the language model learn the relationships between SVG code and its visual output.

SVG annotation. We employ the LLaVA framework [17] for imageflow annotations and filtering using CLIP [21] and Perplexity. When annotating SVG-rendered images, we face several challenges. On one hand, the correspondence between SVG code and image elements is dificult to understand. On the other hand, the sequential rendering of SVG elements results in certain layering efects, which complicate matters for the language model. To address these challenges, we utilize the image sequences generated from the earlier structured SVG rendering to assist in identifying occlusions, element correspondences, and textual descriptions of the images.

Previously, when annotating SVG images, the annotation was typically performed only on the rendered image once [23, 32, 33]. This method did not optimize the structure of the SVG itself, failing to reflect the advantages of the SVG format in generating visual images. Furthermore, it could not stably reconstruct the SVG image from text alone. Moreover, the previous annotation approach lacked robustness, ofering both detailed descriptions with precise location guidance as well as vague summaries and category recognition of the SVG, which caused inconsistencies. This inconsistency hindered the editability and stability of the generation process and negatively impacted model training.

To obtain serialized SVG-rendered image annotations, we used a multimodal large language model to annotate sequences of images generated from an SVG. This provides precise annotation guidance that aligns with human understanding and supports multi-round image-text dialogues for comparing image diferences. For the annotation process, we first present a complete SVG-rendered image and have the multimodal model generate a detailed description, denoted as $t _ { g } .$ Then, in a multi-round dialogue, we annotate each step of SVG construction by adding one instruction at a time. With each new image added, the model is asked to describe the changes from the previous image, thus guiding the next stage of SVG generation.

Assume we have an image sequence $( I _ { 1 } , I _ { 2 } , \ldots , I _ { n } ) , n \in \mathbb { N } ^ { * }$ , from the rendering of the first SVG instruction to the final completion of the SVG rendering. For each input image �<sub>�</sub>, we obtain a description of the diferences from the multimodal model by Equation 1, where $I _ { n }$ denotes the complete SVG image.

$$
t _ {i} \sim \mathbb {P} (x \mid I _ {i}, I _ {i - 1}, t _ {g}, I _ {n}) \quad \forall i \in \{1, \dots , n - 1 \}\tag{1}
$$

In this way, we derive the text sequence $t _ { n - 1 }$ for each diference description. Throughout this dialogue, since we always retain the initial full annotation of the complete SVG, the textual descriptions of the gradually constructed SVG will not be subject to errors or hallucinations due to the incompleteness of the SVG at later stages. Through this session-based approach, we ensure the accuracy of every textual annotation step, making it more robust compared to the traditional method of annotating with only two rendered images (before and after). By retaining the initial comprehensive annotation ofthe complete SVG, subsequent annotations ofthe SVG as it is incrementally constructed maintain accuracy. This approach is more robust compared to annotating just the two images before and after changes in the rendering process. The overall process of text annotation is shown in Table 2.

![](images/fc03d5d823b7f0f1afcecc302ae6de035b898ed9f6591bd9d73072af4e6d52fe.jpg)  
Figure 2: The process of sequential textual annotation for SVG. First, we perform an overall description of the SVG. Then, by parsing and reconstructing the SVG structure, we obtain a set of textual descriptions related to the sequence of SVG primitives, based on the visual changes at each step.

We employed InternVL2.5-38B-MPO-AWQ open-source model [4] for this data annotation process. This model is highly eficient due to its int4 quantization [16], and we utilized the LMDeploy tool [6] to eficiently complete the entire data annotation task.

## 3.2 Training of LLM for Reasoning-Driven SVG Generation

Our goal is to align the reasoning process of the SVG generation model with the inherent sequential drawing instructions of SVGs, proceeding layer-by-layer according to the canvas-building process. Similar to typical large language models, our model takes natu ral language descriptions and commands to generate SVG code. It follows user instructions by internally formulating a sequence of drawing steps, explicitly presenting this reasoning process be fore generating the final SVG code. The training targets are paired textual annotations derived from comparisons of SVG generation sequences and their corresponding SVG outputs. Inputs consist of comprehensive SVG descriptions produced by a multimodal model. This approach tightly integrates the training procedure, data annotation workflow, and downstream user scenarios.

Assume that we are given an SVG composed of a sequence of primitive instructions $( s _ { 1 } , s _ { 2 } , \ldots , s _ { n } ) , n \in \mathbb { N } ^ { * }$ . Based on our previ ously described annotation pipeline, we obtain a complete textual description of the SVG, denoted $t _ { g } ,$ along with an ordered sequence of intermediate descriptions $\left( t _ { 1 } , t _ { 2 } , . . . , t _ { n - 1 } \right)$

During the training of the reasoning model, we begin by model ing probability distribution in Equation 2.

$$
\mathbb {P} \big (\left(t _ {1}, t _ {2}, \dots , t _ {n - 1}\right) \mid t _ {g} \big) = \prod_ {i = 1} ^ {n - 1} \mathbb {P} (t _ {i} \mid (t _ {j}) _ {\forall j <   i}, t _ {g})\tag{2}
$$

Subsequently, based on the inferred drawing process - which corresponds to the ordered sequence of intermediate descriptions - we derive a refined probability distribution to optimize the model in Equation 3.

$$
\begin{array}{c} \mathbb {P} ((s _ {1}, s _ {2}, \ldots , s _ {n}) | (t _ {1}, t _ {2}, \ldots , t _ {n - 1}), t _ {g}) \\ = \prod_ {i = 1} ^ {n} \mathbb {P} (t _ {i} | (s _ {j}) _ {\forall j <   i}, (t _ {1}, t _ {2}, \ldots , t _ {n - 1}), t _ {g}) \end{array}\tag{3}
$$

The learning objective is then grounded in the autoregressive loss function used in language generation models. Specifically, we minimize the negative log-likelihood of the target sequence conditioned on the previous steps in Equation 4, where $( x _ { 1 } , \ldots , x _ { n } )$ is the target sequence.

$$
\mathcal {L} = - \sum_ {i = 1} ^ {n} \log P (x _ {i} \mid x _ {1}, \dots , x _ {i - 1})\tag{4}
$$

Through this training framework in Figure 3, the model first learns the structured drawing process of SVGs, acquiring an understanding of how intermediate visual steps unfold. Based on this reasoning process, the model then generates the corresponding SVG instructions, thereby achieving our cognitively aligned objective for structured SVG generation.

## 4 Experiments

In this section, we present our training procedure, baseline comparisons, ablation studies, and a user study evaluating subjective experiences. We highlight our model’s precise instruction editing and compare it with state-of-the-art methods. To evaluate our approach, we use the same Qwen2.5-7B architecture [28]. Specifically, we train our model initialized from the chain-of-thought [30] distilled weights provided by DeepSeek [8], and compare it against a baseline trained directly on text-labeling and SVG pairs using original Qwen weights [28].

![](images/76fc78dcad1e8bab8428a4f55651b2d9ebb96e376f0c8951da24664276e041d5.jpg)  
Figure 3: The training and inference pipeline of SVGThinker. On the left is the training process, where we use overall descriptions to train the model to reason through the process of constructing an SVG, incorporating special thinking tokens for distinction. On the right is the inference process, where our model receives user instructions, analyzes the instructions, conceptualizes the corresponding SVG primitives, and then generates the SVG code based on the reasoning process.

## 4.1 Experiments Settings

Dataset. The training data used in this study is based on the dataset we previously constructed. From the 270,436 samples, we randomly selected 1,000 data pairs as the test set, ensuring no overlap with the training data. Evaluation and generation were performed on these prompts, guaranteeing that the model had no prior knowledge of them.

Training settings. This setup enables direct comparison between the two models to verify the reliability of our method. Both models were trained using the AdamW optimizer [20] with a learning rate of 4×10<sup>−5</sup> and cosine annealing [19], with a minimum learning rate of 2 × 10<sup>−6</sup>. One epoch was used for warmup, followed by 9 training epochs. We utilized the Accelerate library and LLaMA-Factory [37] for training acceleration, and LMDeploy [6] for eficient inference and sampling. All experiments were run on eight A800 GPUs.

Baselines. For baseline comparison, we included both commercial closed-source large models and strong open-source SVG-specialized generative models. Among the former, GPT-4o-2024-11-20 [1] and DeepSeek-R1 [8] were evaluated via their oficial APIs. For open source models, we selected SVGDreamer [34], which uses image generation and diferentiable SVG rendering, IconShop [32], an autoregressive SVG generation model, and LayerTracer [25], which generates icon-style images and vectorizes them into SVG format. Metrics. We evaluated SVG generation quality using three metrics: FID [9], CLIP [21], and FID-CLIP. FID measures the distance be tween the distributions of generated and real SVGs using Inception v3 [27], while CLIP measures alignment between text and image via cosine similarity. FID-CLIP uses CLIP to compute the image feature vectors for distribution comparison.

## 4.2 Experimental Results and Discussion

The comparison results shown in the Figure 4 indicate that, for various types of generated SVG instructions, our method consistently generates stable and highly usable SVG code. In contrast, imagebased methods tend to generate structures that are not well-suited for SVG, with paths that are complex and cumbersome. Furthermore, the resulting SVGs are not smooth or aesthetically pleasing, losing some of the advantages of SVG. Commercial closed-source large language models, currently lacking optimization for SVGs, fail to generate usable SVGs and often produce results that are not visually coordinated or aligned with the generated instructions. From the results in Table 1, it is evident that our method surpasses existing approaches and models in all metrics. Additionally, our method provides more comprehensive support for SVG instructions, while retaining the essential data storage code format of SVGs, using fewer instructions and smaller file sizes, achieving more eficient and higher-quality SVG generation.

From the experiments on precise prompt editing in Figure 5, it is evident that our model can perform accurate and controllable editing operations. By modifying specific parts of the input instructions, we can make hierarchical, precise adjustments to the relevant areas without afecting the rendering of other parts. In contrast, other baseline models exhibit certain flaws and issues. The details also show that our model generates smoother and more consistent SVGs and better understands the semantic information of SVG instructions. These capabilities are beyond the reach of other models.

## 4.3 Ablation Study

4.3.1 Ablation Study on Our Method. First, we conduct an ablation study on our sequential SVG rendering annotation method and the corresponding training approach. The comparison group uses the same model architecture. Since we selected two diferent weight initialization methods, we also performed experimental tests to examine the impact and efects of these initial weights. Both models were trained using the same hyperparameters based on our training dataset.

The results in Table 2 show that the performance of the R1 distilled model is worse compared to the Qwen2.5 model. We believe this is mainly because the distilled model primarily learned many specific issues and related thought processes. On one hand, it was not optimized for SVG output; on the other hand, it lost

Table 1: Quantitative comparison with SOTA models. We also compared the file size, the number of primitives used, and the variety of supported primitives. Our method supports all primitive types, generating higher-quality, more eficient, and compact SVGs with a more concise use of primitives.

<table><tr><td>Methods</td><td>FID↓</td><td>CLIP Score↑</td><td>FID-CLIP↓</td><td>Primitives Support</td><td>File Size (KB)</td><td>Primitives Used</td></tr><tr><td>LayerTracer [25]</td><td>54.75</td><td>0.2290</td><td>30.46</td><td>path</td><td>16.25</td><td>17.83</td></tr><tr><td>SVGDreamer [34]</td><td>240.87</td><td>0.1923</td><td>150.34</td><td>path</td><td>282.13</td><td>513.0</td></tr><tr><td>IconShop [32]</td><td>89.24</td><td>0.2672</td><td>53.79</td><td>path</td><td>3.14</td><td>1.042</td></tr><tr><td>GPT-4o-2024-11-20 [1]</td><td>62.56</td><td>0.1715</td><td>43.93</td><td>all</td><td>0.67</td><td>5.62</td></tr><tr><td>DeepSeek-R1 [8]</td><td>153.04</td><td>0.1160</td><td>111.42</td><td>all</td><td>0.71</td><td>5.30</td></tr><tr><td>SVGThinker</td><td>34.06</td><td>0.2765</td><td>21.08</td><td>all</td><td>1.16</td><td>3.707</td></tr></table>

Input Prompts

SVGDreamer

LayerTracer

GPT-4o

IconShop

DeepSeek-R1

… two maroon location pins … dotted line, … one pin … top left … the other … bottom right …

Ours

… a person … suit and tie … left … gear … Two curved arrows form a circular flow around the gear and person …

… magnifying glass with a zoom-out function … handle … brown and positioned to the left …

… safe box … dark teal … circular dial lock in the center … two small hinges … two rounded feet …

![](images/583b16d478377dfc529bc89da4e3439c249d238f6e91ef9283f0d9be30a7bf98.jpg)

… harp … yellow, with a brown base … strings … gray lines …

… train … rounded corners. The upper section … two equal parts … yellow … lower section is teal … two black circles with small yellow dots …

Figure 4: Comparison of generation results. We used instructions from the test dataset as input for all models. As shown, the SVGs generated by our method are better aligned with the instructions and demonstrate superior visual quality.

![](images/12fe3e98d311971e389309ce2956fa9fdf02b7f70e65b86a36043ec5963fe578.jpg)  
Figure 5: Comparison of test results generated by controllable editing. Each pair forms a controllable editing group, where the input prompt only modifies the description at the corresponding position without altering other sections. Our model can precisely edit the generated SVG results.The thinking process also validates that the model can understand the prompt and grasp the semantic information of SVG primitives. Other models encounter dificulties such as re-generating or failing to understand the prompt. The SVGs produced by our model have sharp, clear edges and are more eficient. In contrast, other SOTA models exhibit flaws and discontinuous mutations to some extent, resulting in a poor visual experience.

Table 2: Component Analysis. We compared our method with the directly trained method. Our method significantly improved the performance compared to both the original weights and the directly trained method.

<table><tr><td>Methods</td><td>FID↓</td><td>CLIP↑</td><td>FID-CLIP↓</td></tr><tr><td>Qwen2.5-7B (zeroshot)</td><td>81.62</td><td>0.2305</td><td>65.42</td></tr><tr><td>Qwen2.5-7B (trained)</td><td>41.57</td><td>0.2345</td><td>25.92</td></tr><tr><td>R1-7B (zeroshot)</td><td>124.87</td><td>0.2027</td><td>99.26</td></tr><tr><td>SVGThinker</td><td>34.06</td><td>0.2765</td><td>21.08</td></tr></table>

Table 3: Parameter analysis on sampling strategies.

<table><tr><td>Methods</td><td>FID↓</td><td>CLIP↑</td><td>FID-CLIP↓</td></tr><tr><td>Greedy Search</td><td>34.06</td><td>0.2765</td><td>21.08</td></tr><tr><td>top_p=0.4, t=0.6</td><td>33.99</td><td>0.2766</td><td>21.43</td></tr><tr><td>top_p=0.6, t=0.6</td><td>32.86</td><td>0.2867</td><td>20.99</td></tr><tr><td>top_p=0.8, t=0.6</td><td>33.87</td><td>0.2976</td><td>21.80</td></tr><tr><td>top_p=1.0, t=0.6</td><td>33.44</td><td>0.2854</td><td>21.08</td></tr><tr><td>top_p=0.8, t=0.6</td><td>33.73</td><td>0.2672</td><td>21.75</td></tr><tr><td>top_p=0.8, t=0.8</td><td>32.79</td><td>0.2680</td><td>21.28</td></tr><tr><td>top_p=0.8, t=1.0</td><td>33.56</td><td>0.3182</td><td>21.50</td></tr></table>

much of the original model’s weight-related SVG knowledge. However, when considering the worse performance of the original weights, the model using our approach performs better. Furthermore, our method significantly improves the model’s ability to generate higher-quality SVGs that align better with textual descrip tions. This validates the efectiveness of our approach from an experimental perspective.

4.3.2 Ablation Study on Sampling Strategies. We also conducted an ablation experiment on the hyperparameters of diferent sampling methods. In the previous experiments, we used greedy search for all models, meaning that the token with the highest probability was selected and output at each step. This method ensures that the out put for each specific input is consistent, facilitating comparison and analysis. Given that diferent sampling methods may still exhibit certain diferences, we performed an experimental comparison. We primarily focused on nucleus sampling [11] with diferent hyperpa rameter selections, as it ofers greater novelty and higher-quality generation. In this method, when generating a new token, a set of top-probability candidates is first identified. The range is determined by a pre-set top-p value, and sampling is then performed within the selected range.

From the results in Table 3, we observe that, overall, there is no significant diference in the evaluation data across various sampling hyperparameter settings. Based on the two FID calculation results, the model-generated outputs are relatively robust, consistently aligning with the data distribution of the test set. In terms of CLIP scores, the model demonstrates better alignment with the generation instructions when using diferent sampling methods, compared to greedy search. This suggests that, in practical appli cations, we can enhance the generation ability of our model by performing multiple rounds of nucleus sampling and selecting the best results.

Table 4: User study on models comparison.

<table><tr><td>Methods</td><td>Usability↑</td><td>Appearance↑</td><td>Alignment↑</td></tr><tr><td>SVGThinker</td><td>3.58</td><td>3.33</td><td>3.78</td></tr><tr><td>SVGDreamer</td><td>1.25</td><td>2.69</td><td>2.58</td></tr><tr><td>IconShop</td><td>3.54</td><td>3.06</td><td>3.18</td></tr><tr><td>LayerTracer</td><td>2.91</td><td>3.85</td><td>3.55</td></tr><tr><td>GPT-4o</td><td>3.76</td><td>2.16</td><td>1.91</td></tr></table>

## 4.4 User Study

To meet the subjective experience requirements of humans, we conducted a survey with 67 volunteers to collect their opinions and preferences. The volunteers were asked to rank the tools they used from diferent perspectives, comparing our model with three current state-of-the-art models, as well as including a comparison with commercial models. Considering diferent evaluation criteria, we asked the volunteers to assess the usability of the methods, the aesthetic quality of the generated content, and the alignment of the generated content with the instructions. Each volunteer used 10 prompts to input into the model and obtained results, then ranked the results. For each volunteer, based on their generated outputs, they were asked to rank all the methods. Finally, we obtained an integrated scoring table and directly performed a weighted average of the scores to obtain the corresponding scores.

Results in Table 4 indicate that autoregressive models generally provided better user experiences due to inherent interactivity from large language models. Conversely, methods employing longer chains and complex architectures sufered from slower generation speeds and weaker interactivity. Advanced image-generation models achieved higher aesthetic scores, whereas methods without strong image priors were limited by training data, producing less visually appealing SVGs. Notably, our method significantly outperformed others in aligning generated SVGs with user instructions, achieving superior overall results in human preference metrics.

## 5 Conclusion

We introduce SVGThinker, a reasoning-driven framework for SVG generation that bridges the gap between textual instructions and visual output through a sequential and hierarchical generation process. Our model leverages large language models and a multimodal approach to ensure precise alignment between SVG code and its corresponding visual representation, while supporting all SVG primitives. By employing Chain-of-Thought reasoning and a structured annotation process, we enhance the model’s robustness and accuracy in generating stable, high-quality, and editable SVG code. Our experiments show that SVGThinker outperforms state-of-the-art methods in generation quality, editing flexibility, and alignment with textual instructions. Additionally, our user study indicates a preference for our method in terms of ease of use, aesthetic quality, and instruction adherence.

## Acknowledgments

This work is supported by the Science and Technology Commission of Shanghai Municipality under research grant No. 25ZR1401187.

## References

[1] Josh Achiam, Steven Adler, Sandhini Agarwal, Lama Ahmad, Ilge Akkaya, Floren cia Leoni Aleman, Diogo Almeida, Janko Altenschmidt, Sam Altman, Shyamal Anadkat, et al. 2023. Gpt-4 technical report. arXiv preprint arXiv:2303.08774 (2023).

[2] Tom Brown, Benjamin Mann, Nick Ryder, Melanie Subbiah, Jared D Kaplan, Prafulla Dhariwal, Arvind Neelakantan, Pranav Shyam, Girish Sastry, Amanda Askell, et al. 2020. Language models are few-shot learners. Advances in neural information processing systems 33 (2020), 1877–1901.

[3] Alexandre Carlier, Martin Danelljan, Alexandre Alahi, and Radu Timofte. 2020. Deepsvg: A hierarchical generative network for vector graphics animation. Advances in Neural Information Processing Systems 33 (2020), 16351–16361.

[4] Zhe Chen, Weiyun Wang, Yue Cao, Yangzhou Liu, Zhangwei Gao, Erfei Cui, Jinguo Zhu, Shenglong Ye, Hao Tian, Zhaoyang Liu, et al. 2024. Expanding Performance Boundaries of Open-Source Multimodal Models with Model, Data, and Test-Time Scaling. arXiv preprint arXiv:2412.05271 (2024).

[5] Victor Condino. 2022. Large collection of labelled SVG icon graphics in a variety of styles. https://www.kaggle.com/datasets/victorcondino/svgicons/.

[6] LMDeploy Contributors. 2023. LMDeploy: A Toolkit for Compressing, Deploying, and Serving LLM. https://github.com/InternLM/lmdeploy.

[7] Vision Cortex. 2023. Raster to Vector Graphics Converter built on top of vision cortex. https://github.com/visioncortex/vtracer.

[8] Daya Guo, Dejian Yang, Haowei Zhang, Junxiao Song, Ruoyu Zhang, Runxin Xu, Qihao Zhu, Shirong Ma, Peiyi Wang, Xiao Bi, et al. 2025. Deepseek-r1: Incentivizing reasoning capability in llms via reinforcement learning. arXiv preprint arXiv:2501.12948 (2025).

[9] Martin Heusel, Hubert Ramsauer, Thomas Unterthiner, Bernhard Nessler, and Sepp Hochreiter. 2017. Gans trained by a two time-scale update rule converge to a local nash equilibrium. Advances in neural information processing systems 30 (2017).

[10] Jonathan Ho, Ajay Jain, and Pieter Abbeel. 2020. Denoising difusion probabilistic models. Advances in neural information processing systems 33 (2020), 6840–6851.

[11] Ari Holtzman, Jan Buys, Li Du, Maxwell Forbes, and Yejin Choi. 2019. The curious case of neural text degeneration. arXiv preprint arXiv:1904.09751 (2019).

[12] Ajay Jain, Amber Xie, and Pieter Abbeel. 2023. Vectorfusion: Text-to-svg by ab stracting pixel-based difusion models. In Proceedings ofthe IEEE/CVF Conference on Computer Vision and Pattern Recognition. 1911–1920.

[13] Jared Kaplan, Sam McCandlish, Tom Henighan, Tom B Brown, Benjamin Chess, Rewon Child, Scott Gray, Alec Radford, Jefrey Wu, and Dario Amodei. 2020. Scaling laws for neural language models. arXiv preprint arXiv:2001.08361 (2020).

[14] Black Forest Labs. 2024. FLUX. https://github.com/black-forest-labs/flux.

[15] Tzu-Mao Li, Michal Lukáč, Gharbi Michaël, and Jonathan Ragan-Kelley. 2020. Diferentiable Vector Graphics Rasterization for Editing and Learning. ACM Trans. Graph. (Proc. SIGGRAPH Asia) 39, 6 (2020), 193:1–193:15.

[16] Ji Lin, Jiaming Tang, Haotian Tang, Shang Yang, Wei-Ming Chen, Wei-Chen Wang, Guangxuan Xiao, Xingyu Dang, Chuang Gan, and Song Han. 2024. Awq: Activation-aware weight quantization for on-device llm compression and accel eration. Proceedings ofMachine Learning and Systems 6 (2024), 87–100.

[17] Haotian Liu, Chunyuan Li, Qingyang Wu, and Yong Jae Lee. 2023. Visual in struction tuning. Advances in neural information processing systems 36 (2023), 34892–34916.

[18] Raphael Gontijo Lopes, David Ha, Douglas Eck, and Jonathon Shlens. 2019. A learned representation for scalable vector graphics. In Proceedings of the IEEE/CVF

International Conference on Computer Vision. 7930–7939.

[19] Ilya Loshchilov and Frank Hutter. 2016. Sgdr: Stochastic gradient descent with warm restarts. arXiv preprint arXiv:1608.03983 (2016).

[20] Ilya Loshchilov and Frank Hutter. 2017. Decoupled weight decay regularization. arXiv preprint arXiv:1711.05101 (2017).

[21] Alec Radford, Jong Wook Kim, Chris Hallacy, Aditya Ramesh, Gabriel Goh, Sandhini Agarwal, Girish Sastry, Amanda Askell, Pamela Mishkin, Jack Clark, et al. 2021. Learning transferable visual models from natural language supervision. In International conference on machine learning. PmLR, 8748–8763.

[22] Alec Radford, Jefrey Wu, Rewon Child, David Luan, Dario Amodei, Ilya Sutskever, et al. 2019. Language models are unsupervised multitask learners. OpenAI blog 1, 8 (2019), 9.

[23] Juan A Rodriguez, Shubham Agarwal, Issam H Laradji, Pau Rodriguez, David Vazquez, Christopher Pal, and Marco Pedersoli. 2023. Starvector: Generating scalable vector graphics code from images. arXiv preprint arXiv:2312.11556 (2023).

[24] Robin Rombach, Andreas Blattmann, Dominik Lorenz, Patrick Esser, and Björn Ommer. 2022. High-resolution image synthesis with latent difusion models. In Proceedings of the IEEE/CVF conference on computer vision and pattern recognition. 10684–10695.

[25] Yiren Song, Danze Chen, and Mike Zheng Shou. 2025. LayerTracer: Cognitive-Aligned Layered SVG Synthesis via Difusion Transformer. arXiv preprint arXiv:2502.01105 (2025).

[26] SVGRepo. 2016. Open-licensed svg vector and icons. https://www.svgrepo.com/.

[27] Christian Szegedy, Vincent Vanhoucke, Sergey Iofe, Jon Shlens, and Zbigniew Wojna. 2016. Rethinking the inception architecture for computer vision. In Proceedings of the IEEE conference on computer vision and pattern recognition. 2818–2826.

[28] Qwen Team. 2024. Qwen2.5: A Party of Foundation Models. https://qwenlm. github.io/blog/qwen2.5

[29] Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Łukasz Kaiser, and Illia Polosukhin. 2017. Attention is all you need. Advances in neural information processing systems 30 (2017).

[30] Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Fei Xia, Ed Chi, Quoc V Le, Denny Zhou, et al. 2022. Chain-of-thought prompting elicits reasoning in large language models. Advances in neural information processing systems 35 (2022), 24824–24837.

[31] Ronghuan Wu, Wanchao Su, and Jing Liao. 2024. Chat2SVG: Vector Graphics Generation with Large Language Models and Image Difusion Models. arXiv preprint arXiv:2411.16602 (2024).

[32] Ronghuan Wu, Wanchao Su, Kede Ma, and Jing Liao. 2023. Iconshop: Text-guided vector icon synthesis with autoregressive transformers. ACM Transactions on Graphics (TOG) 42, 6 (2023), 1–14.

[33] Ximing Xing, Juncheng Hu, Guotao Liang, Jing Zhang, Dong Xu, and Qian Yu. 2024. Empowering LLMs to Understand and Generate Complex Vector Graphics. arXiv preprint arXiv:2412.11102 (2024).

[34] Ximing Xing, Haitao Zhou, Chuang Wang, Jing Zhang, Dong Xu, and Qian Yu. 2024. Svgdreamer: Text guided svg generation with difusion model. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 4546–4555.

[35] Zhuosheng Zhang, Aston Zhang, Mu Li, Hai Zhao, George Karypis, and Alex Smola. 2023. Multimodal chain-of-thought reasoning in language models. arXiv preprint arXiv:2302.00923 (2023).

[36] Zhongyin Zhao, Ye Chen, Zhangli Hu, Xuanhong Chen, and Bingbing Ni. 2024. Vector Graphics Generation via Mutually Impulsed Dual-domain Difusion. In Proceedings ofthe IEEE/CVFConference on ComputerVision andPattern Recognition. 4420–4428.

[37] Yaowei Zheng, Richong Zhang, Junhao Zhang, Yanhan Ye, Zheyan Luo, Zhangchi Feng, and Yongqiang Ma. 2024. Llamafactory: Unified eficient fine-tuning of 100+ language models. arXiv preprint arXiv:2403.13372 (2024).