# LLM Memory, Context Management & Extended Reasoning: Answer Key

> ⚠️ **Spoilers.** This file has model answers for every check-in question. Try the question in the [lessons doc](llm-memory-lessons.md) first, then come here to grade yourself.

How to use it: each entry has a **model answer** (what full marks looks like), a **why this matters** line, and **traps** (the mistakes that are easy to make, including ones made during this course). Where a lesson had a follow-up question after a correction, it's included as **Follow-up**.

See the [course map](llm-memory-course-map.md).

---

## Session 1: The Stateless Machine

**Q1. In turn 10, where is "Barnaby," and how did it get there?**
In the **transcript**: a few tokens written in turn 3, which the **harness** (on the raw API, *your own code*) has resent with every call since. The model reads it fresh each time and has no idea it has seen it before.

*Why this matters:* Every "the model forgot" or "the model remembered" bug starts here: what exactly was in the window, and who put it there?

**Q2. Two mechanisms for recall in a brand-new conversation, and whether weights are involved.**
- **Push:** the harness injects stored memory (a summary, user instructions, project notes) into the context before the model runs.
- **Pull:** the model calls a tool ("read file," "search past chats") and the result enters the context mid-turn.
- **Weights: not involved in either.** Both are contextual memory delivered by different routes.

*Why this matters:* When you choose how a product remembers users, push and pull have different costs and different ways of failing, and neither one touches the weights.

**Follow-up. Can "Barnaby" get into the weights?**
Only through **training**: conversations used in a future pretraining run (months) or a fine-tune (hours to days). Either way the result is a **new checkpoint** swapped in, never a mutation of the running model. Recall would be **fuzzy and unsourced**: a statistical association, possibly confabulated, with no record that it came from you.

*Why this matters:* It explains why a model can't simply learn your preferences from chatting, and it sets up Session 10: weights change only when a new checkpoint is swapped in.

**Traps:**
- Saying "the chat app resends it." On the API, *you* are the harness.
- Forgetting to state explicitly that weights aren't involved.

---

## Session 2: Anatomy of a Context Window

**Q. (A) fix a typo at the start vs. (B) append a note at the end. Which costs more, and what happens to the cache?**
**A is far more expensive.** Under causal masking, every token's keys and values depend on *all tokens before it*. Change token 1 and every downstream entry is invalid, so the **entire transcript** is recomputed. B touches nothing already computed. **With prompt caching:** A is a **total cache miss**. B is a **cache hit on the whole prefix**, and only the new note is computed.

*Why this matters:* Prompt-caching bills and latency depend on this. Harnesses put stable content first and volatile content last for exactly this reason.

**Follow-up. An edit in turn 10 of 20?**
Turns **1–9 are reusable** (in practice up to the last **cache breakpoint** before the edit). Turns **10–20 are recomputed**. A timestamp at the top of the system prompt would make *every* call a full miss, which is why volatile content goes last.

*Why this matters:* It tells you where to put anything that changes per call (timestamps, user state) so you don't pay a full cache miss every turn.

**Traps:**
- Explaining it from the wrong direction. The key fact is that *everything after the edit depends on it*.
- Thinking B's cheapness depends on conversation length. Appending is cheap at any length.
- Assuming caching saves transmission. It saves **compute**. The full text is still sent so the prefix can be matched, and caches **expire**.

---

## Session 3: How Models Forget Without Deleting

**Q. "Always use British spelling" fails by turn 150. Identify the mechanisms with evidence, and give two fixes targeting different mechanisms.**
- **Attention budget / distractor:** the 20K-token American document isn't just bulk. It's packed with *exactly the disputed words*, so it competes with the rule directly.
- **Position:** the rule's place at the start *helps*, but the drifted replies are **recent** and get the recency boost. Position amplifies the problem rather than causing it.
- **Effective context:** a turn-1 rule applied across 150 turns of a long context is used less reliably than the advertised window suggests.
- **In-context learning (the main driver):** a dozen uncorrected replies in the *model's own voice* are the strongest possible evidence of "how we write here."

**Fixes (any two, each targeting a different mechanism):**
- Remove or summarize the American document once it's been used (**attention budget**).
- Restate the rule near the end of the context (**position / recency**).
- Correct drift immediately, or **rewrite the drifted replies** in history (**in-context learning**). Rewriting costs one cache miss from the first edit onward.

*Why this matters:* It's the standard diagnosis when a long session stops following its rules, and each mechanism has a different fix.

**Traps:**
- Labeling "restate the rule at the end" as an attention-budget fix. It's mainly a **position** fix.
- Treating position as neutral. Here it works for *and* against you.

---

## Session 4: Context Management

**Q. 180K/200K session: choose a policy per region, give its risks, and say where the cache miss happens and why one batch is better.**

| Region | Strategy | Risk |
|---|---|---|
| System prompt + tools | **Leave alone** (pinned) | Minimal. It's a cache hit |
| Postgres decision (turn 12) | **Offload to the pushed, pinned layer** (CLAUDE.md or equivalent), *including the reason* | Losing the **why** ("because JSONB"). A pull-only decision file fails silently when the model doesn't think to read it |
| 60 file reads (~120K) | **Prune to stubs**, and re-read fresh when needed | Re-fetch cost. Summarizing code is risky: the model later trusts a paraphrase as if it were the code |
| Last 10 turns | **Keep verbatim** | Minimal |

**Cache:** hit on the pinned prefix, **miss from the first edited region down**. **One batch** means one miss, and the resulting context is *much smaller*, so cheap appends resume.

*Why this matters:* Every long agent session hits this wall. The policy you choose decides what the agent still knows after compaction and what it pays to get there.

**Traps:**
- Worrying about dilution while *extracting* the decision. Extraction is a focused task. The real risk is losing the reason, and pull failing silently.
- Missing **staleness**: old file reads may be *wrong* now that the files have changed. Pruning forces fresh, correct reads.

---

## Session 5: External Memory

**Q1. User preferences vs. 5,000 pages of docs: which store, push or pull?**
- **Preferences:** a small file, **pushed** every call (exact, always relevant, cheap).
- **Docs:** a search index. Classic RAG is **push** (harness code decides). Agentic search is **pull** (the model decides). Either is defensible.

*Why this matters:* Matching each store to the right access pattern is the core design decision in any assistant that needs memory beyond the window.

**Q2. Payments question, Orders answer: what failed, and why couldn't the model catch it?**
**Retrieval** failed. "Payments timeout" and "Orders timeout" sit close together in embedding space because the vectors mostly encode the *API timeout* topic. Often **chunking damage** is involved too: the "Orders API" heading ended up in a different chunk. The model **can't detect what it wasn't shown**, so it reasons correctly from the wrong page.

*Why this matters:* In RAG systems, most "the model is wrong" reports are actually retrieval bugs. Debug the retrieved text before blaming the model.

**Q3. "The model reads retrieved chunks as embedding vectors." True or false?**
**False.** Retrieval vectors exist only to *search*. The chunk's **text** enters the context and is re-tokenized with the model's own embeddings.

*Why this matters:* Mixing up the two kinds of embedding leads to wrong designs. Only text reaches the model, so chunk wording and headings matter.

**Follow-up. Harness-run retrieval: push or pull? Redesign it as the other.**
**Push.** As pull, the model gets a search tool and decides when to call it. The gain is **iteration**: it sees "Orders," notices the mismatch, and searches again. Costs: more round trips and latency. Keyword tools miss synonyms, so serious systems use **hybrid** search.

*Why this matters:* Who decides determines whether the system can recover from a bad first search. That's the real trade between RAG pipelines and agentic search.

**Traps:**
- Thinking "selective" means pull. **Push vs. pull is about *who decides*, not how much comes in.**
- Crediting grep's literal matching as the main benefit of pull. The main benefit is the model seeing results *before* it answers.

---

## Session 6: Claude's Memory Stack

**Q1. A 3,000-word runbook as CLAUDE.md, a skill, or a subagent: idle cost, and what happens after `/compact`.**

| | Session that never deploys | After `/compact` in a session that deployed |
|---|---|---|
| **CLAUDE.md** | ~4K tokens **every call**. Cheap in money (cached) but expensive in **attention**: a permanent distractor in the pinned region | **Survives** (reloaded) |
| **Skill** | Only the **description line** | The body is transcript, so it gets **summarized**. Re-invoke if still deploying |
| **Subagent** | Only the **description line** (equal to a skill, *not* cheaper) | The runbook was **never in the main window**. Only the report is summarized |

*Why this matters:* Putting guidance on the wrong layer either wastes attention on every call or loses it at compaction. This is how you decide where instructions live.

**Q2. Pick one.** A **skill** is a good answer: loaded only when needed, and in full. **Subagent + skill** is stronger: the subagent pulls the skill into *its* window, keeps noisy logs out of the main window, and writes full logs to a file.

*Why this matters:* The same reasoning applies to any procedure you'd hand an agent: load it when needed, and keep messy execution out of the main window.

**Q3. Why does the description matter most?** It's the only part pushed into every call, so it decides **whether and when** the skill fires. Too vague and it never fires, or fires at the wrong time.

*Why this matters:* A skill with a weak description is dead code. It never runs when it should, or it runs when it shouldn't.

**Follow-up. Subagent windows.**
The full runbook and logs are in the **subagent's** window. The main window holds **only the report**. Detail is lost **at report time, by design** (the subagent window is *discarded*, not compacted), compared with a skill's loss **at compaction time, by accident**.

*Why this matters:* Knowing where detail is lost tells you what a subagent's report must contain and what it should write to a file instead.

**Traps:**
- Thinking a subagent is "cheaper because it summarizes the runbook." Idle, it costs the same as a skill. When it runs, it has the *full* runbook. Only the main agent's view of *what happened* is summarized.
- Counting CLAUDE.md's cost only in tokens. The attention cost matters more.

---

## Session 7: Thinking in Tokens

**Q1. Which schema gives better verdicts: `{verdict, rationale}` or `{rationale, verdict}`?**
**B (rationale first).** Each token gets **one fixed-depth pass**. In B, the verdict token can **read** every rationale token, so the results of many earlier passes feed it. In A, the verdict is committed after one pass, and **causal masking** means nothing written afterward can influence it. Bonus: in B the rationale can't see the verdict, so it isn't steered toward a conclusion already reached.

*Why this matters:* Field order in structured output silently changes answer quality. It's one of the cheapest fixes in production prompting.

**Q2. Why is A's rationale unreliable as an explanation?**
It was generated **after** the verdict and conditioned on it: a **post-hoc** justification. It could not have caused the decision, however convincing it reads.

*Why this matters:* Teams audit model decisions by reading the explanations. A post-hoc rationale can look perfect while explaining nothing.

**Q3. Two consequences of 15K reasoning tokens from step 3 staying in context for steps 4–40.**
- **Budget and space:** about 15K tokens of window used for 37 steps, so compaction comes sooner. Resends are cheaper with caching but not free.
- **Attention:** by step 40 it sits in the **lost-in-the-middle trough**, diluting attention as a stale distractor.
- **Behavior (correction asymmetry):** if step 3 contained a wrong assumption corrected at step 10, **in-context learning** pits thousands of fluent wrong tokens against a few corrective lines. Fix: prune or summarize the stale reasoning and restate the correction near the end.

*Why this matters:* Long-running agents accumulate their own reasoning. Without pruning, old mistakes keep influencing later steps long after they were corrected.

**Traps:**
- Placing step-3 reasoning "near an end." The pinned prefix owns the start and recent turns own the end. Step 3 is in the **middle**.
- Answering about *editing* the reasoning (a cache miss) when the question is about it *sitting there*.
