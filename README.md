# Dia-RAG: Dialect-Aware Retrieval-Augmented Generation

Dia-RAG is a research framework that addresses a fundamental limitation of current
Retrieval-Augmented Generation (RAG) systems when applied to **low-resource dialects**.

Most multilingual RAG pipelines rely on **translation-based standardization** of user
queries into high-resource languages (e.g., English or Hindi). While effective for
fluency, this process often **erases cultural meaning**, introduces **factual distortion**,
and amplifies **bias**—especially for dialectal and indigenous languages.

The full research proposal detailing the problem formulation, architecture, and evaluation plan is provided in [`docs/Dia-RAG-Proposal-V1.pdf`](docs/Dia-RAG-Proposal-v1.pdf).


Dia-RAG proposes a principled alternative: **decouple and augment**, rather than
translate and rewrite.
<img width="926" height="529" alt="image" src="https://github.com/user-attachments/assets/21e5c7c3-2d3f-4fea-9bf4-1541a167fdeb" />

---

## 🔍 Core Problem: The Standardization Bottleneck

When a user queries in a dialect (e.g., Santali):

- The query is translated into a standard language
- Dialect-specific terms are approximated or mistranslated
- Cultural context is lost
- The generator hallucinates facts that “fit” the standard language

This leads to errors such as:
- Translating *Manjhi* as *boatman* instead of *village headman*
- Rewriting queries in ways that invent entities or events
- Producing culturally generic or incorrect answers

We refer to this failure mode as the **Standardization Bottleneck**.

---

## 💡 Key Idea

Dialectal queries are **not monolithic strings**.

They are composed of:
- **Invariant components**: entities, proper nouns, dates (stable across languages)
- **Variant components**: dialect-specific, culturally loaded terms (prone to mistranslation)

Dia-RAG explicitly models this distinction.

---

## 🧠 Dia-RAG Architecture

Dia-RAG consists of three core modules:

### 1. Diversity-Sensitive Decoupling
The input query is decomposed into invariant and variant components using a lightweight
instruction-tuned model.

- Invariant components → exact matching in the knowledge base
- Variant components → flagged for special handling (not blind translation)

### 2. Dictionary-Augmented Retrieval
Instead of translating variant terms, Dia-RAG performs a **lexicon lookup**:

- Retrieves **cultural definitions**, not just surface translations
- Resolves dialectal polysemy explicitly (e.g., “bug” vs “treasure”)
- Falls back to machine translation only when no lexicon entry exists

### 3. Quality-Aware Tagging
Retrieved contexts are **not rewritten**.

Instead, each context is tagged with metadata indicating:
- Source (Lexicon vs Translation)
- Semantic reliability
- Cultural specificity

The generator is instructed to **prioritize high-quality sources**, enabling
risk-guided generation without factual distortion.

---

## 🏗️ High-Level Pipeline

```text
Dialect Query
   ↓
Diversity-Sensitive Decoupler
   ├── Invariant Components → Dense Retrieval
   └── Variant Components → Dictionary Lookup
                                ↓
                         (Fallback: MT if needed)
   ↓
Quality-Aware Tagging
   ↓
Generator LLM (Risk-Guided)
   ↓
Culturally Accurate Answer
```


## 5. Dataset: Santali-CultureQA

To evaluate dialect-aware retrieval and generation, we introduce
**Santali-CultureQA**, a curated benchmark focusing on culturally grounded
question answering in Santali (ISO 639-3: `sat`).

**Dataset characteristics:**

- 500 question–answer pairs
- Coverage of festivals, governance structures, and literature
- Each query contains at least one dialectal variant term
- Annotations verified by native speakers

The dataset is designed to test **cultural disambiguation** rather than
surface-level semantic similarity.

---

## 6. Experimental Baselines

Dia-RAG is evaluated against the following baselines:

### Direct mRAG
- Multilingual embeddings without standardization

### Translate-RAG
- Query translation followed by standard retrieval and back-translation

### Dia-RAG (Proposed)
- Diversity-sensitive decoupling
- Dictionary-augmented retrieval
- Quality-aware generation

---

## 7. Evaluation Metrics

### Retrieval Quality
- **Recall@k**
- **Variant Retrieval Rate**

### Generation Quality
- **Multilingual BERTScore**
- **Cultural Specificity Score (CSS)**  
  A reference-free metric assessing dialectal correctness

---

## 8. Project Status

- Research proposal finalized
- Dataset construction in progress
- Implementation under development

This repository is intended to serve as a **reproducible research artifact**
and a **reference implementation**.

---

## 9. Citation

If you use or build upon this work, please cite:

```bibtex
@misc{mahali2026diarag,
  title  = {Dia-RAG: Dialect-Aware Retrieval for Low-Resource Dialects},
  author = {Mahali, Rohit},
  year   = {2026}
}
```

## 10. Related Work and Citations

Dia-RAG builds upon and extends prior research in multilingual retrieval,
dialectal NLP, and hallucination mitigation in Retrieval-Augmented Generation.
Below, we summarize the most relevant prior work that informs this framework.

### Lexical Diversity-Aware Retrieval
Zhang et al. introduce **DRAG**, a lexical diversity-aware relevance assessment
framework that decomposes queries into components with varying stability.
While effective for high-resource, monolingual settings, DRAG does not address
dialect–standard language mismatch. Dia-RAG adapts the decoupling principle to
explicitly handle dialectal variation.

> Zhang, Z., et al. (2025). *Lexical Diversity-aware Relevance Assessment for
Retrieval-Augmented Generation*. ACL.

---

### Quality-Aware Translation and Hallucination Mitigation
Moon et al. propose **QTT-RAG**, demonstrating that query rewriting and
translation can introduce factual distortion in multilingual RAG systems.
Their work motivates Dia-RAG’s non-destructive **quality-aware tagging**
strategy, which preserves original content while exposing reliability metadata
to the generator.

> Moon, H., et al. (2025). *Quality-Aware Translation Tagging in Multilingual
RAG Systems*. arXiv preprint.

---

### Dialectal Reasoning and Lexical Disambiguation
The **DIALECT-COPA** shared task shows that dialect-specific lexicons
significantly improve reasoning performance by resolving polysemous terms
(e.g., “bug” vs. “treasure”). However, prior approaches rely on prompt-level
augmentation rather than architectural integration. Dia-RAG embeds lexical
lookup directly into the retrieval pipeline.

> Perak, B., et al. (2024). *Incorporating Dialect Understanding into LLMs using
RAG*. VarDial Workshop.

---

### Cultural and Geopolitical Bias in Multilingual Retrieval
Li et al. introduce **BORDIRLINES**, demonstrating that retrieval language
strongly influences model outputs in culturally sensitive tasks. Their findings
highlight the risk of hegemonic bias when relying solely on standard-language
corpora, motivating Dia-RAG’s focus on dialectal cultural fidelity.

> Li, B., et al. (2025). *Multilingual Retrieval-Augmented Generation for
Culturally-Sensitive Tasks*. ACL Findings.

---

### Surveys on Dialectal NLP
Joshi et al. provide a comprehensive survey showing persistent performance gaps
for dialectal and low-resource languages, emphasizing that reasoning capability
alone is insufficient without explicit lexical knowledge. Dia-RAG operationalizes
this insight through dictionary-augmented retrieval.

> Joshi, A., et al. (2024). *Natural Language Processing for Dialects of a
Language: A Survey*. ACM.

---

### Positioning of Dia-RAG
Unlike prior work that relies on translation, rewriting, or prompt-level hints,
Dia-RAG is the first framework to unify:

- Dialect-aware query decoupling  
- Dictionary-augmented retrieval  
- Quality-tagged, non-destructive generation  

This integration enables retrieval and generation that preserve dialectal
meaning throughout the RAG pipeline.

