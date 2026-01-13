# Dia-RAG: Dialect-Aware Retrieval-Augmented Generation

Dia-RAG is a research framework that addresses a fundamental limitation of current
Retrieval-Augmented Generation (RAG) systems when applied to **low-resource dialects**.

Most multilingual RAG pipelines rely on **translation-based standardization** of user
queries into high-resource languages (e.g., English or Hindi). While effective for
fluency, this process often **erases cultural meaning**, introduces **factual distortion**,
and amplifies **bias**—especially for dialectal and indigenous languages.

Dia-RAG proposes a principled alternative: **decouple and augment**, rather than
translate and rewrite.

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

<img width="926" height="529" alt="image" src="https://github.com/user-attachments/assets/21e5c7c3-2d3f-4fea-9bf4-1541a167fdeb" />

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

### 5. Dataset: Santali-CultureQA

To evaluate dialect-aware retrieval and generation, we introduce
Santali-CultureQA, a curated benchmark focusing on culturally grounded
question answering in Santali (ISO 639-3: sat).

Dataset characteristics:

500 question–answer pairs

Coverage of festivals, governance structures, and literature

Each query contains at least one dialectal variant term

Annotations verified by native speakers

The dataset is designed to test cultural disambiguation rather than surface-level
semantic similarity
