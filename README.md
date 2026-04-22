# 🤖 RAG-Based Customer Support Assistant

> An AI-powered customer support bot built with LangGraph, ChromaDB, Groq LLM, and Human-in-the-Loop (HITL) escalation.

---

## 📽️ Demo

▶️ **[Watch Demo Video](YOUR_VIDEO_DRIVE_LINK_HERE)**

---

## 📌 Overview

This project is an AI-powered assistant that reads a PDF knowledge base, understands user questions, and responds with accurate answers using Retrieval-Augmented Generation (RAG). When the system is not confident about an answer, it automatically escalates to a human agent instead of guessing.

Built as part of an internship project using a graph-based workflow with LangGraph.

---

## 🏗️ System Architecture

```
User Question
     │
     ▼
┌─────────────────────────────────────────────────┐
│           DOCUMENT INGESTION PIPELINE            │
│  PDF → PyPDFLoader → Chunks → Embeddings → ChromaDB │
└───────────────────┬─────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────┐
│              LANGGRAPH WORKFLOW                  │
│                                                  │
│  [retrieve] → [generate] → [confidence_check]   │
│                                   │              │
│                      ┌────────────┤              │
│                      ▼            ▼              │
│                 [hitl_node]  [output_node]        │
│                      └────────► [END]            │
└─────────────────────────────────────────────────┘
```

---

## ✨ Features

- 📄 **PDF Ingestion** — Automatically loads and chunks a PDF knowledge base
- 🔍 **Semantic Retrieval** — Uses vector embeddings to find the most relevant context
- 🧠 **LLM-Powered Answers** — Generates responses grounded strictly in the retrieved context
- 🔀 **Graph Workflow** — Clean, modular pipeline managed by LangGraph
- 🚨 **HITL Escalation** — Low-confidence answers are escalated to a human agent automatically
- 💾 **Persistent Vector Store** — ChromaDB persists to disk so embeddings aren't recomputed every run

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python | Core language |
| LangChain | PDF loaders, text splitters, embeddings, vector stores |
| LangGraph | Graph-based workflow orchestration |
| ChromaDB | Vector database for storing and retrieving embeddings |
| Groq (LLaMA 3.3 70B) | LLM for generating answers |
| HuggingFace Sentence Transformers | Local embedding model (`all-MiniLM-L6-v2`) |
| PyPDFLoader | Reading and parsing PDF documents |
| Google Colab | Cloud-based Jupyter notebook environment |
| Google Drive | Persisting ChromaDB across Colab sessions |

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git
cd YOUR_REPO_NAME
```

### 2. Get a Groq API Key

This project uses [Groq](https://console.groq.com) to run the LLaMA 3.3 70B model for free with fast inference.

1. Go to [https://console.groq.com](https://console.groq.com)
2. Sign up / Log in
3. Navigate to **API Keys** → **Create API Key**
4. Copy your key — you'll need it in the next step

### 3. Install Dependencies

```bash
pip install langchain langchain-community langchain-groq langchain-huggingface \
            langchain-text-splitters chromadb sentence-transformers \
            langgraph pypdf reportlab
```

### 4. Set Your API Key

Open the notebook and replace the placeholder in the configuration cell:

```python
os.environ["GROQ_API_KEY"] = "your_groq_api_key_here"
```

### 5. Add Your PDF

Upload your knowledge base PDF to Google Drive and update the path in the notebook:

```python
pdf_path = "/content/drive/My Drive/your_document.pdf"
```

### 6. Run the Notebook

Open `Rag_project.ipynb` in Google Colab and run all cells in order.

---

## 📂 Project Structure

```
├── Rag_project.ipynb                  # Main notebook — run this
├── HLD_RAG_Customer_Support.docx      # High-Level Design Document
├── LLD_RAG_Customer_Support.docx      # Low-Level Design Document
├── Technical_Documentation_RAG.docx   # Technical Documentation
└── README.md
```

---

## 🔄 How It Works

1. **Ingestion (Offline)** — The PDF is loaded, split into 500-character chunks with 50-character overlap, embedded using `all-MiniLM-L6-v2`, and stored in ChromaDB.
2. **Query (Online)** — The user's question is embedded and compared against ChromaDB. The top 3 most relevant chunks are retrieved.
3. **Generation** — The LLM is prompted with the retrieved context and generates an answer strictly based on that context.
4. **Confidence Check** — The answer is scanned for low-confidence phrases like *"I don't know"* or *"not mentioned"*.
5. **Routing** — High confidence → answer displayed. Low confidence → escalated to a human agent (HITL) who types a response manually.
