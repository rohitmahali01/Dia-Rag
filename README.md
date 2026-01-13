

---

````markdown
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
````

---

## 📊 Dataset: Santali-CultureQA

To evaluate dialect-aware reasoning, we introduce **Santali-CultureQA**:

* 500 culturally grounded QA pairs
* Focus areas:

  * Festivals (Baha, Sohrai)
  * Governance (Manjhi–Paragana system)
  * Literature and oral traditions
* Each question contains at least one **variant term**
* Answers verified by native Santali speakers

This dataset is designed to explicitly test **cultural disambiguation**, not just
semantic similarity.

---

## ⚖️ Baselines

We compare Dia-RAG against three paradigms:

### Baseline 1: Direct mRAG

* Multilingual embeddings (LaBSE)
* No standardization
* Tests zero-shot multilingual alignment

### Baseline 2: Translate-RAG

* Query translation using IndicTrans2
* Retrieval in English
* Generation translated back to Santali
* Represents the Standardization Bottleneck

### Baseline 3: Dia-RAG (Ours)

* Query decoupling
* Dictionary-augmented retrieval
* Quality-aware generation

---

## 📐 Evaluation Metrics

### Retrieval Metrics

* **Recall@k**
* **Variant Recall** (retrieval of culturally correct meanings)

### Generation Metrics

* **BERTScore (multilingual)**
* **Cultural Specificity Score (CSS)**
  A reference-free metric classifying answers as:

  * Generic / Hallucinated
  * Ambiguous
  * Culturally Specific

---

## 📈 Expected Outcomes

We hypothesize that:

1. Direct multilingual retrieval fails due to language mismatch
2. Translation-based RAG achieves fluency but low cultural fidelity
3. Dia-RAG significantly improves cultural specificity by preserving dialectal meaning

---

## 📁 Repository Structure

```text
Dia-RAG/
├── docs/              # Proposal, diagrams, design notes
├── data/              # Santali-CultureQA and lexicons
├── src/               # Decoupler, retrieval, tagging modules
├── experiments/       # Baselines and evaluations
├── scripts/           # Utilities and experiment runners
└── README.md
```

---

## 📌 Project Status

* ✅ Research proposal completed
* 🚧 Code implementation in progress
* 🧪 Dataset construction ongoing

This repository is intended as a **research artifact**, not just a codebase.

---

## 📜 Citation

If you use or build upon this work, please cite:

```bibtex
@misc{mahali2026diarag,
  title={Dia-RAG: Dialect-Aware Retrieval for Low-Resource Dialects},
  author={Mahali, Rohit},
  year={2026}
}
```

---

## 🌍 Broader Impact

Dia-RAG aims to preserve **lexical dignity** for speakers of low-resource and
indigenous dialects. True language inclusion cannot be achieved through scale alone;
it requires **explicit cultural knowledge** and **architectural sensitivity**.

---

## 🤝 Contributing

Contributions, discussions, and collaborations are welcome—especially from:

* Dialect and indigenous language researchers
* Multilingual NLP practitioners
* Community language advocates

Open an issue or start a discussion to get involved.


Just say the word.
```
