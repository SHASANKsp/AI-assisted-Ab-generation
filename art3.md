Excellent — I’ll embed clear **visual diagrams** (ASCII-style) showing the *data flow between tools*, plus **tables that compare capabilities**. These diagrams are easy to drop into a Medium article as visuals with captions or recreate as proper graphics later.

---

# AI-Assisted Antibody Design

## Data Flow Diagrams & Capability Figures

Below are **three visual diagrams** showing how different tools connect in an end-to-end workflow. These diagrams are designed to be *human-readable* and easy to turn into Medium visuals.

---

## Figure 1 — High-Level Workflow

```
              ┌────────────────────────────┐
              │  Target Antigen Structure  │
              │        (epitope defined)   │
              └───────────────┬────────────┘
                              │
                              ▼
            ┌────────────────────────────────┐
            │ Structure-Aware Generation     │
            │ (Diffusion Models)            │
            │ e.g., RFdiffusion, AbDiffuser │
            └───────────────┬────────────────┘
                            │     Backbone
                            │  coordinates
                            ▼
      ┌──────────────────────────────────────────────┐
      │ Sequence Design (backbone → sequence)         │
      │ e.g., ProteinMPNN                             │
      └───────────────┬───────────────────────────────┘
                      │   Full amino acid sequences
                      ▼
   ┌────────────────────────────────────────────────────────────┐
   │ Structural Validation & Docking                             │
   │ e.g., IgFold, AlphaFold, ABodyBuilder3                       │
   │   ┌─────────────────────────────────────────────────────┐  │
   │   │ Predictions: Folded Fv, Complex, Confidence Scores │  │
   │   └─────────────────────────────────────────────────────┘  │
   └───────────────┬────────────────────────────────────────────┘
                   │ Validated structure + metrics
                   ▼
        ┌───────────────────────────────────┐
        │ Humanization & Developability      │
        │ e.g., BioPhi, humanness scoring     │
        └───────────────────────────────────┘
```

**Caption:**
*This diagram shows a common flow: from antigen input → structural generation → sequence design → validation → developability scoring.*

---

## Figure 2 — Sequence-Only Exploration vs Structure-Conditioned Design

```
      ┌───────────────┐
      │ Sequence-LMs  │
      │ (AbGPT,      │
      │  AbLang,     │
      │  AntiBERTy)  │
      └───────┬───────┘
              │ Generated
              │ antibody
              ▼
   ┌───────────────────────────┐
   │ Candidate Sequences Only  │
   │ (without geometry)        │
   └────────────┬──────────────┘
                │ Validate later via
                │ structural tools
                ▼
   ┌────────────────────────────┐
   │ Folding + Docking Module   │
   │ (IgFold, AlphaFold)        │
   └────────────────────────────┘

Vs.

      ┌─────────────────────────────────┐
      │ Structure-Conditioned Systems    │
      │ (Diffusion + co-design models)   │
      │ e.g., RFdiffusion, DiffAb        │
      └─────────────────┬───────────────┘
                        │ Generates
                        │ geometry + sequence
                        ▼
      ┌─────────────────────────────────┐
      │ Immediate Structural Context     │
      │ (Likely interacting with antigen)│
      └─────────────────────────────────┘
```

**Caption:**
*Sequence-only models provide breadth quickly but lack geometric context. Structure-conditioned models collapse sequence + geometry together, potentially yielding better binding hypotheses.*

---

## Figure 3 — Iterative Feedback Loop

```
            ┌──────────────────────────┐
            │ Structure-Aware Generator│
            └─────────────┬────────────┘
                          │ structures
                          ▼
            ┌──────────────────────────┐
            │ Sequence Design (MPNN)   │
            └─────────────┬────────────┘
                          ▼
          ┌────────────────────────────────────┐
          │ Structural Validation & Docking    │
          │ (IgFold, AlphaFold, ABodyBuilder3) │
          └─────────────┬──────────────────────┘
                        ▼
      ┌─────────────────────────────────────────────┐
      │ Fitness Evaluation & Developability Filters │
      │ (BioPhi, scoring models)                    │
      └─────────────┬───────────────────────────────┘
                    │  Feedback into generation
                    ▼
            ┌───────────────────────────────┐
            │ Refined Generator Training    │
            └───────────────────────────────┘
```

**Caption:**
*Most real pipeline frameworks are not strictly linear: designs feed back into generation models, enabling incremental improvement through evaluation-informed selection.*

---

# Capability Comparison Tables

Below are clean tables you can insert directly into Medium — each compares tools on **what they actually do**.

---

## Table 1 — Generative Models

| Tool            | Input                        | Output                | Strength                   | Weakness                |
| --------------- | ---------------------------- | --------------------- | -------------------------- | ----------------------- |
| **AbGPT**       | Optional prompt              | Sequences             | Fast diversity generation  | No structure            |
| **AbLang**      | Sequence fragments           | Embeddings / variants | Good representation        | Not structural          |
| **AntiBERTy**   | Sequences                    | Embeddings            | Strong for classification  | Not generative alone    |
| **RFdiffusion** | Epitope + constraints        | Structure (CDRs)      | Targeted geometry          | Needs antigen structure |
| **AbDiffuser**  | Framework + optional antigen | Structure             | Antibody-specific geometry | Lacks separate score    |
| **DiffAb**      | Antigen context              | Seq + structure       | End-to-end design          | Computational heavy     |

---

## Table 2 — Structural Evaluation Tools

| Tool              | Input                 | Output                 | Why it matters                 |
| ----------------- | --------------------- | ---------------------- | ------------------------------ |
| **IgFold**        | Sequence              | Folded Fv + scores     | Fast antibody folding          |
| **ABodyBuilder3** | Sequence              | Canonical structures   | Loop-specific modeling         |
| **AlphaFold**     | Sequence (or complex) | Structure + confidence | General fold validation        |
| **RoseTTAFold**   | Sequence or complex   | Predicted structure    | Alternative geometry predictor |

---

## Table 3 — Developability & Humanization

| Tool                         | Input          | Output           | Focus Area           |
| ---------------------------- | -------------- | ---------------- | -------------------- |
| **BioPhi**                   | Sequence       | Humanness scores | Immunogenic risk     |
| *Humanness scoring (models)* | Seq embeddings | 9-mer humanness  | Compatibility        |
| *Aggregation classifiers*    | Sequences      | Risk scores      | Solubility/stability |

---

# Suggested Figure Captions for Medium

Here are captions you can paste under visual diagrams:

**Figure 1:** *The canonical flow from antigen to validated antibody candidate — each box represents one class of models that performs a specific transformation.*

**Figure 2:** *Sequence-only approaches vs. structure-aware generation. One provides breadth, the other geometric precision.*

**Figure 3:** *AI-assisted antibody design is iterative. Evaluation metrics feed back into generative models to improve designs over rounds.*

