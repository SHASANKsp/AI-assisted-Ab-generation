# AI-Assisted Antibody Design Toolkit

A curated overview of the computational ecosystem used for **AI-assisted antibody discovery and engineering**.

This repository accompanies the Medium article:

**"A Human’s Guide to the AI Tools Transforming Antibody Design"**

The goal is not to present a single pipeline but to map the **tool landscape** and show how different models contribute to antibody generation, structural validation, and developability assessment.

---

# Overview

Antibody design with AI is not a single algorithm.

Instead, it involves multiple specialized tools that operate across different stages of the discovery process:

1. Antigen structural understanding
2. Antibody generation
3. Sequence optimization
4. Structural validation
5. Developability assessment

This repository documents the tools, workflows, and conceptual architecture needed to build **AI-assisted antibody design systems**.

---

# Combined Workflow

Below is a simplified schematic of how the major tool categories interact.

```

          Target Antigen Structure
                  │
                  ▼
    Structure-Aware Generation

    (RFdiffusion / AbDiffuser / DiffAb)
                │
                ▼
    Sequence Design from Backbone
        (ProteinMPNN)
                │
                ▼
    Structural Validation & Docking
    (IgFold / AlphaFold / ABodyBuilder3)
                │
                ▼
    Humanization & Developability Filters
        (BioPhi / Humanness scoring)

    ─────────────────────────

    Sequence-only generation tools
        (AbGPT / AbLang / AntiBERTy)

    can also generate candidate sequences
    which then enter the validation stage.

```


# Tool Categories

## 1. Sequence Generative Models

These models treat antibody sequences as language and generate new variants learned from immune repertoires.

| Tool | Description | Input | Output |
|-----|-------------|------|-------|
| AbGPT | Autoregressive antibody generator | Optional prompt | New antibody sequences |
| AbLang | Antibody representation model | Sequence | Embeddings / variants |
| AntiBERTy | Antibody language model | Sequence | Embeddings |

Use cases:

- Library expansion
- Sequence exploration
- Diversity generation
- Representation learning

Limitations:

- No structural reasoning
- Binding predictions indirect

---

## 2. Structure-Aware Generative Models

These models generate **3D antibody geometries** using diffusion-based methods.

| Tool | Approach | Output |
|-----|----------|-------|
| RFdiffusion | Structure diffusion | Backbone structures |
| AbDiffuser | Antibody-specific diffusion | CDR geometries |
| DiffAb | Joint sequence + structure diffusion | Antibody complex designs |

Strengths:

- Epitope-targeted design
- Spatial reasoning
- Interface-aware generation

Limitations:

- Requires antigen structure
- Computationally heavier

---

## 3. Sequence Design from Structure

After generating structural scaffolds, residues must be assigned.

| Tool | Description |
|----|-------------|
| ProteinMPNN | Predicts sequences compatible with a backbone |

Input:

- 3D backbone coordinates

Output:

- Amino acid sequence likely to fold into the backbone.

---

## 4. Structural Prediction & Validation

These tools evaluate whether generated sequences fold correctly.

| Tool | Focus |
|----|------|
| IgFold | Antibody folding |
| AlphaFold | General protein structure prediction |
| ABodyBuilder3 | Antibody structural modeling |
| RoseTTAFold | Structure prediction |

Common evaluation metrics:

- pLDDT confidence
- interface geometry
- steric clash analysis
- structural consistency

---

## 5. Developability & Humanization

High-affinity antibodies may still fail clinically due to manufacturability or immunogenicity.

| Tool | Purpose |
|----|---------|
| BioPhi | Antibody humanization |
| OASis | Humanness scoring |
| Sapiens | Deep learning-based humanization |

These tools assess:

- immunogenicity risk
- aggregation propensity
- sequence compatibility with human repertoires

---

# Repository Structure

```

AI-Antibody-Design/
│
├── README.md
│
├── diagrams/
│   └── antibody_workflow.svg
│
├── tools/
│   ├── sequence_models.md
│   ├── structure_models.md
│   ├── validation_tools.md
│   └── developability_tools.md
│
├── resources/
│   └── papers.md
│
└── examples/
└── conceptual_pipeline.md

```

---

# How These Tools Fit Together

The full workflow can be summarized as:

1. Define antigen and epitope
2. Generate antibody structures
3. Assign sequences
4. Validate folding
5. Evaluate developability
6. Iterate with experimental feedback

This repository focuses on documenting the **tool ecosystem**, not enforcing a single pipeline architecture.

---

# Important Disclaimer

AI-assisted antibody design **does not replace experimental validation**.

These tools generate hypotheses that must still be tested through:

- binding assays
- structural experiments
- biophysical characterization

---

# References

A curated list of key papers and tools will be maintained in:

```

resources/papers.md

```

---

# Contributing

Contributions are welcome.

You can help by:

- Adding new tools
- Updating benchmark papers
- Improving diagrams
- Sharing experimental validation workflows

---

# License

MIT License
