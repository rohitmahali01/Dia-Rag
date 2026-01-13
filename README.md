# Dia-RAG: Dialect-Aware Retrieval-Augmented Generation (Research Proposal)

![Status](https://img.shields.io/badge/Status-Research_Proposal-blue) ![Focus](https://img.shields.io/badge/Focus-System_Architecture-orange) ![Language](https://img.shields.io/badge/Language-Santali_Low--Resource-green)

> **Note:** This repository hosts the research methodology, architectural design, and experimental roadmap for **Dia-RAG**. The project is currently in the conceptualization and design phase.

## 📄 [Read the Full Research Proposal (PDF)](./docs/Dia-RAG-Proposal-v1.pdf)

## 🔍 The Problem: The "Standardization Bottleneck"
[cite_start]Current RAG systems fail when applied to low-resource dialects (like Santali) due to the **Standardization Bottleneck**[cite: 81]. 

When users query in a dialect, standard systems translate the query into a high-resource language (like English) before retrieval. [cite_start]This causes **Lexical Erasure** and **Factual Distortion**[cite: 82, 85].

**Example of Failure identified in Research:**
* **Query Term:** *Manjhi* (Santali)
* **Standard Translation:** "Boatman" (Hindi/English)
* [cite_start]**Actual Dialect Meaning:** "Village Headman" [cite: 139]
* **Result:** The RAG system retrieves information about boats instead of governance, causing hallucinations.

## 🏗️ Proposed Architecture

[cite_start]Dia-RAG is designed to bypass this bottleneck using a **Decouple-and-Augment** principle rather than "Translate-then-Retrieve"[cite: 151].

![Dia-RAG Architecture](./assets/architecture_diagram.png)
[cite_start]*(Figure 1: The proposed Dia-RAG pipeline featuring Diversity-Sensitive Decoupling and Dictionary-Augmented Retrieval [cite: 148])*

### Key Modules Designed:

1.  [cite_start]**Diversity-Sensitive Decoupler** [cite: 184]
    * [cite_start]**Concept:** Instead of treating the query as a single string, the system decomposes it into "Invariant Components" (Entities, Dates) and "Variant Components" (Dialect terms) [cite: 186-190].
    * **Goal:** Prevent the translation engine from altering cultural terms.

2.  [cite_start]**Dictionary-Augmented Retrieval** [cite: 194]
    * **Concept:** A hybrid retrieval strategy. [cite_start]Invariant terms use standard dense retrieval, while Variant terms trigger a lookup in a specialized **Variant Lexicon** [cite: 196-198].
    * [cite_start]**Goal:** Resolve polysemy (e.g., *Manjhi* = Headman) before generation[cite: 202].

3.  [cite_start]**Quality-Aware Tagger** [cite: 203]
    * **Concept:** A scoring agent that tags context sources. [cite_start]It flags high-confidence lexicon definitions versus low-confidence machine translations[cite: 207].
    * **Goal:** Enables the LLM to prioritize cultural accuracy over translation fluency.

## 🧪 Experimental Roadmap

We have formulated the following experimental setup to validate the architecture:

### 1. Proposed Dataset: Santali-CultureQA
[cite_start]We are designing a benchmark dataset consisting of **500 QA pairs** sourced from Santali Wikipedia and FLORES-200[cite: 225]. 
* [cite_start]**Focus:** Culturally specific domains (Festivals like *Baha*, Governance like *Manjhi-Paragana*)[cite: 225].
* [cite_start]**Methodology:** Human-in-the-loop annotation to ensure "Variant" terms are used in correct dialectal contexts[cite: 224].

### 2. Custom Evaluation Metrics
Standard metrics like BLEU/ROUGE are insufficient for measuring cultural fidelity. We proposed:
* [cite_start]**Variant Recall:** Measures retrieval specifically for dialectal meanings (e.g., retrieving "Headman" docs instead of "Boatman" docs)[cite: 253].
* [cite_start]**Cultural Specificity Score (CSS):** A reference-free metric using an LLM evaluator to grade answers as Hallucinated, Ambiguous, or Culturally Specific [cite: 256-261].

## 🚀 Future Work
* [cite_start]**Community-Driven Lexicons:** Establishing a feedback loop for native speakers to refine the Variant Lexicon[cite: 313].
* [cite_start]**Speech Integration:** Extending the decoupling logic to ASR (Automatic Speech Recognition)[cite: 314].

## 👤 Author
**Rohit Mahali**
*Integrated B.Tech & M.Tech in Computer Science*
*Specialization in Machine Learning & Data Science*
*Central University of Jharkhand*
