# LLM Automation Course — Lesson Plan
*Professor Eggman's Graduate Seminar — Full Course Map*

**Student profile:** BSc Computer Science, 15 years in cybersecurity consulting. Research-rusty, technically fluent. Arrives having completed the *LLM & GPT* core curriculum (8 sessions) and the *Claude Capabilities & Safety* arc (6 sessions) — already fluent in context windows, statelessness, the instruction/data collapse, injection (direct and indirect), agentic escalation, and defense at the blast radius. Analogies lean on systems thinking, software architecture, and adversarial/threat-modelling intuition.

**Format:** ~10-minute conversational sessions. Bottom-up scaffolding: problem space → vocabulary → mechanisms → application → frontiers → failure modes. One topic per session, Socratic check-in before advancing.

**Premise of the whole course:** A raw LLM automates *nothing*. It is a stateless text-predictor that can only *think*, never *act*, and only *once*. Everything here is about engineering the impure system *around* that pure function — the **eyes, hands, and loop** that turn a brain-in-a-jar into an automated workflow. The model turns out to be the least interesting component.

**Status key:** ✅ complete · ▶ next · ◻ planned · ✦ candidate for expansion

---

## Part I — The Problem Space

### ✅ Session 1 — The Automation Problem
Why a raw LLM automates *nothing* — and the three limits every automation must build past.
- The inference call as a **pure function**: same input → same output distribution, no memory between calls, no side effects. Computes; does not *do*.
- Three limits, three prosthetics:
  - **Sealed in the window** — knows only what's on the whiteboard, can't reach out → needs **eyes** → RAG
  - **Text-only output** — generation is its sole operation; a described action sends zero emails → needs **hands** → tools / function-calling / MCP
  - **Runs once and stops** — one forward pass, no iterate-inspect-decide → needs a **loop** → the harness
- The reframe: not three gadgets, but three answers to *one* limitation
- The value lives in the **scaffolding**, not the model — what you retrieve, which tools you expose, how tightly you govern the loop
- **Capability/vulnerability duality:** every prosthetic that reaches *out* is a channel for something to reach *in*
- *Security anchor:* **hands** are the phase change (inert text → real action); the **loop** removes the human checkpoint

---

## Part II — The Prosthetics

### ▶ Session 2 — RAG: Giving the Model Eyes
Retrieval-augmented generation — the read-only prosthetic. Chunk, embed, store, retrieve by similarity, inject into the window. Grounding and freshness as a statelessness workaround. *(The tab owed from the Claude course.)*

### ◻ Session 3 — Function-Calling: Giving the Model Hands
The raw mechanism of tool use, and the uncomfortable truth that the model never actually "calls" anything — it emits a structured request the harness executes. Generate-request → execute → feed-result-back.

### ◻ Session 4 — MCP: The Universal Adapter
The Model Context Protocol. The M×N integration mess and how a standard collapses it to M+N. Client/server architecture; tools, resources, prompts. The key correction: MCP *exposes* capabilities in a standard way — it does not *grant* them.

---

## Part III — Orchestration & Application

### ◻ Session 5 — The Harness: The Loop That Runs It All
Orchestration: propose → act → observe → repeat, until done. Where "agent" actually lives. Single-model loop first; multi-agent orchestration second. ReAct-style reason/act interleaving.

### ◻ Session 6 — Assembling a Workflow
The three prosthetics wired into one real automated pipeline. Worked example end to end. The discipline of knowing when a rigid workflow beats an open-ended agent — and when *not* to automate at all.

---

## Part IV — Failure

### ◻ Session 7 — Failure Modes & Security
The session built for the fifteen years. Every seam from the prior courses chains here: indirect injection via retrieved documents, injection → action via tools, compounding failure across the trust boundaries, human-in-the-loop placement, least privilege on tools, ground-truth validation. Defense at the blast radius, applied to a live agentic system.

---

## Flagged for Possible Expansion

Candidates for dedicated sessions if the student wants them:

- ✦ **Embeddings & vector databases proper** — indexing, ANN search, chunking strategy. Touched in Session 2, mechanism not fully unpacked.
- ✦ **Memory architectures** — beyond RAG: scratchpads, episodic stores, the statelessness workarounds that aren't retrieval.
- ✦ **Evaluation & observability for agents** — how you test, trace, and audit a non-deterministic looping system.
- ✦ **Multi-agent orchestration patterns** — supervisor/worker, routing, hand-offs. The "penthouse" model of the harness.

---

*Last updated: end of Session 1 — The Automation Problem.*
*Companion docs: automation-vocabulary.md (running glossary), automation-references.md (references by session).*
