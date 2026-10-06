# Understanding Claude: Capability, Use & Safety — Course Map
*Professor Eggman's Seminar*

A seven-session seminar (plus a bonus) on how Claude actually works — aimed at a technically literate professional who wanted to both use Claude better and understand where LLM systems fail. Bottom-up: foundations → mechanisms → safety → the assembled system.

**Companion docs:** `claude-vocabulary.md` (running glossary) · `threat-modeling-safety-layers.md` (full threat-model treatment) · `claude-references.md` (reading list).

---

## The arc

The course walks from "what is this thing" to "how do I threat-model a production deployment of it." The through-line, stated once and earned repeatedly: **the model is rarely the interesting failure — the vulnerability lives in the trust relationships between components, not in the component that happens to be new.**

---

## Sessions

**1 — What Claude Actually Is**
Language model as next-token predictor; whole input processed at once, not read sequentially; statelessness between conversations. The load-bearing idea the rest of the course rests on: *the context window is Claude's only memory.* Analogy layer: a brilliant consultant who reads every document fresh and forgets on leaving.

**2 — The Context Window**
The window as a whiteboard: everything on it is visible, everything off it is invisible. Finite size in tokens. Attention skews to the start and end ("lost-in-the-middle"). Overflow silently drops the oldest content. Practical payload: front-load critical context; a structured *handoff summary* beats a raw transcript for multi-session work, and belongs at the top of the new session.

**3 — Conversation Chaining**
Claude re-reads the whole transcript — including its own past replies — every turn. Consequences: precedent-setting (early tone/format persists), correction persistence (fixes stick because the demonstration stays visible), drift (small deviations compound), and relative weight decay (early instructions dilute as text piles up). The fix for a faded instruction isn't just restating it — it's *re-seeding precedent* by regenerating compliant output into the recent, high-weight zone.

**4 — Safety Architecture**
The core lesson, and the spine of the back half. "Eggman's Three Layers" (a teaching scaffold, not a citable taxonomy): Layer 1 trained temperament in the weights, Layer 2 system-prompt/in-context instructions, Layer 3 external classifiers. Key splits: generative vs. inspective; in-window vs. external; temperament vs. text. "Depth wins." This session spawned an extended threat-modeling side-thread — captured in full in `threat-modeling-safety-layers.md`.

**5 — Where the Legitimate Flexibility Lives**
The inverse of adversarial framing. Manipulation (change the constraints) fails and fails worse when aggressive; clarification (change Claude's understanding with true context) works, because caution calibrates to the model's read of the situation. Genuine vs. performed purpose; hard floors that no context moves. To a defender, though, the manipulation/clarification distinction collapses — motive isn't a control and isn't observable.

**6 — The Assembled System: Features and Failure**
Capability/vulnerability duality: every feature that reaches outside the window is a channel for something to reach in. Tools (stakes become actions, not sentences), retrieval/RAG (indirect injection — the trust boundary is every source retrieval touches), search/browsing (the open internet as adversarial input), artifacts/code (fluency is not correctness). The compounding thesis: in agentic loops, layer weaknesses *chain* — each control passes in its own scope while the system fails in the seams.

**Bonus — Cross-Tenant Leakage at the LLM/SaaS Seam**
Applied threat modeling for multi-tenant LLM SaaS. The model is usually not the leak; the plumbing is. Four channels ranked by real-world frequency (retrieval-filter/vector-store → shared caches → training-time → agentic write-side). The control surface is *three* enforcement points wearing one name — embed-time, write-time, read-time — and most audits only check read-time. Structural (non-adversarial) cross-tenant leakage is a documented risk of pooled indexes.

---

## What the student walked out able to do

Read a product's safety layers from the outside (the field-guide tells in the threat-model doc); explain why an operator's strongest control is the one they can't tune and their weakest is the one they wrote; and threat-model a multi-tenant LLM deployment across embed/write/read boundaries.

---

*Course complete. Standing offer: follow-on seminars on the black-boxed threads — training mechanics, and the specific agentic defenses (sandboxing, privilege separation, input/output segregation).*
