# Lab 4: Tools & MCP

> **Course:** Arize AI – Agents Mastery: From Architecture to Optimization (2026)
> **Lab video:** [Watch on YouTube](https://www.youtube.com/watch?v=sT2PW5xzLs4)
> **Lab file:** `lab/lab.ipynb`

---

## What this lab does

You take the travel agent from earlier labs and upgrade its tools — replacing generic search with purpose-built, API-specific functions. Every tool call is traced end-to-end in Arize AX so you can see exactly what each API returned and debug any failures.

The agent itself doesn't change. Only the tool definitions change. The same three tools from Module 3 (`essential_info`, `budget_basics`, `local_flavors`) are kept, but their internals are now powered by dedicated APIs instead of a single generic search.

---

## Setup (same pattern as earlier labs)

```python
# 1. Install packages
pip install arize-otel agno openai openinference-instrumentation-openai

# 2. Set API keys via getpass
ARIZE_API_KEY, ARIZE_SPACE_ID
OPENAI_API_KEY
TAVILY_API_KEY  # used for budget and local flavor tools

# 3. Register tracing
register(model_id="travel-agent-demo", space_id=..., api_key=...)
```

`register()` connects the notebook to your Arize AX project so every agent run, tool call, and API request appears in your trace tree automatically.

---

## Helper functions

These two utility functions underpin all three tools. They are not tools themselves — the agent never calls them directly.

---

### `search_api(query: str) → str | None`

**What it does:** Centralizes all Tavily search calls. Every tool that needs a web search goes through this function rather than calling Tavily directly.

**Decorated with:** `@tracer.chain(name="search_api")` — every invocation appears as its own `search_api` span inside the parent tool span in the trace tree.

**How it works:**

1. Checks for a `TAVILY_API_KEY` environment variable; returns `None` immediately if it isn't set
2. Sends a POST request to `https://api.tavily.com/search` with `search_depth: "basic"` and `include_answer: True`
3. Pulls the main `answer` field plus text snippets from the results
4. Combines them into one string, trimmed to ~400 characters
5. On any failure (missing key, HTTP error, unexpected response) — returns `None` instead of raising, so the calling tool can fall back gracefully

**Why it's a separate function:** Keeps Tavily-specific logic in one place. If you swap search providers, you change this one function and all three tools get the update automatically.

---

### `compact(text: str, limit: int) → str`

**What it does:** Trims responses to a readable length without cutting words mid-sentence.

**No `@tracer.chain` decorator** — intentionally invisible in traces. To make it visible, add `@tracer.chain(name="compact")`.

**How it works:**

1. Collapses all extra whitespace in the input string
2. If the string is shorter than `limit`, returns it as-is
3. If longer, finds the last space before the character limit and cuts there — you always get complete words, never a truncated one

**Used in:** All three tools, to keep LLM context short and readable.

---

## Dedicated API helper functions

These two functions replace generic search for the `essential_info` tool. Both appear as their own spans in Arize AX.

---

### `wiki_summary(destination: str) → str`

**What it does:** Fetches a Wikipedia summary paragraph for a city or destination.

**Decorated with:** `@tracer.chain` — appears as its own span with destination as input and the summary as output.

**How it works:**

1. Calls `https://en.wikipedia.org/api/rest_v1/page/summary/{destination}` (spaces replaced with underscores)
2. If HTTP 200, returns the `extract` field — a clean, structured summary paragraph
3. If anything fails, returns an empty string silently (no crash)

**Why Wikipedia over search:** Summaries are stable, structured, and high-quality for city-level context. No API key required.

---

### `weather_api(destination: str) → str`

**What it does:** Fetches current weather using the Open-Meteo API (free, no key required).

**Decorated with:** `@tracer.chain` — appears as its own span.

**How it works:**

1. Calls the Open-Meteo geocoding API to convert the destination name into latitude/longitude
2. If geocoding fails or returns no result, returns `""` and stops
3. If geocoding succeeds, calls the Open-Meteo weather API with `current_weather=true`
4. Extracts temperature and wind speed; returns a one-line summary, e.g.: `"Weather now: 22°C, wind 6 km/h"`

**What you see in Arize AX:** The span shows destination name as input and the weather string as output — confirming what the agent actually received before generating its response.

---

## The three agent tools

These are the functions registered directly with the agent.

---

### `essential_info(destination: str) → str`

**Purpose:** A quick, reliable brief on the destination — the "who is this city" context.

**How it works:**

1. Calls `wiki_summary(destination)` → appends to a parts list if non-empty
2. Calls `weather_api(destination)` → appends if non-empty
3. Returns a combined string like:

```
Tokyo essentials: Tokyo is the capital of Japan and one of the most populous cities...
Weather now: 22°C, wind 6 km/h
```

**Trace shape:** Tool span with two child spans — `wiki_summary` and `weather_api`.

---

### `budget_basics(destination: str, duration: int) → str`

**Purpose:** Estimates realistic daily costs for a trip.

**How it works:**

1. Builds a focused query: `"Tokyo travel budget average daily cost for 5 days"`
2. Passes to `search_api()`
3. **Happy path** (API key set, search returns): Returns grounded result like `"Tokyo budget for 5 days: ~$120/day covering mid-range accommodation, meals, transport"`
4. **Fallback** (no key or search fails): Returns a generic but still useful response: `"Budget for 5 days in Tokyo will depend on lodging, meals, transport, and attractions"`

**Trace shape:** Tool span with one `search_api` child span showing the exact query and raw result.

---

### `local_flavors(destination: str, interests: str) → str`

**Purpose:** Surfaces on-the-ground recommendations matched to the user's stated interests.

**How it works:**

1. Builds a query combining destination and interests: `"Tokyo authentic local experiences food culture fashion"`
2. Passes to `search_api()`
3. **Happy path**: Returns interest-specific suggestions grounded in search results
4. **Fallback**: Returns a reasonable generic response: `"Explore Tokyo's unique food culture through markets, neighborhoods, and local eateries"`

**Trace shape:** Tool span with one `search_api` child span.

---

## The agent

```python
agent = Agent(
    name="Trip Planner",
    role="AI travel assistant",
    model=OpenAIChat(id="gpt-4o"),
    tools=[essential_info, budget_basics, local_flavors],
    instructions=[
        "Combine multiple tools: include essentials, budget, and local flavor",
        "Keep tone friendly and response under 1,000 words"
    ],
    markdown=True
)
```

The agent structure is identical to previous labs. Only the tool implementations changed.

---

## What the trace tree looks like in Arize AX

```
Trip Planner Agent
├── essential_info
│   ├── wiki_summary          ← Wikipedia API call
│   └── weather_api           ← Open-Meteo geocode + weather calls
├── budget_basics
│   └── search_api            ← Tavily POST request
└── local_flavors
    └── search_api            ← Tavily POST request
```

Each span shows its input and output — you can verify exactly what each API returned before it reached the LLM.

---

## Graceful degradation pattern

All three tools are designed to return something useful even when APIs are unavailable:

| Scenario | What happens |
|---|---|
| `TAVILY_API_KEY` not set | `search_api` returns `None`; tools return a sensible generic fallback |
| Wikipedia returns non-200 | `wiki_summary` returns `""`; `essential_info` continues with just weather |
| Geocoding fails | `weather_api` returns `""`; `essential_info` continues with just wiki summary |
| Any HTTP error | Functions return `None` or `""` — never raise, never crash the agent |

---

## Key takeaways

- Replacing generic search with dedicated APIs (Wikipedia, Open-Meteo, Tavily) makes each tool more focused, reliable, and debuggable.
- `@tracer.chain` is the decorator that makes any function visible as a span in Arize AX. Without it, internal helper calls are invisible to traces.
- Graceful fallbacks are not optional — in production, APIs will fail. Design every tool to degrade safely rather than error out.
- The agent didn't change at all. Better tools = better agent, with no architectural changes needed.
- Module 5 continues building on this tools pattern, but shifts the retrieval source from live APIs to a curated RAG dataset of local guides.
