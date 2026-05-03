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



## From Models to Pipelines

While individual models are powerful, antibody discovery is inherently a multi-stage process. Effective computational design therefore requires integrating these components into coherent pipelines.

A typical workflow includes:
1. Sequence or structure generation
2. Structural validation
3. Antigen binding evaluation
4. Developability filtering
5. Iterative optimization

Different pipelines emphasize different starting points and design philosophies.

### Epitope-Targeted Antibody Design

---

**RFdiffusion → ProteinMPNN / AntiFold → IgFold → BioPhi**

This pipeline represents a structure first approach to antibody design, where the primary objective is to generate a binding geometry compatible with a specific antigen epitope.

The process begins with RFdiffusion, which generates antibody backbone structures conditioned on antigen geometry. By reversing a learned noise process, the model produces candidate structures whose CDR loops are oriented toward the target epitope.

Because these structures lack amino acid sequences, inverse folding methods are used next. ProteinMPNN assigns sequences that stabilize the generated backbone by modeling residue interactions as a graph. Antibody specific models such as AntiFold can further refine these sequences by incorporating domain specific constraints.

The resulting sequences are then evaluated using IgFold, which predicts whether they fold into the intended structure. Candidates that deviate significantly are discarded.

Finally, BioPhi is used to assess developability, ensuring that sequences resemble human antibodies and are less likely to trigger immune responses.

This pipeline separates structure generation from sequence design, allowing each stage to be optimized independently while maintaining overall coherence.



### Joint Sequence–Structure Generation
---
**DiffAb → IgFold → AlphaFold-Multimer → BioPhi**

An alternative approach generates sequence and structure simultaneously.

DiffAb uses a diffusion based framework to model both amino acid identity and spatial configuration. By conditioning on antigen structure, it generates antibody candidates that are inherently compatible with the target.

Because generative models may introduce geometric inconsistencies, IgFold is used to refine predicted structures.

Subsequently, AlphaFold-Multimer evaluates the antibody–antigen complex, providing insight into binding interfaces and structural compatibility.

Developability filtering is again performed using BioPhi.

This approach captures the coupling between sequence and structure more directly, potentially enabling more coherent designs, while still requiring downstream validation.


### Sequence-Driven Library Generation
---
**Antibody Language Model → AntiBERTy → IgFold → BioPhi**

In contrast to structure-first approaches, this pipeline explores sequence space directly.

Generative language models produce large libraries of antibody sequences that resemble natural repertoires. These sequences are then embedded using AntiBERTy, allowing similarity-based filtering against known functional antibodies.

Promising candidates are structurally evaluated using IgFold, and filtered for developability using BioPhi.

This approach mirrors aspects of natural immune diversity generation, offering rapid exploration of sequence space without requiring antigen structural information.


### Computational Affinity Maturation
---
**Mutation Generation → Structural Evaluation → Binding Assessment → Filtering**

Even well-designed antibodies often require further optimization to achieve high binding affinity.

This pipeline introduces targeted mutations—primarily within CDR regions—to explore the local sequence landscape. Mutation strategies may be guided by inverse folding models, language models, or structural heuristics.

Each variant is re-evaluated structurally to ensure stability, followed by binding assessment using docking methods or structure-based scoring approaches. Developability filters are then applied to remove unfavorable candidates.

This iterative process is analogous to somatic hypermutation in the immune system, but implemented in a controlled computational framework.


### Toward Integrated Antibody Design Systems
---

Taken together, these pipelines illustrate a shift from isolated modeling tasks toward integrated computational systems. Rather than relying solely on experimental screening, these approaches aim to:
* reduce the search space
* prioritize high-quality candidates
* and accelerate early-stage discovery

At the same time, several challenges remain. The accuracy of binding predictions, the reliability of developability assessments, and the integration of experimental feedback all continue to influence real-world performance.

As the field evolves, the emphasis is likely to move further toward end-to-end systems that combine generative modeling, structural reasoning, and iterative optimization within unified frameworks.

## Conclusion

AI-driven antibody design is transitioning from a collection of individual tools to a structured, pipeline-oriented discipline. Sequence models, structure predictors, diffusion-based generators, and developability filters each address distinct aspects of the problem. When combined effectively, they offer a computational pathway from antigen to candidate antibody.

While experimental validation remains essential, these approaches have the potential to significantly accelerate discovery by guiding and constraining the search process. Understanding how these components interact—and how they can be assembled into coherent workflows—will be central to the next phase of progress in computational antibody engineering.
