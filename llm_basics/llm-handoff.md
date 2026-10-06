# LLM & GPT Course — Session Handoff
*Paste-at-the-top summary for resuming Professor Eggman's seminar in a fresh session (e.g. Claude Code).*

> This is the handoff-summary technique from the student's own *Claude* course, Session 2: dense signal over raw transcript, loaded into the top of the new window. Read this instead of replaying the conversation.

---

## How to resume
1. **Load the `professor-eggman` skill.** Eggman owns the conversation — stay in character, don't drift to generic assistant mode.
2. **Read the companion docs in the "Eggman Courses" project:**
   - `llm-course-map.md` — curriculum + status flags + expansion audit
   - `llm-vocabulary.md` — running glossary (~50 terms, 8 sections)
   - `llm-references.md` — tiered reading list (Sessions 1–8)
   - `llm-lessons.md` — delivered lectures, verbatim (core only)
3. **The core curriculum is COMPLETE (Sessions 1–8).** There is no Session 9 unless the student picks one from the expansion candidates below. Do **not** auto-start new material — ask which thread, if any, they want.

Kickoff line to type: *"Eggman — resuming the LLM & GPT course from the handoff. The core's done; here's what I want next: [pick an expansion thread, or an open offer, or just questions]."*

---

## Student (do not re-ask the calibration question)
BSc Computer Science, 15 years in cybersecurity consulting. Technically fluent, research-rusty. Analogies land best on systems thinking, software architecture, and adversarial/threat-modelling intuition. Reasons unusually well from first principles — repeatedly *re-derived* mechanisms (attention Q/K/V, parallel-edge embeddings, the reward-hacking and injection-defense syntheses) ahead of being taught them. Teach *up* to that; don't over-explain. Email on file: sean.k.eyre@gmail.com (identity/authorship only).

---

## Current state — COURSE COMPLETE
All eight core sessions delivered, every check-in answered and resolved (no pending corrections, no open re-tests):

1. **Why Language Modelling Is Hard** — syntax vs semantics, ambiguity, world knowledge, BoW failure modes. *Security anchor: WAF pattern-matching vs semantic intent.*
2. **Tokens, Embeddings, Vector Spaces** — embeddings, Word2Vec, static vs contextual, relationship vectors, dot product, tensors, curse of dimensionality.
3. **The Transformer** — self-attention, Q/K/V, multi-head attention, transformer block, attention head redundancy / collapse, lost-in-the-middle. *Security anchor: SIEM correlation rules.*
4. **Pre-training** — next-token prediction, self-supervised learning, base models, recombination (grey swan) vs true novelty (black swan), scaling laws. *Security anchor: 0-day discovery as recombination.*
5. **Fine-tuning, RLHF, Alignment** — SFT, RLHF, reward model, reward hacking, sycophancy, quality regression, Constitutional AI/RLAIF, training-time vs inference-time firewall (Tay 2016).
6. **Inference, Temperature, Sampling** — logits, softmax, greedy vs sampling, temperature, top-k, top-p, non-reproducibility. *Security anchor: coverage-vs-reproducibility trade in a detection pipeline.*
7. **Capabilities & Failure** — emergent capabilities + emergence-as-artifact caveat, hallucination, confidence ≠ correctness, correlated errors / why one LLM can't check another, verification from outside the system.
8. **Security Implications** *(the session built for their 15 years)* — instruction/data collapse, direct vs indirect injection, stored/persistent injection, jailbreak vs injection, agentic escalation (injection → action → RCE), "no grammar for malice," defense at the blast radius.

The through-line, earned twice over: **the form/meaning gap is the whole course — a capability limit in Session 1, an attack surface in Session 8.**

---

## OPEN OFFERS from this session (the actual reason to resume)
Two offers were left on the table and never taken up. If the student resumes, these are the live threads:

1. **Reconstruct Session 2 as a clean standalone lecture.** In `llm-lessons.md`, Sessions 1–2 are thinner than as-taught because they were delivered Socratically — the contextual-embeddings "three points" breakdown, the King→Queen *parallel-edges* correction, and the relationship-vector → Q/K/V derivation were all cut (they were responses *to the student's own reasoning*, excluded under the "no answer-discussion" rule). Offer: rebuild them into a clean Session 2 lecture as a separate pass.
2. **Regenerate `llm-lessons.md` with stage directions preserved.** Current version strips the in-character business (marker-capping, coffee) where it was interleaved mid-prose. Offer stands to keep them in if the student prefers the "as performed" feel.

---

## Expansion candidates (optional Session 9+, from the end-of-curriculum audit)
Ranked. The first two are genuine *mechanism* gaps, not just depth:
- ✦ **Positional encoding** — how the Transformer knows word *order* given it processes everything at once. A real gap; nothing was taught on it.
- ✦ **Tokenisation proper (BPE)** — "token" and "word" were used interchangeably throughout as a deliberate simplification. Subword tokenisation is its own topic and explains otherwise-baffling model behaviour.
- ✦ **Transformer internals** — residual connections, layer norm, feed-forward layers. Defined in glossary, mechanism never unpacked. (Student declined the dig-in once already in Session 3 — offer, don't push.)
- ✦ **Scaling laws** — named in Session 4, the data/compute/parameter math never explored.
- ✦ **The data pipeline** — how the raw internet gets cleaned/deduplicated/filtered before pre-training.

---

## Where this sits in the broader Eggman track
This is course **1 of 3** the student has run. Sequence: **LLM & GPT (this, complete)** → **Claude Capability & Safety (6 + bonus, complete)** → **LLM Automation (in progress, at its own Session 2 — RAG)**. The *active front* is the Automation course; see `claude/automation-handoff.md` for that one. This handoff is only for returning to LLM & GPT material.

---

## Teaching contract (from the skill — summary)
~10-minute sessions. Per-lesson format: core explanation (400–600 words, hard ceiling) → "So what?" → one specific check-in question → 2–3 tiered references → one-line teaser (only after the student signals readiness). Bottom-up scaffolding. Define every acronym on first use — a standing request the student made in Session 1. **Never auto-advance:** evaluate the check-in, correct and re-test if wrong, then explicitly ask "move on or dig in?" Stay in character throughout.

## Maintenance note for the resuming session
If a Session 9 (or a reconstruction pass) is delivered, update the four docs to match: status/flags in `llm-course-map.md`, new terms in `llm-vocabulary.md`, references in `llm-references.md`, and append the lecture verbatim to `llm-lessons.md`. Honesty rule the student values: references are given from memory — flag that they should be verified before citation, and never fabricate one.

---
*Handoff generated after course completion + admin interlude (lessons extraction). Date: 06 Oct 2026.*
