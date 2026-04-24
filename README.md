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
Create a .env file in the root directory and add your OpenAI API Key.

**3. Run the Evaluation Pipeline:**
Open and execute the cells in main.ipynb. The notebook is designed to run end-to-end and outputs clean, comparative logs for both the baseline and the KG-RAG architectures.

## 📊 Experimental Results 

To evaluate performance and cost-efficiency, I compared the brute-force Baseline (~5,000 tokens) against the Pre-Filtered KG-RAG (~250 tokens) on a 25-question subset of the HotpotQA validation split.

| Model Tier | Approach | Exact Match (EM) | LLM Judge | Est. Input Tokens |
| :--- | :--- | :--- | :--- | :--- |
| **gpt-4o-mini** | Baseline | 68.0% | 92.0% | ~2,000 |
| | **KG-RAG** | **52.0%** | **72.0%** | **~200 (10x Reduction)** |
| **gpt-4o** | Baseline | 60.0% | 84.0% | ~2,000 |
| | **KG-RAG** | **56.0%** | **72.0%** | **~200 (10x Reduction)** |
| **o3-mini (medium reasoning)** | Baseline | 56.0% | 84.0% | ~2,000 |
| | **KG-RAG** | **52.0%** | **72.0%** | **~200 (10x Reduction)** |

## ⚙️ Architecture Flow

```text
Raw Text Haystack (10 paragraphs, ~2k tokens) 
   │
   ▼ [text-embedding-3-small] (Dense Pre-Filtering)
Top-2 Paragraphs
   │
   ▼ [gpt-4o-mini] (Triple Extraction)
Knowledge Graph Sub-graph (Subject, Relation, Object)
   │
   ▼ [Generative Model] (gpt-4o / o3-mini)
Final Deterministic Answer (~200 tokens)
```
