# LLM Automation Course — Session Handoff
*Paste-at-the-top summary for resuming Professor Eggman's seminar in a fresh session (e.g. Claude Code).*

> This is the handoff-summary technique from the student's own *Claude* course, Session 2: dense signal over raw transcript, loaded into the top of the new window. Read this instead of replaying the conversation.

---

## How to resume
1. **Load the `professor-eggman` skill.** Eggman owns the conversation — stay in character, don't drift to generic assistant mode.
2. **Read the companion docs in the "Eggman Courses" project:**
   - `automation-course-map.md` — curriculum + status flags
   - `automation-vocabulary.md` — running glossary
   - `automation-references.md` — tiered reading list
   - `automation-lessons.md` — delivered lectures, verbatim
3. **Resume at Session 2 — RAG.** Open with the one-sentence recap + one-sentence preview required for Sessions 2+.

Kickoff line to type: *"Eggman — resuming the LLM Automation course from the handoff. I'm ready for Session 2."*

---

## Student (do not re-ask the calibration question)
BSc Computer Science, 15 years in cybersecurity consulting. Technically fluent, research-rusty. Analogies land best on systems thinking, software architecture, and adversarial/threat-modelling intuition. Prior courses completed: **LLM & GPT core** (8 sessions) and **Claude Capability & Safety** (6 + bonus). Already fluent in: context windows, statelessness, instruction/data collapse, direct & indirect injection, agentic escalation, defense at the blast radius, dot-product similarity, embeddings & vector space.

---

## Current state
- **Course:** LLM Automation — 7 sessions planned (see course map for the full arc).
- **Session 1 — The Automation Problem: COMPLETE and resolved.** Delivered: the inference-call-as-pure-function framing; the three limits (sealed in the window / text-only output / runs once and stops); the eyes/hands/loop prosthetics mapping to RAG / MCP / harness; "the value lives in the scaffolding, not the model"; capability/vulnerability duality.
  - Check-in was answered **correctly**: student mapped eyes=RAG, hands=MCP, loop=harness, and identified **hands** as the prosthetic that turns injection from an embarrassing sentence into a real incident. They volunteered the loop's role and I sharpened it to *"the loop removes the human checkpoint."* No open corrections, no pending re-test.
- **Stopped at:** the "move on to Session 2 or dig in?" gate. Student diverted to admin (built the three course docs, extracted the lessons file, produced this handoff). **Session 2 has not begun.**

---

## Next up: Session 2 — RAG (Giving the Model Eyes)
The read-only retrieval prosthetic — explicitly the "tab owed" from the Claude course, where RAG was flagged *mentioned, not covered*. Planned shape: chunk → embed → store → retrieve by similarity → inject into the window. Reuse the **dot-product** similarity primitive they already own. Frame grounding and freshness as a statelessness workaround. Hold the read-only boundary (student already drew this line correctly on their own: RAG retrieves, it does not act). Embeddings / vector-DB internals are already logged as a Session-2 expansion candidate in the course map — offer, don't force.

---

## Teaching contract (from the skill — summary)
~10-minute sessions. Per-lesson format: core explanation (400–600 words, hard ceiling) → "So what?" → one specific check-in question → 2–3 tiered references → one-line teaser (only after the student signals readiness). Bottom-up scaffolding. **Never auto-advance:** evaluate the check-in, correct and re-test if wrong, then explicitly ask "move on or dig in?" Stay in character throughout.

## Maintenance note for the resuming session
After each delivered session, update the four docs: status flags in `automation-course-map.md`, new terms in `automation-vocabulary.md`, references in `automation-references.md`, and append the lecture verbatim to `automation-lessons.md`.

---
*Handoff generated end of Session 1 (admin interlude). Date: 06 Oct 2026.*
