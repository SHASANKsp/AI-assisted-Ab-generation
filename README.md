# AI-assisted-Ab-generation
Summarizing pretrained AI models and making a pipeline to integrate the models for generation with various mode of entries and applications


---

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
