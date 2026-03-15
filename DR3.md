# Comprehensive AI-Powered Antibody Design Pipeline

Therapeutic antibodies are complex biomolecules, and designing them requires integrating many specialized tools. Over the past few years, numerous deep learning models have been developed for antibody sequence generation, structure prediction, affinity optimization, and developability screening. Individually, each model addresses a part of the problem. In practice, a full end-to-end pipeline must **chain these tools together** to generate, validate, and optimize candidate antibodies with therapeutic potential. Below we describe leading models in each category and detail example pipelines (P1–P20) that combine them. We also provide references for each tool and any published pipeline-like workflows.  

## Sequence Generative Models

- **AB-Gen** – A GPT-2-based generative model trained on ~75 million heavy-chain CDR3 sequences from the Observed Antibody Space (OAS) database【78†L343-L352】. It uses reinforcement learning with custom rewards to generate new CDR3 loops with desired properties (e.g. target specificity)【78†L343-L352】. *Input:* Desired sequence length; *Output:* Novel CDR3 sequence. *Drawback:* Focuses only on the heavy chain CDR3, not full antibody.  
- **AntiBERTy** – A protein language model (similar to BERT) trained on 558 million antibody sequences【84†L175-L183】. It produces sequence embeddings that correlate with binding affinity and maturation, enabling unsupervised affinity clustering. *Input:* Heavy/light sequence; *Output:* Embedding features. *Drawback:* It does not directly generate sequences, only provides representation for analysis【84†L175-L183】.  
- **IgBERT / IgT5** – Transformer-based language models trained on billions of antibody sequences【35†L178-L187】. These models (from Exscientia) achieve state-of-the-art performance on tasks like sequence recovery, expression level prediction, and binding affinity prediction【35†L178-L187】. *Architecture:* BERT and T5 style, trained on 2.2B unpaired and 2M paired sequences. *Limitation:* Very large, and primarily used for scoring/prediction rather than de novo design.  
- **IgLM** – An infilling language model for antibodies (Ruffolo *et al.*, Sci. Adv. 2023). Trained on 558M paired heavy/light sequences【40†L318-L326】, IgLM can fill in masked regions (e.g. CDR loops) conditioned on the rest of the antibody. *Input:* Partially masked sequence; *Output:* Completed sequence. This allows design of full antibody sequences or focused edits of CDRs. *Limitation:* Requires defined context (cannot generate completely from scratch without input context)【40†L318-L326】.  
- **IgCraft** – A multi-task antibody design model using Bayesian Flow Networks (Greenig *et al.*, 2024). It simultaneously supports tasks like unconditional generation, inpainting (mask filling), inverse folding (structure→sequence), and CDR grafting【58†L53-L62】. Trained on human antibody sequences, IgCraft excels at grafting novel CDR motifs onto existing frameworks. *Input/Output:* Varies by task (e.g. mask region to fill, or scaffold sequence to extend). *Limitation:* Designed for human antibody scaffolds; does not explicitly incorporate antigen information【58†L53-L62】.  
- **IgBlend** – A multimodal protein language model (Malherbe & Uçar, 2024) that learns both antibody sequence and structure. Trained on ~4 million antibody structures and 200 million sequences【56†L15-L24】, it can perform sequence-structure co-design tasks: e.g. given a partial structure, it can predict missing sequence (inverse folding) or vice versa. *Advantage:* Embeds physical 3D information for richer designs. *Limitation:* Very large and still mostly tested on benchmarks; not a turnkey design engine.  
- **IgGM** – A generative model that produces paired antibody heavy+light sequences and their 3D structures given an antigen (Wang *et al.*, 2025)【60†L189-L197】. It uses a pretrained transformer and geometric module to sample antibodies specific to a target epitope. *Input:* Antigen structure; *Output:* Paired antibody sequence + complex structure. *Limitation:* New method (poster abstract); training data and performance details are limited.  
- **PALM** – A RoFormer-based antibody language model (He *et al.*, Nat. Commun. 2023) focused on SARS-CoV-2【38†L83-L92】. It generates CDRH3 sequences conditioned on viral epitopes and includes a secondary model (“A2binder”) to predict ACE2-binding. *Use:* Antigen-specific CDR design for viral targets. *Limitation:* Specialized to SARS-CoV-2 heavy chains.  
- **AbX** – A hybrid diffusion model (Zhu *et al.*, ICML 2024) that jointly models antibody sequence and structure【52†L25-L34】. It applies a score-based diffusion process with evolutionary and physics-informed constraints. *Advantage:* First continuous-time diffusion treating sequence and 3D coordinates together【52†L25-L34】. *Limitation:* Complex training; currently demonstrated on synthetic benchmarks.  

## Structure Prediction and Affinity Design

- **IgFold** – A rapid end-to-end structure predictor for antibodies (Ruffolo *et al.*, Sci. Adv. 2022)【84†L175-L183】. It uses a pretrained antibody language model (AntiBERTy embeddings) and SE(3)-equivariant graph networks to predict Fv backbone coordinates. IgFold achieves accuracy comparable to AlphaFold in **under 25 seconds** per antibody【84†L175-L183】. *Input:* Heavy/light sequence; *Output:* Full 3D structure (backbone + sidechains). *Limitation:* Only predicts structure, not binding properties.  
- **ABodyBuilder3** – The latest structure modeller (Abanades *et al.*, Bioinformatics 2024) combining AlphaFold-like networks with language model embeddings【49†L147-L155】. It refines antibody CDR loops with high accuracy using ProtT5 embeddings. *Input:* VH/VL sequences; *Output:* 3D coordinates with confidence scores. *Limitation:* Computationally heavy (similar to AlphaFold) but open-source.  
- **AbFlex** – A CDR-design model (Jeon *et al.*, Bioinformatics 2024) that optimizes antibody loops on a given antigen. It uses an equivariant GNN with “flexible CDR definition” to generate diverse loops with superior binding energy【47†L106-L114】. *Input:* Antigen–antibody complex (wild-type); *Output:* New CDR loop sequences + structures. *Limitation:* Requires starting antibody structure; only redesigns loops.  
- **DiffAb** – A diffusion-based model (Luo *et al.*, NeurIPS 2022) for **antigen-specific** antibody design【67†L23-L32】. It jointly generates CDR sequences and structures that target a given epitope, the first DL method to do so. *Input:* Antigen 3D structure; *Output:* Antibody CDR loops (sequence+structure). *Limitation:* Prototype method; heavy compute.  
- **RFDiffusion** – An adapted version of RoseTTAFold diffusion (Bennett *et al.*, Nature 2026) for antibody design. Given an epitope and/or framework, it generates antibody backbones that bind the target with atomic precision【64†L101-L110】. Its designs can be experimentally matured via directed evolution to achieve nanomolar affinity【64†L101-L110】. *Input:* Antigen epitope (and optionally scaffold); *Output:* Fv backbone coordinates + suggested sequence. *Limitation:* Requires subsequent validation (yeast display) for high-affinity leads.  
- **AbDockGen** – A hierarchical graph-network decoder (Jin *et al.*, ICML 2022) for **paratope docking and design**【45†L12-L21】. It autoregressively generates an antibody paratope, simultaneously folding and docking it to the antigen using force-prediction refinement. *Input:* Antigen epitope structure; *Output:* Paratope sequence + docked 3D structure. *Limitation:* Focuses on the binding region only; more research needed for full Fv.  

## Multi-Modal and Hybrid Models

- **AntiFold** – An antibody-specific *inverse folding* model (Olsen *et al.*, Bioinf. Advances 2025)【88†L162-L170】. Starting from a solved Fv backbone, it generates novel sequences that maintain that structure. AntiFold outperforms generic tools on CDR recovery and preferentially avoids mutations that disrupt antigen binding【88†L162-L170】. *Input:* Backbone coordinates; *Output:* Compatible sequence variants. *Limitation:* Structure must be predefined.  
- **AbX** – (Described above under generative models) is hybrid sequence-structure diffusion.  
- **IgBlend** – (Described above) integrates structure into its language model.  
- **IgGM** – (Described above) also produces full seq+structure given target.  
- **ProteinMPNN and Derivatives** – A message-passing neural network (MPNN) for sequence design given a backbone (Dauparas *et al.*, Science 2022)【81†L399-L407】. It has many antibody-specific forks (AbMPNN, IgMPNN). *Input:* Backbone; *Output:* Sequence. *Limitation:* Does not alter the backbone.  
- **AbGPT, AbLang, etc.** – Other generative LMs mentioned in literature (e.g. AbGPT, AbLang) augment antibody design, though peer-reviewed details are limited.  

## Humanization and Developability Tools

- **BioPhi (Sapiens/OASis)** – An online platform for antibody humanization (Weng *et al.*, Nat. Commun. 2024)【62†L216-L224】. *Sapiens* is a deep language model trained on OAS that humanizes antibody sequences (matching expert humanizer output), and *OASis* is an interpretable humanness score. These help ensure candidates have low immunogenicity risk.  
- Other developability predictors (aggregation, stability) exist but are often proprietary or generic. In pipelines, one may include generic biophysical filters or custom ML models for solubility and expression.  

## Integrated Pipeline Workflows

By combining the above tools, we can build end-to-end antibody design workflows. Here are illustrative pipelines (with entry point, tools, inputs/outputs, objectives, plus advantages/limitations):

1. **P1: Epitope-Targeted Design (Antigen Structure Entry)** – RFdiffusion → ProteinMPNN → IgFold → BioPhi. *Goal:* De novo design of antibodies targeting a given epitope. RFdiffusion generates antibody backbones that bind the epitope【64†L101-L110】. ProteinMPNN sequences them【81†L399-L407】, IgFold predicts final structures【84†L175-L183】, and BioPhi humanizes. *Advantages:* Atomic-level binding specificity. *Limitations:* Requires antigen structure; heavy computation.  

2. **P2: Joint Sequence–Structure Design (Antigen Structure Entry)** – DiffAb → IgFold → AlphaFold-Multimer (AF2Complex). *Goal:* Simultaneous generation of sequence and structure for epitope binding. DiffAb outputs CDR loops (seq+3D) targeting the antigen【67†L23-L32】. IgFold refines structure【84†L175-L183】 and AF2Complex can model the full antibody–antigen complex. *Advantages:* Integrates binding context into design. *Limitations:* Extremely compute-intensive; early-stage method.  

3. **P3: CDR Diversification (Framework + Antigen)** – AbDiffuser → ProteinMPNN → IgFold. *Goal:* Diversify antibody loops on an existing scaffold for epitope binding. AbDiffuser applies diffusion (equivariant, physics-aware) to sample new loops【86†L223-L231】. ProteinMPNN finalizes sequences【81†L399-L407】, IgFold predicts structure【84†L175-L183】. *Advantages:* Incorporates antibody-specific priors via diffusion. *Limitations:* Needs initial framework; limited to loop variations.  

4. **P4: Structure-Guided Redesign (Framework + Antigen)** – RFdiffusion → AntiFold → IgFold. *Goal:* Modify an antibody scaffold for better binding while preserving geometry. RFdiffusion tweaks the backbone【64†L101-L110】. AntiFold then redesigns the sequence on that fixed backbone【88†L162-L170】. IgFold checks the result. *Advantages:* Maintains binding geometry; AntiFold avoids affinity-disrupting mutations. *Limitations:* Relies on a good initial backbone; bound by diffusion capacity.  

5. **P5: Sequence-Only Library Generation (Sequence Prompt)** – Generative LM → AntiBERTy → IgFold → BioPhi. *Goal:* Create large antibody libraries from scratch. A GPT-like model (e.g. AB-Gen【78†L343-L352】) samples sequences. AntiBERTy embeddings prioritize diverse/affine candidates【84†L175-L183】. IgFold models structures【84†L175-L183】 and BioPhi filters for human-likeness【62†L216-L224】. *Advantages:* Vast search space; fast generation. *Limitations:* No guarantee of target binding; may need downstream selection.  

6. **P6: Developability Screening (Sequence Dataset)** – (Generative or input) → AntiBERTy → BioPhi. *Goal:* Identify naturally plausible antibodies with good developability. AntiBERTy ranks sequences by naturalness. BioPhi scores humanness【62†L216-L224】 and optionally other ML models score solubility. *Advantages:* Rapid triage. *Limitations:* Ignores structure and target binding.  

7. **P7: Binding Prediction (Sequence Library)** – AntiBERTy → IgFold → AF2Complex. *Goal:* In silico affinity screening of many candidates. AntiBERTy pre-filters or clusters sequences【84†L175-L183】. IgFold predicts each Fv structure【84†L175-L183】. AF2Complex models the antibody–antigen complex and estimates binding. *Advantages:* Can scale to thousands of candidates. *Limitations:* AF2Complex predictions are approximations; expensive for large libraries.  

8. **P8: Affinity Maturation (Existing Antibody)** – AntiBERTy → ProteinMPNN → IgFold. *Goal:* Optimize a known antibody’s affinity. AntiBERTy identifies weak spots in the sequence【84†L175-L183】. ProteinMPNN suggests improved mutations on the fixed backbone【81†L399-L407】. IgFold verifies structural integrity【84†L175-L183】. *Advantages:* Structure-aware local search. *Limitations:* Limited to nearby sequence space; risk of suboptimal local solutions.  

9. **P9: Humanization (Known Antibody)** – AbLang/LM → IgFold → BioPhi. *Goal:* Reduce immunogenicity of a non-human antibody. An LM (or custom humanization model) suggests human-like substitutions. IgFold checks the fold, and BioPhi scores humanness【62†L216-L224】. *Advantages:* Systematic humanization with fold conservation. *Limitations:* May sacrifice binding if key residues are altered.  

10. **P10: Inverse Folding (Structural Scaffold)** – ProteinMPNN → IgFold → BioPhi. *Goal:* Design sequence for an existing backbone. ProteinMPNN (or similar) predicts an optimal sequence for the given 3D scaffold【81†L399-L407】. IgFold confirms the structure; BioPhi filters human-likeness. *Advantages:* Ensures scaffold integrity. *Limitations:* Backbone is fixed (no novelty in fold).  

11. **P11: Antigen–Antibody Complex Design** – RFdiffusion → ProteinMPNN → RosettaDock/RF2. *Goal:* Engineer an antibody scaffold directly onto an antigen. RFdiffusion shapes the interface【64†L101-L110】; ProteinMPNN sequences it; RosettaDock or RoseTTAFold2 refines the complex. *Advantages:* Direct optimization of interface. *Limitations:* Very resource-intensive; requires sophisticated docking.  

12. **P12: End-to-End Binder Discovery (Library)** – Generative LM → IgFold → AF2Complex → BioPhi. *Goal:* From library to lead candidate. A generative model expands the library; IgFold/AF2Complex screen for binding; BioPhi filters for developability. *Advantages:* Holistic discovery pipeline. *Limitations:* High computational cost; many heuristics.  

13. **P13: Epitope-Focused Design** – DiffAb → ProteinMPNN → IgFold. *Goal:* Precisely target a known epitope. DiffAb generates CDR loops against that epitope【67†L23-L32】; ProteinMPNN and IgFold finalize the design. *Advantages:* Explicitly uses epitope info. *Limitations:* GPU-intensive; relies on DiffAb’s quality.  

14. **P14: Framework-Only Variation** – AbDiffuser → AntiFold → IgFold. *Goal:* Invent new loops on a fixed framework. AbDiffuser samples CDRs with diffusion【86†L223-L231】; AntiFold sequences them【88†L162-L170】; IgFold structures them. *Advantages:* Realistic loop ensembles. *Limitations:* Biased by the chosen framework.  

15. **P15: Structural Optimization (Folded Fv)** – AntiFold → BioPhi → IgFold. *Goal:* Fine-tune sequence for an already-folded antibody. AntiFold mutates while preserving structure【88†L162-L170】; BioPhi checks humanness; IgFold validates. *Advantages:* Retains fold, minimal changes. *Limitations:* Cannot change binding mode.  

16. **P16: Hybrid Sequence+Structure Exploration** – (Seq prompt + Antigen) → RFdiffusion → ProteinMPNN → IgFold. *Goal:* Combine user-provided sequence hints with epitope binding. A prompt guides initial design; RFdiffusion adapts backbone for binding; ProteinMPNN and IgFold complete the design. *Advantages:* Leverages domain expertise plus AI creativity. *Limitations:* Complex, with many moving parts.  

17. **P17: Full Discovery Workflow (Antigen)** – RFdiffusion → ProteinMPNN → IgFold → AF2Complex → BioPhi. *Goal:* One-stop design from antigen to candidate. Merges P1 and binding check: design leads, predict structures【64†L101-L110】【81†L399-L407】, model complexes, filter developability. *Advantages:* Exhaustive, end-to-end. *Limitations:* Requires massive compute and integration effort.  

18. **P18: Active Learning Loop** – RFdiffusion → ProteinMPNN → IgFold → BioPhi → Experimental testing → Retraining. *Goal:* Iteratively improve designs with lab data. After each round of selection or mutagenesis, the models are retrained to focus on promising regions. *Advantages:* Bridges *in silico* and *in vitro*, leading to continuous improvement. *Limitations:* Depends on experimental feedback, which is costly and slow.  

19. **P19: Manufacturability Screening (Sequence List)** – AntiBERTy → ML Developability → BioPhi. *Goal:* Filter a set of antibodies for expression and solubility. AntiBERTy embeddings identify outliers; custom ML models (or known predictors) score stability; BioPhi checks humanness【62†L216-L224】. *Advantages:* Fast initial filter. *Limitations:* Binding is not assessed here.  

20. **P20: Complex Modeling** – DiffAb → IgFold → Docking. *Goal:* From antigen to predicted complex. DiffAb proposes a binder【67†L23-L32】, IgFold refines it【84†L175-L183】, and a docking tool (e.g. HADDOCK) models the full interface. *Advantages:* Automated *de novo* complex modeling. *Limitations:* Docking accuracy can vary; still speculative.  

Each pipeline above has been demonstrated or inspired by recent research. For example, pipelines using RFdiffusion and ProteinMPNN build on the work of Bennett *et al.* (Nature 2026)【64†L101-L110】. Diffusion-based pipelines derive from Luo *et al.* (NeurIPS 2022)【67†L23-L32】 and Martinkus *et al.* (NeurIPS 2023)【86†L223-L231】. Inverse-folding steps invoke Olsen *et al.* (Bioinf. Adv. 2025)【88†L162-L170】. When even higher accuracy is needed, AlphaFold-Multimer is used as in Ruffolo *et al.* (Sci. Adv. 2022)【84†L175-L183】. Finally, all designs are checked with tools like IgFold and BioPhi for realism and developability【84†L175-L183】【62†L216-L224】. 

Together, these models and pipelines illustrate a path toward fully *computational antibody discovery*. By linking sequence generation, structural modeling, binding evaluation, and developability filters, one can in principle accelerate therapeutic development while reducing lab workload. As computational methods continue to improve, these integrated pipelines hold great promise for rapidly delivering safe, high-affinity antibodies.

## References

- Jian *et al.*, “AB-Gen: Text-based CDR sequence generation with reinforced transformer” (Genomics Proteomics Bioinformatics, 2023)【78†L343-L352】.  
- Ruffolo *et al.*, “Fast, accurate antibody structure prediction from deep learning” (Sci. Adv., 2022)【84†L175-L183】.  
- Dauparas *et al.*, “ProteinMPNN: Robust sequence design on arbitrary backbone structures” (Science, 2022)【81†L399-L407】.  
- Weng *et al.*, “BioPhi: A developability assessment platform for therapeutics” (Nat. Commun., 2024)【62†L216-L224】.  
- Luo *et al.*, “DiffAb: Diffusion model for antibody design” (NeurIPS, 2022)【67†L23-L32】.  
- Martinkus *et al.*, “AbDiffuser: Full-atom generation of antibodies via diffusion” (NeurIPS, 2023)【86†L223-L231】.  
- Olsen *et al.*, “AntiFold: Antibody-specific inverse folding” (Bioinformatics Advances, 2025)【88†L162-L170】.  
- Bennett *et al.*, “Design of antibodies with RFdiffusion” (Nature, 2026)【64†L101-L110】.  
- Ruffolo *et al.*, “An autoregressive masked language model for antibody generation” (Sci. Adv., 2023)【40†L318-L326】.  
- Greenig *et al.*, “IgCraft: Multi-task antibody design with Bayesian flows” (arXiv, 2024)【58†L53-L62】.  
- Malherbe & Uçar, “IgBlend: Multimodal antibody language model” (Bioinformatics, 2024)【56†L15-L24】.  
- He *et al.*, “PALM: Pre-trained Antibody Language Model” (Nat. Commun., 2023)【38†L83-L92】.  
- Jeon *et al.*, “AbFlex: Data-augmented antibody loop design” (Bioinformatics, 2024)【47†L106-L114】.  
- Abanades *et al.*, “ABodyBuilder3: Language model-driven antibody modeling” (Bioinformatics, 2024)【49†L147-L155】.  
- Ruffolo *et al.*, “Deciphering antibody affinity maturation with language models” (bioRxiv, 2021).  
- Venderley *et al.*, “AntiBERTy: Antibody language model for affinity maturation” (arXiv, 2021).  

