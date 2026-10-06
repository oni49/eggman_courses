# LLM Automation Vocabulary Reference
*Professor Eggman's Seminar — Running Glossary*

---

## Session 1 — The Automation Problem

**The Automation Problem**
The founding fact of the course: a raw LLM automates *nothing*. Not *won't* — *can't*. It reads a block of text, emits a block of text, and forgets it happened. Automation is the engineering done *around* that inert core, not a property of the model itself.

**Pure Function (analogy)**
The mental model for a single inference call. Same input → same output distribution, no memory between calls, no side effects. Like a pure function, it *computes* but does not *do*. Every automated system is the *impure* machinery built around this pure core — the part that holds state, causes effects, and runs more than once.

**The Three Limits**
The three specific things a raw model cannot do, each of which automation must build past:
1. It is **sealed in the window** — it knows only what is currently on the whiteboard and cannot reach out for anything else.
2. It **only produces text** — generation is its one and only operation; it cannot execute anything.
3. It **runs once and stops** — a single forward pass, with no way to act, inspect the result, and decide what to do next.

**Eyes / Hands / Loop**
The three prosthetics that answer the three limits, in order:
- **Eyes** — retrieval. Pulling outside information *into* the window so the model can see it. → **RAG**
- **Hands** — tools. Turning the model's text into real actions in the world. → **function-calling / MCP**
- **Loop** — orchestration. Running the model repeatedly, feeding results back so it can iterate toward a goal. → **the harness**

**The Prosthetic Reframe**
The central insight of Session 1: RAG, MCP, and the harness are *not* three separate gadgets. They are three answers to a *single* limitation — that the model is a stateless text-predictor that can only think, never act, and only once. Bolt-ons to the same brain-in-a-jar.

**Scaffolding (where the value lives)**
The engineered system surrounding the model — what gets retrieved, which tools are exposed, how the loop is governed. In a real deployment, the reasoning core is *one component*; reliability, safety, and business value are overwhelmingly properties of the scaffolding, not the model. Hence why "just use an LLM to automate my job" disappoints: it mistakes the core for the system.

**Capability/Vulnerability Duality**
*(Carried over from the Claude course, and the through-line of this one.)* Every prosthetic that lets the model reach *out* is simultaneously a channel for something to reach *in*. Give it eyes, and it can read a poisoned document. Give it hands, and an injection can *act*. Capability and vulnerability are the same surface viewed from two chairs.

**The Phase Change (hands)**
The specific prosthetic that turns a prompt-injection from an embarrassment into an incident. Because *text is inert*, an injection in a plain chatbot yields only a rude sentence. The same injection behind a **tool** yields a deletion, a transfer, a misdirected email. The attack does not get more sophisticated — the model simply stops *describing* the action and starts *taking* it.

**The Vanishing Checkpoint (loop)**
The loop's specific contribution to danger. Its risk is not merely doing *more* of a bad thing — it is that the loop, by design, *removes the human from the gap* between the model's decision and its execution. Hands make one unsupervised action possible; the loop makes an unsupervised *sequence* of them possible. Putting the human back in the right place is a core Session 7 concern.

---

*Last updated: Session 1 — The Automation Problem.*
*Companion docs: automation-course-map.md · automation-references.md*
