--- 
title: Gen AI/ML Handbook – Chapter 3: Transformers & Hugging Face
archetype: guidebook
status: draft
owner: Shailesh Rawat
maintainer: self
version: 0.2
tags: [Transformers, HuggingFace, PyTorch, TensorFlow, GenAI]
last_reviewed: 2025-09-03
---

# Overview
This chapter explains **transformers** — the core architecture behind today’s AI models — and how to use **Hugging Face**, the most popular library and platform for working with them.  

Transformers power **ChatGPT, Claude, LLaMA, Mistral, BERT, GPT-4, Gemini**.  
They are what made language-based AI (GenAI) **actually useful for enterprises**.  

We’ll keep it simple:
- What a transformer is (and why it’s different from older models).  
- How Hugging Face makes transformers easy to use.  
- A few **hands-on examples** that show enterprise relevance (classification, question answering, entity extraction).  

---

# Why It Matters
- **Transformers = the brain of modern AI.** Without them, you only had older models that couldn’t handle long sentences well.  
- **Hugging Face = the toolbox.** It’s like GitHub but for AI models: download, share, fine-tune, deploy.  
- **Enterprises care.** Almost all AI/ML job descriptions now mention Hugging Face or transformers.  

---

# Key Terms in Plain English
- **Transformer**: A type of neural network that understands the *relationships between words in context*.  
  - Example: “bank” in *“river bank”* vs. *“money bank.”* Older models would confuse them; transformers can tell the difference.  

- **Attention**: The “spotlight” inside a transformer.  
  - It highlights which words in a sentence matter to each other.  
  - Example: In *“The dog that chased the cat was fast”*, attention links “dog” ↔ “fast.”  

- **BERT (Bidirectional Encoder Representations from Transformers)**: Reads text both left-to-right and right-to-left.  
  - Good for understanding and classifying text (e.g., sentiment, categories).  

- **GPT (Generative Pretrained Transformer)**: Predicts the next word in a sequence.  
  - Good for generating text (chatbots, summarization, creative writing).  

- **Embedding**: A way to turn words or sentences into numbers (vectors) that capture meaning.  
  - Example: “car” and “automobile” → embeddings close together in vector space.  

- **Hugging Face Hub**: A website where thousands of models are stored.  
  - Think: *App Store for AI models*.  

- **Pipeline**: A Hugging Face shortcut for common tasks.  
  - Instead of building everything manually, you can say `pipeline("sentiment-analysis")` and get results.  

---

# Hugging Face Pipelines (Quick Wins)
```python
from transformers import pipeline

# Sentiment Analysis
classifier = pipeline("sentiment-analysis")
print(classifier("I love working with AI systems!"))
# → [{'label': 'POSITIVE', 'score': 0.999}]

# Named Entity Recognition (NER)
ner = pipeline("ner", grouped_entities=True)
print(ner("Barclays is hiring a GenAI Lead in Pune."))
# → [{'entity_group': 'ORG', 'word': 'Barclays'}, {'entity_group': 'LOC', 'word': 'Pune'}]

# Question Answering
qa = pipeline("question-answering")
context = "TensorFlow and PyTorch are the two most popular deep learning frameworks."
print(qa(question="Which frameworks are popular?", context=context))
# → {'answer': 'TensorFlow and PyTorch'}

Why this matters:

Enterprises use these three tasks everywhere:

HR → classify employee queries.

Finance → extract invoice details.

Supply Chain → answer product/order questions.




---

How Transformers Actually Work (Simple View)

1. Break text into tokens (chunks like words or sub-words).


2. Convert tokens into embeddings (numbers).


3. Apply attention: figure out which words depend on each other.


4. Stack layers of attention + feedforward networks.


5. Output predictions: classification, next word, embedding vector, etc.



Think of a transformer as a team of readers:

One reader focuses on who is mentioned,

Another focuses on actions,

Another on time.
Together, they build a strong understanding.



---

Loading Models with Hugging Face

PyTorch Example

from transformers import AutoTokenizer, AutoModel
import torch

# Load model & tokenizer
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModel.from_pretrained("bert-base-uncased")

# Encode text
inputs = tokenizer("GenAI is changing enterprises.", return_tensors="pt")
outputs = model(**inputs)

print(outputs.last_hidden_state.shape)  # (batch_size, tokens, hidden_dim)

TensorFlow Example

from transformers import TFAutoModel, AutoTokenizer

# Load model
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = TFAutoModel.from_pretrained("bert-base-uncased")

inputs = tokenizer("Transformers power modern chatbots.", return_tensors="tf")
outputs = model(inputs)

print(outputs.last_hidden_state.shape)

✅ Why both? Because some companies use PyTorch, others use TensorFlow — but Hugging Face supports both.


---

Fine-Tuning Basics

You rarely train transformers from scratch. Instead, you:

Take a pretrained model (e.g., BERT).

Add a small custom layer (for classification).

Train it briefly on your own dataset.


Example: classify HR support tickets as leave request, payroll issue, IT query.

from transformers import AutoTokenizer, AutoModelForSequenceClassification, Trainer, TrainingArguments
from datasets import load_dataset

# Load dataset (emotion classification as example)
dataset = load_dataset("emotion")

tokenizer = AutoTokenizer.from_pretrained("distilbert-base-uncased")
def tokenize(batch):
    return tokenizer(batch["text"], padding=True, truncation=True)

dataset = dataset.map(tokenize, batched=True)

model = AutoModelForSequenceClassification.from_pretrained("distilbert-base-uncased", num_labels=6)

training_args = TrainingArguments(
    output_dir="./results",
    evaluation_strategy="epoch",
    per_device_train_batch_size=16,
    num_train_epochs=1
)

trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=dataset["train"].shuffle().select(range(200)),
    eval_dataset=dataset["test"].shuffle().select(range(50))
)

trainer.train()


---

Enterprise Applications

HR → Q&A over policies, classify employee queries.

Finance → extract entities from invoices (amount, date, vendor).

Supply Chain → summarize delivery reports, flag delays.



---

Insights

Transformers replaced older RNN/LSTM models because they scale better and capture long-range dependencies.

Hugging Face abstracts away complexity → lets you focus on business problem, not ML plumbing.

Fine-tuning with LoRA or adapters (lighter methods) is popular in enterprises because it’s cheaper and faster than full retraining.



---

Practical Example (✅/❌)

✅ Example: Use DistilBERT via Hugging Face to fine-tune a model for classifying HR queries.
❌ Non-example: Training GPT-3 scale models from scratch (costs millions).


---

Known Issues & Friction Points

Transformers are large → memory/GPU bottlenecks for beginners.

Pipelines are easy but hide “what’s going on under the hood.”

Tokenization can confuse new learners: why text becomes sub-words (e.g., “unhappiness” → “un”, “##happy”, “##ness”).



---

Tips & Best Practices

Start with pipelines → then move to AutoModel for more control.

Use smaller models (DistilBERT, MiniLM) for quick learning.

Save models locally → enterprises need reproducibility.

Learn Hugging Face Datasets → clean datasets = better fine-tuning.



---

Success Metrics & Outcomes

By the end of this chapter you should:

Be able to explain what a transformer is in simple terms.

Run Hugging Face pipelines for sentiment, NER, Q&A.

Load a transformer model/tokenizer in PyTorch or TensorFlow.

Understand the basics of fine-tuning for enterprise tasks.



---

Resources & References

The Illustrated Transformer (Jay Alammar)

Hugging Face Transformers Docs

Hugging Face Datasets

Hugging Face Course



---

Last Reviewed / Last Updated

2025-09-03

---

