# Week 14 · Streamlit bills RAG

A chat app that answers questions about legislative bill PDFs. It loads every PDF in `bills/`, embeds the chunks with OpenAI, stores them in a local Chroma index, and answers from that context only.

The model is called through the [Vocareum OpenAI endpoint](https://openai.vocareum.com/v1). You paste the API key into the Streamlit sidebar when the app starts. The key is not stored in this repo.

## What's here

```
streamlit/
├── rag.py                 # Bills chat — load PDFs, index, answer with citations
├── chat.py                # Streamlit chat demo (no LLM; canned replies)
├── hello.py               # Smallest Streamlit page
├── bills/                 # PDFs the retriever reads
├── pyproject.toml
├── uv.lock
├── .gitignore             # Keeps .venv, .chroma, and secrets out of Git
├── publish-to-github.md   # Learner steps to publish a public copy
└── README.md
```

## Setup

Requires Python 3.12+ and [uv](https://docs.astral.sh/uv/).

```bash
uv sync
```

## Run the bills chat

```bash
uv run streamlit run rag.py
```

Opens http://localhost:8501.

1. Paste your Vocareum API key in the sidebar.
2. Wait until the sidebar lists the PDFs and the page reports how many chunks were indexed.
3. Ask a question about the bills. Answers should cite the source file. If the PDFs do not contain the answer, the app says so.

The index is written to `.chroma/` and is rebuilt when the file list or the key changes. That folder is gitignored.

## Other pages

```bash
uv run streamlit run hello.py    # "Hello world"
uv run streamlit run chat.py     # Chat UI with scripted replies, no API key
```

## Use your own bills

Add or replace PDFs in `bills/`, then restart the app and enter the API key again. `rag.py` indexes every `*.pdf` in that folder.

## Publish

Follow [publish-to-github.md](publish-to-github.md) to put your copy on a public GitHub repository.

## Common errors

| Symptom | Fix |
|---|---|
| `No PDF files in .../bills` | Put at least one `.pdf` in `bills/` and restart. |
| `None of the bill PDFs could be read.` | The PDFs are image-only or corrupt. Use text PDFs. Unreadable files are skipped when others succeed. |
| Authentication error from the API | The sidebar key is wrong, expired, or for a different endpoint. The app calls `https://openai.vocareum.com/v1`. |
| Indexing takes a long time | The first run embeds every chunk. Later runs reuse `.chroma/` for the same key and file list. |
