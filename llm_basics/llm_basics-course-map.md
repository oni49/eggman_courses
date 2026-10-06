# LLM & GPT Course — Lesson Plan
*Professor Eggman's Graduate Seminar — Full Course Map*

**Student profile:** BSc Computer Science, 15 years in cybersecurity consulting. Research-rusty, technically fluent. Analogies lean on systems thinking, software architecture, and adversarial/threat-modelling intuition.

**Format:** ~10-minute conversational sessions. Bottom-up scaffolding: problem space → vocabulary → mechanisms → application → frontiers → failure modes. One topic per session, Socratic check-in before advancing.

**Status key:** ✅ complete · ▶ next · ◻ planned · ✦ candidate for expansion

---

## Part I — The Problem Space

### ✅ Session 1 — Why Language Modelling Is Hard
The gap between *form* and *meaning*. Why language resists naive computation.
- Syntax vs semantics
- Ambiguity in isolation ("bank"), compositional meaning ("dog bites man")
- World knowledge required for disambiguation (the trophy/suitcase problem)
- Bag of Words (BoW) and its failure modes: negation, word order, token specificity
- *Security anchor:* WAF pattern-matching vs semantic intent

### ✅ Session 2 — Tokens, Embeddings, and Vector Spaces
Representing meaning as geometry.
- Embeddings: words as points in high-dimensional space
- Vectors, vector space, consistent directional structure
- Word2Vec and vector arithmetic (King − Man + Woman ≈ Queen)
- Static vs contextual embeddings
- Relationship vectors — meaning baked into geometry, not stored explicitly
- Dot product as the similarity primitive
- Scalars, matrices, tensors; the curse of dimensionality

---

## Part II — The Mechanism

### ✅ Session 3 — The Transformer Architecture
The engine underneath every modern LLM.
- Self-attention: every word weighs relevance to every other, simultaneously
- Query / Key / Value — the search-engine analogy
- Attention scores and the dot product
- Multi-Head Attention (MHA): parallel heads, specialised relationship types
- Transformer block assembly: attention → residual → norm → feed-forward → residual → norm
- Failure modes: attention head redundancy, attention collapse, lost-in-the-middle
- Prompt-engineering implications: diverse signals over repetition; critical instructions early
- *Security anchor:* SIEM correlation rules as MHA failure analogy

### ✅ Session 4 — Pre-training: Objectives, Data, and Scale
Where the knowledge actually comes from.
- The empty Transformer: structure without knowledge
- Next-token prediction as the training objective
- Self-supervised learning: the internet labels itself
- Competence (geography, arithmetic, causality) emerging as a side effect
- Base models vs assistants
- Recombination / grey swan vs true novelty / black swan
- *Security anchor:* 0-day discovery as recombination, not conceptual invention

---

## Part III — Steering the Model

### ✅ Session 5 — Fine-tuning, RLHF, and Alignment
Turning a knowledgeable autocomplete into a useful assistant.
- Why base models are nearly useless as assistants
- Supervised Fine-Tuning (SFT): demonstrating good behaviour
- Reinforcement Learning from Human Feedback (RLHF): ranking over authoring
- The reward model as a learned proxy for human preference
- Reward hacking: maximising the proxy while degrading the goal
- Sycophancy and quality regression (regression to the rater pool)
- Constitutional AI (CAI) / RLAIF
- Training time vs inference time — the structural firewall Tay (2016) lacked
- *Security anchor:* optimising the metric instead of the goal; invisible failures in production

### ✅ Session 6 — Inference, Temperature, and Sampling
How the frozen model turns probabilities into actual words.
- From logits to probability distributions (softmax)
- Greedy decoding vs sampling
- Temperature: tuning randomness/creativity vs determinism
- Top-k and top-p (nucleus) sampling
- Why the same prompt yields different answers
- Practical levers for controlling output behaviour
- *Security anchor:* temperature as a coverage-vs-reproducibility trade in a detection pipeline

---

## Part IV — Frontiers & Failure

### ✅ Session 7 — Capabilities, Emergent Behaviour, and Failure Modes
What these systems can and can't do — and how they break.
- Emergent capabilities and the scale-threshold debate (plus the emergence-as-artifact caveat)
- Hallucination: confident, fluent, wrong — and why it happens
- Fluency and accuracy from the same machinery; confidence ≠ correctness
- Connecting hallucination back to attention collapse and missing contradiction
- Why "one LLM checking another" fails: correlated errors, sycophancy, framing
- Verification must come from outside the probabilistic system

### ✅ Session 8 — Security Implications *(leaned hard into student expertise)*
The adversarial surface of deployed LLMs.
- The instruction/data collapse — the root vulnerability
- Prompt injection: direct vs indirect
- Stored/persistent injection via write access (LLM-native stored XSS)
- Jailbreaks vs injection — alignment target vs instruction-hierarchy target
- Lost-in-the-middle as an injection vector
- The agentic escalation: injection → action → RCE with a semantic trigger
- Why the SQL-injection playbook fails: malice has no grammar
- Defense at the blast radius, not the input
- *This was the session built for the student's fifteen years.*

---

## Flagged for Possible Expansion (End-of-Curriculum Audit)

Terms introduced as definitions or touched briefly, but never given full treatment. Candidates for dedicated sessions if the student wants them:

- ✦ **Transformer internals** — residual connections, layer normalisation, feed-forward layers. Defined in the glossary, mechanism not unpacked. *(Tracked: #8. Student declined this dig-in once in Session 3 — offer, don't push.)*
- ✦ **Scaling laws** — named in Session 4, but the empirical data/compute/parameter relationship was not explored in depth. *(Tracked: #3.)*
- ✦ **The data pipeline** — how the raw internet gets cleaned, deduplicated, and filtered before pre-training. Mentioned, not covered. *(Tracked: #3.)*
- ✦ **Tokenisation proper** — we treated "tokens" and "words" loosely; subword tokenisation (BPE) is its own topic. *(Tracked: #8.)*
- ✦ **Positional encoding** — how the Transformer knows word *order*, given it processes everything simultaneously. A genuine gap worth closing. *(Tracked: #8.)*

Session 9 is **on hold** at the student's request (06 Oct 2026). Do not auto-start new material.

---

## Open Offers (post-course)

- **Reconstruct Session 2 as a clean standalone lecture** — the contextual-embeddings "three points" breakdown, the King→Queen *parallel-edges* correction, and the relationship-vector → Q/K/V derivation were cut from `llm_basics-lessons.md` as answer-discussion. *(Tracked: #5.)*
- **Regenerate `llm_basics-lessons.md` with stage directions preserved** — the current version strips in-character business (marker-capping, coffee) where it was interleaved mid-prose. Offer stands if the student prefers the "as performed" feel.

---

## Course Notes

- **Through-line:** the form/meaning gap is the whole course — a capability limit in Session 1, an attack surface in Session 8.
- **Student calibration (do not re-ask):** reasons unusually well from first principles — repeatedly re-derived mechanisms (attention Q/K/V, parallel-edge embeddings, the reward-hacking and injection-defense syntheses) ahead of being taught them. Teach *up*; don't over-explain. Define every acronym on first use (standing request from Session 1).
- **Track position:** course 1 of the Eggman track — LLM & GPT (`llm_basics/`) → Claude Capability & Safety (`claude_basics/`) → LLM Automation (`automation/`).

---

*Last updated: end of Session 8 — Security Implications. **Core curriculum complete.***
*Optional Session 9+ available from the expansion candidates above, at the student's discretion.*
