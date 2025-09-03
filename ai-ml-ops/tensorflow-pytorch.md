---
title: Gen AI/ML Handbook – Chapter 2: AI/ML Fundamentals with PyTorch & TensorFlow
archetype: guidebook
status: draft
owner: Shailesh Rawat
maintainer: self
version: 0.1
tags: [AI, ML, PyTorch, TensorFlow, Handbook, Enterprise]
last_reviewed: 2025-09-03
---

# Overview
This chapter introduces the **core building blocks of AI/ML** with two widely used frameworks:  
- **PyTorch** – flexible, developer-friendly, widely used in research and production.  
- **TensorFlow/Keras** – optimized for enterprise deployments, scalable, Google ecosystem.  

You’ll learn the **minimum fundamentals**: tensors, training loops, and how to build a simple model.  
Think of this chapter as your **ML grammar primer** — the vocabulary you need before building advanced systems.

---

# Why It Matters
- **Recruiter signal**: “Knows PyTorch/TensorFlow” often appears in JDs. Even if you focus on LangChain or agentic AI, familiarity here shows breadth.  
- **Enterprise reality**: Many orgs still train or fine-tune models internally, even if they consume APIs.  
- **Translation skill**: Being able to explain “why PyTorch vs. TensorFlow” helps you act as a bridge between dev teams and business leaders.  

---

# Prerequisites
- Python basics (variables, functions, loops).  
- `pip install torch tensorflow`.  
- Curiosity — you don’t need advanced math here.  

---

# Key Terms Explained
- **Tensor**: a multi-dimensional array (like an Excel sheet, but for numbers in any dimension).  
- **Autograd**: automatic differentiation (lets models “learn” by adjusting weights).  
- **Forward pass**: input flows through the network to produce output.  
- **Backward pass**: errors are calculated and weights are updated.  
- **Optimizer**: algorithm that adjusts weights (e.g., SGD, Adam).  

---

# PyTorch: First Steps
```python
import torch

# Create a tensor
x = torch.tensor([[1, 2], [3, 4]], dtype=torch.float32)
print("Tensor:", x)

# Basic operation
y = x + 2
print("Tensor + 2:", y)

# Check GPU availability
print("CUDA available?", torch.cuda.is_available())

What’s happening?

tensor = basic unit.

Supports GPU acceleration (important in enterprises with large models).

PyTorch is “eager execution” → prints values directly (debug-friendly).



---

TensorFlow/Keras: First Steps

import tensorflow as tf

# Create a tensor
x = tf.constant([[1, 2], [3, 4]], dtype=tf.float32)
print("Tensor:", x)

# Basic operation
y = x + 2
print("Tensor + 2:", y)

# Check GPU availability
print("GPU available?", len(tf.config.list_physical_devices('GPU')) > 0)

What’s happening?

Very similar to PyTorch, but TensorFlow uses tf.constant.

TensorFlow integrates with Keras, a high-level API for building models fast.



---

Building a Simple Neural Network

PyTorch Version

import torch
import torch.nn as nn
import torch.optim as optim

# Define a simple model
class SimpleNN(nn.Module):
    def __init__(self):
        super(SimpleNN, self).__init__()
        self.fc = nn.Linear(2, 1)  # input_dim=2, output_dim=1

    def forward(self, x):
        return self.fc(x)

# Initialize
model = SimpleNN()
criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=0.01)

# Dummy data
inputs = torch.tensor([[1.0, 2.0], [2.0, 3.0]])
targets = torch.tensor([[1.0], [2.0]])

# Training loop
for epoch in range(10):
    outputs = model(inputs)
    loss = criterion(outputs, targets)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    print(f"Epoch {epoch+1}, Loss: {loss.item()}")


---

TensorFlow/Keras Version

import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import layers

# Define a simple model
model = keras.Sequential([
    layers.Dense(1, input_shape=(2,))
])

# Compile
model.compile(optimizer='sgd', loss='mse')

# Dummy data
import numpy as np
inputs = np.array([[1.0, 2.0], [2.0, 3.0]])
targets = np.array([[1.0], [2.0]])

# Train
model.fit(inputs, targets, epochs=10)


---

Insights: PyTorch vs TensorFlow

Feature	PyTorch	TensorFlow/Keras

Learning curve	Easier for beginners, more Pythonic	Slightly steeper, but Keras simplifies
Debugging	Eager execution (prints instantly)	Historically static graphs, now eager by default
Industry adoption	Favored in research/startups	Favored in enterprises (Google, healthcare, finance)
Deployment	TorchServe, ONNX	TensorFlow Serving, TFLite, TPU support
Your need	Good for quick fine-tuning	Good for enterprise-scale integration



---

Practical Example (✅/❌)

✅ Example: Fine-tune DistilBERT in PyTorch on HR policies.
❌ Non-example: Training a GPT-scale model from scratch (requires $M resources).


---

Known Issues & Friction Points

Beginners confuse framework syntax with ML logic → the math is universal, frameworks are just wrappers.

PyTorch/TensorFlow code looks different, but the building blocks (tensors, loss, optimizer) are the same.



---

Tips & Best Practices

Always start with dummy data → focus on syntax, not datasets.

In enterprise work, you’ll usually fine-tune existing models, not train from scratch.

Learn to read loss curves — recruiters value “knows how to debug training.”



---

Dependencies, Risks & Escalation Path

Risk: Without PyTorch/TensorFlow exposure, you’ll be seen as “just a LangChain user.”

Escalation: Start with small models → progress to Hugging Face fine-tuning.



---

Success Metrics & Outcomes

By the end of this chapter you should:

Understand what tensors are and why they matter.

Be able to write a simple PyTorch and TensorFlow model.

Know how to explain the difference between the two frameworks.

Be ready to move toward transformers and Hugging Face (next chapter).



---

Resources & References

PyTorch Tutorials

TensorFlow Tutorials

Hugging Face Course

Scikit-learn Guide



---

Last Reviewed / Last Updated

2025-09-03

---

