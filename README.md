# RAG Document & Timesheet Assistant

A LangGraph agent with a Streamlit UI that answers questions about uploaded documents and about project timesheet records. I built it as the final Python assessment of my graduate programme.

## Features

- **Document Q&A.** Upload a `.txt`, `.pdf` or `.docx` file. It's chunked, embedded with HuggingFace sentence-transformers and indexed in FAISS, and answers come back with source metadata and similarity scores.
- **Timesheet chat.** Ask in natural language ("my approved timesheets for Feb 03 - Feb 09"). The agent builds a JSON-logic filter and calls a project-management REST API, and the results show as a DataFrame.
- **Retrieved prompts.** System prompts live in [`docs/prompts.txt`](docs/prompts.txt) and are themselves retrieved from a vector index, so the agent picks the prompt that fits the request type.
- **LangGraph orchestration.** A `GraphAgent` state graph covers prompt selection, tool routing and conversation summarisation, running on Mistral through `langchain_mistralai`.
- **Persistent chat history** in PostgreSQL, plus session management and an inactivity timeout in the UI.

## Setup

```bash
python -m venv .venv
.venv\Scripts\activate          # macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env            # fill in keys, database and API settings
streamlit run scripts/interface.py
```

You need a PostgreSQL database for chat history. The timesheet feature needs access to a compatible project-management API (`PD_API_BASE_URL`, `PD_USERNAME`, `PD_PASSWORD`). Document Q&A works without it.

## Project structure

```
scripts/
  agent.py       LangGraph agent: state, prompt engineering, tool routing, summarisation
  retriever.py   FAISS index creation and similarity search over uploaded documents
  pd_api.py      Auth + dynamic-filter timesheet queries against the project API
  database.py    PostgreSQL chat-history persistence
  interface.py   Streamlit app (Timesheet chat and Document Q&A pages)
  logger.py      Logging setup
docs/prompts.txt Prompt templates (indexed for retrieval)
data/            FAISS indexes (created at runtime, git-ignored)
```

## Stack

Python · LangGraph · LangChain · Mistral · FAISS · HuggingFace embeddings · PostgreSQL · Streamlit
