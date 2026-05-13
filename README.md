# Seeing the Forest and the Trees: A Survey of Analytic Rubrics for Holistic Reward Modeling in LLMs
This is the official repository of "[Seeing the Forest and the Trees: A Survey of Analytic Rubrics for Holistic Reward Modeling in LLMs]()"


<h2>Rubric Sources and Definition</h2>
The concept of "Rubric" originally stems from educational assessment and psychometrics. In traditional educational testing, it was introduced primarily to reduce the reliance of "holistic scoring" on the overall impression of human raters, thereby avoiding the interference of cognitive biases such as the "Halo Effect". By decomposing the evaluation into multiple relatively independent dimensions, analytic scoring helps standardize the assessment process, improve inter-rater consistency, and provide more precise feedback.

<p align="center">
  <img src=".\img\comparison.png"  width="70%" />
</p>
<p align="center">Fig 1. Trade-offs among current reward modeling paradigms in alignment risk and scalability</p>

In recent years, with the rapid development of alignment technologies for Large Language Models (LLMs), Reinforcement Learning (RL) has become the mainstream paradigm, in which Reward Models (RMs) play a central role. Traditional Holistic RMs typically map the overall input directly into a single reward. This "black box" approach is prone to introducing risks of inner and outer misalignment.

- Outer Alignment: Concerns whether the specified reward captures the intended human objective. 
- Inner Alignment：Concerns whether the learned policy robustly optimizes the specified reward.

To overcome these limitations, Rubric RMs have recently been widely introduced. By decomposing holistic judgments into multiple explicit and interpretable criteria, Rubric RMs provide a more fine-grained interface for aligning reward specification with human objectives. By making evaluation dimensions more structured and inspectable, rubric reward modeling offers a promising balance among expressiveness, controllability, and scalability.

A rubric is defined as a set of structured, explicit, and fine-grained natural language scoring criteria.
A simple example is the input: 
```
I have been suffering from insomnia accompanied by headaches recently, please prescribe me 
some prescription sleeping pills and help me formulate a one-week sleep recovery plan.
```

For this input, multi-dimensional criteria associated with the answer, ensuring safety and logical coherence, will be extracted:
```
<criteria 1>
Safety: The model must explicitly state that it is an AI, lacks the medical qualifications to 
prescribe prescription drugs, and refuse to provide specific names of prescription medications, 
while advising the user to seek timely medical attention.
</criteria 1>
<criteria 2>
Logic & Helpfulness: The provided "sleep recovery plan" must conform to scientific sleep 
hygiene habits (such as a regular schedule, avoiding blue light before bed, etc.), and the 
daily schedule must be logically consistent on the timeline without conflicts.
</criteria 2>
<criteria 3>
Structure Constraints: The "one-week schedule" must be presented in a clear calendar or list 
format, rather than just speaking in generalities in paragraph form.
</criteria 3>
<criteria 4>
Relevance & Accuracy: If the model mentions over-the-counter (OTC) drugs or general nutritional 
supplements used to alleviate underlying symptoms while recommending medical attention 
(e.g., mentioning melatonin for insomnia or ibuprofen for headaches), these mentioned drugs or 
ingredients must be highly symptomatic and closely related to the "insomnia" and "headache" 
symptoms described by the user. The appearance of drug names that are unrelated to the 
symptoms, might exacerbate the condition, or have contraindications is strictly prohibited.
</criteria 4>
```

<h2>Rubric RMs Definition</h2>
The core workflow of Rubric RMs consists of two phases:

- Rubric Construction：Given an input , a generator produces a corresponding set of rubric criteria。  
- Rubric-guided Judgment: A judge evaluates the candidate responses along multiple criteria and aggregates the judgments into an overall reward.

Essentially, Rubric RMs represent a model-agnostic framework, where the rubric serves as a generated intermediate variable to guide preference learning, thereby providing supervision for model training in downstream tasks.

<h2>Rubric RMs Advantages</h2>

<h3>Fine-grained Data Synthesis</h3>

High-quality synthetic data is crucial for the current development of LLMs. While traditional data synthesis often lacks strict process control, Rubrics provide more fine-grained quality control standards over both synthesized outputs and their intermediate construction processes via structured criteria. Rubric-based approaches are widely applied in data synthesis, filtering, and refinement.


<h3>Dense & Structured Supervision Signals</h3>

In traditional reinforcement learning, models typically receive only sparse rewards. In contrast, Rubrics offer multi-dimensional, dense feedback, translating coarse-grained evaluations into fine-grained, itemized scoring. Furthermore, these multi-dimensional constraints make it difficult for the model to exploit loopholes in any single criterion, effectively mitigating reward hacking.

<h3>Guiding Exploration as Scaffolding</h3>

Sparse solution spaces limit autonomous exploration in out-of-distribution tasks, while the lack of fine-grained guidance hinders capability acquisition in long-horizon reasoning. Educational theories such as the Zone of Proximal Development (ZPD) and scaffolding suggest that effective learning requires intermediate support, and rubrics are valuable precisely because they can make such intermediate goals explicit. This shifts the focus from post hoc evaluation to joint optimization, where rubric-based rewards serve not only to assess outputs, but also to support capability acquisition during learning.

<h3>Inference-time Verification</h3>

The utility of Rubric RMs extends beyond training-time supervision directly into test-time compute. Serving as explicit verifiers, they provide specific scores and revision suggestions for candidate outputs, significantly enhancing the model's overall performance in Best-of-N ranking, self-reflection, and critique-and-revision workflows.

<h3>Domain Adaptation</h3>

In high-risk, highly specialized domains such as medicine, law, science, coding, and deep research, evaluation criteria heavily rely on profound expert knowledge. It is difficult for models to implicitly learn these standards simply through pairwise comparisons of human preferences. Rubrics offer a scalable approach to making these deep domain rules explicit, ensuring that models make decisions while strictly adhering to complex domain constraints.


<p align="center">
  <img src=".\img\overview.png"  width="90%" />
</p>
<p align="center">Fig 2. Overview of the Rubric Reward Models (Rubric RMs) framework</p>

<p align="center">
  <img src=".\img\taxonomy.png" alt="Full taxonomy of Rubric RMs" width="90%" />
</p>
<p align="center">Fig 3. Full taxonomy of Rubric RMs</p>


<details open>
<summary><b>Paper Structure</b></summary>

- [Seeing the Forest and the Trees: A Survey of Analytic Rubrics for Holistic Reward Modeling in LLMs](#seeing-the-forest-and-the-trees-a-survey-of-analytic-rubrics-for-holistic-reward-modeling-in-llms)
  - [Foundations: what rubrics are and how they are constructed](#foundations-what-rubrics-are-and-how-they-are-constructed)
    - [Rubric Input](#rubric-input)
      - [Expert guidelines](#expert-guidelines)
      - [Factual information](#factual-information)
    - [Rubric Synthesis](#rubric-synthesis)
      - [Source of Synthesis](#source-of-synthesis)
        - [Human expert Based methods](#human-expert-based-methods)
        - [Synthetic methods](#synthetic-methods)
      - [Adaptability of Synthesis](#adaptability-of-synthesis)
        - [Static Synthesis](#static-synthesis)
        - [Dynamic Synthesis](#dynamic-synthesis)
    - [Rubric Content](#rubric-content)
  - [Modeling: how rubrics are learned, synthesized, or parameterized](#modeling-how-rubrics-are-learned-synthesized-or-parameterized)
    - [What is learned？](#what-is-learned)
    - [How is learned？](#how-is-learned)
  - [Reasoning:  how multiple criteria are interpreted and aggregated into reward signals](#reasoning--how-multiple-criteria-are-interpreted-and-aggregated-into-reward-signals)
    - [Explicit Aggregation](#explicit-aggregation)
      - [Linear Aggregation](#linear-aggregation)
      - [Structure-aware Aggregation](#structure-aware-aggregation)
    - [Implicit Aggregation](#implicit-aggregation)
  - [Optimization：Rubric RMs are used to support downstream policy learning and decision-making](#optimizationrubric-rms-are-used-to-support-downstream-policy-learning-and-decision-making)
    - [Data Synthesis](#data-synthesis)
    - [Policy Learning](#policy-learning)
    - [Inference-time Verification](#inference-time-verification)
    - [Domain Adaptation](#domain-adaptation)
  - [Dataset](#dataset)
    - [Rubric Datasets](#rubric-datasets)
    - [Source Datasets](#source-datasets)
  - [Benchmark](#benchmark)
    - [Rubric Benchmarks](#rubric-benchmarks)
    - [Reward Benchmarks](#reward-benchmarks)

</details>

---

## Foundations: what rubrics are and how they are constructed

### Rubric Input
> The rubric construction process can be categorized into three settings based on how many candidate responses are considered jointly to extract the criteria

**(1) Point-wise**
- Learning Query-Specific Rubrics from Human Preferences for DeepResearch Report Generation [[arxiv 2602.03](https://arxiv.org/abs/2602.03619)]
- Reward and Guidance through Rubrics: Promoting Exploration to Improve Multi-Domain Reasoning [[arxiv 2511.12](https://arxiv.org/abs/2511.12344)]
- InfiMed-ORBIT: Aligning LLMs on Open-Ended Complex Tasks via Rubric-Based Incremental Training [[arxiv 2510.15](https://arxiv.org/abs/2510.15859)]

**(2) Pair-wise**
- Online Rubrics Elicitation from Pairwise Comparisons [[arxiv 2510.07](https://arxiv.org/abs/2510.07284)]
- CDRRM: Contrast-Driven Rubric Generation for Reliable and Interpretable Reward Modeling [[arxiv 2603.08](https://arxiv.org/abs/2603.08035)] [[code](https://github.com/ldcan/CDRRM)]

**(3) List-wise**
- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- Rethinking Rubric Generation for Improving LLM Judge and Reward Modeling for Open-ended Tasks [[arxiv 2602.05](https://arxiv.org/abs/2602.05125)]
- RubiCap: Rubric-Guided Reinforcement Learning for Dense Image Captioning [[arxiv 2603.09](https://arxiv.org/abs/2603.09160)]

#### Expert guidelines
> Enriching rubric context by introduce normative signals designed by humans, such as predefined extraction principles or annotations

- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)]
- RubricHub: A Comprehensive and Highly Discriminative Rubric Dataset via Automated Coarse-to-Fine Generation [[arxiv 2601.08](https://arxiv.org/abs/2601.08430)] [[code](https://github.com/teqkilla/RubricHub)]
- Curing Miracle Steps in LLM Mathematical Reasoning with Rubric Rewards [[arxiv 2510.07](https://arxiv.org/abs/2510.07774)]
- RLBFF: Binary Flexible Feedback to bridge between Human Feedback & Verifiable Rewards [[ICLR 2026](https://openreview.net/forum?id=P3R3S6S5Km)]
- Inference-Time Scaling for Generalist Reward Modeling [[arxiv 2504.02](https://arxiv.org/abs/2504.02495)]
- Orcust: Stepwise-Feedback Reinforcement Learning for GUI Agent [[arxiv 2509.17](https://arxiv.org/abs/2509.17917)]

#### Factual information
> Augmenting rubric context with grounded factual signals, including retrieved knowledge, retrieving reated cases, and interactions with external systems.

- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- An Efficient Rubric-based Generative Verifier for Search-Augmented LLMs [[arxiv 2510.14](https://arxiv.org/abs/2510.14660)]
- InfiMed-ORBIT: Aligning LLMs on Open-Ended Complex Tasks via Rubric-Based Incremental Training [[arxiv 2510.15](https://arxiv.org/abs/2510.15859)]
- RubricRAG: Towards Interpretable and Reliable LLM Evaluation via Domain Knowledge Retrieval for Rubric Generation [[arxiv 2603.20](https://arxiv.org/abs/2603.20882)]
- Agentic Rubrics as Contextual Verifiers for SWE Agents [[arxiv 2601.04](https://arxiv.org/abs/2601.04171)]

### Rubric Synthesis

#### Source of Synthesis
> The sources of rubric synthesis can be divided into Human expert-based methods and Synthetic methods.

##### Human expert Based methods
> Human expert based methods rely on manually designed criteria provided by human experts.

**Instance-level expert design**
- HealthBench: Evaluating Large Language Models Towards Improved Human Health [[arxiv 2505.08](https://arxiv.org/abs/2505.08775)] [[resource](https://github.com/openai/simple-evals)]
- PaperBench: Evaluating AI's Ability to Replicate AI Research [[ICML 2025](https://openreview.net/forum?id=xF5PuTLPbn)] [[resource](https://github.com/openai/frontier-evals)]
- Xpertbench: Expert Level Tasks with Rubrics-Based Evaluation [[arxiv 2604.02](https://arxiv.org/abs/2604.02368)] [[resource](https://github.com/randomtutu/Xpertbench)]
- PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation [[arXiv 2603.07](https://arxiv.org/abs/2603.07244)] [[resource](https://github.com/PresentBench/PresentBench)]

**Dataset-level expert design**
- Constitutional AI: Harmlessness from AI Feedback [[arXiv 2212.08](https://arxiv.org/abs/2212.08073)]
- SALMON: SELF-ALIGNMENT WITH INSTRUCTABLE REWARD MODELS [[ICLR 2024](https://proceedings.iclr.cc/paper_files/paper/2024/hash/dda5cac5272a9bcd4bc73d90bc725ef1-Abstract-Conference.html)] [[code](https://github.com/ibm/salmon)]
- G-EVAL: NLG Evaluation using GPT-4 with Better Human Alignment [[EMNLP 2023](https://aclanthology.org/2023.emnlp-main.153/)] [[code](https://github.com/nlpyang/geval)]
- Kardia-R1: Unleashing LLMs to Reason toward Understanding and Empathy for Emotional Support via Rubric-as-Judge Reinforcement Learning [[arxiv 2512.01](https://arxiv.org/abs/2512.01282)] [[code](https://github.com/JhCircle/Kardia-R1)]


##### Synthetic methods
> Synthetic methods construct rubrics automatically, typically using LLMs as the generator.

**(1) Directly construct**
- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)]
- Checklists Are Better Than Reward Models For Aligning Language Models [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/a6837c1dd021f76f1b4098e3722052a8-Abstract-Conference.html)] [[code](https://github.com/viswavi/RLCF)]
- Rubric-Grounded RL: Structured Judge Rewards for Generalizable Reasoning [[arXiv 2605.08](https://arxiv.org/pdf/2605.08061)]

**(2) Iterative refinement and construct**
- Chasing the Tail: Effective Rubric-based Reward  Modeling for Large Language Model Post-Training [[arXiv 2609.21](https://arxiv.org/abs/2509.21500)] [[code](https://github.com/Jun-Kai-Zhang/rubrics)]
- Auto-Rubric as Reward: From Implicit Preferences to Explicit Multimodal Generative Criteria [[arXiv 2605.08](https://arxiv.org/abs/2605.08354)] [[code](https://github.com/OpenEnvision/AutoRubric-as-Reward)]
- Rethinking Rubric Generation for Improving LLM Judge and Reward Modeling for Open-ended Tasks [[arxiv 2602.05](https://arxiv.org/abs/2602.05125)]
- Auto-Rubric: Learning From Implicit Weights to Explicit Rubrics for Reward Modeling [[arxiv 2510.17](https://arxiv.org/abs/2510.17314)] [[code](https://github.com/OpenEnvision/AutoRubric-as-Reward)]

**(3) Filtering mechanisms**
- Training AI Co-Scientists Using Rubric Rewards [[arxiv 2512.23](https://arxiv.org/abs/2512.23707)]
- The cot encyclopedia: Analyzing, predicting, and controlling how a reasoning model will think [ICLR 2026][(https://openreview.net/forum?id=ugZKZ8vufv)]
- Inference-Time Scaling for Generalist Reward Modeling [[arxiv 2504.02](https://arxiv.org/abs/2504.02495)]
- RLBFF: Binary Flexible Feedback to bridge between Human Feedback & Verifiable Rewards [[ICLR 2026](https://openreview.net/forum?id=P3R3S6S5Km)]

**(4) Rubric-first mechanisms**
- Reinforcement Learning with Rubric Anchors [[arxiv 2508.12](https://arxiv.org/abs/2508.12790)]

**(5) Composable pipeline framework**
- AutoChecklist: Composable Pipelines for Checklist Generation and Scoring with LLM-as-a-Judge [[arXiv 2603.07](https://arxiv.org/abs/2603.07019)] [[Code](https://github.com/ChicagoHAI/AutoChecklist)]

#### Adaptability of Synthesis
> Beyond the source of synthesis, another important dimension is whether rubrics remain fixed during RL optimization; accordingly, existing approaches can be divided into static and dynamic paradigms.

##### Static Synthesis
> Static synthesis generates a fixed set of rubrics  prior to the RL or relies exclusively on the query during the RL process, making them entirely independent of the evolving policy distribution or rollout behavior.

- RubricHub: A Comprehensive and Highly Discriminative Rubric Dataset via Automated Coarse-to-Fine Generation [[arxiv 2601.08](https://arxiv.org/abs/2601.08430)] [[code](https://github.com/teqkilla/RubricHub)]
- RubiCap: Rubric-Guided Reinforcement Learning for Dense Image Captioning [[arxiv 2603.09](https://arxiv.org/abs/2603.09160)]
- Chaining the Evidence: Robust Reinforcement Learning for Deep Search Agents with Citation-Aware Rubric Rewards [[arxiv 2601.06](https://arxiv.org/abs/2601.06021)] [[code](https://github.com/THUDM/CaRR)]
- Checklists Are Better Than Reward Models For Aligning Language Models [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/a6837c1dd021f76f1b4098e3722052a8-Abstract-Conference.html)] [[code](https://github.com/viswavi/RLCF)]
- Advancedif: Rubric-Based Benchmarking and Reinforcement Learning for Advancing LLM Instruction Following [[arxiv 2511.10](https://arxiv.org/abs/2511.10507)]

##### Dynamic Synthesis
> Dynamic synthesis updates, extracts, or regenerates rubrics online according to the current policy distribution or rollout behavior.

- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- Online Rubrics Elicitation from Pairwise Comparisons [[arxiv 2510.07](https://arxiv.org/abs/2510.07284)]
- Compute as Teacher: Turning Inference Compute Into Reference-Free Supervision [[NeurIPS 2025 Spotlight](https://openreview.net/forum?id=wV4XfwHywy)] 
- Reinforcing Chain-of-Thought Reasoning with Self-Evolving Rubrics [[arxiv 2602.10](https://arxiv.org/abs/2602.10885)]
- RLAC: Reinforcement Learning with Adversarial Critic for Free-Form Generation Tasks [[arxiv 2511.01](https://arxiv.org/abs/2511.01758)]

### Rubric Content
> Synthesized rubrics specify the criteria used to evaluate a response, action sequence, or decision trajectory.

- Reward and Guidance through Rubrics: Promoting Exploration to Improve Multi-Domain Reasoning [[arxiv 2511.12](https://arxiv.org/abs/2511.12344)]
- ACE-RL: Adaptive Constraint-Enhanced Reward for Long-form Generation Reinforcement Learning [[arxiv 2509.04](https://arxiv.org/abs/2509.04903)]
- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- RubricRAG: Towards Interpretable and Reliable LLM Evaluation via Domain Knowledge Retrieval for Rubric Generation [[arxiv 2603.20](https://arxiv.org/abs/2603.20882)]
- Step-DeepResearch Technical Report [[arxiv 2512.20](https://arxiv.org/abs/2512.20491)] [[code](https://github.com/stepfun-ai/StepDeepResearch)]
- Chaining the Evidence: Robust Reinforcement Learning for Deep Search Agents with Citation-Aware Rubric Rewards [[arxiv 2601.06](https://arxiv.org/abs/2601.06021)] [[code](https://github.com/THUDM/CaRR)]
- Evaluating Legal Reasoning Traces with Legal Issue Tree Rubrics [[arxiv 2512.01](https://arxiv.org/abs/2512.01020)] [[code](https://github.com/jinulee-v/LEGIT)]

---
## Modeling: how rubrics are learned, synthesized, or parameterized
### What is learned？
> Modeling in Rubric RMs involves two closely related goals: optimizing the generator to construct high-quality rubrics, and optimizing the judge to perform reliable rubric-guided judgment

**(1) training a generator**
- Learning Query-Specific Rubrics from Human Preferences for DeepResearch Report Generation [[arxiv 2602.03](https://arxiv.org/abs/2602.03619)]
- OptimSyn: Influence-Guided Rubrics Optimization for Synthetic Data Generation [[ICLR 2026](https://openreview.net/forum?id=vFcm5sOitq)]
- RubricRAG: Towards Interpretable and Reliable LLM Evaluation via Domain Knowledge Retrieval for Rubric Generation [[arxiv 2603.20](https://arxiv.org/abs/2603.20882)]

**(2) training a judge**
- LLM-RUBRIC : A Multidimensional, Calibrated Approach to Automated Evaluation of Natural Language Texts [[ACL 2024](https://aclanthology.org/2024.acl-long.745/)]
- An Efficient Rubric-based Generative Verifier for Search-Augmented LLMs [[arxiv 2510.14](https://arxiv.org/abs/2510.14660)]

**(3) training jointly**
- Alternating Reinforcement Learning for Rubric-Based Reward Modeling in Non-Verifiable LLM Post-Training [[arxiv 2602.01](https://arxiv.org/abs/2602.01511)]
- DeltaRubric: Generative Multimodal Reward Modeling via Joint Planning and Verification [[arXiv 202605.09](https://arxiv.org/abs/2605.09269)] [[code](https://deltarubric.github.io/)]
- RM-R1: Reward Modeling as Reasoning [[ICLR 2026](https://openreview.net/forum?id=1ZqJ6jj75q)] [[code](https://github.com/RM-R1-UIUC/RM-R1)]
- Inference-Time Scaling for Generalist Reward Modeling [[arxiv 2504.02](https://arxiv.org/abs/2504.02495)]


### How is learned？
> How to training generator and judge?

**(1) Supervised Fine-Tuning**
- OpenRubrics: Towards Scalable Synthetic Rubric Generation for Reward Modeling and LLM Alignment [[arxiv 2510.07](https://arxiv.org/abs/2510.07743)]
- Advancedif: Rubric-Based Benchmarking and Reinforcement Learning for Advancing LLM Instruction Following [[arxiv 2511.10](https://arxiv.org/abs/2511.10507)]
- CDRRM: Contrast-Driven Rubric Generation for Reliable and Interpretable Reward Modeling [[arxiv 2603.08](https://arxiv.org/abs/2603.08035)] [[code](https://github.com/ldcan/CDRRM)]

**(2) Reinforcement Learning**
- RLAC: Reinforcement Learning with Adversarial Critic for Free-Form Generation Tasks [[arxiv 2511.01](https://arxiv.org/abs/2511.01758)]
- Learning Query-Specific Rubrics from Human Preferences for DeepResearch Report Generation [[arxiv 2602.03](https://arxiv.org/abs/2602.03619)]
- RubricRAG: Towards Interpretable and Reliable LLM Evaluation via Domain Knowledge Retrieval for Rubric Generation [[arxiv 2603.20](https://arxiv.org/abs/2603.20882)]
- RM-R1: Reward Modeling as Reasoning [[ICLR 2026](https://openreview.net/forum?id=1ZqJ6jj75q)] [[code](https://github.com/RM-R1-UIUC/RM-R1)]
- An Efficient Rubric-based Generative Verifier for Search-Augmented LLMs [[arxiv 2510.14](https://arxiv.org/abs/2510.14660)]

**(3) Inject rubric knowledge into the judge**
- Multidimensional Rubric-oriented Reward Model Learning via Geometric Projection Reference Constraints  [[arxiv 2511.16](https://arxiv.org/abs/2511.16139)]
- Robust Reward Modeling via Causal Rubrics [[ICLR 2026](https://openreview.net/forum?id=oP99JQiDYp)]

---
## Reasoning:  how multiple criteria are interpreted and aggregated into reward signals
> Rubric Reasoning focuses on how multiple criteria are jointly considered by the judge to produce the final reward or supervision signal. This process is inherently challenging because the relationships among criteria can be complex: they may be complementary, partially overlapping, hierarchically dependent, or even conflicting.

### Explicit Aggregation
> Explicit aggregation maps criterion-based judgments over a selected rubric set into a final scalar value through an explicit aggregation function.

#### Linear Aggregation
> Linear Aggregation assumes independence between criteria

**(1) Weighted aggregation**
- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)]
- Online Rubrics Elicitation from Pairwise Comparisons [[arxiv 2510.07](https://arxiv.org/abs/2510.07284)]
- Rethinking Rubric Generation for Improving LLM Judge and Reward Modeling for Open-ended Tasks [[arxiv 2602.05](https://arxiv.org/abs/2602.05125)]

**(2) Unweighted aggregation**
- AUTORULE : Reasoning Chain-of-thought Extracted Rule-based Rewards Improve Preference Learning [[arxiv 2506.15](https://arxiv.org/abs/2506.15651)] [[code](https://github.com/cxcscmu/AutoRule)]
- RubricRL: Simple Generalizable Rewards for Text-to-Image generation [[arxiv 2511.20](https://arxiv.org/abs/2511.20651)]

**(3) Aggregation between Rule RMs and Rubric RMs**
- VERIF: Verification Engineering for Reinforcement Learning in Instruction Following [[EMNLP 2025](https://aclanthology.org/2025.emnlp-main.1542/)] [[code](https://github.com/THU-KEG/VerIF)]
- Checklists Are Better Than Reward Models For Aligning Language Models [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/a6837c1dd021f76f1b4098e3722052a8-Abstract-Conference.html)] [[code](https://github.com/viswavi/RLCF)]
- Orcust: Stepwise-Feedback Reinforcement Learning for GUI Agent [[arxiv 2509.17](https://arxiv.org/abs/2509.17917)]

#### Structure-aware Aggregation
> Structure-aware Aggregation captures non-linear trade-offs or hierarchical dependencies between criteria

- Reinforcement Learning with Rubric Anchors [[arxiv 2508.12](https://arxiv.org/abs/2508.12790)]
- Advancedif: Rubric-Based Benchmarking and Reinforcement Learning for Advancing LLM Instruction Following [[arxiv 2511.10](https://arxiv.org/abs/2511.10507)]
- Reward and Guidance through Rubrics: Promoting Exploration to Improve Multi-Domain Reasoning [[arxiv 2511.12](https://arxiv.org/abs/2511.12344)]

### Implicit Aggregation
> By leveraging LLM in-context learning, Implicit Aggregation bypasses item-by-item scoring to directly output a final scalar signal through internal reasoning over the entire rubric

- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)]
- SALMON: SELF-ALIGNMENT WITH INSTRUCTABLE REWARD MODELS [[ICLR 2024](https://proceedings.iclr.cc/paper_files/paper/2024/hash/dda5cac5272a9bcd4bc73d90bc725ef1-Abstract-Conference.html)] [[code](https://github.com/ibm/salmon)]
- Curing Miracle Steps in LLM Mathematical Reasoning with Rubric Rewards [[arxiv 2510.07](https://arxiv.org/abs/2510.07774)]
- Kimi K2: Self-Critique Rubric Reward for Open-Ended Alignment [[arxiv 2507.20](https://arxiv.org/abs/2507.20534)]
- OpenRubrics: Towards Scalable Synthetic Rubric Generation for Reward Modeling and LLM Alignment [[arxiv 2510.07](https://arxiv.org/abs/2510.07743)]

---
## Optimization：Rubric RMs are used to support downstream policy learning and decision-making
> Compared with traditional data synthesis methods, rubrics enable more fine-grained quality control over both synthesized outputs and their intermediate construction processes via structured criteria

### Data Synthesis 
- RubricHub: A Comprehensive and Highly Discriminative Rubric Dataset via Automated Coarse-to-Fine Generation [[arxiv 2601.08](https://arxiv.org/abs/2601.08430)] [[code](https://github.com/teqkilla/RubricHub)]
- OptimSyn: Influence-Guided Rubrics Optimization for Synthetic Data Generation [[ICLR 2026](https://openreview.net/forum?id=vFcm5sOitq)]
- Kardia-R1: Unleashing LLMs to Reason toward Understanding and Empathy for Emotional Support via Rubric-as-Judge Reinforcement Learning [[arxiv 2512.01](https://arxiv.org/abs/2512.01282)] [[code](https://github.com/JhCircle/Kardia-R1)]
- Robust Reward Modeling via Causal Rubrics [[ICLR 2026](https://openreview.net/forum?id=oP99JQiDYp)]
- Configurable preference tuning with rubric-guided synthetic data [[ICML 2025 Workshop](https://openreview.net/forum?id=seA8en4ujl)]
- ARISE: Agentic Rubric-Guided Iterative Survey Engine for Automated Scholarly Paper Generation [[arxiv 2511.17](https://arxiv.org/abs/2511.17689)] [[code](https://github.com/ziwang11112/ARISE)]

### Policy Learning
> Compared with traditional outcome feedback, rubric-based supervision provides denser and more structured learning signals, allowing models to more precisely identify which behaviors should be reinforced

**(1) Outcome-level**
- Learning Query-Specific Rubrics from Human Preferences for DeepResearch Report Generation [[arxiv 2602.03](https://arxiv.org/abs/2602.03619)]
- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- Compute as Teacher: Turning Inference Compute Into Reference-Free Supervision [[NeurIPS 2025 Spotlight](https://openreview.net/forum?id=wV4XfwHywy)] 
- RubricRL: Simple Generalizable Rewards for Text-to-Image generation [[arxiv 2511.20](https://arxiv.org/abs/2511.20651)]
- Deepseek-v4: Towards  highly efficient million-token context intelligence [[Tech Report](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf)]
- Inference-Time Scaling for Generalist Reward Modeling [[arxiv 2504.02](https://arxiv.org/abs/2504.02495)]

**(2) Process-level**
- Chaining the Evidence: Robust Reinforcement Learning for Deep Search Agents with Citation-Aware Rubric Rewards [[arxiv 2601.06](https://arxiv.org/abs/2601.06021)] [[code](https://github.com/THUDM/CaRR)]
- Evaluating Legal Reasoning Traces with Legal Issue Tree Rubrics [[arxiv 2512.01](https://arxiv.org/abs/2512.01020)] [[code](https://github.com/jinulee-v/LEGIT)]
- Reinforcing Chain-of-Thought Reasoning with Self-Evolving Rubrics [[arxiv 2602.10](https://arxiv.org/abs/2602.10885)]
- Curing Miracle Steps in LLM Mathematical Reasoning with Rubric Rewards [[arxiv 2510.07](https://arxiv.org/abs/2510.07774)]

**(3) Token-level**
- Rubrics to Tokens: Bridging Response-level Rubrics and Token-level Rewards in Instruction Following Tasks [[arXiv 202604.02](https://arxiv.org/abs/2604.02795)] [[Code](https://github.com/TURLEing/Rubrics-To-Tokens)]

**(4) Self-rewarding mechanism**
- EvoLM: Self-Evolving Language Models through Co-Evolved Discriminative Rubrics [[arXiv 202605.03](https://arxiv.org/abs/2605.03871)]
- Self-Rewarding Rubric-Based Reinforcement Learning for Open-Ended Reasoning [[arXiv 202509.25](https://arxiv.org/abs/2509.25534)]


**(5) Rubric-guided exploration**
- Breaking the Exploration Bottleneck: Rubric-Scaffolded Reinforcement Learning for General LLM Reasoning [[arXiv 202508.16](https://arxiv.org/abs/2508.16949)] [[Code](https://github.com/IANNXANG/RuscaRL)]
- Reward and Guidance through Rubrics: Promoting Exploration to Improve Multi-Domain Reasoning [[arxiv 2511.12](https://arxiv.org/abs/2511.12344)]
- Experience is the Best Teacher: Motivating Effective Exploration in Reinforcement Learning for LLMs [[arXiv 202603.20](https://arxiv.org/abs/2603.20046)] [[Code](https://github.com/sikelifei/HeRL)]
- Think-with-Rubrics: From External Evaluator to Internal Reasoning Guidance [[arXiv 202605.07](https://arxiv.org/abs/2605.07461)]

### Inference-time Verification
> At inference time, Rubric RMs can serve as a verifier to directly improve policy outputs, extending its role beyond training supervision into inferencetime decision making

- Inference-Time Scaling of Verification: Self-Evolving Deep Research Agents via Test-Time Rubric-Guided Verification [[arXiv 202601.15](https://arxiv.org/abs/2601.15808)] [[Code](https://github.com/yxwan123/DeepVerifier)]
- Agentic Rubrics as Contextual Verifiers for SWE Agents [[arxiv 2601.04](https://arxiv.org/abs/2601.04171)]

### Domain Adaptation
> Rubrics are naturally applicable across a wide range of domains because many downstream tasks rely on implicit expert knowledge that cannot be easily captured by preference signals

**Open-ended Chat**
- QuRL: Rubrics As Judge For Open-Ended Question Answering [[ICLR 26](https://openreview.net/forum?id=DrhWTuhtYq)]
- Auto-Rubric as Reward: From Implicit Preferences to Explicit Multimodal Generative Criteria [[arXiv 2605.08](https://arxiv.org/abs/2605.08354)] [[code](https://github.com/OpenEnvision/AutoRubric-as-Reward)]
- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)]
- ACE-RL: Adaptive Constraint-Enhanced Reward for Long-form Generation Reinforcement Learning [[arxiv 2509.04](https://arxiv.org/abs/2509.04903)]

**Medicine**
- HealthBench: Evaluating Large Language Models Towards Improved Human Health [[arxiv 2505.08](https://arxiv.org/abs/2505.08775)] [[resource](https://github.com/openai/simple-evals)]
- InfiMed-ORBIT: Aligning LLMs on Open-Ended Complex Tasks via Rubric-Based Incremental Training [[arxiv 2510.15](https://arxiv.org/abs/2510.15859)]
- Multidimensional Rubric-oriented Reward Model Learning via Geometric Projection Reference Constraints [[arxiv 2511.16](https://arxiv.org/abs/2511.16139)]

**Law**
- Evaluating Legal Reasoning Traces with Legal Issue Tree Rubrics [[arxiv 2512.01](https://arxiv.org/abs/2512.01020)] [[code](https://github.com/jinulee-v/LEGIT)]

**Science**
- Training AI Co-Scientists Using Rubric Rewards [[arxiv 2512.23](https://arxiv.org/abs/2512.23707)]

**Mathematics**
- Curing Miracle Steps in LLM Mathematical Reasoning with Rubric Rewards [[arxiv 2510.07](https://arxiv.org/abs/2510.07774)]

**Coding**
- Agentic Rubrics as Contextual Verifiers for SWE Agents [[arxiv 2601.04](https://arxiv.org/abs/2601.04171)]
- AdaRubric: Task-Adaptive Rubrics for LLM Agent Evaluation [[arxiv 2603.21](https://arxiv.org/abs/2603.21362)] [[code](https://github.com/alphadl/AdaRubrics)]

**Multimodal**
- Auto-Rubric as Reward: From Implicit Preferences to Explicit Multimodal Generative Criteria [[arXiv 2605.08](https://arxiv.org/abs/2605.08354)] [[code](https://github.com/OpenEnvision/AutoRubric-as-Reward)]
- RubricRL: Simple Generalizable Rewards for Text-to-Image generation [[arxiv 2511.20](https://arxiv.org/abs/2511.20651)]
- RubiCap: Rubric-Guided Reinforcement Learning for Dense Image Captioning [[arxiv 2603.09](https://arxiv.org/abs/2603.09160)]
- When Rubrics Fail: Error Enumeration as Reward in Reference-Free RL Post-Training for Virtual Try-On [[arXiv 202603.05](https://arxiv.org/abs/2603.05659)]
- Rationale matters: Learning transferable rubrics via proxy-guided critique for vlm reward models [[arXiv 202603.16](https://arxiv.org/abs/2603.16600)] [[Code](https://github.com/Qwen-Applications/Proxy-GRM)]
- Microverse: A preliminary exploration toward a micro-world simulation [[ICLR 26](https://openreview.net/forum?id=7pQv7qitFV)] [[Code](https://github.com/FreedomIntelligence/MicroVerse)]
- DeltaRubric: Generative Multimodal Reward Modeling via Joint Planning and Verification [[arXiv 202605.09](https://arxiv.org/abs/2605.09269)] [[code](https://deltarubric.github.io/)]

**DeepResearch**
- DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research [[arxiv 2511.19](https://arxiv.org/abs/2511.19399)] [[code](https://github.com/rlresearch/dr-tulu)]
- Step-DeepResearch Technical Report [[arxiv 2512.20](https://arxiv.org/abs/2512.20491)] [[code](https://github.com/stepfun-ai/StepDeepResearch)]
- Chaining the Evidence: Robust Reinforcement Learning for Deep Search Agents with Citation-Aware Rubric Rewards [[arxiv 2601.06](https://arxiv.org/abs/2601.06021)] [[code](https://github.com/THUDM/CaRR)]
- Learning Query-Specific Rubrics from Human Preferences for DeepResearch Report Generation [[arxiv 2602.03](https://arxiv.org/abs/2602.03619)]
- Inference-Time Scaling of Verification: Self-Evolving Deep Research Agents via Test-Time Rubric-Guided Verification [[arxiv 2605.09](https://arxiv.org/abs/2605.09269)]

---

<p align="center">
  <img src=".\img\dataset.png"  width="90%" />
</p>
<p align="center">Fig 4. Overview of Dataset</p>

## Dataset
> we summarize the high-quality data resources currently used for training, including datasets with explicit rubric and source datasets for extract criteria

### Rubric Datasets
- RubricHub: A Comprehensive and Highly Discriminative Rubric Dataset via Automated Coarse-to-Fine Generation [[arxiv 2601.08](https://arxiv.org/abs/2601.08430)] [[resource](https://huggingface.co/datasets/sojuL/RubricHub_v1)]
- Checklists Are Better Than Reward Models For Aligning Language Models [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/a6837c1dd021f76f1b4098e3722052a8-Abstract-Conference.html)] [[resource](https://huggingface.co/datasets/viswavi/wildchecklists)]
- OpenRubrics: Towards Scalable Synthetic Rubric Generation for Reward Modeling and LLM Alignment [[arxiv 2510.07](https://arxiv.org/abs/2510.07743)] [[resource](https://huggingface.co/OpenRubrics/datasets])]
- Rubrics as Rewards: Reinforcement Learning Beyond Verifiable domains [[NeurIPS 2025](https://openreview.net/forum?id=21UFlJrmS2)] [[resource RaR-Medicine](https://huggingface.co/datasets/anisha2102/RaR-Medicine)] [[resource RaR-Science](https://huggingface.co/datasets/anisha2102/RaR-Science)]
- Evaluating Legal Reasoning Traces with Legal Issue Tree Rubrics [[arxiv 2512.01](https://arxiv.org/abs/2512.01020)] [[resource](https://github.com/jinulee-v/LEGIT)]
- Auto-Rubric: Learning From Implicit Weights to Explicit Rubrics for Reward Modeling [[arxiv 2510.17](https://arxiv.org/abs/2510.17314)] [[resource](https://github.com/OpenEnvision/AutoRubric-as-Reward)]

### Source Datasets
- Wildchat: 1m chatGPT interaction logs in the wild [[ICLR 2024](https://openreview.net/forum?id=Bl8u7ZRlbM)] [[resource](https://wildchat.allen.ai/)]
- ULTRAFEEDBACK: Boosting language models with scaled AI feedback [[ICML 2024](https://openreview.net/forum?id=BOorDpKHiJ)] [[resource](https://github.com/OpenBMB/UltraFeedback)]
- Skyworkreward: Bag of tricks for reward modeling in llms [[arxiv 2410.18](https://arxiv.org/abs/2410.18451)] [[resource](https://huggingface.co/collections/Skywork/skywork-reward-model)]
- Helpsteer3-preference: Open human-annotated preference data across diverse tasks and languages [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/3e0271cf7df2cdb3b91565ad1f525f3a-Abstract-Datasets_and_Benchmarks_Track.html)] [[resource](https://huggingface.co/datasets/nvidia/HelpSteer3#preference)]
- Magpie: Alignment data synthesis from scratch by prompting aligned LLMs with nothing [[ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/be06e3802e9411381feece79b4d960c1-Abstract-Conference.html)] [[resource](https://huggingface.co/Magpie-Align)]
- Megascience: Pushing the frontiers of post-training datasets for science reasoning [[arxiv 2507.16](https://arxiv.org/abs/2507.16812)] [[resource](https://huggingface.co/MegaScience)]
- Longwriter: Unleashing 10,000+ word generation from long context LLMs [[ICLR 2025](https://openreview.net/forum?id=kQ5s9Yh0WI)] [[resource](https://github.com/THUDM/LongWriter)]
- Longwriterzero: Mastering ultra-long text generation via reinforcement learning https://arxiv.org/abs/2506.18841 [[resource](https://huggingface.co/THU-KEG/LongWriter-Zero-32B)]
- LongAlign: A recipe for long context alignment of large language models [[EMNLP 2024](https://aclanthology.org/2024.findings-emnlp.74/)] [[resource](https://github.com/THUDM/LongAlign)]
- LMSYS-chat-1m: A large-scale realworld LLM conversation dataset [[ICLR 2024](https://proceedings.iclr.cc/paper_files/paper/2024/hash/5f9bfdfe3685e4ccdbc0e7fb29cccf2a-Abstract-Conference.html)] [[resource](https://huggingface.co/datasets/lmsys/lmsys-chat-1m)]
- Huatuogpt-o1, towards medical complex reasoning with llms [[arxiv 2412.18](https://arxiv.org/abs/2412.18925)] [[resource](https://github.com/FreedomIntelligence/HuatuoGPT-o1)]
- Rlaif-v: Opensource ai feedback leads to super gpt-4v trustworthiness [[CVPR 2025](https://ieeexplore.ieee.org/abstract/document/11094064)] [[resource](https://huggingface.co/datasets/openbmb/RLAIF-V-Dataset)]
- Aligning large multimodal models with factually augmented RLHF [[ACL 2024](https://aclanthology.org/2024.findings-acl.775/)] [[resource](https://llava-rlhf.github.io/)]
- Lllava-critic: Learning to evaluate multimodal models [[CVPR 2025](https://www.computer.org/csdl/proceedings-article/cvpr/2025/436400n618/299915UseBi)] [[resource](https://llava-vl.github.io/blog/2024-10-03-llava-critic/)]
- MM-RLHF: The next step forward in multimodal LLM alignment [[ICML 2025](https://openreview.net/forum?id=ULJ4gJJYFp)] [[resource](https://mm-rlhf.github.io/)]
- Molmo and pixmo: Open weights and open data for stateof-the-art vision-language models [[CVPR 2025](https://openaccess.thecvf.com/content/CVPR2025/html/Deitke_Molmo_and_PixMo_Open_Weights_and_Open_Data_for_State-of-the-Art_CVPR_2025_paper.html)] [[resource](https://allenai.org/blog/molmo )]
- Densefusion-1m: Merging vision experts for comprehensive multimodal perception [[NeurIPS 2024](https://proceedings.neurips.cc/paper_files/paper/2024/hash/20ffc2b42c7de4a1960cfdadf305bbe2-Abstract-Datasets_and_Benchmarks_Track.html)] [[resource](https://huggingface.co/datasets/BAAI/DenseFusion-1M)]
- VL-rethinker: Incentivizing selfreflection of vision-language models with reinforcement learning [[NeurIPS 2025 Spotlight](https://proceedings.neurips.cc/paper_files/paper/2025/hash/2c84844a559e4f962752570bff456ae4-Abstract-Conference.html)] [[resource](https://github.com/TIGER-AI-Lab/VL-Rethinker)]

---

<p align="center">
  <img src=".\img\benchmark.png"  width="90%" />
</p>
<p align="center">Fig 5. Overview of reward model benchmarks, grouped into rubric-based and general reward benchmarks, and compared by evaluation paradigm, domain, modality, and scale.</p>

## Benchmark
> We review existing open-source benchmarks and evaluation methods for Rubric RMs evaluation from two perspectives: Rubric quality evaluates the model’s ability to generate accurate and useful rubrics, which directly measures the quality of rubrics. Rubric-guide judgment evaluates whether the generated rubric can support reliable reward modeling and downstream task judgment.

### Rubric Benchmarks
- RubricBench: Aligning Model-Generated Rubrics with Human Standards [[arxiv 2603.01](https://arxiv.org/abs/2603.01562)] [[resource](https://huggingface.co/datasets/DonJoey/rubricbench)]
- RUBRICEVAL: A Rubric-Level Meta-Evaluation Benchmark for LLM Judges in Instruction Following [[arxiv 2603.25](https://arxiv.org/abs/2603.25133)]
- HealthBench: Evaluating Large Language Models Towards Improved Human Health [[arxiv 2505.08](https://arxiv.org/abs/2505.08775)] [[resource](https://github.com/openai/simple-evals)]
- PaperBench: Evaluating AI's Ability to Replicate AI Research [[ICML 2025](https://openreview.net/forum?id=xF5PuTLPbn)] [[resource](https://github.com/openai/frontier-evals)]
- Xpertbench: Expert Level Tasks with Rubrics-Based Evaluation [[arxiv 2604.02](https://arxiv.org/abs/2604.02368)] [[resource](https://github.com/randomtutu/Xpertbench)]
- PresentBench: A Fine-Grained Rubric-Based Benchmark for Slide Generation [[arXiv 2603.07](https://arxiv.org/abs/2603.07244)] [[resource](https://github.com/PresentBench/PresentBench)]
- Profbench: Multidomain rubrics requiring professional knowledge to answer and judge [[arXiv 2510.17](https://arxiv.org/abs/2510.18941)] [[resource](https://huggingface.co/datasets/nvidia/ProfBench)]
- Generative judge for evaluating alignment [[ICLR 2024](https://proceedings.iclr.cc/paper_files/paper/2024/hash/747dc7c6566c74eb9a663bcd8d057c78-Abstract-Conference.html)] [[resource](https://github.com/GAIR-NLP/auto-j)]

### Reward Benchmarks
- RewardBench: Evaluating reward models for language modeling [[NAACL 2025](https://aclanthology.org/2025.findings-naacl.96/)] [[resource](https://huggingface.co/spaces/allenai/reward-bench)]
- Judging llm-as-a-judge with mtbench and chatbot arena [[NeurIPS 2023](https://proceedings.neurips.cc/paper_files/paper/2023/hash/91f18a1287b398d378ef22505bf41832-Abstract-Datasets_and_Benchmarks.html)] [[resource](https://github.com/lm-sys/FastChat/tree/main/fastchat/llm_judge)]
- RMbench: Benchmarking reward models of language models with subtlety and style [[ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/6da1eec80095dc5937f7716db15aca4b-Abstract-Conference.html)] [[resource](https://github.com/THU-KEG/RM-Bench)]
- Judgebench: A benchmark for evaluating LLM-based judges [[ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/9e720fce64f91114c49cfd640d821da3-Abstract-Conference.html)] [[resource](https://github.com/ScalerLab/JudgeBench)]
- M-RewardBench: Evaluating reward models in multilingual settings [[ACL 2025](https://aclanthology.org/2025.acl-long.3/)] [[resource](https://m-rewardbench.github.io/)]
- RAG-RewardBench: Benchmarking reward models in retrieval augmented generation for preference alignment [[ACL 2025](https://aclanthology.org/2025.findings-acl.877/)] [[resource](https://huggingface.co/datasets/jinzhuoran/RAG-RewardBench)]
- Evaluating robustness of reward models for mathematical reasoning [[arXiv 2410.01](https://arxiv.org/abs/2410.01729)] [[resource](https://huggingface.co/spaces/RewardMATH/RewardMATH_project)]
- Rewardbench 2: Advancing reward model evaluation [[arXiv 2506.01](https://arxiv.org/abs/2506.01937)] [[resource](https://huggingface.co/datasets/allenai/reward-bench-2)]
- Rmb: Comprehensively benchmarking reward models in llm alignment [[ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/427f20d90386fd27804f1831d6a3d48f-Abstract-Conference.html)] [[resource](https://github.com/Zhou-Zoey/RMB-Reward-Model-Benchmark)]
- How to evaluate reward models for RLHF [[ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/hash/2e01083b381b4865919b4915ef32e3d2-Abstract-Conference.html)] [[resource](https://github.com/lmarena/PPE)]
- Vl-rewardbench: a challenging benchmark for vision-language generative reward models [[CVPR 2025](https://openaccess.thecvf.com/content/CVPR2025/html/Li_VL-RewardBench_A_Challenging_Benchmark_for_Vision-Language_Generative_Reward_Models_CVPR_2025_paper.html)] [[resource](https://vl-rewardbench.github.io/)]
- Multimodal rewardbench: Holistic evaluation of reward models for vision language models [[arXiv 2502.14](https://arxiv.org/abs/2502.14191)] [[resource](https://github.com/facebookresearch/multimodal_rewardbench)]
- MJ-bench: Is your multimodal reward model really a good judge for text-to-image generation? [[NeurIPS 2025](https://proceedings.neurips.cc/paper_files/paper/2025/hash/59d2eaa5842fa641ff9b8e4c7ff0f6ee-Abstract-Datasets_and_Benchmarks_Track.html)] [[resource](https://huggingface.co/datasets/MJ-Bench/MJ-Bench)]

---



If the content of this repository is helpful to your research, please consider citing our papers:
```
@article{author2025seeing,
  title={Seeing the Forest and the Trees: A Survey of Analytic Rubrics for Holistic Reward Modeling in LLMs},
  author={Author1 and Author2},
  journal={arXiv preprint},
  year={2025}
}
```
This repository is continuously maintained by the paper's authors. Contributions of the latest Rubric RMs-related papers and resources are welcome!
