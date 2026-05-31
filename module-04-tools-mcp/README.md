# Module 4: Tools and MCP

> **Course:** Arize AI – Agents Mastery: From Architecture to Optimization (2026)
> **Resources:** [Lecture Video](https://www.youtube.com/watch?v=X5LumwnyzsM) · [Lab 4 Video](https://www.youtube.com/watch?v=sT2PW5xzLs4)

---

## Overview

Tools and MCP are what move an agent from a text generator to a system that can actually *do things*. This module defines what agent tools are, why they matter, and how MCP (Model Context Protocol) provides a standardized, plug-and-play interface for connecting agents to both local and remote services.

---

## 1. What Are AI Agent Tools?

Agent tools are **external capabilities** that an agent can call when it needs information or needs to take an action beyond what the base LLM can produce on its own. Without tools, an agent is essentially just a text generator — the LLM is the only component, and it can only produce text.

### Four categories of tools

| Category | What it enables |
|---|---|
| **Data Access** | Query databases, APIs, file systems |
| **Extraction** | Parse, chunk, and retrieve information from documents or the web |
| **Analysis** | Run calculations, process datasets, interpret results |
| **Execution** | Generate and run code, trigger workflows, call services |

### Benefits of tools

- **Grounded knowledge** — agents get real, up-to-date information rather than relying on training data
- **Real-world actions** — agents can write to databases, send messages, deploy code
- **Specialization and modularity** — each tool does one thing well; tools can be swapped without rebuilding the agent
- **Scalability** — new capabilities are added by adding tools, not retraining the model

### Common tool call examples

- Code generation
- Data queries
- Web search
- Planning
- MCP calls
- ...and many more

The important mental model: *an agent without tools is just an LLM. Tools are what turn LLM calls into agent behavior.*

---

## 2. What is MCP?

**MCP (Model Context Protocol)** is a standardized protocol for agents to communicate with local or remote services. It creates a clean, consistent interface so an agent can add new capabilities without any changes to its own code.

### System diagram

```
User
 └── Host (Cursor, Claude Desktop, Claude Code)
       └── MCP Client
             ├── MCP Server A (sqlite-mcp-server)  ←→  Local SQLite Database
             ├── MCP Server B (deploy-app-mcp-server)  ←→  Web API
             └── MCP Server C (send-email-mcp-server)  ←→  Remote Email API
```

### Key components

| Component | Role |
|---|---|
| **Host** | The application the user interacts with (e.g. Cursor, Claude Desktop) |
| **MCP Client** | Lives inside the host; manages connections to MCP servers |
| **MCP Server** | Exposes a specific capability (database access, email, deployment, etc.) |
| **Local data source** | Files, databases, or services on the user's machine |
| **Remote service** | External APIs reached over the internet |

### Why MCP matters

The key insight is **decoupling**: you can plug in new MCP servers (new capabilities) without touching the agent itself. This is the same benefit that APIs gave to web development — you don't rebuild your app every time you want to add a new integration.

### Tools vs. MCP

Regular tools (like the `essential_info` or `budget_basics` functions from Module 3) are custom Python functions you write and register with your agent. MCP servers are pre-built, protocol-compliant services that expose capabilities through a standardized interface. In practice, an MCP call *is* a tool call from the agent's perspective — it just happens to talk to an MCP server rather than a local function.

---

## 3. Lab 4: Implement Tools & MCP

> **Lab video:** [Watch on YouTube](https://www.youtube.com/watch?v=sT2PW5xzLs4)
> **Lab file:** `lab/lab.ipynb`

The lab has two parts: comparing RAG-based tools vs. MCP-driven actions, and evaluating whether an orchestrator agent reasons correctly about which sub-agent to call.

### Part 1: RAG vs. MCP

You'll run the same query through two versions of the agent — one using a RAG tool (Chroma + sentence-transformers, as in Module 3) and one using an MCP server. The goal is to observe the difference in:

- How the agent retrieves context
- What shows up in the trace (RAG retriever span vs. MCP tool span)
- The quality and grounding of the final response

### Part 2: Orchestrator planning + reasoning evals

Using the orchestrator-worker architecture from Module 3, you'll evaluate whether the orchestrator correctly plans which sub-agent to delegate to. Two evals are introduced here:

| Eval | Question it answers |
|---|---|
| **Context preservation** | Did the orchestrator pass the right context from the user's request to the sub-agent? |
| **Correct agent selection** | Did the orchestrator route the task to the most appropriate worker? |

These are the first evals in the course that specifically target the *reasoning* layer of the agent, not just the final output. Traces from Module 2's observability setup are essential here — without them you can't see which sub-agent was selected or what context it received.

### Connecting an MCP server (general pattern)

```python
from agno.tools.mcp import MCPTools  # or framework equivalent

agent = Agent(
    tools=[MCPTools(server_url="http://localhost:8000/mcp")],
    ...
)
```

Each MCP server is registered by URL; the agent's framework discovers available tools automatically via the MCP protocol handshake.

---

## Key Takeaways

- Tools are the bridge between LLM reasoning and real-world action. Without them, an agent can only generate text.
- The four tool categories — data access, extraction, analysis, execution — cover nearly every agent capability you'll need.
- MCP standardizes how agents connect to external services. Adding a new MCP server adds a new capability with zero changes to the agent itself.
- From an observability perspective, tool calls and MCP calls both produce spans in your trace tree — this is how you verify the agent called the right tool with the right inputs.
- The lab introduces the first reasoning-layer evals: context preservation and correct agent selection. These require traces to answer and preview the evaluation work in Module 6.
