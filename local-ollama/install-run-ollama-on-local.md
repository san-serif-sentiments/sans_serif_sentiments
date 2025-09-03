---
title: Ollama Local LLM – How To (All Platforms, Beginner-Friendly)
archetype: technical-doc
status: stable
owner: Shailesh “Shaily” Rawat
maintainer: self
version: 1.4
tags: [Ollama, Local LLM, Installation, REST API, Beginner Guide]
last_reviewed: 2025-09-03
---

# Overview
This guide explains **how to install and run Ollama on macOS, Linux, and Windows**.  
It is written for beginners — every command is shown with:  
1. The **full command** (ready to copy).  
2. A **syntax breakdown** (what each part means).  
3. The **expected output** (so you know it worked).

---

# Why It Matters
- **Cross-platform setup**: same workflow across Mac, Linux, Windows.  
- **Clarity**: syntax explained line by line, no blind copy-pasting.  
- **Confidence**: expected outputs help you confirm success.  

---

# Install Ollama

## macOS

### Option A — Homebrew (Recommended for devs)
```bash
brew install ollama
````

**Syntax breakdown**

* `brew` → Homebrew, the macOS package manager.
* `install` → tells Homebrew to install software.
* `ollama` → the package name.

**Expected output**
You’ll see Homebrew downloading and linking Ollama. At the end:

```
🍺  ollama was successfully installed!
```

Run the background service:

```bash
brew services start ollama
```

---

### Option B — Official App (GUI install)

1. Download `.dmg` from [ollama.com/download](https://ollama.com/download).
2. Drag **Ollama.app** into Applications.
3. Open Terminal and type:

   ```bash
   ollama run llama3.1
   ```

   → This both tests the install and downloads the model.

---

## Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

**Syntax breakdown**

* `curl` → download tool.
* `-f` → fail silently if error.
* `-sSL` → run silently, follow redirects.
* `https://ollama.com/install.sh` → official install script.
* `| sh` → pipe into shell for execution.

**Expected output**

```
Downloading Ollama...
Installing Ollama...
Ollama installed successfully.
```

Start the service:

```bash
ollama serve
```

---

## Windows

1. Download installer from [ollama.com/download](https://ollama.com/download).
2. Run the `.exe` and follow setup wizard.
3. Open **PowerShell** and run:

   ```powershell
   ollama run llama3.1
   ```

**Expected output**
Ollama downloads the model and enters interactive mode:

```
pulling manifest
pulling 7b-q4_K_M.bin (first run only)
>>>   (waiting for your prompt)
```

---

# Run Models (CLI)

## 1. Interactive mode

```bash
ollama run llama3.1
```

**Syntax breakdown**

* `ollama` → the CLI tool installed.
* `run` → subcommand to start a model.
* `llama3.1` → model name. If missing, it downloads automatically.

**Expected output**

```
pulling manifest (first run only)
>>> 
```

Now type:

```
Explain RAG in 2 sentences.
```

And the model responds with a short explanation.

Exit: `Ctrl + C`.

---

## 2. One-shot prompt

```bash
echo "Explain RAG in 3 bullets." | ollama run llama3.1
```

**Syntax breakdown**

* `echo "..."` → prints the text inside quotes.
* `|` (pipe) → sends that output to the next command.
* `ollama run llama3.1` → starts the model with your prompt.

**Expected output**

```
- Retrieves relevant documents
- Uses them as context
- Produces grounded answers
```

---

## 3. Pre-download model

```bash
ollama pull phi3.5
```

**Syntax breakdown**

* `pull` → fetch a model into cache.
* `phi3.5` → model name.

**Expected output**

```
pulling manifest for phi3.5
downloading 3.8GB model...
success
```

---

# REST API Basics

Ollama runs a server on **[http://localhost:11434](http://localhost:11434)**.

## 1. Generate text

```bash
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.1","prompt":"One sentence on embeddings."}'
```

**Syntax breakdown**

* `curl` → make HTTP requests.
* `http://localhost:11434/api/generate` → endpoint for text generation.
* `-H "Content-Type: application/json"` → tells server the request is JSON.
* `-d '{...}'` → `-d` means data; the JSON body with model + prompt.

**Expected output**

```json
{"model":"llama3.1","created_at":"...","response":"Embeddings turn text into vectors for search.","done":true}
```

---

## 2. Chat style

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

**Syntax breakdown**

* `/api/chat` → endpoint for multi-turn chat.
* `"messages"` → conversation history array.
* `"role"` → who is speaking (`system`, `user`, `assistant`).

**Expected output**

```json
{"response":"RAG retrieves documents and uses them to ground answers.","done":true}
```

---

## 3. Embeddings

```bash
curl http://localhost:11434/api/embeddings \
  -H "Content-Type: application/json" \
  -d '{"model":"nomic-embed-text","prompt":"local vector search"}'
```

**Syntax breakdown**

* `/api/embeddings` → endpoint for embeddings.
* `"model"` → use an embedding model.
* `"prompt"` → text to convert.

**Expected output**

```json
{"embedding":[0.0132,-0.0417,0.0729,...],"num_dimensions":768}
```

---

# Useful Tips

* Models are stored in:

  * macOS: `~/.ollama/models`
  * Linux: `/usr/share/ollama/.ollama/models`
  * Windows: `C:\Users\<USER>\.ollama\models`
* Stop a model: `Ctrl + C`.
* Smaller models = faster. Larger = need more RAM/VRAM.
* For JSON-only output: add `"Respond ONLY with JSON"` in your prompt.
* API integrates with LangChain, LlamaIndex, CrewAI at `http://localhost:11434`.

---

# Quick Workflow Recap

```bash
# Install
brew install ollama         # macOS
# OR
curl -fsSL https://ollama.com/install.sh | sh   # Linux

# Run interactive
ollama run llama3.1

# One-shot
echo "Summarize RAG in 2 lines." | ollama run llama3.1

# Test API
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model":"llama3.1","prompt":"Say hello in French."}'
```

---

# Troubleshooting

| Symptom                                         | Cause                          | Fix                                             |
| ----------------------------------------------- | ------------------------------ | ----------------------------------------------- |
| “This script is intended to run on Linux only.” | You ran Linux command on macOS | Use `brew install ollama` or DMG installer.     |
| `curl` outputs streaming chunks                 | Ollama streams by default      | Add `"stream":false` in JSON.                   |
| Port 11434 already in use                       | Another service is bound       | Run `OLLAMA_HOST=127.0.0.1:11500 ollama serve`. |
| Out of memory                                   | Model too large                | Use quantized models (`:Q4_K_M`).               |
| Downloads blocked                               | Corporate proxy                | Pre-pull models manually, set system proxy.     |

---

# Next Step

You now know how to **install and run models**.
Move on to the [Ollama Tuning Guide](./OLLAMA_TUNING.md) to learn **how to control randomness, length, and structure of outputs**.

---

# Last Reviewed / Last Updated

2025-09-03

```

---
