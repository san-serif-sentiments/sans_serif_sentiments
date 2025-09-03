# Local LLM – Side-by-Side Syntax & Output (Ollama)

> Goal: show exactly what you type vs. what you’ll see.  
> Note: Outputs will vary slightly by model/version; these are representative.

---

## 1) Install Ollama

| What you type | Example output |
|---|---|
| ```bash\ncurl -fsSL https://ollama.com/install.sh \| sh\n``` | ```text\nDownloading Ollama...\nInstalling to /usr/local/bin/ollama\nOllama installed.\nTo start the service run: sudo systemctl start ollama (Linux) or it's started automatically on macOS.\n``` |

**What this does:** Downloads and runs the official install script. After this, the `ollama` command is available.

---

## 2) Run a Model (Interactive session)

| What you type | Example output |
|---|---|
| ```bash\nollama run llama3.1\n``` | ```text\npulling manifest\npulling 7b-q4_K_M.bin (first run only; ~few minutes)\n...\n>>> (your cursor is waiting for a prompt)\n``` |

**How to use interactive mode:**  
Type your prompt after `>>>` and press Enter.

| You type at the prompt | You see |
|---|---|
| ```text\nExplain RAG in two bullet points.\n``` | ```text\n- RAG retrieves the most relevant passages from a knowledge base.\n- The model uses those passages to ground its answer and reduce hallucinations.\n>>> \n``` |

Exit with `Ctrl+C`.

---

## 3) One-Shot Prompt (pipe into the model)

| What you type | Example output |
|---|---|
| ```bash\necho "Explain RAG in 3 bullets." \| ollama run llama3.1\n``` | ```text\n- It converts your query into a search for relevant documents.\n- Retrieved snippets are fed back into the model as context.\n- The answer is grounded in those snippets, lowering hallucinations.\n``` |

**What this does:** Sends one prompt through stdin, prints the model’s reply, and exits.

---

## 4) Text Generation via REST API

| What you type | Example output |
|---|---|
| ```bash\ncurl http://localhost:11434/api/generate \\\n  -H "Content-Type: application/json" \\\n  -d '{\"model\":\"llama3.1\",\"prompt\":\"One sentence on embeddings.\"}'\n``` | ```json\n{\"model\":\"llama3.1\",\"created_at\":\"2025-09-03T11:22:10Z\",\"response\":\"Embeddings map text to numeric vectors that capture meaning for search and retrieval.\",\"done\":true}\n``` |

**What this does:** Calls the local HTTP endpoint (`/api/generate`) with JSON.  
**Tip:** Many clients stream partial JSON chunks; the final object has `"done": true`.

---

## 5) Embeddings via REST API

| What you type | Example output |
|---|---|
| ```bash\ncurl http://localhost:11434/api/embeddings \\\n  -H \"Content-Type: application/json\" \\\n  -d '{\"model\":\"nomic-embed-text\",\"prompt\":\"local vector search\"}'\n``` | ```json\n{\"embedding\":[0.0132,-0.0417,0.0729, ... , -0.0055],\"num_dimensions\":768}\n``` |

**What this does:** Returns a numeric vector for the text. Store these in a vector DB (e.g., FAISS/Chroma) for semantic search/RAG.

---

## 6) Pre-Download (Pull) a Model

| What you type | Example output |
|---|---|
| ```bash\nollama pull phi3.5\n``` | ```text\npulling manifest for phi3.5\npulling 3.8GB model (quantized)\nverifying sha256 ... done\nsuccess\n``` |

**What this does:** Downloads the model so it’s ready to use offline later.

---

## 7) JSON-Like Output (prompt steering)

| What you type | Example output |
|---|---|
| ```bash\necho 'Return JSON with keys: {"task":"name","priority":"high|low"} for "clean inbox".' \| ollama run llama3.1\n``` | ```json\n{\"task\":\"clean inbox\",\"priority\":\"high\"}\n``` |

**What this does:** Coaxes the model to emit structured text you can parse.  
**Tip:** Add “Respond with only valid JSON” to tighten formatting.

---

## 8) Basic Parameter Control (temperature, tokens)

> Ollama lets you set parameters via a model “modelfile” or inline options in some clients.  
> Quick inline style (supported by many wrappers) is shown below; if your client doesn’t support it, create a Modelfile.

| What you type | Example output |
|---|---|
| ```bash\necho \"Give three concise bullets on RAG.\" \| ollama run llama3.1 --temperature 0.2 --num-predict 128\n``` | ```text\n- Retrieves relevant context before answering.\n- Grounds responses in sources to reduce hallucinations.\n- Works best with clean chunking and top-k retrieval.\n``` |

**What this does:** Lowers randomness (`temperature`) and caps tokens generated (`num-predict`).

---

## 9) Quick Health Checks

| What you type | Example output |
|---|---|
| ```bash\nollama --version\n``` | ```text\nollama version 0.x.y (platform details)\n``` |
| ```bash\ncurl http://localhost:11434/api/tags\n``` | ```json\n{\"models\":[{\"name\":\"llama3.1\",\"size\":...},{\"name\":\"phi3.5\",\"size\":...}]}\n``` |

**What this does:** Verifies CLI version and lists locally available models via API.

---

## Notes & Gotchas

- First run of a model triggers a **download**; subsequent runs start immediately.  
- If `curl` outputs multiple JSON lines, your client is **streaming**; the final line has `"done": true`.  
- If responses are slow: try a **smaller quantized model** (e.g., `:8b` or `phi3.5`) and keep prompts/context short.  
- Embeddings dimensionality depends on the embedding model (e.g., 768 or 1024).  
- For deterministic, parseable outputs, keep prompts short and enforce **JSON-only** in your instruction.

---

## Minimal Sanity Sequence (copy/paste)

```bash
# Install
curl -fsSL https://ollama.com/install.sh | sh

# Run once interactively (downloads if needed)
ollama run llama3.1

# One-shot
echo "Summarize RAG in two lines." | ollama run llama3.1

# API test
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.1","prompt":"Say hello in French."}'

# Embedding test
curl http://localhost:11434/api/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"nomic-embed-text","prompt":"local vector search"}'