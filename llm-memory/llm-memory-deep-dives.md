# LLM Memory, Context Management & Extended Reasoning: Deep Dives

Off-curriculum side threads the student asked to keep. These are not part of the core lessons. See the [course map](llm-memory-course-map.md).

---

## Deep dive (after Session 5): push vs. pull, and how tool calls actually work

**Push vs. pull is about who triggers the search, not how the search works.** Any search method fits either pattern:

| | Push (harness triggers it) | Pull (model triggers it) |
|---|---|---|
| **Semantic search** | Classic RAG: embed the question, insert the top-k chunks | Model calls `search_docs("payments timeout")` |
| **Deterministic search** | Harness always greps for the ticket ID it sees in the message | Model calls `grep("Payments API")` |

- Iteration isn't what *makes* it pull. A single search the model chose to run is already pull. Iteration is what pull *makes possible*: the model sees results before answering and can retry.
- Real systems mix both. Push the top 3 chunks, and also give the model a search tool to pull more.

**How it works in practice: the model asks and the harness acts.** The model never executes anything.

1. **The harness advertises tools.** Each request includes tool definitions (name, plain-English description, JSON schema for arguments) at the top of the context. That's why tools come first in the prompt-cache order.
2. **The model "decides" by generating a tool call.** This is still next-token prediction: training taught it to emit a structured block instead of prose. On Claude's API:
   ```json
   { "type": "tool_use", "id": "tu_01", "name": "search_docs",
     "input": { "query": "Payments API timeout" } }
   ```
   Generation then **stops** with stop reason `tool_use`.
3. **The harness executes it**, and may refuse. Claude Code's permission prompts are this step.
4. **The harness appends a `tool_result` block** (linked by `id`) and **resends the whole transcript**. Every tool round trip is a full new call, and prompt caching keeps it affordable.
5. **Repeat** until the model replies with plain text and no tool call. That loop *is* "an agent": a `while` loop around a stateless function, with the harness doing the I/O.

**Consequences:**
- **Tool results are untrusted input.** They enter the context like retrieved chunks, so the Session 5 poisoning risk applies to every tool.
- **Server-side tools** (for example, provider-run web search) follow the same pattern. The provider's infrastructure acts as the harness for that step.
