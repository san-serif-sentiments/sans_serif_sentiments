# Ollama Local LLM - How To

## Install
| Command | Explanation |
|---------|-------------|
| `curl -fsSL https://ollama.com/install.sh \| sh` | Download and run Ollama installer (macOS/Linux). Windows → download installer from [ollama.com](https://ollama.com). |

---

## Run Models
| Command | Explanation |
|---------|-------------|
| `ollama run llama3.1` | Start **interactive session** with the `llama3.1` model. Auto-downloads if missing. |
| `echo "Explain RAG in 3 bullets." \| ollama run llama3.1` | Send a **one-shot prompt** directly from terminal. Prints the model’s response. |
| `ollama pull phi3.5` | Download a model without running it (pre-cache). |

---

## REST API (default port: `11434`)
| Command | Explanation |
|---------|-------------|
| `curl http://localhost:11434/api/generate -d '{"model":"llama3.1","prompt":"One sentence on embeddings."}'` | Call **text generation API**. Returns the response as JSON/text. |
| `curl http://localhost:11434/api/embeddings -d '{"model":"nomic-embed-text","prompt":"local vector search"}'` | Get **embeddings** (numeric vector) for semantic search or RAG. |

---

## Useful Tips
- Models are stored locally (usually in `~/.ollama/models`).
- Use `Ctrl+C` to stop a running model session.
- Smaller models (e.g., `phi3.5`) are faster; bigger ones (e.g., `llama3.1:70b`) need more RAM/VRAM.
- Ollama supports **JSON schema outputs** (good for tool use).
- Works well with frameworks (LangChain, CrewAI, LlamaIndex) by pointing to `http://localhost:11434`.

---

## Common Models to Try
| Model | Best Use |
|-------|----------|
| `llama3.1:8b` | General-purpose reasoning, solid default. |
| `mistral:7b` | Fast, versatile, lightweight. |
| `phi3.5` | Very small, fast structured tasks (JSON outputs, utilities). |
| `nomic-embed-text` | For embeddings in RAG/search. |

---

## Quick Test Workflow
1. Install Ollama  
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh

2. Run a model

ollama run llama3.1


3. Test a one-shot prompt

echo "Summarize RAG in 2 lines." | ollama run llama3.1


4. Test the API

curl http://localhost:11434/api/generate \
  -d '{"model":"llama3.1","prompt":"Say hello in French."}'




---
