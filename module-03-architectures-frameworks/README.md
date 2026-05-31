# Module 3: Agent Architecture & Frameworks

> **Course:** Arize AI – Agents Mastery: From Architecture to Optimization (2026)
> **Resources:** [Lecture Video](https://www.youtube.com/watch?v=RALneTfOweI) · [Lab 3 Video](https://www.youtube.com/watch?v=3lxNBh_PqyI)

---

## Overview

Architecture is the foundational decision in any agent system. The pattern you choose determines how your agent reasons, takes action, and manages interaction — and it directly shapes latency, debuggability, and output quality. This module covers the four core multi-agent architectures and the major frameworks that implement them.

---

## 1. What Agent Architecture Controls

Every architecture makes choices across three dimensions:

| Dimension | What it governs |
|---|---|
| **Reasoning** | How agents plan and decide what to do next |
| **Action** | How agents use tools, APIs, and the environment |
| **Interaction** | How agents manage conversations, memory, and feedback |

Choosing the right architecture is not a stylistic decision — it directly affects latency, cost, and whether your system is easy to debug with traces.

---

## 2. The Four Core Architectures

### Routing

A **router agent** inspects the incoming request, classifies the task, and forwards it to the most appropriate specialized agent. Each specialized agent has its own tools and a prompt optimized for that domain.

```
User Call → Router Agent → Specialized Agent A (+ tools)
                         → Specialized Agent B (+ tools)  → Output
                         → Specialized Agent C (+ tools)
```

**When to use:** Tasks that fall into clearly distinct categories (e.g. billing vs. technical support vs. refunds). Routing keeps each agent's context focused and avoids the cost of loading irrelevant tools.

---

### Orchestrator-Worker

An **orchestrator** receives the full request, breaks it into subtasks, and dynamically assigns each subtask to a worker agent. Workers report results back to the orchestrator, which synthesizes a final response and applies a termination condition.

```
User Call → Orchestrator ↔ Agent #1
                         ↔ Agent #2   → Synthesized Output
                         ↔ Agent #3
                         ↔ Agent #4
```

**Steps:**
1. Orchestrator splits the task
2. Tasks are dynamically assigned to workers
3. Worker results are collected and synthesized

**When to use:** Coordination-heavy tasks where subtasks have dependencies or need a central authority to manage sequencing. Trade-off: higher latency because workers run semi-sequentially.

---

### Parallelization

The input is divided into **independent subtasks**, each sent to a separate agent or LLM call simultaneously. All results are aggregated into a final output — often by a synthesizer agent.

```
Input Divided → [Agent 1]
              → [Agent 2]  → Output Aggregated
              → [Agent 3]
              → [Agent 4]
```

**Steps:**
1. Input is divided into independent subtasks
2. Each agent completes its subtask concurrently
3. Results are aggregated to form the final output

**When to use:** When subtasks are genuinely independent (no data dependencies between them) and latency matters. The fan-out / fan-in pattern is implemented via `asyncio.gather` in Python.

---

### Evaluator-Optimizer

A **generator** agent produces an initial response. A separate **evaluator** agent scores or critiques it and sends feedback. The generator revises based on feedback and the loop continues until the evaluator approves or a max iteration count is hit.

```
User Call → Agent/LLM (generate) ⇄ Agent/LLM (evaluate) → Output
                    ↑_________feedback + iteration_________|
```

**Steps:**
1. Generator produces a response
2. Evaluator scores and provides feedback
3. Loop continues until approval

**When to use:** Quality-critical tasks where the first attempt is rarely good enough — writing, code review, planning documents. The cost is additional LLM calls per iteration.

---

## 3. Architecture Comparison

| Architecture | Execution | Latency | Best for |
|---|---|---|---|
| Routing | Single agent per request | Low | Clearly categorizable tasks |
| Orchestrator-Worker | Sequential / semi-sequential | Higher | Coordination-heavy, dependent subtasks |
| Parallelization | Concurrent | Lower | Independent subtasks, speed priority |
| Evaluator-Optimizer | Iterative loop | Variable | Quality-critical outputs |

---

## 4. Common Agent Frameworks

Frameworks abstract away the boilerplate of implementing these patterns so you can focus on agent logic.

| Framework | Language | Core features |
|---|---|---|
| **CrewAI** | Python | Event-driven manager–worker with retries, observability, async parallelism, and flexible memory |
| **Mastra** | TypeScript | Built-in streaming, suspend-resume, tracing, and configurable structured memory with persistence defaults |
| **Autogen** | Python | Multi-agent orchestration, human-in-the-loop, conversation-driven workflows |
| **Agno** | Python | Team-based agents, role assignment, collaboration protocols |
| **LangGraph** | Python | Graph-based; controlled parallelism, recursion, and durable checkpointed memory with resumability |

The lab uses **Agno** for its clean team-based API, but the architectural patterns transfer directly to any of these frameworks.

---

## 5. Lab 3: Orchestrator-Worker & Parallelization with Agno

> **Lab video:** [Watch on YouTube](https://www.youtube.com/watch?v=3lxNBh_PqyI)
> **Lab file:** `lab/lab.ipynb`

The lab runs a travel planning query through both an orchestrator-worker team and a parallel synthesizer architecture, then compares their traces side-by-side in Arize AX.

### Shared tools across both architectures

| Tool | What it does |
|---|---|
| `essential_info` | Summarizes weather + Wikipedia essentials for a destination |
| `budget_basics(destination, duration)` | Pulls budget ranges from search |
| `local_flavors` | RAG-based recommendations using Chroma + sentence-transformers |

Note: `local_flavors` includes a manual RAG retriever span visible in traces.

---

### Architecture 1: Orchestrator-Worker (Team)

Three specialized worker agents, each with one dedicated tool:

- **Destination agent** → `essential_info`
- **Budget agent** → `budget_basics`
- **Local activity agent** → `local_flavors`

The orchestrator wraps them into an Agno `Team` with `show_members_response=True` so each worker's contribution is visible before the orchestrator stitches together the final plan.

**Trace structure in Arize AX:**

```
Team / Orchestrator span
├── Destination Agent
│   └── essential_info (tool call)
├── Budget Agent
│   └── budget_basics (tool call)
└── Activity Agent
    └── local_flavors (tool call + RAG retriever span)
        └── Chat completion (final synthesized plan)
```

The hierarchy makes it easy to debug which worker contributed what and whether each tool call behaved correctly.

---

### Architecture 2: Parallelization with Synthesizer

Same three worker agents, but now launched concurrently using `asyncio.gather`. Their outputs are fed into a **synthesizer agent** that merges the partial results into the final response.

```python
# Fan-out: run all three workers at the same time
results = await asyncio.gather(
    destination_agent.arun(query),
    budget_agent.arun(query),
    activity_agent.arun(query)
)

# Fan-in: synthesizer merges partial results
final = await synthesizer_agent.arun(combined_prompt)
```

Because all three run concurrently, there is no natural step 1 → 2 → 3 ordering. Manual instrumentation is added using the OpenInference semantic conventions (built on OTel) to correctly label the top-level span as `parallelization_agent` and tag each sub-agent's attributes.

**Trace structure in Arize AX:**

```
Parallelization Agent (top-level span)
├── Destination Agent (concurrent)
├── Budget Agent (concurrent)
├── Activity Agent (concurrent)
└── Synthesizer Agent
    └── Chat completion (merged final plan)
```

---

### Comparing the two architectures

| | Orchestrator-Worker | Parallelization |
|---|---|---|
| Execution | Semi-sequential | Concurrent |
| Latency | Higher | Lower |
| Trace shape | Clear step hierarchy | Flat concurrent spans + synthesizer |
| Best for | Coordination-heavy, dependent | Independent subtasks, speed priority |
| Debugging | Easy to see which worker caused an issue | Need manual span attributes for context |

Both architectures reuse the same tools and models — the architecture alone changes the performance profile.

---

## Key Takeaways

- The four core patterns — routing, orchestrator-worker, parallelization, and evaluator-optimizer — cover the majority of real-world agent use cases.
- Architecture choice is a latency vs. coordination trade-off: parallelization wins on speed when subtasks are independent; orchestrator-worker wins when sequencing and delegation matter.
- Frameworks like Agno, LangGraph, and CrewAI implement these patterns with different APIs but the same underlying ideas.
- Observability (Module 2) is essential here: without traces you can't see whether the architecture you chose is actually executing as designed, or where the latency is coming from.
- When running agents concurrently, add manual OTel/OpenInference instrumentation — auto-instrumentors assume sequential execution and won't correctly attribute parallel spans.
