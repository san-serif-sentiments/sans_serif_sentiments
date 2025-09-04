---
title: Gen AI/ML Handbook – Chapter 6: Agentic AI & Orchestration
archetype: guidebook
status: draft
owner: Shailesh Rawat
maintainer: self
version: 0.1
tags: [AgenticAI, LangChain, CrewAI, LangGraph, Flowise, Enterprise]
last_reviewed: 2025-09-03
---

# Overview
So far we’ve seen **chatbots and RAG systems** that answer questions.  
But enterprises often need **more than a single answer**:  
- Multi-step workflows.  
- Tool usage (databases, APIs, calculators).  
- Collaboration between specialized agents.  

This is where **Agentic AI** comes in.  
It treats LLMs not just as text generators, but as **reasoning agents** that can:  
- Plan,  
- Call tools,  
- Use memory,  
- Work with other agents.  

---

# Why It Matters
- **Enterprise tasks are rarely one-shot.** HR, Finance, Supply Chain all involve multi-step workflows.  
- **Agentic AI enables orchestration**: agents delegate, retrieve info, and take actions.  
- **Job descriptions** increasingly mention “agent frameworks” like LangChain, CrewAI, and Flowise.  

---

# Key Terms Explained
- **Agent**: An LLM that decides what action to take next (answer directly, call a tool, ask again).  
- **Tool**: An external function the agent can use (database query, API call, calculator).  
- **Memory**: Stores conversation or workflow context across steps.  
- **Planner / Executor**: Agent decides the plan (planner) and then runs steps (executor).  
- **Multi-Agent System**: Several agents (HR agent, Finance agent) collaborating like a team.  
- **LangChain Agent**: A built-in abstraction where LLMs + tools are stitched together.  
- **CrewAI / LangGraph**: Frameworks for building multi-agent workflows.  
- **Flowise**: Visual, drag-and-drop agent orchestration.  

---

# Simple LangChain Agent Example
```python
from langchain.chat_models import ChatOpenAI
from langchain.agents import initialize_agent, Tool
from langchain.utilities import SerpAPIWrapper

# LLM
llm = ChatOpenAI(temperature=0)

# Tool (Google search via SerpAPI)
search = SerpAPIWrapper()

tools = [
    Tool(
        name="Search",
        func=search.run,
        description="Useful for answering questions about current events"
    )
]

# Initialize agent
agent = initialize_agent(tools, llm, agent="zero-shot-react-description", verbose=True)

# Ask question
agent.run("What is the latest update on AI regulations in India?")

Explanation:

LLM = brain, tools = hands.

The agent first plans → then uses a tool → then explains.



---

Multi-Agent Example (CrewAI)

from crewai import Agent, Task, Crew, Process

# Define agents
researcher = Agent(
    role="Researcher",
    goal="Gather information about HR policies",
    backstory="Expert in HR compliance and documentation"
)

analyst = Agent(
    role="Analyst",
    goal="Summarize and simplify HR policy findings",
    backstory="Skilled in communication and translation"
)

# Define tasks
task1 = Task(description="Collect HR leave policy info", agent=researcher)
task2 = Task(description="Summarize findings in simple terms", agent=analyst)

# Orchestrate
crew = Crew(
    agents=[researcher, analyst],
    tasks=[task1, task2],
    process=Process.sequential
)

crew.kickoff()

Explanation:

Multiple agents, each with a role.

They work sequentially or in parallel.

Very close to how enterprise teams operate.



---

Visual: Orchestration Flow

flowchart LR
    User --> Agent1[Planner Agent]
    Agent1 -->|calls| Tool1[Database/API]
    Agent1 --> Agent2[Domain Expert Agent]
    Agent2 -->|summarizes| Agent3[Communicator Agent]
    Agent3 --> User

✅ This shows how one query can move through multiple agents and tools before returning a final answer.


---

Enterprise Applications

HR → One agent fetches leave rules, another explains in simple language.

Finance → One agent extracts invoice data, another checks compliance, a third summarizes.

Supply Chain → One agent queries order database, another predicts delays, another generates report.



---

Insights

Agentic AI ≠ just chatbots. It’s closer to a workflow engine with intelligence.

Tools + memory + planning = enterprise-grade reasoning.

Multi-agent systems mimic human teams → each agent has a role.

Orchestration frameworks (LangChain, CrewAI, LangGraph, Flowise) help manage complexity.



---

Practical Example (✅/❌)

✅ Example: Use CrewAI to assign agents for HR policy research + simplification.
❌ Non-example: Ask a single GPT model to act like an HR + Finance + Supply Chain expert at once (no specialization).


---

Known Issues & Friction Points

Complexity: Multi-agent systems can be hard to debug.

Overhead: Each tool/agent call adds latency + cost.

Control: Enterprises need guardrails (don’t let agents make risky API calls unsupervised).



---

Tips & Best Practices

Start small: one agent + one tool, then expand.

Use memory carefully — too much context can overwhelm the model.

Assign clear roles and goals → avoids “confused agents.”

Monitor latency and costs → multi-agent = multi-queries.



---

Success Metrics & Outcomes

By the end of this chapter you should:

Understand what agents are and how they differ from chatbots.

Run a simple LangChain agent with a tool.

Build a multi-agent workflow with CrewAI.

Recognize enterprise use cases for orchestration.



---

Resources & References

LangChain Agents

CrewAI Framework

LangGraph

Flowise AI

NeMo Guardrails



---

Last Reviewed / Last Updated

2025-09-03

---