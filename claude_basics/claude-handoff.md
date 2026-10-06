# Claude Capability & Safety Course — Session Handoff
*Paste-at-the-top summary for resuming Professor Eggman's seminar in a fresh session (e.g. Claude Code).*

> This is the handoff-summary technique from this very course, Session 2: dense signal over raw transcript, loaded into the top of the new window. Read this instead of replaying the conversation.

---

## How to resume
1. **Load the `professor-eggman` skill.** Eggman owns the conversation — stay in character, don't drift to generic assistant mode.
2. **Read the companion docs in the "Eggman Courses" project:**
   - `claude-course-map.md` — curriculum + status flags
   - `claude-lessons.md` — delivered lectures, verbatim
   - `claude-vocabulary.md` — running glossary
   - `claude-references.md` — tiered reading list
   - `threat-modeling-safety-layers.md` — the extended threat-model deep-dive (side-threads + cross-tenant bonus)
3. **This course is COMPLETE.** There is no next numbered session. Resume only if the student wants a follow-on seminar on a deferred thread (see "Next up"), or has follow-up questions on delivered material.

Kickoff line to type: *"Eggman — the Claude Capability & Safety course is done. I want to pick up [the training-mechanics thread / the agentic-defenses thread / a question about Session N]."*

---

## Student (do not re-ask the calibration question)
BSc Computer Science, 15 years in cybersecurity consulting. Technically fluent, research-rusty. Analogies land best on systems thinking, software architecture, and adversarial/threat-modelling intuition. In this course the student arrived already comfortable with detailed prompting, prompt-improvement, and skill-authoring; came to understand content/safety limits and session memory. Performed at graduate level — repeatedly extended the material ahead of the lecture (derived the dilution-as-injection vector, the "context fork bomb," and the embed/write/read three-point control surface on their own). Teach up, not down.

---

## Current state — all sessions COMPLETE and resolved
Six core sessions plus a bonus, all check-ins passed (no open corrections, no pending re-tests):

1. **What Claude Actually Is** — next-token prediction, whole-input processing, statelessness; the context window as the only memory.
2. **The Context Window** — the whiteboard model, lost-in-the-middle, overflow eviction, the handoff-summary technique.
3. **Conversation Chaining** — re-reading the transcript each turn, precedent-setting, drift, relative weight decay, re-seeding precedent.
4. **Safety Architecture** — Eggman's Three Layers (trained temperament / system prompt / external classifiers); generative vs. inspective; "depth wins."
5. **Where the Legitimate Flexibility Lives** — manipulation vs. clarification; genuine vs. performed purpose; hard floors.
6. **The Assembled System** — capability/vulnerability duality; tools, RAG, search, artifacts; compounding failure in the seams.
- **Bonus — Cross-Tenant Leakage at the LLM/SaaS Seam** — plumbing-not-model; four channels; the embed/write/read control surface; structural (non-adversarial) leakage.

The extended threat-modeling side-threads (the field guide for reading layers from outside, the dilution attack, the context fork bomb, GUI-vs-API, the defender's lens, the full reference list) all live in `threat-modeling-safety-layers.md`, kept out of `claude-lessons.md` to keep the lecture record pure.

---

## Next up (optional follow-on threads Eggman explicitly deferred)
These were "black-boxed" during the course and offered as future seminars — not started:
- **Training mechanics** — how the Layer 1 temperament is actually trained (the black box behind Session 4). Deeper than the Constitutional-AI summary given.
- **Agentic defenses** — the specific controls glossed in Session 6: input/output segregation, tool sandboxing, privilege separation, human-in-the-loop on consequential actions.

Either would be a fresh short seminar in its own right. Offer; don't force.

---

## Teaching contract (from the skill — summary)
~10-minute sessions. Per-lesson format: core explanation (400–600 words, hard ceiling) → "So what?" → one specific check-in question → 2–3 tiered references → one-line teaser (only after the student signals readiness). Bottom-up scaffolding. **Never auto-advance:** evaluate the check-in, correct and re-test if wrong, then explicitly ask "move on or dig in?" Stay in character throughout.

## Maintenance note for the resuming session
If a follow-on seminar is taught, keep it OUT of `claude-lessons.md` unless the student wants the slug extended — a new thread is arguably its own course. Update the four `claude-*` docs only for material that belongs to this course; capture side-threads in the threat-model deep-dive doc, as before.

---
*Handoff generated after course completion. Date: 06 Oct 2026.*
