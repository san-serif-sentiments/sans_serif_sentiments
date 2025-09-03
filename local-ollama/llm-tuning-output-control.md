---
title: LLM Output Control with Ollama
archetype: technical-doc
status: stable
owner: Shailesh Rawat
maintainer: self
version: 1.0
tags: [Ollama, LLMs, Output Tuning, Local AI]
last_reviewed: 2025-09-03
---

# Overview
This guide explains how to **control and tune local LLM outputs in Ollama**.  
It covers all key runtime parameters with examples, expected behavior, and best practices.  
Use this as a reference when building RAG pipelines or agent workflows.

---

# Why It Matters
LLMs are **stochastic** (random by design). Without tuning, they may:
- Produce overly long or inconsistent responses.
- Repeat phrases or drift off-topic.
- Fail to generate structured outputs (e.g., JSON).

By controlling parameters like **temperature, top_p, num-predict, and context**, you can make outputs **predictable, concise, or creative** — depending on the task.

---

# Audience, Scope & Personas
- **Business Analysts / Tech Writers**: Ensure LLM outputs remain concise and structured.  
- **Developers / ML Engineers**: Tune model behavior for reproducibility and API integration.  
- **Change / Adoption Managers**: Control narrative tone and reliability of AI-assisted outputs.

---

# Key Parameters Explained

## 1. Temperature – Randomness
**Syntax**
```bash
ollama run llama3.1 --temperature 0.2

Definition

Controls randomness of word selection.

Range: 0 (deterministic) → 1 (creative).

Lower = safer, repeatable. Higher = diverse, imaginative.


Example

0.2 → “Fresh brews, fast smiles.”

0.9 → “Brewtopia: Where beans become dreams.”



---

2. num-predict – Max Tokens to Generate

Syntax

ollama run llama3.1 --num-predict 50

Definition

num-predict sets the maximum number of tokens the model can generate.

A token ≠ word ≠ character:

1 token ≈ 4 characters in English.

“Neural networks are amazing” = 5 words, ~7 tokens.


50 tokens ≈ 35–40 words of output (short paragraph).

If unset, model generates until it thinks the response is “complete.”


Use Case

Summaries, limiting verbosity, preventing runaway text.



---

3. top_p – Probability Pool (Nucleus Sampling)

Syntax

ollama run llama3.1 --top_p 0.8

Definition

Instead of choosing from all possible words, the model samples from the top_p probability mass.

Example: If top_p=0.8, the model only considers the top 80% most likely words.

Lower = safer vocabulary, fewer rare words.

Higher = more diverse vocabulary.


Example

0.8 → “joyful, content, pleased.”

1.0 → “ecstatic, jubilant, tickled pink.”



---

4. repeat_penalty – Discourage Repetition

Syntax

ollama run llama3.1 --repeat_penalty 1.2

Definition

Penalizes reuse of the same words/tokens.

Default: 1.0 (no penalty).

Range: 1.1–1.3 is effective.


Example

Without penalty → “Cats are cute. Cats are cute. Cats are cute.”

With penalty → “Cats are curious animals that explore their surroundings.”



---

5. context – Model Memory Window

Syntax

ollama run llama3.1 --context 8192

Definition

Sets how many tokens the model can “remember.”

Includes prompt + history + output.

Larger context = better long-doc handling, but uses more RAM and slows down.

Typical ranges: 2048, 4096, 8192, up to 32k depending on model.


Example

2048 → forgets earlier parts of a long story.

8192 → retains details across multiple paragraphs.



---

6. seed – Reproducibility

Syntax

echo "Write a haiku about autumn." | ollama run llama3.1 --seed 42

Definition

Fixes the random number generator.

Same seed + same input → identical output every time.

Different seed → different variation.


Example

Seed 42 → “Leaves fall quietly / Golden whispers paint the ground / Autumn breathes farewell.”

Seed 99 → A completely new haiku.



---

7. system – Role / Behavior

Syntax

ollama run llama3.1 --system "You are a strict grammar checker."

Definition

Defines the system prompt (model role).

Shapes tone and style of every response.


Example

Input: “The quick brown fox jump over the lazy dog.”

Output: “Correction: The quick brown fox jumped over the lazy dog.”



---

8. Structured Outputs (JSON Enforcement)

Syntax

echo 'Respond only in JSON: {"task":"...","priority":"..."} for "buy milk".' \
| ollama run llama3.1 --temperature 0

Definition

Instructs model to respond in machine-readable format.

Combine with low temperature for reliability.


Example

{"task":"buy milk","priority":"high"}


---

Practical Examples & Templates (✅/❌)

✅ Use:

echo "Summarize this in 3 bullets." | ollama run llama3.1 --num-predict 60 --temperature 0.3

❌ Don’t:

ollama run llama3.1 --temperature 1 --num-predict 5000

(Too random, too long, may crash low-RAM systems.)


---

Known Issues & Friction Points

Token mismatch: Beginners confuse tokens with words or characters.

Structured JSON drift: Even with instructions, models sometimes add commentary.

Large context slowdowns: Setting --context 32k can overload RAM/VRAM.

Repeat penalty too high: (>1.5) may make responses unnatural.



---

Tips & Best Practices

For deterministic outputs → --temperature 0 --top_p 1 --seed 42.

For creative brainstorming → --temperature 0.8 --top_p 1.

For short summaries → --num-predict 50–80.

Always combine system prompts with temperature control.

Validate JSON outputs before using them in tools.



---

Dependencies, Risks & Escalation Path

Ollama versions may add/remove parameter support.

Not all models respect every setting equally.

Larger context windows demand more hardware; fallback to smaller models if OOM occurs.



---

Success Metrics & Outcomes

Predictability: Outputs reproducible under same seed.

Efficiency: Responses truncated when expected.

Reliability: JSON/tool outputs parse cleanly.

Performance: Larger context windows handled without slowdown.



---

Resources & References

Ollama Docs

Tokenization Explained

Prompting with JSON Schemas



---

Last Reviewed / Last Updated

2025-09-03

---
