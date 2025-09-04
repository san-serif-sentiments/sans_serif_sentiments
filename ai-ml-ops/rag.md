--- 
title: Gen AI/ML Handbook – Chapter 5: Generative AI & RAG
archetype: guidebook
status: draft
owner: Shailesh Rawat
maintainer: self
version: 0.1
tags: [GenAI, RAG, HuggingFace, LangChain, Enterprise]
last_reviewed: 2025-09-03
---

# Overview
Generative AI (GenAI) refers to models that **create new content** — text, images, code, audio — instead of just classifying or predicting.  
Retrieval-Augmented Generation (RAG) is a **design pattern** where an LLM is connected to a **knowledge source** (like a database or document store) so it produces **grounded, accurate answers**.  

Together, GenAI + RAG form the **most common enterprise use case today**: chatbots, assistants, and copilots that can answer real business questions.  

---

# Why It Matters
- **GenAI alone is unreliable** → it hallucinates (makes up answers).  
- **RAG fixes this** by letting the model retrieve facts before generating.  
- **Enterprises demand RAG**: it ensures answers are aligned with **internal policies, data, and compliance**.  
- **Job descriptions** increasingly say: “Experience with RAG pipelines.”  

---

# Key Terms Explained
- **Generative AI**: AI that produces new content (LLMs like GPT, LLaMA, Claude).  
- **Prompt Engineering**: Designing inputs to guide model outputs.  
- **Retrieval**: Searching for relevant documents from a database.  
- **Embedding**: Converting text into a vector (numeric representation of meaning).  
- **Vector Database**: Specialized DB that stores embeddings and finds similar text (FAISS, Pinecone, Weaviate, pgvector).  
- **RAG (Retrieval-Augmented Generation)**: Retrieve facts → feed them into the LLM → generate accurate answer.  

---

# Simple Example: GenAI Without RAG
```python
from langchain.chat_models import ChatOpenAI

llm = ChatOpenAI(temperature=0)
response = llm.predict("What is the HR leave policy at our company?")
print(response)

⚠️ Problem: The LLM will “guess” — it doesn’t know your HR policy.


---

Adding Retrieval (RAG Example)

from langchain.chat_models import ChatOpenAI
from langchain.chains import RetrievalQA
from langchain.vectorstores import FAISS
from langchain.embeddings import OpenAIEmbeddings
from langchain.text_splitter import CharacterTextSplitter
from langchain.docstore.document import Document

# Sample HR documents
docs = [
    Document(page_content="Employees are entitled to 20 days of annual leave."),
    Document(page_content="Sick leave requires a medical certificate if more than 2 days.")
]

# Step 1: Split docs
splitter = CharacterTextSplitter(chunk_size=200, chunk_overlap=50)
chunks = splitter.split_documents(docs)

# Step 2: Embed docs
embeddings = OpenAIEmbeddings()
vectorstore = FAISS.from_documents(chunks, embeddings)

# Step 3: Create RAG pipeline
qa = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(temperature=0),
    retriever=vectorstore.as_retriever()
)

# Ask question
print(qa.run("How many days of annual leave do employees get?"))

✅ Now the answer comes from your documents, not the model’s imagination.


---

Enterprise Applications

HR → Answer policy questions with RAG grounded in HR manuals.

Finance → Retrieve invoice data and generate compliance-friendly summaries.

Supply Chain → Track delivery docs, contracts, and generate order insights.



---

Insights

GenAI ≠ reliable by default. RAG is what makes it enterprise-ready.

Embeddings are the bridge between natural language and database search.

Small models + RAG often outperform big LLMs without RAG.

Cloud providers (AWS, Azure, GCP) now offer RAG-ready services out of the box.



---

Practical Example (✅/❌)

✅ Example: Build a RAG chatbot that answers HR queries using your own policy docs.
❌ Non-example: Ask GPT about company policies without providing internal context.


---

Known Issues & Friction Points

Chunking: Splitting documents too small → lose context. Too large → exceed token limits.

Latency: Retrieval + generation can be slow if vector DB isn’t optimized.

Evaluation: Hard to measure correctness — needs human review + automated metrics.



---

Tips & Best Practices

Always use RAG for enterprise use cases.

Keep embeddings consistent (don’t mix models for retrieval vs. generation).

Choose vector DB carefully:

FAISS (simple, local).

Pinecone/Weaviate (cloud, scalable).

pgvector (Postgres extension for enterprises).


Monitor costs: embedding + retrieval queries add up in production.



---

Success Metrics & Outcomes

By the end of this chapter you should:

Understand the difference between GenAI and RAG.

Know why RAG is essential for enterprise adoption.

Be able to build a basic RAG pipeline in LangChain.

Relate RAG to HR, Finance, and Supply Chain examples.



---

Resources & References

LangChain Retrieval Docs

FAISS Library

Pinecone Vector DB

Weaviate Docs

pgvector (Postgres extension)



---

Last Reviewed / Last Updated

2025-09-03

---
