---
title: Ollama Setup Guide (All Platforms)
archetype: technical-doc
status: stable
owner: Shailesh “Shaily” Rawat
maintainer: self
version: 1.0
tags: [Ollama, Local LLM, Installation, Quick Start]
last_reviewed: 2025-09-03
---

# Overview
This guide is the **quick-start reference** for setting up Ollama on **macOS, Linux, and Windows**.  
It focuses on **installation, running models, REST API basics, and sanity checks**.

If you want to control or fine-tune Ollama outputs, see [Tuning Guide](./OLLAMA_TUNING.md).

---

# Why It Matters
Before you integrate LLMs into pipelines, you need a **reliable local runtime**.  
Ollama makes it simple to pull, run, and serve models with one command.

---

# Install Ollama

## macOS
- **Option A – Official App (Recommended)**
  1. Download the `.dmg` from [ollama.com/download](https://ollama.com/download).
  2. Drag **Ollama.app** into Applications.
  3. Test in Terminal:
     ```bash
     ollama run llama3.1
     ```

- **Option B – Homebrew**
  ```bash
  brew install ollama/tap/ollama
  ollama serve
````

## Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama serve
```

## Windows

1. Download installer from [ollama.com/download](https://ollama.com/download).
2. Run setup wizard.
3. Test in **PowerShell**:

   ```powershell
   ollama run llama3.1
   ```

---

# Run Models (CLI)

| Command                                                   | Explanation                                                     |
| --------------------------------------------------------- | --------------------------------------------------------------- |
| `ollama run llama3.1`                                     | Start **interactive** session. Auto-downloads model if missing. |
| `echo "Explain RAG in 3 bullets." \| ollama run llama3.1` | One-shot prompt from stdin.                                     |
| `ollama pull phi3.5`                                      | Pre-download a model.                                           |
| `ollama list`                                             | List available models.                                          |
| `ollama rm llama3.1`                                      | Remove model to free disk.                                      |
| `ollama ps`                                               | List active sessions.                                           |
| `Ctrl+C`                                                  | Exit session.                                                   |

> PowerShell piping syntax:
> `"Explain RAG in 3 bullets." | ollama run llama3.1`

---

# REST API (Default: `http://localhost:11434`)

### Text generation

```bash
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.1","prompt":"One sentence on embeddings."}'
```

### Chat style

```bash
curl http://localhost:11434/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "model":"llama3.1",
    "messages":[
      {"role":"system","content":"Be concise."},
      {"role":"user","content":"Explain RAG in 2 lines."}
    ]
  }'
```

### Embeddings

```bash
curl http://localhost:11434/api/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"nomic-embed-text","prompt":"local vector search"}'
```

---

# Quick Test Workflow

## macOS/Linux

```bash
ollama run llama3.1
echo "Summarize RAG in 2 lines." | ollama run llama3.1
curl http://localhost:11434/api/generate \
  -d '{"model":"llama3.1","prompt":"Say hello in French."}'
curl http://localhost:11434/api/embeddings \
  -d '{"model":"nomic-embed-text","prompt":"vector search"}'
```

## Windows (PowerShell)

```powershell
ollama run llama3.1
"Summarize RAG in 2 lines." | ollama run llama3.1
Invoke-RestMethod -Uri http://localhost:11434/api/generate -Method Post -Body '{"model":"llama3.1","prompt":"Say hello in French."}' -ContentType "application/json"
```

---

# Troubleshooting

| Issue                                           | Fix                                                      |
| ----------------------------------------------- | -------------------------------------------------------- |
| “This script is intended to run on Linux only.” | You tried Linux install on macOS. Use Homebrew or DMG.   |
| Download slow                                   | Pre-pull smaller models (`ollama pull mistral`).         |
| Port conflict                                   | Change host: `OLLAMA_HOST=127.0.0.1:11500 ollama serve`. |
| Memory errors                                   | Use quantized models (e.g., `:Q4_K_M` suffix).           |
| Behind proxy                                    | Configure proxy in system or pre-seed models.            |

---

# Resources

* [Ollama Docs](https://github.com/ollama/ollama)
* [Model Library](https://ollama.com/library)
* [LangChain Integration](https://python.langchain.com/docs/integrations/llms/ollama)

---

# Next Step

Now that you can run models, learn how to **tune their outputs** → [Ollama Tuning Guide](./OLLAMA_TUNING.md).

---

# Last Reviewed / Last Updated

2025-09-03
