# Understanding Claude — Reference List
*Professor Eggman's Seminar · reading list*

Anchors for the course's claims, tiered by how to use each one: **Foundational** (the primary source), **Verify** (read this to check the mechanism yourself), **Go deeper** (extends the topic). The scaffold ("Eggman's Three Layers") is pedagogy; everything below is the real, separately-documented material underneath it.

**Companion docs:** `claude_basics-course-map.md` · `claude_basics-vocabulary.md` · `claude_basics-threat-modeling-safety-layers.md`.

---

## Alignment & trained temperament (Layer 1)

- **Foundational** — Bai et al., "Constitutional AI: Harmlessness from AI Feedback," Anthropic, Dec 2022. `arXiv:2212.08073`. The Layer 1 component end to end: a written constitution of natural-language principles, a supervised self-critique/revision phase, then RL from AI-generated preference labels (RLAIF) in place of human harmlessness labels. The source for "temperament trained in, not rules looked up." Companion code: `github.com/anthropics/ConstitutionalHarmlessnessPaper`.
- **Go deeper** — Anthropic, "Claude's Character" (anthropic.com). How disposition and traits are deliberately shaped beyond bare harmlessness — supports the "temperament, not a filter" framing. Pull the current version directly.

## Prompt injection & the system-prompt surface (Layer 2)

- **Verify** — Greshake et al., "Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection," AISec '23 (16th ACM Workshop on AI and Security). `arXiv:2302.12173`. The canonical indirect-injection paper — formalizes "retrieved prompts act as arbitrary code," dissolves the data/instruction line, and gives the original threat taxonomy (data theft, worming, ecosystem contamination, unauthorized API calls). The academic spine of Sessions 4–6. Demos: `github.com/greshake/llm-security`.
- **Go deeper** — Yi et al., "BIPIA: Benchmarking Indirect Prompt Injection Attacks." The first benchmark for indirect injection — quantifies how widely models fail to separate external content from instructions. For when a client wants numbers, not just a mechanism.

## The assembled agentic system (Layer 3 & compounding failure)

- **Go deeper** — OWASP, "Top 10 for Large Language Model Applications" (current version, owasp.org). Industry-standard risk vocabulary — prompt injection (LLM01), insecure output handling, excessive agency. The right language for client-facing findings; revised periodically, so pull the latest.
- **Go deeper** — Debenedetti et al., "AgentDojo: A Dynamic Environment to Evaluate Attacks and Defenses for LLM Agents," 2024. An evaluation harness for tool-using agents under attack — maps directly onto the "compounding failure in agentic loops" thesis and reasoning about defenses at trust boundaries.

## Multi-tenant isolation & cross-tenant leakage (bonus session)

- **Foundational** — "Silo, Pool, and Bridge for Multi-Tenant RAG" (IJETCSIT). A taxonomy paper defining three isolation patterns (Silo = per-tenant everything; Pool = shared with a filter; Bridge = hybrid) and a RAG-specific threat model covering cross-tenant embedding leakage via similarity search, membership inference, index poisoning, retrieval contamination from incorrect scoping, and metadata inference. The best academic anchor for the "embed / write / read" three-point control surface.
- **Go deeper** — "Security Challenges of LLM Integration in Multi-Tenant SaaS" (cybersecurityjournal.info). Reference architecture of a multi-tenant LLM SaaS with attack-surface points mapped to a vulnerability taxonomy; notes RAG poisoning's high amplification factor (one poisoned doc influencing all tenants). Good for a client-facing architecture diagram.
- **Verify (with caution)** — the "structural leakage" finding: a reported multi-tenant corpus in which a large majority of *benign* queries triggered cross-tenant retrieval through organic entity overlap (shared vendors, personnel, common terms), not adversarial attack. Circulating via practitioner write-ups (e.g. Blaxel, "Multi-tenant isolation for AI agents") citing an arXiv preprint — track down the primary preprint before citing the exact percentage to a client.
- **Go deeper (practitioner pattern)** — AWS, "Multi-tenant RAG implementation with Amazon Bedrock and Amazon OpenSearch Service for SaaS using JWT" (aws.amazon.com/blogs). A concrete Pool-with-hard-controls pattern using JWT + fine-grained access control (FGAC) for tenant routing and isolation. The operational cardinal rule it embodies: *filter before retrieval, never after* — and enforce tenant-ID at write time, not just query time.

---

*Caveat: this list reflects a Jan 2026 knowledge cutoff and a small number of search passes. Alignment, prompt-injection, and multi-tenant-isolation research all move fast — treat these as anchors, not a complete survey, and re-check for newer work (and confirm arXiv IDs and venues) before citing to a client.*
