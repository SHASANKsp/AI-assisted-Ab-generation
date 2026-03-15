# Artificial Intelligence–Driven Antibody Generation:

## A Methodological Taxonomy and Systems-Level Review

---

## Abstract

Recent advances in deep generative modeling have transformed computational protein design, with antibodies emerging as a primary application domain. Unlike classical display-based discovery methods, AI-assisted antibody engineering decomposes the discovery problem into modular computational stages: sequence generation, structure modeling, antigen-conditioned interface design, scoring, and developability optimization. However, no single framework spans the entire antibody lifecycle. Instead, a heterogeneous ecosystem of specialized tools has emerged.

This review organizes current AI antibody design methods into a methodological taxonomy based on input modality, generative paradigm, conditioning strategy, and downstream objective. We compare sequence-only language models, structure-aware diffusion systems, docking-integrated frameworks, and humanization/developability predictors. We further outline integration principles required for end-to-end systems and discuss limitations in thermodynamic modeling, data bias, and generalization to novel epitopes.

---

# 1. Introduction

Antibody development traditionally proceeds through:

1. Target identification
2. Library screening (phage/yeast/mammalian display)
3. Affinity maturation
4. Humanization and developability engineering

This process is experimentally intensive and probabilistic. AI reframes antibody discovery as a generative modeling problem over sequence and structural manifolds.

The core challenge is multi-objective:

* Epitope-specific binding
* Structural stability
* Human repertoire compatibility
* Manufacturability
* Low immunogenicity

Each objective maps to a different modeling strategy. Consequently, modern AI antibody design is inherently modular.

---

# 2. A Taxonomy of AI Methods for Antibody Design

We classify tools along four orthogonal dimensions:

| Dimension           | Categories                                                                    |
| ------------------- | ----------------------------------------------------------------------------- |
| Input Modality      | Sequence, Backbone Coordinates, Full-Atom Structure, Antibody–Antigen Complex |
| Generative Paradigm | Autoregressive LM, VAE, Diffusion Model, Graph Neural Network                 |
| Conditioning        | Unconditioned, Framework-Conditioned, Epitope-Conditioned, Complex-Co-Design  |
| Output Objective    | Sequence Diversity, Structural Validity, Interface Optimization, Humanness    |

---

# 3. Sequence-Only Generative Models

## 3.1 Representation Learning in Antibody Space

Large-scale antibody language models (ALMs) trained on OAS and SAbDab encode evolutionary priors.

Representative systems:

* AntiBERTy
* AbLang
* AbGPT

These models learn:

* CDR length distributions
* Framework conservation
* Canonical loop motifs
* Heavy/light chain pairing statistics

### Table 1. Sequence-Based Generative Models

| Model     | Architecture             | Conditioning     | Output              | Strength                         | Limitation                |
| --------- | ------------------------ | ---------------- | ------------------- | -------------------------------- | ------------------------- |
| AntiBERTy | BERT-based LM            | None             | Embeddings          | Strong repertoire representation | Not generative by default |
| AbLang    | RoBERTa variant          | Chain-specific   | Sequence likelihood | Heavy/light specialization       | No structural reasoning   |
| AbGPT     | GPT-style autoregressive | Optional prompts | Full Fv sequences   | Library-scale generation         | No geometric constraints  |

### Technical Observations

* These models optimize likelihood, not binding affinity.
* Structural plausibility is indirect.
* Useful for diversity expansion and humanization pre-filtering.

Sequence LMs approximate evolutionary statistics, not thermodynamic binding.

---

# 4. Structure-Aware Generative Models

Binding is geometric. Structure-aware models explicitly operate in coordinate space.

## 4.1 Diffusion-Based Antibody Design

Diffusion models iteratively denoise structural representations to generate plausible conformations.

Representative systems:

* RFdiffusion
* AbDiffuser
* DiffAb

### Table 2. Structure-Aware Generative Systems

| Model       | Representation                     | Conditioning                  | Output               | Notable Capability                          |
| ----------- | ---------------------------------- | ----------------------------- | -------------------- | ------------------------------------------- |
| RFdiffusion | Backbone frames                    | Epitope hotspots + framework  | CDR backbone         | De novo epitope targeting                   |
| AbDiffuser  | Full-atom diffusion                | Optional antigen conditioning | CDR structures       | Antibody-specific priors                    |
| DiffAb      | Joint residue+coordinate diffusion | Antigen structure             | Sequence + structure | Simultaneous geometry and sequence modeling |

### Strengths

* Explicit spatial reasoning
* Epitope-level targeting
* Atomic detail

### Limitations

* Requires antigen structure
* Computationally expensive
* Binding free energy not explicitly modeled

These models approximate interface thermodynamics through learned geometric priors.

---

# 5. Sequence Design from Generated Backbones

Backbone generation is only half the problem.

Residue assignment is often performed with:

* ProteinMPNN

ProteinMPNN optimizes sequence likelihood conditioned on fixed backbone geometry. It decouples:

1. Geometry generation
2. Residue optimization

This modularity improves stability but does not explicitly encode antibody repertoire constraints unless fine-tuned.

---

# 6. Structural Validation and Complex Modeling

After generation, structural consistency must be verified.

Key predictors:

* IgFold
* ABodyBuilder3
* AlphaFold
* RoseTTAFold

### Table 3. Structural Predictors in Antibody Workflows

| Tool          | Specialization                | Use Case              |
| ------------- | ----------------------------- | --------------------- |
| IgFold        | Antibody-specific AF2 variant | Fast Fv modeling      |
| ABodyBuilder3 | Canonical loop + ML hybrid    | CDR modeling          |
| AlphaFold     | General protein prediction    | Complex re-prediction |
| RoseTTAFold   | Geometric reasoning           | Interface modeling    |

Evaluation Metrics:

* pLDDT
* Predicted alignment error (PAE)
* Self-consistency score
* Interface RMSD
* Steric clash analysis

Important caveat: none directly compute binding free energy.

---

# 7. Humanization and Developability Modeling

High-affinity binders frequently fail in clinical translation.

## 7.1 Humanness Scoring

* BioPhi

BioPhi integrates:

* Sapiens (deep-learning humanization)
* OASis (9-mer repertoire matching)

### Table 4. Developability-Oriented Models

| Model                | Objective                  | Data Source               |
| -------------------- | -------------------------- | ------------------------- |
| Sapiens              | Humanization               | OAS                       |
| OASis                | Humanness scoring          | Repertoire 9-mer matching |
| AntiBERTy embeddings | Developability classifiers | Large antibody corpora    |

These models address:

* Immunogenicity risk
* Aggregation propensity
* Non-natural motif detection

They do not address binding specificity directly.

---

# 8. Comparative Methodological Landscape

### Table 5. Cross-Category Comparison

| Category                 | Examples                | Epitope Targeting | Structural Output    | Humanization | Scalability |
| ------------------------ | ----------------------- | ----------------- | -------------------- | ------------ | ----------- |
| Sequence LM              | AbGPT, AbLang           | No                | Indirect             | Moderate     | Very high   |
| Diffusion Structural     | RFdiffusion, AbDiffuser | Yes               | Full backbone        | No           | Moderate    |
| Joint Sequence-Structure | DiffAb                  | Yes               | Sequence + structure | Limited      | Moderate    |
| Humanization Tools       | BioPhi                  | No                | Sequence only        | Yes          | High        |
| Structure Predictors     | IgFold, AF2             | Validation only   | Full structure       | No           | Moderate    |

---

# 9. Systems-Level Integration Principles

An end-to-end system requires:

1. Multi-modal conditioning
2. Multi-objective optimization
3. Iterative refinement
4. Closed-loop experimental validation

Key challenges:

* Data bias toward known scaffolds
* Underrepresentation of rare epitopes
* Lack of high-quality negative binding datasets
* Incomplete thermodynamic modeling

AI models interpolate well inside known distribution. They extrapolate poorly beyond it.

---

# 10. Emerging Research Directions

1. Joint antibody–antigen co-diffusion models
2. Explicit free-energy learning frameworks
3. Reinforcement learning with wet-lab feedback
4. Active learning over structural manifolds
5. Integration of biophysical simulation with generative models

The next generation of models will not simply generate binders.
They will optimize across binding, stability, and immunogenicity simultaneously.

---

# 11. Conclusion

AI-assisted antibody generation is not a single algorithmic breakthrough. It is a systems engineering challenge.

Sequence models capture evolutionary priors.
Diffusion models encode geometric constraints.
Structure predictors approximate folding.
Humanization models encode clinical feasibility.

Each component approximates a different projection of the antibody design manifold.

The future of antibody AI lies not in larger models alone, but in coherent integration — where generative sampling, geometric reasoning, and translational constraints are unified into adaptive closed-loop systems.

Antibodies are not just sequences.
They are structured thermodynamic solutions to biological problems.

And we are just beginning to model that solution space.