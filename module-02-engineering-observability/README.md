# Module 2: Agent Engineering & Observability

> **Course:** Arize AI – Agents Mastery: From Architecture to Optimization (2026)
> **Resources:** [Lecture Video](https://youtu.be/r6vVTeRZqbI) · [Lab 2 Video](https://www.youtube.com/watch?v=D_Gsp24I9MY)

Lab notebooks: `labs/module-02-engineering-observability/`

---

## Overview

This module covers two tightly coupled concerns: how to *design* an agent system (agent engineering) and how to *see inside* it once it's running (observability). The core thesis is that you can't improve what you can't observe — and evals only close the loop if your instrumentation is already in place.

---

## 1. Agent Engineering

Agent engineering is the discipline of designing a system to accomplish a task using an LLM-powered agent. Unlike traditional software, the engineer's role shifts from writing logic to *shaping the environment the agent operates in*.

### Key questions to answer before building

| Dimension | Question |
|---|---|
| UX | What is the ideal user experience for this product? |
| Fit | Does an agent actually help here, or is a simpler approach better? |
| Context | What information does the agent need to reason well? |
| Tools | What tools does the agent need to take action? |
| Visibility | Should the agent operate in the foreground (interactive) or background (autonomous)? |

The foreground vs. background distinction matters for latency expectations, error handling, and how much the user tolerates uncertainty in intermediate steps.

---

## 2. What is Agent Observability?

Observability is the ability to understand what is happening *inside* an agent system from the outside. It turns opaque "black box" LLM reasoning into something **visible, interpretable, and diagnosable**.

### Why it matters

- **Debugging** — trace exactly where the agent went wrong
- **Bottleneck identification** — spot slow tool calls, repeated loops, or inefficient retrieval
- **Hallucination and error tracking** — surface failure modes at scale
- **Stakeholder transparency** — explain agent decisions to non-technical audiences

### Observability as a feedback loop

Observability enables the following change-and-verify cycle:

```
Change system → Observe effect → Evaluate improvement → Repeat
```

Concrete changes you can verify with observability:

- Switching architecture (e.g., serial → parallel agent)
- Modifying the system prompt
- Swapping models
- Tuning context (context engineering)

Without observability, you're guessing whether a change made things better or worse.

---

## 3. The Three Pillars of Observability

| Pillar | What it captures |
|---|---|
| **Traces & Spans** | End-to-end request lifecycles; represented as attribute JSONs |
| **Metrics** | Numerical measurements such as latency, token count, and success rate |
| **Logs** | Detailed event-level records for debugging individual steps |

For agents specifically, **traces are the most important pillar** because they reveal reasoning and decision-making across multi-step workflows.

---

## 4. Traces and Spans

A **trace** represents the complete lifecycle of a single request through your agent system — from the user's input all the way to the final response. Think of it as the full delivery route.

A **span** is one step within that trace. Spans are nested to show parent-child relationships between components.

### Span types for agents

| Span type | What it captures |
|---|---|
| **Prompt spans** | Inputs and outputs of LLM calls |
| **Tool spans** | External API or function calls |
| **Decision spans** | Reasoning or planning steps |

### Trace tree structure

A trace is visualized as a tree of spans. Example from a RAG pipeline:

```
BaseQueryEngine (0.91s)
└── RetrieverQueryEngine (0.90s)
    ├── BaseRetriever (0.24s)
    │   └── VectorIndexRetriever (0.23s)
    │       └── BaseEmbedding (0.18s)
    │           └── OpenAIEmbedding (0.17s)
    ├── CohereRerank (0.17s)
    └── BaseSynthesizer (0.48s)
        └── CompactAndRefine (0.90s)
```

Each node shows latency, making bottlenecks immediately visible.

### Trace data format

Traces are stored as JSON attribute objects:

```json
{
  "trace": "ID1",
  "spans": [
    { "span": "id1", "input": "...", "output": "...", "tool": "toolX" },
    { "span": "id2", "input": "...", "output": "..." },
    { "span": "id3", "input": "...", "output": "...", "tool": "toolY" }
  ]
}
```

This trace data can be passed directly to an LLM evaluator to assess decision quality:

```
You are an evaluator of agent decisions.
Determine if the agent is making the right tool call choices
and choosing a valid path to solve the problem.

{trace_data}
```

---

## 5. Standards: OTel, OI, and Auto-instrumentors

### OpenTelemetry (OTel)

The industry-standard framework for collecting traces, metrics, and logs. It provides a common language across systems and ensures consistency when agents call APIs, databases, or external services.

### OpenInference (OI)

An extension of OTel built specifically for LLM and ML systems. It adds span types for prompts, tool calls, and evaluations, giving you richer semantics for agent reasoning that OTel alone doesn't cover.

### Auto-instrumentors

Pre-built connectors that sit under the OpenInference umbrella. They automatically capture spans for popular frameworks including **LangChain**, **AutoGen**, and **LlamaIndex**. You get visibility with minimal setup, and you can add custom spans when you need finer control.

---

## 6. Lab 2: Set Up Observability for Your Agent

> **Lab video:** [Watch on YouTube](https://www.youtube.com/watch?v=D_Gsp24I9MY)
> **Lab file:** `labs/module-02-engineering-observability/lab.ipynb`

The lab walks through instrumenting an agent with OpenInference auto-instrumentors and routing traces to Arize Phoenix (or a compatible OTel backend).

### What you'll do

1. Install the OpenInference auto-instrumentor for your framework
2. Initialize a tracer provider pointing at your observability backend
3. Run your agent and inspect the resulting trace tree
4. Use trace data to write a simple LLM-as-evaluator check

### Key packages

```bash
pip install openinference-instrumentation-<framework>
pip install arize-phoenix  # or opentelemetry-exporter-otlp
```

---

## Key Takeaways

- Agent engineering is about *designing the environment* — context, tools, and interaction model — not just writing LLM calls.
- Observability converts black-box agent behavior into something you can debug, measure, and improve.
- **Traces are the primary signal** for agents; spans break a trace into inspectable steps.
- OTel + OpenInference is the standard stack; auto-instrumentors give you coverage with near-zero setup.
- Evals are only meaningful when paired with good observability — you need to see what happened before you can judge it.
