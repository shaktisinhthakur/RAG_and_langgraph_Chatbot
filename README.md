LangGraph RAG Chatbot

> A conversational AI agent built with **LangGraph** that combines retrieval-augmented generation (RAG) with tool use. The agent answers questions from a local knowledge base and performs arithmetic — all with a live Streamlit UI showing the tool call trace.

![Python](https://img.shields.io/badge/Python-3.12+-3776AB?style=flat&logo=python&logoColor=white)
![LangGraph](https://img.shields.io/badge/LangGraph-Agent-FF6B35?style=flat)
![Groq](https://img.shields.io/badge/Groq-LLM-F55036?style=flat)
![Gemini](https://img.shields.io/badge/Google-Gemini-4285F4?style=flat&logo=google&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-UI-FF4B4B?style=flat&logo=streamlit&logoColor=white)

---

## Features

- **RAG Search** — semantic search over a FAISS vector index built from local `.txt` documents
- **Arithmetic Tools** — built-in `add`, `multiply`, and `divide` operations
- **LangGraph Agent Loop** — the LLM autonomously decides when to call tools and loops until it produces a final answer
- **Streamlit UI** — chat interface with a sidebar showing every tool call, its inputs, and its output
- **Groq LLM** — blazing-fast inference via `ChatGroq`
- **Google Gemini Embeddings** — `gemini-embedding-001` for document and query embedding

---

## Architecture

```
User prompt
    │
    ▼
┌─────────────┐     tool calls      ┌─────────────┐
│  llm_call   │ ─────────────────► │  tool_node  │
│  (ChatGroq) │ ◄───────────────── │             │
└─────────────┘   tool results      └─────────────┘
    │
    │ no more tool calls
    ▼
 Final answer
```

### Available Tools

| Tool | Description |
|------|-------------|
| `search_docs` | Semantic search over the FAISS knowledge base |
| `add` | Adds two integers |
| `multiply` | Multiplies two integers |
| `divide` | Divides two integers (with zero-division guard) |

---

## Project Structure

```
.
├── agent.py          # LangGraph agent definition (state, tools, graph)
├── app.py            # Streamlit UI
├── ingest.py         # One-time script to build the FAISS index
├── main.py           # CLI entrypoint placeholder
├── sample_docs/      # Source .txt documents for the knowledge base
│   └── ai_concepts.txt
├── faiss_index/      # Generated FAISS index (created by ingest.py)
├── pyproject.toml
└── .env.example
```

---

## Setup

### Prerequisites

- Python 3.12+
- `uv` (recommended) or `pip`
- A [Google AI Studio](https://aistudio.google.com) API key
- A [Groq](https://console.groq.com) API key

### 1. Clone and Install

```bash
git clone <repo-url>
cd "LangGraph RAG"
uv sync
```

### 2. Configure Environment Variables

```bash
cp .env.example .env
```

Edit `.env` and add your API keys:

```env
GOOGLE_API_KEY=your_google_api_key_here
GROQ_API_KEY=your_groq_api_key_here
```

### 3. Build the Knowledge Base Index

Run this **once** to embed your documents and create the FAISS index:

```bash
uv run python ingest.py
```

> This reads all `.txt` files from `sample_docs/`, splits them into ~500-character chunks, embeds them with Gemini, and saves the index to `faiss_index/`.

### 4. Launch the Streamlit App

```bash
uv run streamlit run app.py
```

Open **http://localhost:8501** in your browser.

---

## Usage

| Query type | Example |
|---|---|
| AI/ML questions | `"What is RAG?"` or `"Explain how transformers work"` |
| Math | `"What is 42 multiplied by 7?"` |
| Combined | `"If embeddings have 768 dimensions, how many numbers is that for 5 documents?"` |

> The sidebar shows a live trace of every tool the agent called — including inputs and outputs — for each conversation turn.

---

## Adding Documents

1. Add `.txt` files to `sample_docs/`
2. Re-run `ingest.py` to rebuild the index

```bash
uv run python ingest.py
```

---

## Dependencies

| Package | Purpose |
|---------|---------|
| `langgraph` | Agent graph orchestration |
| `langchain` | LLM and tool abstractions |
| `langchain-groq` | Groq LLM integration |
| `langchain-google-genai` | Gemini embedding model |
| `langchain-community` | FAISS vector store integration |
| `faiss-cpu` | Local vector similarity search |
| `streamlit` | Web UI framework |
| `python-dotenv` | Environment variable loading |

---

## Troubleshooting

**`gaierror: getaddrinfo failed` when rendering the agent graph**
```python
# Use local rendering instead of the mermaid.ink API
from langchain_core.runnables.graph import MermaidDrawMethod
agent.get_graph().draw_mermaid_png(draw_method=MermaidDrawMethod.PYPPETEER)
```

**`VIRTUAL_ENV does not match` warning with uv**
```bash
# Activate the correct environment before running
deactivate
.venv\Scripts\activate   # Windows
source .venv/bin/activate  # macOS/Linux
```

---

*Built with LangGraph • Groq • Google Gemini • FAISS • Streamlit*
