# Context-Aware RAG Agent (n8n Workflow)

This project enhances the retrieval efficiency and accuracy of multi-document **Retrieval-Augmented Generation (RAG)** systems. By implementing a metadata-driven classification layer, it solves the "context drift" problem commonly found in dense vector databases containing diverse document sets.

---

## 🛑 The Problem: Out-of-Context Retrieval
Standard RAG systems often struggle when a vector database contains chunks from multiple, unrelated documents. 

* **Semantic Overlap:** A query might trigger a "nearest neighbor" match that is semantically similar but belongs to a completely different context (e.g., a query about "Apple" returns results for the fruit instead of the tech company).
* **Diluted Context:** When the top $K$ results are pulled from various unrelated documents, the LLM’s context window becomes cluttered with irrelevant noise, leading to hallucinations or incomplete answers.



---

## ✅ The Solution: Metadata-Filtered Retrieval
This workflow introduces a **Classification Layer** during both the ingestion and inference phases to ensure the LLM only sees chunks from the relevant domain.

### 1. Ingestion Phase
Before chunks are embedded, the document is processed by a classification LLM chain.
* **Classification:** Assigns a fixed category (e.g., *Legal*, *Technical*, *HR*).
* **Metadata Tagging:** This category is stored as a metadata field alongside the vector embeddings.

### 2. Inference Phase
When a user submits a query:
* **Query Classification:** An LLM classifies the user's intent into one of the predefined categories.
* **Metadata Filtering:** The vector store retriever applies a filter (e.g., `WHERE category == 'Technical'`) before performing the similarity search.
* **Clean Context:** The LLM receives only the most relevant, domain-specific chunks.

---

## 🏗️ Technical Architecture
The project is built using **n8n** and utilizes the following components:

| Component | Tooling |
| :--- | :--- |
| **Orchestration** | n8n |
| **Vector Database** | Pinecone / Supabase / Milvus (Configurable) |
| **Embedding Model** | OpenAI `text-embedding-3-small` |
| **LLM Classifier** | GPT-4o-mini / Claude 3 Haiku |
| **Search Algorithm** | Cosine Similarity with Metadata Filtering |

---

## 🚀 Future Upgrades
- [ ] **Hash-Based File Management:** Implement a tracking system using file hashes to prevent duplicate document ingestion.
- [ ] **Automated Re-indexing:** Trigger updates when a source document is modified.
- [ ] **Multi-Category Tagging:** Allow documents to exist in multiple context "bubbles."

---

## 📦 Installation & Setup

1.  **Import Workflow:** Download the `.json` workflow file from this repository.
2.  **Import to n8n:** Open n8n, create a new workflow, and import the JSON file.
3.  **Configure Credentials:** Set up your API keys for your LLM provider and Vector Database.
4.  **Define Categories:** Customize the classification prompt in the "Classifier" nodes to match your specific document types.

---

