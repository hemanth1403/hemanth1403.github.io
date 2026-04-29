---
title: "VLM COUNTERFACTUAL CONSISTENCY"
excerpt: "Research project probing whether Vision-Language Models (LLaVA-1.5, InstructBLIP) truly reason about visual content or rely on pattern-matching — using counterfactual question families and a novel Consistency Score metric, plus LoRA fine-tuning with a pairwise consistency loss."
collection: portfolio
---

<h1>VLM Counterfactual Consistency</h1>

A research project investigating whether Vision-Language Models (VLMs) truly reason about visual content or rely on superficial pattern-matching. The core insight: a model that answers "red" to *"What color is the car?"* should logically answer "no" to *"Is the car blue?"* — but many don't.

**GitHub:** [sujithpeddireddy-neu/vlm-counterfactual-consistency](https://github.com/sujithpeddireddy-neu/vlm-counterfactual-consistency)

---

<h2>Problem & Approach</h2>

Standard VQA accuracy metrics don't reveal whether a model understands logical relations between related questions. This project introduces **counterfactual consistency testing**: generating logical variants of original questions through four intervention types and measuring whether model predictions satisfy the expected logical relations across the entire family.

**Intervention types:**
- **Negation** — Yes/no questions flipped to test logical opposition
- **Attribute swaps** — Color, size, material changed to test attribute reasoning
- **Entailment** — Logical implications tested for soundness
- **Spatial perturbations** — Left/right, on/under relationships reversed

---

<h2>Models & Dataset</h2>

- **Models evaluated:** LLaVA-1.5-7B, InstructBLIP-FlanT5-XL
- **Dataset:** GQA (1,000 questions → 1,961 counterfactuals); VQA v2 for generalization
- **Hardware constraint:** 4-bit quantization (QLoRA) enables inference on 8GB VRAM GPUs

---

<h2>Results</h2>

| Model | Consistency Score | VQA Accuracy |
|-------|-------------------|--------------|
| LLaVA-1.5 (base) | **0.6925** | 0.439 |
| InstructBLIP (base) | 0.6263 | **0.502** |
| LLaVA-1.5 + LoRA | 0.6598 | 0.493 |

**Pass rate by intervention type:**

| Intervention | LLaVA-1.5 | InstructBLIP | LLaVA + LoRA |
|---|---|---|---|
| Entailment | **99.1%** | 90.6% | — |
| Spatial | **53.0%** | 27.6% | 45.1% |
| Negation | 14.9% | **27.0%** | 17.5% |
| Attribute swap | 6.8% | 6.2% | **8.6%** |

**Key findings:**
- LLaVA shows higher consistency (0.69 vs 0.63) despite lower raw accuracy — suggesting better compositional reasoning
- Both models fail dramatically on attribute swaps (<7%): they answer based on the *entity*, not the changed *attribute*
- LoRA fine-tuning improves VQA accuracy (+5.4%) and attribute reasoning (+1.8%), but introduces a tradeoff — spatial reasoning drops (−7.9%), revealing that different reasoning modes compete during training

---

<h2>Architecture & Pipeline</h2>

The pipeline runs five sequential phases:

1. **Counterfactual Generation** — Converts raw GQA questions into "families" (original + 1–3 logical variants), dispatching on question type to apply appropriate interventions
2. **VLM Inference** — Runs pretrained models on all family members; auto-detects VRAM and falls back to 4-bit quantization when memory is tight
3. **Consistency Scoring** — Evaluates per-family scores by checking whether predictions satisfy logical relations, with per-intervention-type and per-question-type breakdowns
4. **Consistency-Aware Training** — LoRA rank-8 fine-tuning with a novel **pairwise consistency loss**:
   - *Contradiction loss*: Penalizes agreement when answers should be opposite
   - *Entailment loss*: Penalizes failure to confirm entailed questions
   - *Attribute/spatial loss*: Penalizes similar distributions for swapped attributes
   - Total loss: `CE_orig + CE_cf + λ·PC_loss`
5. **Generalization Evaluation** — Validates that fine-tuning on GQA counterfactuals transfers to VQA v2 without degrading standard accuracy

---

<h2>Tech Stack</h2>

- **Frameworks:** PyTorch, HuggingFace Transformers, PEFT (LoRA), bitsandbytes (4-bit quantization)
- **Models:** LLaVA-1.5-7B, InstructBLIP-FlanT5-XL
- **Datasets:** GQA, VQA v2
- **Supporting tools:** scikit-learn, pandas, matplotlib, OpenCV

---

### Submitted By

- **Authors:** Hemanth Sai Madadapu & Sujith Peddireddy
- **Program:** M.S. — Khoury College of Computer Sciences, Northeastern University
- **Contact:**
  - _E-Mail:_ madadapu.h@northeastern.edu
  - _Website:_ [hemanth1403.github.io](https://hemanth1403.github.io)
