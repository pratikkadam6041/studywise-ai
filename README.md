# StudyWise AI

**Turn academic PDFs into a searchable study companion.**

StudyWise AI is an early-stage Python application that lets you ask questions about documents. Add a PDF URL, wait for it to be indexed, and ask a question in the browser. The app retrieves relevant material and uses a locally running language model to prepare an answer.

> **Project status:** This is a starting point for a fourth-year project. The application is adapted from an existing open-source example; the improvements in the roadmap below are ideas for future work, not completed features.

## How it works

1. **Add a source:** Enter the URL of a PDF in the sidebar.
2. **Build the knowledge base:** The app reads the document, creates embeddings with EmbeddingGemma, and stores them in LanceDB.
3. **Ask a question:** Your question is matched to relevant content through vector search.
4. **Explore the answer:** An Agno agent passes retrieved information to Llama 3.2 through Ollama and streams the response to the interface.

```text
PDF URL → Document processing → EmbeddingGemma → LanceDB
                                                  ↓
Question → Search → Retrieved content → Llama 3.2 → Answer
```

This technique is called **retrieval-augmented generation (RAG)**. Model inference and embeddings run locally after the models have been downloaded; fetching PDF URLs requires network access. Answers can still be incorrect, so check important information against the original document.

## Technology

| Component | Role |
| --- | --- |
| Python and Streamlit | Application and browser interface |
| Agno | Agent and knowledge-base orchestration |
| Ollama | Local model runtime |
| EmbeddingGemma | Text embeddings for search |
| LanceDB | Local vector storage and retrieval |
| Llama 3.2 | Answer generation |

## Get started on Windows

You need Python 3.12, [Git](https://git-scm.com/downloads), and [Ollama for Windows](https://ollama.com/download/windows). The models and application need several gigabytes of disk space; the available memory and hardware affect speed. After installing Ollama, open a new PowerShell window.

Clone this repository, then enter its folder:

```powershell
git clone https://github.com/pratikkadam6041/studywise-ai.git
cd studywise-ai
```

Create a virtual environment and install dependencies:

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Download the models and start the application:

```powershell
ollama pull embeddinggemma:latest
ollama pull llama3.2:latest
.\.venv\Scripts\python.exe -m streamlit run agentic_rag_embeddinggemma.py
```

Ollama must be running. Open the local address shown by Streamlit, add a publicly accessible PDF URL, and enter a question. The `openai` Python package is included because the currently installed Agno package requires it for an import; using this app with Ollama does not require an OpenAI API key.

## Example questions

- What are the main ideas in this lecture?
- Explain the difference between the two methods described in the paper.
- What assumptions does the author make?

## Roadmap

These are possible future additions; they are **not implemented yet**:

- Upload local PDFs and manage multiple documents.
- Show citations with document names and page numbers.
- Generate revision notes and practice quizzes.
- Evaluate answer correctness and compare retrieval methods.
- Improve handling of scanned PDFs and unanswerable questions.

## Project files

```text
agentic_rag_embeddinggemma.py  Streamlit application
requirements.txt               Python dependencies
agno.png, google.png, ollama.png  Interface images
LICENSE                        Upstream Apache 2.0 license
```

Local vector data is written under `tmp/lancedb`. Avoid committing private documents, downloaded models, environment files, or the virtual environment.

## Acknowledgements and license

This project starts from [Agentic RAG with EmbeddingGemma](https://github.com/Shubhamsaboo/awesome-llm-apps/tree/main/rag_tutorials/agentic_rag_embedding_gemma) in [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps), source commit `14c5f8ff0094659f321801385d2b52ccc65fea3b`. The application code and images originate from that project. This copy adds a new README, repository configuration, and an additional dependency required by the installed Agno version. The upstream project is distributed under the [Apache License 2.0](LICENSE); retain its license and attribution when redistributing or modifying this copy. Model licenses and terms are separate.

