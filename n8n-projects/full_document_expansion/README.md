# Context Expansion for Enhanced RAG Accuracy

This n8n workflow implements advanced **Context Expansion** strategies to improve the performance of Retrieval-Augmented Generation (RAG) systems. While standard RAG retrieves the "Top K" most relevant chunks, this project addresses the "context gap" that occurs when an LLM needs surrounding information to reason effectively.



## 🚀 The Core Problem
Simple vector retrieval often misses the "big picture." If a relevant chunk is part of a larger technical argument or a multi-step process, providing only that isolated snippet leads to hallucinations or incomplete reasoning. 

**The Challenge:** How do we give the LLM enough context to be "smart" without blowing our token budget on massive documents?

---

## 🛠 Context Expansion Strategies
The choice of strategy depends on document size, complexity, and the level of reasoning required.

* **Full Document Expansion:** Providing the entire source file.
* **Neighbor Chunks Expansion:** Fetching the chunks immediately preceding and following the hit.
* **Section Expansion:** Using metadata to grab the specific chapter or section.
* **Parent Expansion:** Retrieving a larger "parent" block from which the smaller "child" chunk was derived.

---

## 🔍 Deep Dive: Full Document Expansion
This workflow focuses on the **Full Document Expansion** method, optimized for high-accuracy reasoning on small-to-medium documents.

### The Pipeline
1.  **Initial Retrieval:** The AI Agent queries a multi-document vector database to find the Top K chunks.
2.  **Chunk Selection & Re-ranking:**
    * **LLM Selection:** A secondary LLM evaluates the chunks for relevance.
    * **Cohere Re-ranking:** Uses cross-encoding to score the Top K results and select the absolute best match.
3.  **Metadata Extraction:** The workflow extracts the `file_id` from the top-ranked chunk's metadata.
4.  **Re-Querying:** A second search is performed to fetch **all** chunks associated with that specific `file_id`.
5.  **Reconstruction:** Chunks are joined into a single string and passed to the LLM as the final context.

### ⚖️ Optimization & Cost Control
To prevent token overflow and high costs, the workflow includes a **Size-Based Gatekeeper**:
* **Metadata Tagging:** During ingestion, documents are tagged with a `word_count` or `total_size` metadata field.
* **Conditional Logic:** During inference, the workflow checks this size.
    * **If size < threshold:** The full document is loaded for maximum accuracy.
    * **If size > threshold:** The workflow automatically falls back to a more surgical expansion method (like Neighbor or Parent expansion).

---

## 📦 Requirements
* **n8n** (Version supporting Re-ranking nodes)
* **Vector Database** (Pinecone, Milvus, or Supabase)
* **Cohere API Key** (For Re-ranking)
* **OpenAI/Anthropic API Key** (For the LLM Agent)

---
<img width="1502" height="647" alt="image" src="https://github.com/user-attachments/assets/1993d486-0251-44af-9489-2bfe8bd56771" />
---


## 💡 How to Use
1.  **Ingestion:** Ensure your ingestion workflow adds `file_id` and `total_words` to your metadata.
2.  **Import:** Download the `.json` workflow file from this repo and import it into your n8n instance.
3.  **Configure:** Set your `threshold` variable in the Edit Fields node to match your LLM's context window limits (e.g., 5,000 words).
