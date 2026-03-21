# The Stack Behind AI-Driven Antibody Discovery

Therapeutic antibodies have become one of the most successful classes of modern biologics, with applications spanning oncology, autoimmune diseases, and infectious disorders. Yet despite their success, antibody discovery remains a slow, expensive, and experimentally intensive process.

Traditional approaches—such as immunization, phage display, and hybridoma screening—have been highly effective, but fundamentally limited. These methods explore only a tiny fraction of the vast antibody sequence space. Even a single complementarity-determining region (CDR), such as CDR-H3, can adopt an enormous number of possible sequences and conformations. Exhaustively searching this space experimentally is not feasible.

Recent advances in artificial intelligence are beginning to reshape this landscape. A rapidly growing ecosystem of models now enables the generation of antibody sequences, prediction of structures, modeling of antigen interactions, and evaluation of developability properties. Individually, these models address specific sub-problems in antibody engineering. Increasingly, however, the focus is shifting toward integrating them into cohesive pipelines that can move from antigen target to candidate antibody in a largely computational workflow.

This article presents a structured overview of the AI stack for antibody design and examines how these components can be assembled into end-to-end computational pipelines.

AI systems for antibody engineering can be broadly organized into four functional categories:

1. Sequence generation models
2. Structure prediction models
3. Antigen-specific design models
4. Developability and humanization models

Each category corresponds to a distinct stage in the antibody discovery process.

## Sequence Generation Models

Antibody function is encoded primarily in its amino acid sequence, particularly within the six CDRs that form the antigen-binding site. Generating plausible and diverse sequences is therefore a foundational step in computational antibody design.

Recent protein language models have been trained on large-scale antibody datasets such as the Observed Antibody Space (OAS), enabling them to learn statistical patterns underlying immune repertoires.

Early models such as **AB-Gen** focus on targeted generation of regions like CDR-H3, often incorporating reinforcement learning to bias outputs toward desirable properties such as solubility or reduced immunogenicity.

Representation-focused models such as **AntiBERTy**, **AbLang**, and **PARA** generate high-dimensional embeddings that capture structural and functional features of antibody sequences. These embeddings are widely used for clustering, ranking, and downstream prediction tasks.

Generative transformer-based models, including **IgLM**, **IgBERT**, and **AbGPT**, extend this paradigm by enabling full-sequence generation or masked infilling. These models learn repertoire-level distributions such as germline usage, CDR length variability, and residue preferences.

More recent approaches move beyond pure sequence modeling:

* **AbBFN2** introduces a Bayesian Flow Network–based framework capable of jointly modeling sequence, genetic attributes, and developability properties within a single generative system. This enables conditional and multi-objective antibody design without retraining.
* **IgCraft** and related models extend generative capabilities to structured design tasks such as CDR grafting and constrained generation.

In parallel, diffusion-based approaches such as **DiffAb**, **AbDiffuser**, and **AbX** provide an alternative paradigm. These models iteratively denoise random inputs to generate sequences (and often structures), enabling exploration of antibody space with built-in geometric consistency.

## Structure Prediction Models

Following sequence generation, accurate prediction of three dimensional structure is essential. Antibody binding is inherently dependent on spatial geometry, particularly within flexible CDR loops.

General purpose protein folding models such as **AlphaFold2** have demonstrated near experimental accuracy across a wide range of proteins, significantly advancing structure prediction capabilities. 

Antibody specific models further refine this capability:

* **IgFold** combines antibody language model embeddings with SE(3)-equivariant neural networks to rapidly predict Fv structures.
* **ABodyBuilder3** incorporates AlphaFold-inspired architectures with antibody-specific priors to improve loop modeling.
* **DeepAb** and similar CNN-based models provide earlier approaches tailored to antibody structures.

More recent developments aim to improve both accuracy and efficiency:

* **xTrimoABFold** introduces an end-to-end architecture integrating antibody language models with efficient structural modules, achieving significant improvements in RMSD while being substantially faster than AlphaFold2.
* Emerging methods increasingly leverage pretrained language representations to reduce reliance on multiple sequence alignments (MSAs), enabling faster and more scalable predictions.

Structure prediction models act as a critical validation layer, ensuring that generated sequences correspond to physically plausible conformations.



## Antigen-Specific Antibody Design

Designing antibodies that bind a specific antigen requires explicitly modeling the interaction between antibody and antigen structures.

Diffusion-based models such as **DiffAb** generate antibody sequences and structures conditioned on antigen geometry. These models jointly model residue identity, position, and orientation, capturing the coupling between sequence and structure.

Graph-based approaches such as **AbDockGen** generate antibody binding sites by explicitly modeling geometric complementarity between paratope and epitope.

More general protein design frameworks extend these ideas:

* **RFdiffusion** generates protein backbones conditioned on spatial constraints, enabling de novo design of binding interfaces.
* **ProteinMPNN** and **AntiFold** perform inverse folding, assigning sequences to generated structures while preserving stability.

Recent work has also begun incorporating antigen flexibility:

* **dyAb** introduces a flow-matching framework that accounts for conformational changes in antigens during binding, improving the realism of antibody–antigen interaction modeling.

Evaluation of antigen binding is increasingly treated as a complex modeling problem:

* **AlphaFold-Multimer** and **AF2Complex** predict antibody–antigen complexes and provide confidence metrics for interaction interfaces.
* Benchmarking frameworks such as **AbBiBench** emphasize evaluating antibody–antigen complexes as functional units rather than isolated sequences, highlighting the importance of structure-conditioned modeling.

These approaches collectively shift antibody design from sequence generation toward interaction-aware modeling.


## Developability and Humanization

Beyond binding, therapeutic antibodies must satisfy a range of developability constraints, including stability, solubility, immunogenicity, and manufacturability.

Tools such as **BioPhi** provide humanization capabilities by comparing candidate sequences to human antibody repertoires and suggesting mutations that reduce immunogenicity risk while preserving structural features.

Recent models extend developability prediction into integrated frameworks:

* **AbBFN2** jointly models sequence, humanness, and developability attributes, enabling simultaneous optimization of multiple properties within a unified generative process. 
* Language model–based approaches increasingly predict properties such as aggregation propensity, expression yield, and thermal stability directly from sequence.

Additionally, emerging predictive models such as **AbAffinity** use large language model architectures to estimate binding affinity, providing complementary signals for candidate prioritization.

Developability assessment remains a critical filtering stage, ensuring that computationally designed antibodies are viable for downstream experimental validation and clinical development.



## The Need for Integrated Pipelines

Individually, each tool performs a specialized task. But antibody discovery requires multiple stages:

1. sequence generation
2. structure prediction
3. antigen binding evaluation
4. developability screening
5. optimization



### Epitope-Targeted Antibody Design
---
#### RFdiffusion → ProteinMPNN/AntiFold → IgFold → BioPhi

#### Scientific rationale

This pipeline embodies the most ambitious goal in computational antibody engineering: **designing a functional antibody from scratch that binds a specific antigen epitope**. Historically, this task required immunization or extremely large library screens. Those experimental approaches rely on the immune system’s natural evolutionary process—generating billions of B-cell variants and selecting those that bind the target antigen.

AI-driven design attempts to replace that enormous biological search with **guided computational generation**.

Antibody binding is dominated by the six **complementarity-determining regions (CDRs)**, which form the antigen-binding site (paratope). Among these, the CDR-H3 loop contributes the largest share of binding specificity. The geometric arrangement of these loops determines whether the antibody can physically complement the antigen surface. The key idea behind this pipeline is therefore simple in principle but difficult in practice: **Design a protein structure whose CDR loops geometrically complement a chosen antigen epitope.**
Once that structure exists, sequence design algorithms can assign amino acids that stabilize it. 

This pipeline therefore separates antibody design into three major computational problems:

1. **Generate a backbone structure compatible with antigen binding**
2. **Design a sequence that folds into that backbone**
3. **Evaluate structural stability and developability**

---

#### Step 1 — RFdiffusion: generating antibody backbones

The pipeline begins with **RFdiffusion**, a diffusion-based generative model derived from the RoseTTAFold architecture. Diffusion models generate structures by reversing a noise process. During training, real protein structures are gradually corrupted by noise. The neural network learns to reverse this process by predicting how to denoise the structure step by step. When generating new proteins, the model starts with random noise and progressively converts it into a plausible protein backbone.

In the antibody context, the diffusion process is **conditioned on antigen geometry** (Motif scaffolding/Topology constraints/Spatial conditioning). The model receives structural information describing the epitope surface and is trained to generate protein backbones whose CDR loops orient toward that surface.

This allows RFdiffusion to produce antibody structures that complement antigen surfaces. Experiments have demonstrated that RFdiffusion can design antibodies targeting viral proteins, toxins, and other biological targets with extremely accurate binding geometries. Importantly, RFdiffusion generates only backbone coordinates, not sequences. The output is therefore a geometric template for the antibody variable region. The reason for separating backbone and sequence generation is: **protein structure constrains sequence but not vice versa**. A given backbone geometry restricts which amino acids can occupy each position due to steric and energetic constraints. Thus, the pipeline next performs inverse folding.

#### Step 2.1 — ProteinMPNN: sequence design via inverse folding

ProteinMPNN is a graph neural network trained to solve the **inverse protein folding problem**. In classical protein folding, we attempt to predict structure from sequence. In inverse folding, the input is the structure, and the task is to determine which amino acid sequence would stabilize that structure.

ProteinMPNN treats the protein backbone as a graph where residues are nodes and spatial relationships are edges. Message-passing neural networks propagate information across the structure, allowing the model to choose residues that stabilize the overall fold.

In the context of antibody design, ProteinMPNN performs two important tasks:  
• stabilizing the CDR loops generated by RFdiffusion  
• ensuring that the framework regions resemble natural antibody structures

Inverse folding is critical because diffusion-generated backbones lack side chains. Without sequence assignment, the structure cannot be expressed as a protein.


#### Step 2.2 — AntiFold: inverse folding


AntiFold is an antibody-specific inverse folding model trained on antibody structures. Unlike general protein design models, AntiFold incorporates **antibody-specific structural constraints**, such as conserved framework residues and canonical CDR loop conformations.
This allows it to propose mutations that preserve antibody folding while potentially improving binding interactions. 


#### Step 3 — IgFold: structure validation

Even if a sequence is predicted to stabilize a backbone, it is essential to verify that the sequence actually folds into the desired structure. This step is performed by **IgFold**, a deep learning model specialized for antibody structure prediction.

IgFold combines embeddings from antibody language models with SE(3)-equivariant neural networks to predict antibody variable region structures quickly and accurately. In the pipeline, IgFold acts as a consistency check. If the predicted structure deviates significantly from the diffusion-generated backbone, the candidate antibody is rejected. This step is analogous to folding validation in classical protein design pipelines.


#### Step 4 — BioPhi: developability filtering

Finally, candidate antibodies must be evaluated for developability. Even if an antibody binds its target strongly, it may fail due to:

• immunogenicity  
• aggregation  
• poor stability  
• manufacturing challenges

BioPhi addresses this problem by comparing candidate sequences to large human antibody repertoires. The tool’s Sapiens model predicts mutations that increase “humanness” while preserving structural integrity. This step ensures that AI-designed antibodies resemble natural antibodies and therefore have a higher chance of being tolerated by the immune system.


This approach has already been validated experimentally. In RFdiffusion studies, antibodies designed entirely computationally were expressed and experimentally shown to bind target epitopes with atomic-level accuracy confirmed by cryo-electron microscopy.

---

#### Alternative — Joint Sequence-Structure Antibody Generation - DiffAb → IgFold → AlphaFold-Multimer → BioPhi

Instead of generating structure first and sequence second, generate both together in a unified probabilistic model. This can be achived using DiffAb, a diffusion model that generates antibody sequences and structures simultaneously.

DiffAb is a generative model specifically designed for antibody design. It uses diffusion probabilistic models combined with equivariant neural networks to generate antibody CDR loops conditioned on antigen structure. The model operates on three types of variables simultaneously:

• amino acid identity  
• residue position  
• residue orientation

During training, both sequences and structures are corrupted with noise. The neural network learns to reverse this process and recover the original antibody–antigen complexes. At generation time, the model starts from random noise and gradually reconstructs a new antibody binding site compatible with the antigen. This approach has several advantages over sequential pipelines.

First, it captures the coupling between sequence and structure. Certain residues prefer particular backbone conformations, and the model learns these correlations.

Second, the diffusion process naturally explores multiple candidate binding geometries, enabling diversity in antibody designs.


Although DiffAb generates both sequence and structure, the generated models can still contain geometric inaccuracies. IgFold is therefore used to refine the antibody structure. The reason this step is necessary lies in the difference between generative models and predictive models.

The final stage evaluates whether the designed antibody actually binds the antigen. AlphaFold-Multimer predicts protein complexes and estimates interaction interfaces. In this pipeline, AlphaFold-Multimer predicts the antibody–antigen complex structure and provides confidence scores. This step approximates the thermodynamics of binding. 
Binding affinity arises from multiple molecular interactions:  
• hydrogen bonds  
• electrostatic interactions  
• hydrophobic contacts  
• shape complementarity  



### Sequence Only Antibody Library Generation
---
**Antibody Language Model → AntiBERTy → IgFold → BioPhi**

#### Scientific rationale

Unlike structure-based pipelines, this pipeline explores **sequence space first**. The human immune system generates enormous antibody diversity through **V(D)J recombination**, junctional diversity, and somatic hypermutation. Exploring this space experimentally is impossible. Large antibody language models attempt to learn the statistical patterns of antibody repertoires and generate plausible sequences.


#### Step 1 — Generative antibody language model

Generative models such as **AB-Gen, IgLM, and IgBERT** are trained on millions of antibody sequences from databases like Observed Antibody Space. These models learn patterns such as:  
• germline gene usage  
• CDR length distributions  
• conserved framework motifs

During generation, the model samples sequences that resemble natural antibodies but may not exist in nature. This allows exploration of enormous regions of sequence space.

#### Step 2 — AntiBERTy embedding analysis

AntiBERTy generates embeddings representing antibody sequences in a high-dimensional space.
Sequences that cluster near known functional antibodies are considered more likely to bind antigens. Embedding analysis is therefore used to filter large sequence libraries.


#### Step 3 — Structural prediction

IgFold predicts structures for promising sequences. Because language models generate sequences without structural constraints, many sequences may not fold correctly. Structure prediction removes these invalid designs.

#### Step 4 — Developability filtering

BioPhi evaluates humanness and immunogenicity risk. Sequences that diverge significantly from human antibody repertoires are removed.



This pipeline mirrors **natural immune repertoire generation**, where large numbers of antibodies are generated before antigen selection. The difference is that AI models generate sequences computationally rather than through biological recombination. The pipeline provides few advantages such as:  
• Extremely large sequence exploration  
• Requires no antigen structure  
• Fast generation  

Although structural and developability factors needs to be looked at very closely.

### Affinity Maturation
---

**Mutation Generation → Structural Evaluation → Binding Optimization**

#### Scientific rationale

This pipeline represents the computational analogue of one of the most fundamental processes in adaptive immunity: affinity maturation.

In the biological immune system, antibody producing B-cells undergo a highly specialized evolutionary process in which mutations are introduced into their variable regions, particularly within the CDRs. These mutations, generated through somatic hypermutation, create a diverse population of antibody variants that are subjected to stringent selection pressure in germinal centers. Only those variants that exhibit improved binding to the target antigen are retained and expanded, leading to a progressive refinement of binding affinity and specificity over successive cycles. This process is essential for producing high-affinity antibodies capable of effective neutralization.

Computational affinity maturation attempts to replicate this evolutionary mechanism in silico, transforming it into a structured optimization problem. Instead of relying on stochastic biological processes, the pipeline introduces controlled mutations, evaluates their structural and energetic consequences, and iteratively refines antibody candidates. This reframing is necessary because even the most advanced generative models—whether diffusion-based or sequence-based—typically produce initial binders that are structurally plausible but not yet optimal in terms of binding strength. Achieving therapeutic-grade affinity therefore requires systematic exploration of the local sequence landscape surrounding a candidate antibody.

From a computational perspective, this pipeline integrates methods from structural biology, statistical learning, and molecular modeling to navigate the complex relationship between sequence, structure, and binding. Established frameworks such as Rosetta-based antibody design demonstrate that simultaneous sampling of sequence, structure, and binding space can effectively identify mutations that improve affinity while maintaining structural integrity


#### Step 1 — Seed Antibody Input

The pipeline begins with a starting antibody, which may be derived from experimental discovery, prior computational design pipelines, or curated antibody libraries. Regardless of its origin, the antibody must have a reliable structural representation, either experimentally determined or predicted using structure prediction tools. Affinity maturation operates directly on the antibody–antigen interface, where binding interactions are governed by precise spatial arrangements of residues. Even small perturbations in this interface can significantly alter binding energetics. When available, an explicit antibody–antigen complex structure provides the most informative starting point, as it allows direct evaluation of interaction geometry and contact residues.

#### Step 2 — Mutation Generation (In Silico Somatic Hypermutation)

The next step introduces mutations into the antibody sequence, primarily in CDR loops (H1–H3, L1–L3) or interface residues. This step mimics **somatic hypermutation** in a controlled and targeted manner. The computational mutation generation is guided by structural and statistical constraints. Mutations are primarily focused on the CDR loops and interface residues, as these regions contribute most directly to antigen recognition and binding.

Structure-aware approaches such as ProteinMPNN, AntiFold, and ESM-IF1 use the three-dimensional backbone of the antibody to propose amino acid substitutions that are compatible with local structural environments. These models effectively solve the inverse folding problem, ensuring that proposed mutations do not destabilize the protein while still allowing functional variation. While language model based approaches such as IgLM and AntiBERTy explore sequence space using learned representations of antibody repertoires, biasing mutations toward biologically plausible and evolutionarily consistent patterns. The idea is to do mutations based on:  
* biased toward structurally valid residues
* focused on binding interface regions
* constrained by antibody-specific priors

#### Step 3 — Structural Re-evaluation

Following mutation, each candidate sequence must be re-evaluated to ensure that it folds correctly and maintains the desired binding geometry. This step is critical because even single point mutations can introduce conformational changes, particularly in flexible CDR loops, that disrupt binding. Structure prediction tools such as IgFold, AlphaFold2, and AlphaFold-Multimer are used to assess whether the mutated antibody retains its structural integrity. These models provide high resolution predictions of antibody conformations and, in the case of multimeric prediction, can also evaluate the consistency of the antibody–antigen complex. The goal of this step is not only to confirm that the antibody remains stable but also to ensure that the spatial arrangement of binding residues is preserved.

#### Step 4 — Binding Affinity Evaluation

This step estimates whether mutations improve antigen binding.

1. Structure-based scoring: Rosetta InterfaceAnalyzer
2. Docking-based evaluation: HADDOCK or ClusPro
3. Deep learning-based affinity prediction: AlphaFold-Multimer

#### Step 5 — Developability Filtering
This step incorporates predictive tools that assess structural stability, aggregation risk, and humanness.


## The Future of AI Antibody Discovery


