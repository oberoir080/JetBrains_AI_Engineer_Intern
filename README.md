# High-Efficiency Knowledge Graph RAG (KG-RAG) for Multi-Hop QA

**Author:** Rahul Oberoi  
**Program:** M.Sc. Computational Linguistics, University of Stuttgart  
**Target Role:** AI Engineer Intern @ JetBrains

## Quick Start (Reproducibility)

**1. Clone the repository and install dependencies:**
```bash
git clone [https://github.com/oberoir080/JetBrains_AI_Engineer_Intern.git](https://github.com/oberoir080/JetBrains_AI_Engineer_Intern.git)
cd JetBrains_AI_Engineer_Intern
pip install -r requirements.txt
```

**2. Configure Environment:**
Create a .env file in the root directory and add your OpenAI API Key:
```bash
OPENAI_API_KEY=<insert your key here>
```

**3. Run the Evaluation Pipeline:**
Open and execute `main.ipynb` end-to-end. The notebook runs the full stem agent loop — architecture discovery, self-evaluation, crystallization, and final evaluation — outputting comparative logs for both the baseline and KG-RAG pipelines.

**Output files generated:**
| File | Contents |
| :--- | :--- |
| `predictions.json` | Raw baseline predictions across all three models |
| `eval_results.json` | Scored baseline results (Exact Match + LLM Judge) |
| `kg_rag_predictions.json` | KG-RAG predictions across all three models |
| `kg_eval_results.json` | Scored KG-RAG results (Exact Match + LLM Judge) |

---

## 📊 Experimental Results 

The stem agent selected KG-RAG for the task class *Multi-Hop Question Answering over Noisy Text* and was evaluated against a flat-context baseline on the first 25 rows of the HotpotQA validation split.

| Model | Approach | Exact Match | LLM Judge | Est. Input Tokens |
| :--- | :--- | :--- | :--- | :--- |
| **gpt-4o-mini** | Baseline | 64.0% | 84.0% | ~3,500 |
| | **KG-RAG** | **52.0%** | **68.0%** | **~250 (~10x reduction)** |
| **gpt-4o** | Baseline | 60.0% | 88.0% | ~3,500 |
| | **KG-RAG** | **60.0%** | **76.0%** | **~250 (~10x reduction)** |
| **o3-mini** | Baseline | 52.0% | 72.0% | ~3,500 |
| | **KG-RAG** | **52.0%** | **72.0%** | **~250 (~10x reduction)** |

The stem agent adds a one-time overhead of approximately 5,700 tokens (discovery + self-evaluation). At 25 questions, the per-query savings of ~3,250 tokens accumulate to roughly 70,000 tokens saved, meaning the stem agent pays for its own overhead within the first 2 queries.

## ⚙️ Architecture Flow

```text
Task Class: "Multi-Hop Question Answering over Noisy Text"
   │
   ▼ [Stem Agent — stem_discover_and_choose()]
Architecture Decision: KG-RAG
   │
   ▼ [Stem Agent — stem_evaluate() on 5 questions]
Self-eval score crosses 50% threshold → Crystallize
   │
   ▼ [KG-RAG Pipeline]
   │
   ├── Raw Text Haystack (10 paragraphs, ~3,500 tokens)
   │      │
   │      ▼ [text-embedding-3-small] (Dense Pre-Filtering)
   │   Top-2 Paragraphs
   │      │
   │      ▼ [gpt-4o-mini] (Triple Extraction)
   │   Knowledge Graph (Subject, Relation, Object) + source sentences
   │      │
   │      ▼ [Cosine Similarity Node Scoring]
   │   Top-ranked source sentences (~250 tokens)
   │      │
   │      ▼ [Answering Model] (gpt-4o-mini / gpt-4o / o3-mini)
   │
   └── Final Answer
```
