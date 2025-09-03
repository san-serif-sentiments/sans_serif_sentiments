---
title: Ollama Output Tuning Guide
archetype: technical-doc
status: stable
owner: Shailesh “Shaily” Rawat
maintainer: self
version: 1.1
tags: [Ollama, Output Tuning, Parameters, JSON, Reproducibility, Beginner Guide]
last_reviewed: 2025-09-03
---

# Overview
This guide explains **how to control Ollama’s outputs** using runtime parameters.  
Every section shows:  
1. The **command** (copy-paste ready).  
2. The **syntax breakdown** (what each option means).  
3. The **expected output** (what you’ll see if it works).

---

# Why It Matters
- Without control: responses are long, random, or inconsistent.  
- With control: responses are concise, reproducible, and integration-ready (e.g., JSON).  
- This is essential before moving to RAG or agents.

---

# Key Parameters & Examples

## 1. Temperature — Randomness / Creativity
```bash
ollama run llama3.1 --temperature 0.2
````

**Syntax breakdown**

* `--temperature` → controls randomness in word choice.
* `0` = fully deterministic.
* `1` = very creative/random.

**Expected output** (low temperature → predictable)

```
>>> Write two slogans for a coffee shop
- Fresh brews, fast smiles.
- Your daily dose of warmth.
```

**Higher temperature example**

```bash
ollama run llama3.1 --temperature 0.9
```

**Expected output**

```
>>> Write two slogans for a coffee shop
- Brewtopia: Where beans become dreams.
- Liquid lightning in every cup!
```

---

## 2. num\_predict — Maximum Output Tokens

```bash
ollama run llama3.1 --num-predict 50
```

**Syntax breakdown**

* `--num-predict` → caps how many tokens are generated.
* ⚠️ **Tokens ≠ words ≠ characters**.

  * 1 token ≈ 3–4 characters (in English).
  * 50 tokens ≈ \~35–40 words (short paragraph).

**Expected output**

```
>>> Explain neural networks in simple terms.
Neural networks are systems inspired by the brain. They process information in layers, learning patterns from data.
```

(Output stops after \~40 words.)

---

## 3. top\_p — Probability Pool (Nucleus Sampling)

```bash
ollama run llama3.1 --top_p 0.8
```

**Syntax breakdown**

* `--top_p` → nucleus sampling.
* Model only chooses from the top p% of most likely words.
* Lower values → safer vocabulary. Higher values → more diverse.

**Expected output**

```
>>> Give 3 synonyms for "happy"
- joyful
- content
- pleased
```

With `--top_p 1.0`:

```
- jubilant
- ecstatic
- tickled pink
```

---

## 4. top\_k — Vocabulary Cutoff

```bash
ollama run llama3.1 --top_k 40
```

**Syntax breakdown**

* `--top_k` → consider only the top 40 most likely tokens at each step.
* Lower = tighter, safer predictions.

**Expected output**

```
>>> Write one line about AI
AI is transforming how we work and live.
```

(Uses common words, avoids unusual phrasing.)

---

## 5. repeat\_penalty — Avoid Loops

```bash
ollama run llama3.1 --repeat_penalty 1.2
```

**Syntax breakdown**

* `--repeat_penalty` → discourages repeating tokens.
* `1.0` = no penalty.
* `>1.0` = stronger penalty (common: 1.1–1.3).

**Expected output**

```
>>> Write a sentence about cats
Cats are curious animals that love exploring their surroundings.
```

Without penalty, you might see:

```
Cats are cute. Cats are cute. Cats are cute.
```

---

## 6. num\_ctx — Context Window (Memory)

```bash
ollama run llama3.1 --context 8192
```

**Syntax breakdown**

* `--context` (or `--num_ctx`) → sets how many tokens the model can “remember.”
* Includes **prompt + history + output**.
* Larger values = more memory but slower and heavier on RAM.

**Expected output**

```
>>> (Paste long text, then ask:) Summarize in 5 points
The model includes early parts of your text in the summary (no forgetting).
```

---

## 7. seed — Reproducibility

```bash
echo "Write a haiku about autumn." | ollama run llama3.1 --seed 42
```

**Syntax breakdown**

* `--seed` → sets the random generator seed.
* Same input + same seed = identical output.
* Different seed = variation.

**Expected output (with seed=42)**

```
Leaves fall quietly
Golden whispers paint the ground
Autumn breathes farewell
```

---

## 8. stop — Stop Sequences

```bash
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model":"llama3.1",
    "prompt":"List fruits:",
    "options":{"stop":["banana"]}
  }'
```

**Syntax breakdown**

* `"stop":["banana"]` → output stops as soon as “banana” appears.

**Expected output**

```
- apple
- orange
```

(Output cut before “banana”.)

---

## 9. system — Role Prompt

```bash
ollama run llama3.1 --system "You are a strict grammar checker."
```

**Syntax breakdown**

* `--system` → sets a persistent role instruction before user input.

**Expected output**

```
>>> The fox jump
Correction: The fox jumped.
```

---

# Practical API Example

Best practice: pass options in JSON via API.

```bash
curl http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{
    "model": "llama3.1",
    "prompt": "Summarize RAG in 3 bullets.",
    "options": {
      "temperature": 0.2,
      "top_p": 0.9,
      "num_predict": 80,
      "repeat_penalty": 1.15,
      "seed": 42
    }
  }'
```

**Expected output**

```json
{"response":"- Retrieves relevant documents\n- Uses them as context\n- Produces grounded answers","done":true}
```

---

# Practical ✅ / ❌

✅ **Controlled + reproducible**

```json
{"options":{"temperature":0.2,"num_predict":80,"seed":7}}
```

❌ **Unbounded + random**

```json
{"options":{"temperature":1.0,"num_predict":4096}}
```

---

# Known Issues & Fixes

* **Token confusion** → `num_predict` is tokens, not words.
* **JSON drift** → enforce `"Respond ONLY with JSON"`, `temperature=0`, and stop sequences.
* **Large `num_ctx`** → may cause OOM (out-of-memory). Use smaller models.
* **Repeat penalty too high (>1.5)** → can make responses unnatural.

---

# Tips & Best Practices

* For **deterministic summaries**: `temperature=0, top_p=1, seed=42`.
* For **creative brainstorming**: `temperature=0.8, top_p=1`.
* Use **stop sequences** for truncation in pipelines.
* Always **version-control prompt + options** for reproducibility.

---

# Resources

* [Ollama API Docs](https://github.com/ollama/ollama)
* [Tokenization Basics](https://huggingface.co/docs/transformers/tokenizer_summary)
* [LangChain Output Parsers](https://python.langchain.com/docs/modules/model_io/output_parsers/)

---

# Last Reviewed / Last Updated

2025-09-03

```

---
