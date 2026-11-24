# 📚 StudyWise AI

### Ask better questions of your study material.

An early-stage, local-first document question-answering project using Python, retrieval-augmented generation, and open models.

[How it works](#how-it-works) · [Get started](#get-started) · [Roadmap](#roadmap) · [Credits](#credits-and-license)

---

> **Project status: Baseline.** StudyWise AI currently adapts an existing open-source RAG example. The application is the upstream starting point; the roadmap describes planned work, not completed features. See [Credits and license](#credits-and-license).

> **Validation:** The Python dependencies were installed and required imports were checked. An end-to-end test with Ollama, both models, and a PDF remains to be completed.

## The idea

Lecture notes, research papers, and course guides can be difficult to navigate when you need one specific answer. StudyWise AI explores a simpler workflow: add a PDF source, ask a question, and use relevant document content to help a language model respond.

The current application accepts **PDF URLs**, creates a searchable knowledge base, and streams an answer in a browser interface. It is a starting point for an academic project on document retrieval, answer quality, and the practical limits of language models.

## Current implementation

- **PDF URL input:** Add a reachable PDF URL through the sidebar.
- **Semantic retrieval:** Represent document content with EmbeddingGemma and search it using LanceDB.
- **Local model inference:** Use Llama 3.2 through Ollama to generate responses.
- **Interactive interface:** Ask questions and view streamed answers in Streamlit.
- **Local vector storage:** Store LanceDB data in `tmp/lancedb` relative to the working directory.

The current app has **no local file upload, page-level citations, evaluation dashboard, or guarantee that answers are supported**. Those are candidates for future work. Inference and embeddings run locally after the models are available; fetching a PDF URL requires network access.

## How it works

StudyWise AI uses **retrieval-augmented generation (RAG)**: it searches for relevant document content and gives that context to a language model before it answers.

```text
INDEXING
PDF URL  →  Read and process document  →  EmbeddingGemma  →  LanceDB

QUESTION ANSWERING
Question  →  Search LanceDB  →  Relevant content  →  Llama 3.2  →  Answer
```

1. **Add a source.** Provide a PDF URL in the sidebar. The application adds its content to the knowledge base.
2. **Create a searchable representation.** EmbeddingGemma converts text into numerical vectors stored in LanceDB.
3. **Retrieve context.** When a question is asked, the agent searches the knowledge base for relevant information.
4. **Generate an answer.** Llama 3.2, served by Ollama, generates a response for the Streamlit interface.

Retrieval can miss important information, and the model can produce inaccurate answers. **Check important responses against the original document.**

## Tech stack

| Component | Role |
| --- | --- |
| **Python** | Application language |
| **Streamlit** | Browser interface |
| **Agno** | Agent and knowledge-base orchestration |
| **Ollama** | Local model runtime |
| **EmbeddingGemma** | Text embeddings for retrieval |
| **LanceDB** | Vector storage and search |
| **Llama 3.2** | Response generation |
| **pypdf** | PDF processing dependency |

## Get started

### Prerequisites

- **Python 3.12** and Git.
- [Ollama for Windows](https://ollama.com/download/windows), installed and running. Restart PowerShell after installation if the `ollama` command is not found.
- Internet access to install dependencies, download the models, and retrieve a PDF URL.
- Several gigabytes of free disk space. The default models together require roughly 2.6 GB, in addition to Ollama, Python packages, and document data. Performance depends on your computer.

These steps use **Windows PowerShell**. Run each command separately and wait for it to finish.

### 1. Download the project

```powershell
git clone https://github.com/pratikkadam6041/studywise-ai.git
cd studywise-ai
```

### 2. Set up Python dependencies

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

If `py -3.12` cannot find Python, install a working Python 3.12 distribution and reopen PowerShell. The `openai` Python package is included because the tested Agno version required it during imports; this local Ollama workflow does not require an OpenAI API key.

### 3. Download the models

```powershell
ollama pull embeddinggemma:latest
ollama pull llama3.2:latest
```

These are the model names used by the current code. Ollama must be running before you use the application.

### 4. Start StudyWise AI

```powershell
.\.venv\Scripts\python.exe -m streamlit run agentic_rag_embeddinggemma.py
```

Open the local URL printed by Streamlit, normally `http://localhost:8501`. Add a reachable PDF URL in the sidebar, wait for processing, and enter a question.

### Try asking

> “What are the main ideas in this document?”

> “How does the author compare the two approaches?”

> “What assumptions or limitations are described?”

## Troubleshooting

| Problem | What to check |
| --- | --- |
| `requirements.txt` not found | Run `cd studywise-ai` first, then retry the installation command. |
| `ollama` is not recognized | Install Ollama and open a new PowerShell window. |
| A model is missing | Run the two `ollama pull` commands above and wait for each download to finish. |
| Python 3.12 cannot be found | Install or repair Python 3.12, then confirm `py -3.12 --version` works. |
| The PDF cannot be loaded | Check that the URL is reachable and points to a PDF. Image-only scans may not provide usable text. |
| An answer looks wrong | Compare it with the source; the baseline does not verify claims or provide reliable page citations. |

## Roadmap

The next stage is to turn the baseline into a more useful and measurable student project:

1. **Document management:** Add local PDF uploads and controls for multiple documents.
2. **Trustworthy references:** Attach document names, page numbers, and supporting passages to answers.
3. **Evidence-aware responses:** Handle missing information and unanswerable questions more clearly.
4. **Evaluation:** Build a question set and measure retrieval success, answer correctness, citation accuracy, and response time.
5. **Study tools:** Explore source-based summaries and practice quizzes after the core workflow is reliable.

These are proposed improvements. No accuracy, speed, or other benchmark results are claimed yet.

## Project structure

```text
studywise-ai/
├── agentic_rag_embeddinggemma.py  # Streamlit app and RAG workflow
├── requirements.txt               # Python dependencies
├── agno.png                       # Interface asset
├── google.png                     # Interface asset
├── ollama.png                     # Interface asset
├── .gitignore                     # Local files excluded from Git
├── LICENSE                        # Apache License 2.0
└── README.md
```

The PDF URL list is held in the Streamlit session; vector data is written to `tmp/lancedb` in the `studywise_documents` table. Earlier copies used a `recipes` table; its indexed PDFs are not automatically moved to the new table and must be added again. Avoid committing private documents, model files, virtual environments, or secrets.

## Credits and license

The application code and interface images come from [Agentic RAG with EmbeddingGemma](https://github.com/Shubhamsaboo/awesome-llm-apps/tree/main/rag_tutorials/agentic_rag_embedding_gemma), part of [Shubhamsaboo/awesome-llm-apps](https://github.com/Shubhamsaboo/awesome-llm-apps), at source commit [`14c5f8ff0094659f321801385d2b52ccc65fea3b`](https://github.com/Shubhamsaboo/awesome-llm-apps/commit/14c5f8ff0094659f321801385d2b52ccc65fea3b). This repository adds project documentation, repository configuration, a dependency adjustment, and small naming changes. The application remains an adaptation of the upstream baseline.

The upstream repository is distributed under the [Apache License 2.0](LICENSE). Model licenses and terms are separate; review them before use or redistribution.
