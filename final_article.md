# Computational Pipeline for Antibody discovery

## From generative language models to full end-to-end discovery pipelines
Therapeutic antibodies dominate modern biologics. Over a hundred antibody drugs are approved globally and they treat diseases ranging from cancer to autoimmune disorders. The challenge is that discovering a successful antibody is still slow, expensive, and experimentally intensive.

Traditional antibody discovery relies on immunization, phage display, or hybridoma screening. These methods work, but they explore only a tiny fraction of the possible antibody sequence space. The theoretical space of antibody sequences is astronomically large, far beyond what laboratory screening alone can search.

Artificial intelligence is now beginning to change this landscape. In the last five years, deep learning has produced a diverse ecosystem of computational tools capable of generating antibody sequences, predicting structures, modeling antigen binding, and evaluating developability properties. These tools individually solve pieces of the antibody engineering puzzle. The real opportunity lies in **connecting them into automated pipelines** that move from antigen target to therapeutic candidate entirely in silico.

Such pipelines combine generative sequence models, structure predictors, diffusion-based protein design methods, docking algorithms, and developability predictors. When assembled correctly, they enable the design, evaluation, and optimization of antibodies in a continuous computational workflow.

This article presents a comprehensive overview of each stacks with their application and describes **detailed AI pipelines for antibody discovery**, illustrating how modern tools can be chained together to perform full computational antibody design.


## The AI Toolbox for Antibody Design

The antibody AI systems generally fall into four major classes:

1. **Sequence generation models**
2. **Structure prediction models**
3. **Antigen-binding design models**
4. **Developability and humanization models**

Each category solves a different part of the antibody engineering problem.

---

### Sequence Generation Models:

Antibodies are defined primarily by their amino acid sequence, especially the six complementarity determining regions (CDRs) that form the antigen-binding site. Generating novel sequences that resemble functional antibodies is therefore the first step in computational design. Recent advances in protein language models have enabled systems that learn statistical patterns from hundreds of millions of antibody sequences.

#### AB-Gen

AB-Gen is a transformer model trained on tens of millions of heavy-chain CDR3 sequences from the Observed Antibody Space database. It uses reinforcement learning to bias generation toward sequences with desirable properties such as improved solubility or reduced immunogenicity. The model focuses specifically on the CDRH3 loop.

#### AntiBERTy

AntiBERTy is a large protein language model trained on over 500 million antibody sequences. Rather than generating sequences directly, it produces high-dimensional embeddings representing antibody sequence features. These embeddings capture patterns related to affinity maturation and binding specificity, making the model useful for clustering, ranking, and feature extraction.

#### IgBERT and IgT5

These models are massive transformer architectures trained on billions of antibody sequences. Their size allows them to model the statistical landscape of antibody repertoires with high fidelity. They can predict sequence recovery, expression levels, and sometimes binding potential.

#### IgLM

IgLM is an infilling language model that can complete missing regions of an antibody sequence. If a framework sequence is provided with masked CDR loops, the model generates plausible loop sequences consistent with the rest of the antibody.

#### IgCraft

IgCraft represents a newer generation of antibody design models using Bayesian flow networks. It supports multiple design tasks simultaneously, including sequence generation, CDR grafting, and inverse folding.

### Diffusion models for antibody generation
---
Another powerful class of models uses **diffusion processes**. These models learn to generate protein structures by gradually denoising random noise into physically plausible structures.

Diffusion models are particularly useful for antibody design because they can jointly generate sequence and structure.

Examples include:

* DiffAb
* AbDiffuser
* AbX

Diffusion-based approaches have recently demonstrated the ability to generate antibody binding loops directly conditioned on antigen structure.



### Structure Prediction Models:
--- 

Once sequences are generated, the next step is predicting the three-dimensional structure of the antibody. Structural information is critical because antibody binding depends on precise spatial geometry between paratope and epitope.

#### IgFold

IgFold is one of the fastest antibody structure predictors available. It combines embeddings from antibody language models with SE(3)-equivariant graph neural networks to predict Fv structures in seconds.

#### ABodyBuilder3

ABodyBuilder3 builds on AlphaFold-style architectures and integrates language-model embeddings to improve CDR loop prediction accuracy.

Both tools provide high-quality structural predictions needed for downstream binding analysis.



### Antigen-Specific Antibody Design
---

Structure prediction alone does not guarantee antigen binding. For targeted design, models must consider the antigen.

#### DiffAb

DiffAb is a diffusion-based model that generates CDR sequences and structures conditioned on antigen structure.

#### AbDockGen

AbDockGen uses graph neural networks to generate antibody paratopes that dock to antigen epitopes.

#### RFdiffusion

RFdiffusion is currently one of the most powerful generative protein design methods. It generates entirely new protein structures that bind specific targets.

The method starts from random noise and iteratively refines structures that complement the antigen surface. ([bakerlab.org][2])

Recent work demonstrated that RFdiffusion can generate antibodies that bind target epitopes with near atomic precision. ([PMC][3])



### Developability and Humanization
---

Binding affinity alone does not make a therapeutic antibody. Drug candidates must also be:

* stable
* soluble
* non-immunogenic
* manufacturable

#### BioPhi

BioPhi provides tools for antibody humanization and humanness scoring.

Its Sapiens model suggests mutations that transform non-human antibodies into human-like sequences while preserving structure.

Other machine learning models can predict:

* aggregation propensity
* expression yield
* thermal stability

These filters ensure candidates are suitable for clinical development.

---

## The Need for Integrated Pipelines

Individually, each tool performs a specialized task. But antibody discovery requires **multiple stages**:

1. sequence generation
2. structure prediction
3. antigen binding evaluation
4. developability screening
5. optimization



### Pipeline 1 — Epitope-Targeted Antibody Design
---
#### RFdiffusion → ProteinMPNN/AntiFold → IgFold → BioPhi

#### Scientific motivation

This pipeline embodies the most ambitious goal in computational antibody engineering: **designing a functional antibody from scratch that binds a specific antigen epitope**. Historically, this task required immunization or extremely large library screens. Those experimental approaches rely on the immune system’s natural evolutionary process—generating billions of B-cell variants and selecting those that bind the target antigen.

AI-driven design attempts to replace that enormous biological search with **guided computational generation**.

Antibody binding is dominated by the six **complementarity-determining regions (CDRs)**, which form the antigen-binding site (paratope). Among these, the CDR-H3 loop contributes the largest share of binding specificity. The geometric arrangement of these loops determines whether the antibody can physically complement the antigen surface.

The key idea behind this Pipeline is therefore simple in principle but difficult in practice:

**Design a protein structure whose CDR loops geometrically complement a chosen antigen epitope.**

Once that structure exists, sequence design algorithms can assign amino acids that stabilize it.

This pipeline therefore separates antibody design into **three major computational problems**:

1. **Generate a backbone structure compatible with antigen binding**
2. **Design a sequence that folds into that backbone**
3. **Evaluate structural stability and developability**

---

#### Step 1 — RFdiffusion: generating antibody backbones

The pipeline begins with **RFdiffusion**, a diffusion-based generative model derived from the RoseTTAFold architecture.

Diffusion models generate structures by reversing a noise process. During training, real protein structures are gradually corrupted by noise. The neural network learns to reverse this process by predicting how to denoise the structure step by step. When generating new proteins, the model starts with random noise and progressively converts it into a plausible protein backbone.

In the antibody context, the diffusion process is **conditioned on antigen geometry**(Motif scaffolding/Topology constraints/Spatial conditioning). The model receives structural information describing the epitope surface and is trained to generate protein backbones whose CDR loops orient toward that surface.

This allows RFdiffusion to produce antibody structures that complement antigen surfaces. Experiments have demonstrated that RFdiffusion can design antibodies targeting viral proteins, toxins, and other biological targets with extremely accurate binding geometries. ([Nature][1])

Importantly, RFdiffusion generates only **backbone coordinates**, not sequences. The output is therefore a geometric template for the antibody variable region.

The reason for separating backbone and sequence generation is: **protein structure constrains sequence but not vice versa**. A given backbone geometry restricts which amino acids can occupy each position due to steric and energetic constraints.

Thus, the pipeline next performs inverse folding.



#### Step 2.1 — ProteinMPNN: sequence design via inverse folding

ProteinMPNN is a graph neural network trained to solve the **inverse protein folding problem**.

In classical protein folding, we attempt to predict structure from sequence. In inverse folding, the input is the **structure**, and the task is to determine which amino acid sequence would stabilize that structure.

ProteinMPNN treats the protein backbone as a graph where residues are nodes and spatial relationships are edges. Message-passing neural networks propagate information across the structure, allowing the model to choose residues that stabilize the overall fold.

In the context of antibody design, ProteinMPNN performs two important tasks:

• stabilizing the CDR loops generated by RFdiffusion
• ensuring that the framework regions resemble natural antibody structures

Inverse folding is critical because diffusion-generated backbones lack side chains. Without sequence assignment, the structure cannot be expressed as a protein.

Modern pipelines nearly always combine **RFdiffusion + ProteinMPNN** because the two methods complement each other: diffusion generates geometry, while inverse folding generates sequence. ([Nature][2])

#### Step 2.2 — AntiFold inverse folding

Once a modified backbone is generated, AntiFold predicts sequences compatible with that structure.

AntiFold is an antibody-specific inverse folding model trained on antibody structures. Unlike general protein design models, AntiFold incorporates **antibody-specific structural constraints**, such as conserved framework residues and canonical CDR loop conformations.

This allows it to propose mutations that preserve antibody folding while potentially improving binding interactions.


#### Step 3 — IgFold: structure validation

Even if a sequence is predicted to stabilize a backbone, it is essential to verify that the sequence actually folds into the desired structure. This step is performed by **IgFold**, a deep learning model specialized for antibody structure prediction.

IgFold combines embeddings from antibody language models with SE(3)-equivariant neural networks to predict antibody variable region structures quickly and accurately.

In the pipeline, IgFold acts as a **consistency check**. If the predicted structure deviates significantly from the diffusion-generated backbone, the candidate antibody is rejected. This step is analogous to folding validation in classical protein design pipelines.


#### Step 4 — BioPhi: developability filtering

Finally, candidate antibodies must be evaluated for **developability**. Even if an antibody binds its target strongly, it may fail as a drug due to:

• immunogenicity
• aggregation
• poor stability
• manufacturing challenges

BioPhi addresses this problem by comparing candidate sequences to large human antibody repertoires. The tool’s Sapiens model predicts mutations that increase “humanness” while preserving structural integrity. This step ensures that AI-designed antibodies resemble natural antibodies and therefore have a higher chance of being tolerated by the immune system.

---

This approach has already been validated experimentally. In RFdiffusion studies, antibodies designed entirely computationally were expressed and experimentally shown to bind target epitopes with atomic-level accuracy confirmed by cryo-electron microscopy. ([PubMed][3])


#### Alternative — Joint Sequence-Structure Antibody Generation - DiffAb → IgFold → AlphaFold-Multimer → BioPhi

Instead of generating structure first and sequence second, **generate both together** in a unified probabilistic model.
This can be achived using **DiffAb**, a diffusion model that generates antibody sequences and structures simultaneously.

DiffAb is a generative model specifically designed for antibody design. It uses diffusion probabilistic models combined with **equivariant neural networks** to generate antibody CDR loops conditioned on antigen structure. The model operates on three types of variables simultaneously:

• amino acid identity
• residue position
• residue orientation

During training, both sequences and structures are corrupted with noise. The neural network learns to reverse this process and recover the original antibody–antigen complexes. At generation time, the model starts from random noise and gradually reconstructs a new antibody binding site compatible with the antigen.

This approach has several advantages over sequential pipelines.

First, it captures the **coupling between sequence and structure**. Certain residues prefer particular backbone conformations, and the model learns these correlations.

Second, the diffusion process naturally explores **multiple candidate binding geometries**, enabling diversity in antibody designs.

DiffAb was the first deep learning model capable of explicitly generating antibodies targeting specific antigens. ([proceedings.neurips.cc][6])

Although DiffAb generates both sequence and structure, the generated models can still contain geometric inaccuracies. IgFold is therefore used to refine the antibody structure. The reason this step is necessary lies in the difference between **generative models and predictive models**.

The final stage evaluates whether the designed antibody actually binds the antigen. AlphaFold-Multimer predicts protein complexes and estimates interaction interfaces. In this pipeline, AlphaFold-Multimer predicts the antibody–antigen complex structure and provides confidence scores. This step approximates the **thermodynamics of binding**.

Binding affinity arises from multiple molecular interactions:

• hydrogen bonds
• electrostatic interactions
• hydrophobic contacts
• shape complementarity






### Pipeline 2 — CDR Loop Diversification
---

**AbDiffuser / AbX / Diffusion models → ProteinMPNN → IgFold**

#### Scientific rationale

The central scientific insight behind this pipeline comes from immunology: **most antibody binding diversity arises from mutations in the complementarity-determining regions (CDRs)** rather than the framework regions. These loops form the antigen-binding interface and determine both specificity and affinity.

Among the six loops (H1–H3, L1–L3), **CDR-H3 is the most diverse** and often contributes the majority of binding contacts. Natural antibody evolution therefore focuses mutations in these regions during **affinity maturation in germinal centers**, where B-cells accumulate somatic mutations that improve antigen binding.

The computational analogue of this biological process is **loop diversification**.

Instead of designing a completely new antibody, the pipeline begins with an existing scaffold and explores alternative CDR conformations that may improve binding.

This approach has two major advantages:

1. **Structural stability is preserved** because the antibody framework remains fixed.
2. **Search space is dramatically reduced**, allowing algorithms to focus on functionally relevant residues.

---

#### Step 1 — Diffusion-based CDR generation

The pipeline begins with a diffusion model specialized for antibody loop generation. Models such as **AbDiffuser, AbX, and IgDiff** generate CDR loop structures conditioned on the surrounding antibody framework and sometimes the antigen.

Diffusion models operate by learning to reverse a noise process applied to training data. In the case of antibody loops, the model learns the distribution of loop conformations observed in experimentally solved antibody structures.

During generation, the model begins with random noise and iteratively reconstructs loop geometries consistent with antibody structural constraints.

These models are typically **SE(3)-equivariant**, meaning their predictions are invariant to rotation and translation of the molecule in 3-D space. This property is critical because protein structures have no absolute orientation.

Recent work has shown that diffusion models can generate CDR loops with atomic-level accuracy while maintaining realistic backbone dihedral angle distributions. ([arXiv][1])

Some models go further and incorporate **physical and evolutionary constraints**, such as steric clashes or residue conservation patterns. ([researchgate.net][2])

---

#### Step 2 — Sequence assignment via ProteinMPNN

Once a candidate loop geometry is generated, the next challenge is assigning amino acid residues that stabilize that conformation.

This is accomplished using **ProteinMPNN**, a graph neural network trained on thousands of experimentally solved protein structures.

The model treats residues as nodes in a graph connected by spatial edges representing proximity. Message-passing allows the network to evaluate which amino acids best stabilize the local environment.

This step solves the **inverse protein folding problem**: determining sequence from structure.

For antibodies, this is particularly important because CDR loops often contain unusual conformations requiring specific residues (e.g., glycine or proline) to stabilize tight turns.

---

#### Step 3 — Structural validation with IgFold

The final step validates the structural stability of the design.

IgFold predicts antibody structures from sequence using deep learning models trained on antibody structural databases.

If the predicted structure matches the designed geometry, the candidate is considered structurally stable.

If not, the design is rejected.

---

This pipeline mirrors the natural **affinity maturation process**.

In the immune system:

1. Antibody frameworks remain largely unchanged.
2. CDR loops accumulate mutations.
3. B-cells expressing higher-affinity antibodies are selected.

The computational pipeline performs the same process but replaces biological selection with structural prediction and energy evaluation.
• Maintains antibody structural stability
• Efficiently explores binding-relevant sequence space
• Mimics natural immune evolution


#### References

• Antibody affinity maturation and CDR diversity.
• Structural role of CDR loops in antigen recognition.
• Diffusion models for antibody generation. ([PMC][3])
• SE(3) diffusion for antibody design. ([arXiv][1])
• Physics-guided antibody diffusion models. ([researchgate.net][2])



### Pipeline 5 — Sequence-Only Antibody Library Generation
**Antibody Language Model → AntiBERTy → IgFold → BioPhi**

#### Scientific rationale

Unlike structure-based pipelines, this pipeline explores **sequence space first**. The human immune system generates enormous antibody diversity through **V(D)J recombination**, junctional diversity, and somatic hypermutation. The total theoretical antibody sequence space is estimated to exceed **10¹³–10¹⁵ possible antibodies**. Exploring this space experimentally is impossible.

Large antibody language models attempt to learn the statistical patterns of antibody repertoires and generate plausible sequences.


#### Step 1 — Generative antibody language model

Generative models such as **AB-Gen, IgLM, and IgBERT** are trained on millions of antibody sequences from databases like Observed Antibody Space.

These models learn patterns such as:

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

---

This pipeline mirrors **natural immune repertoire generation**, where large numbers of antibodies are generated before antigen selection. The difference is that AI models generate sequences computationally rather than through biological recombination. The pipeline provides few advantages such as:

• Extremely large sequence exploration
• Requires no antigen structure
• Fast generation

Although structural and developability factors needs to be looked at very closely.






### Pipeline 8 — Affinity Maturation - TBD
### Pipeline 11 — Interface Engineering - TBD


Both are a part of the above pipeline and should be mentioned at the respective pipeline sections
### Pipeline 14 — Framework-Constrained Loop Generation
### Pipeline 15 — Structural Sequence Optimization




## The Future of AI Antibody Discovery



#### References


• Reviews of AI-driven antibody generation. ([PMC][3])
• Diffusion and generative models for antibody design. ([biorxiv.org][6])


[1]: https://arxiv.org/abs/2405.07622?utm_source=chatgpt.com "De novo antibody design with SE(3) diffusion"
[2]: https://www.researchgate.net/publication/383360200_Antibody_Design_Using_a_Score-based_Diffusion_Model_Guided_by_Evolutionary_Physical_and_Geometric_Constraints?utm_source=chatgpt.com "(PDF) Antibody Design Using a Score-based Diffusion ..."
[3]: https://pmc.ncbi.nlm.nih.gov/articles/PMC10361400/?utm_source=chatgpt.com "AI Models for Protein Design are Driving Antibody Engineering"
[4]: https://www.nature.com/articles/s41586-025-09721-5?utm_source=chatgpt.com "Atomically accurate de novo design of antibodies with ..."
[5]: https://journals.sagepub.com/doi/10.1177/21679436251385401?utm_source=chatgpt.com "Stunning Progress in De Novo Computationally Designed ..."
[6]: https://www.biorxiv.org/content/10.1101/2024.10.07.617023v3.full-text?utm_source=chatgpt.com "Benchmarking Generative Models for Antibody Design & ..."


• Bennett et al., *Atomically accurate de novo design of antibodies with RFdiffusion*, Nature 2026. ([Nature][1])  
• Dauparas et al., *ProteinMPNN: sequence design on protein backbones*, Science 2022.  
• Ruffolo et al., *IgFold: deep learning antibody structure prediction*, Science Advances 2022.  
• Chungyoun et al., *AI-driven computational methods for antibody design*, 2023. ([PMC][4])  
• Baker Lab review of RFdiffusion antibody design. ([Baker Lab][5])
• Luo et al., *Antigen-specific antibody design with diffusion generative models*, NeurIPS 2022. ([proceedings.neurips.cc][7])  
• Villegas-Morcillo et al., diffusion models for antibody design. ([link.aps.org][8])  


[1]: https://www.nature.com/articles/s41586-025-09721-5 "Atomically accurate de novo design of antibodies with RFdiffusion"
[2]: https://www.nature.com/articles/s41586-025-09429-6 "One-shot design of functional protein binders with BindCraft"
[3]: https://pubmed.ncbi.nlm.nih.gov/41193805 "Atomically accurate de novo design of antibodies with RFdiffusion"
[4]: https://pmc.ncbi.nlm.nih.gov/articles/PMC10361400 "AI Models for Protein Design are Driving Antibody Engineering"
[5]: https://www.bakerlab.org/2025/02/28/designing-antibodies-with-rfdiffusion "Designing antibodies with RFdiffusion - Baker Lab"
[6]: https://proceedings.neurips.cc/paper_files/paper/2022/hash/3fa7d76a0dc1179f1e98d1bc62403756-Abstract-Conference.html "Antigen-Specific Antibody Design and Optimization with ..."
[7]: https://proceedings.neurips.cc/paper_files/paper/2022/file/3fa7d76a0dc1179f1e98d1bc62403756-Paper-Conference.pdf "Antigen-Specific Antibody Design and Optimization with ..."
[8]: https://link.aps.org/doi/10.1103/PRXLife.2.033012 "Guiding Diffusion Models for Antibody Sequence and ..."