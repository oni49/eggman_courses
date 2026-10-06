# Claude Capability & Use Vocabulary Reference
*Professor Eggman's Seminar — Running Glossary*

---

## Session 1 — What Claude Actually Is

**Language Model**
A system whose core operation is predicting what text should come next, given some input text. Every visible capability — writing, coding, argument — is this one operation applied at scale.

**Token**
The basic unit Claude processes — roughly a word or word fragment. Claude doesn't read sequentially the way a person does; it processes its entire input as one block, simultaneously.

**Stateless**
Claude retains nothing between separate conversations. Each new chat starts with zero knowledge of you, your prior work, or past sessions. The only thing Claude "knows" in a given moment is what's present in the current conversation.

**Context Window (introduced)**
The conversation Claude can currently see — your messages, its replies, any system instructions, any attached documents. This is Claude's *only* memory. Nothing exists for Claude outside of it.

---

## Session 2 — The Context Window

**Context Window (deep dive)**
The full container of everything visible to Claude in a session: system prompt, message history, attached files. Has a finite size, measured in tokens. Most ordinary conversations never approach the limit; long documents, codebases, or transcripts can.

**Whiteboard Analogy**
A teaching model for the context window: everything written on the board, Claude can see; everything off the board, Claude cannot. Used to make the abstract idea of "context" concrete and spatial.

**Lost-in-the-Middle**
A documented pattern where content at the very beginning and very end of a long context gets more weight in Claude's output than content buried in the middle. Practical implication: put critical instructions at the start (and reinforce at the end), not deep in the body of a long document.

**System Prompt**
Instructions loaded into the context window by the operator (e.g. Anthropic, or a developer building on Claude) before the user's first message. Sits at the start of the whiteboard, shaping behavior for the whole conversation.

**Context Window Overflow Handling**
When a conversation exceeds the window's size limit, Claude.ai quietly drops the oldest messages to make room for new ones. The conversation appears continuous to the user, but early context has silently fallen out of view.

**Handoff Summary**
A structured summary — decisions made, open questions, constraints, current state — generated at the *end* of a session and pasted at the *top* of a new one. The practical technique for working around statelessness on long or multi-session tasks. More effective than pasting a raw transcript, since it's denser signal with less noise.

**Retrieval-Augmented Generation (RAG)** *(mentioned, not covered in depth)*
A more advanced architecture where external systems dynamically pull relevant content into the context window on demand, rather than requiring everything to be present upfront. Flagged as existing; not yet unpacked.

---

## Session 3 — Conversation Chaining

**Re-reading the Transcript**
Every time Claude generates a reply, it re-reads the entire conversation so far — your messages *and its own previous replies* — as one block of input. There is no separate memory module; everything is inferred fresh, each turn, from the transcript in front of it.

**Precedent-Setting**
Because Claude's own past outputs go back onto the whiteboard, the tone, format, and depth it used earlier become examples that shape later turns. Consistency persists not by decision but because it's the path of least resistance through the existing pattern. Set a strong example early.

**Correction Persistence**
Why a correction usually sticks: your correction and Claude's compliant response sit in the transcript as a live example that every future turn re-reads. The rule holds because the demonstration of it stays visible, not because Claude "remembers" a rule.

**Drift**
The compounding of small, unapproved deviations over a long conversation. Each minor deviation becomes part of the precedent the next turn matches against, so looseness can self-reinforce until the conversation lands far from where it started — with no single moment of "deciding" to drift.

**Relative Weight Decay**
A consequence of lost-in-the-middle in a growing conversation: original instructions near the start carry relatively *less* pull as more text piles up after them, even while technically still in the window. The reason some platforms periodically reinforce original instructions in long threads.

**Re-seeding Precedent**
The most effective fix for drift or a faded instruction: don't just restate the rule — regenerate the non-compliant output and validate it, so fresh compliant examples land in the recent, high-weight part of the transcript. Re-asserts the rule *and* re-establishes the demonstration at once.

---

## Session 4 — Safety Architecture
*(Full threat-modeling treatment lives in `claude_basics-threat-modeling-safety-layers.md`. Core terms below.)*

**Eggman's Three Layers**
A teaching scaffold — not a citable taxonomy — for the safety architecture: Layer 1 trained temperament (in the weights), Layer 2 system prompt & in-context instructions (in the window), Layer 3 external classifiers (outside the window). Organizing principle: "depth wins" — deeper layers override shallower ones.

**Trained Temperament (Layer 1)**
Disposition baked into the model's weights during alignment training. Not a rule it looks up — it's *in* how Claude generates text. Can't be diluted or pasted over, because it isn't in the window. The robust layer; also the one an operator can least control.

**Generative vs. Inspective**
The key mechanical split. Layers 1 & 2 are *generative* — they shape how output is written, token by token, before and during generation. Layer 3 is *inspective* — it judges a completed input or output and makes a block/allow call on the finished artifact.

**Dilution Attack**
Pushing enough text into the window to shove system instructions out of the high-weight zones (into lost-in-the-middle or off the whiteboard via overflow), then landing an override against weakened resistance. Works only against Layer 2 — temperament isn't in the window to dilute.

**Reinforcement as Security Control**
Re-seeding precedent (RAG, skills, periodic restatement) does double duty: improves adherence *and* counters dilution by refreshing the weights an attacker is trying to decay. Defense by recency. But it hardens Layer 2 without changing its negotiable nature — pair with Layer 1 and Layer 3 for defense in depth.

**Indirect Injection**
Planting attacker-controlled text where the system will later retrieve it (a document, webpage, ticket, code comment) so it loads into the window without the attacker touching the interface. The trust boundary isn't the chat box — it's every source retrieval can reach.

**The Defender's Lens**
From a defender's chair, attacker intent is irrelevant — clarification and evasion produce the same failure surface. The synthesis: the strongest layer is the one you can't control, and the layers you can control are the ones that bend.

---

## Session 5 — Where the Legitimate Flexibility Lives

**Manipulation vs. Clarification**
Manipulation tries to change Claude's *constraints* ("ignore your rules") — it fails, and fails worse when aggressive, because it pattern-matches to an attack. Clarification changes Claude's *understanding of the situation* with genuine context — it works, because caution calibrates to the model's read of what's actually happening. One fights the model; the other informs it. The words can be identical; the difference is whether the context is true.

**Genuine vs. Performed Purpose**
Real context (true goal, constraints, use) is coherent and shifts Claude's read legitimately. Performed context worn as a costume tends to be thin or internally inconsistent — and that incoherence is itself a signal. Claude reads whether the whole picture hangs together, not credentials.

**Hard Floors**
The parts of the deep temperament that no amount of context moves. Clarification widens the band of what's appropriate; it doesn't remove the floor.

---

## Session 6 — The Assembled System: Features and Failure

**Capability/Vulnerability Duality**
Every feature that lets Claude reach outside its window is also a channel for something to reach in. Capabilities and vulnerabilities are the same surface viewed from two chairs.

**Tool / Function-Calling**
Giving Claude the ability to act — run code, query a database, call an API. Inherits every Layer 2 window weakness, but now the stakes are *actions*, not sentences. A prompt injection that produced rude text in a chatbot can produce a deletion in an agent.

**Fluency vs. Correctness**
Generated code (or any output) can be confident, clean, and well-commented yet subtly wrong or insecure — because the model optimizes for plausible continuation, not verified behavior. Polish is not proof.

**Compounding Failure (the Seams)**
In a simple chatbot, layer weaknesses coexist. In an agentic system (retrieval → tools → actions in a loop) they *chain*: each control can pass within its own narrow scope while the system as a whole fails. The vulnerability lives in the *trust relationships between components*, not the components themselves. Defense belongs at the trust boundaries — validate against ground truth, treat retrieved content as untrusted data never instructions, human-in-the-loop on consequential actions.

---

## Bonus Session — Cross-Tenant Leakage at the LLM/SaaS Seam

**Plumbing vs. Model Failure**
The reframe for cross-tenant leakage: a deployed model is stateless and frozen at inference, so it doesn't accumulate one tenant's data and emit it to another. Almost every real cross-tenant leak is a *plumbing* failure in the shared infrastructure around the model, not a model failure. The LLM draws the eye because it's new; the leak lives where it always has in multi-tenant SaaS.

**The Four Channels**
Cross-tenant leakage paths, ranked by real-world frequency: (1) the boring SaaS layer — a mis-scoped retrieval filter on a shared vector store; (2) shared context contamination — mis-keyed prompt/KV/session caches; (3) the training-time door — tenant data reaching a training run and being memorized in weights (rare, high-impact, only path to data genuinely "in the model"); (4) agentic/tool-mediated — an injected agent reading or writing a shared resource across the boundary.

**Three-Point Control Surface**
"The tenant-ID filter" hides three distinct enforcement points: *embed-time* (shared embedding service can leak before the filter exists), *write-time* (a mis-stamped tenant-ID poisons data at rest — the filter then faithfully serves it to the wrong tenant), and *read-time* (the retrieval filter everyone pictures — necessary, not sufficient). Most audits only check read-time.

**Structural Leakage**
Cross-tenant leakage in a pooled/shared RAG index can be *structural, not adversarial* — benign queries pull other tenants' data through organic entity overlap (shared vendors, personnel, common terms) that similarity search crosses. The embedding geometry itself bridges tenants. Strengthens the case for hard partitioning (per-tenant index/namespace) over a shared index with a filter.

**Probability vs. Impact (cross-tenant)**
Probability is driven by how much infrastructure is *shared* (shared indexes/caches/agent memory raise it); sharing-with-a-filter is riskier than hard partition. Impact is driven by *channel*: plumbing leaks are high-probability/bounded/patchable; training-time leaks are low-probability/unbounded/hard to remediate; agentic leaks can escalate from disclosure to action.

---

*Last updated: Bonus Session — Cross-Tenant Leakage (course complete)*
*Companion docs: claude_basics-course-map.md · claude_basics-threat-modeling-safety-layers.md · claude_basics-references.md*
