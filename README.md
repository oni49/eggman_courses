# Eggman Courses

```
               ___________________
          ____/___________________\____
          '------------._.------------'
                   .-'"""""'-.         \
                .-'           '-.       @
               /   ___     ___   \
              |   /   \___/   \   |
              |   \_o_/   \_o_/   |
              |         ^         |
       .~~~~~~~~~~~~.  ___  .~~~~~~~~~~~~.
      (~~~~~~~~~~~~~~\/   \/~~~~~~~~~~~~~~)
       '~~~~~~~~~~~~'  '-'  '~~~~~~~~~~~~'
               \                 /
                '-.  '-----'  .-'
                  '-._______.-'

     +------------------------------------+
     |  PROF. EGGMAN  .  form != meaning  |
     +------------------------------------+
```

Course records from **Professor Eggman**, a Claude skill that teaches any topic as a series of short, seminar-style sessions.

## How Eggman works

The skill lives in [`.claude/skills/professor-eggman/SKILL.md`](.claude/skills/professor-eggman/SKILL.md). Start it with "Eggman, teach me about [topic]". Pick up a course again with "next lesson" or "I'm ready".

- **Calibrate first.** Before teaching, Eggman asks one question about what you already know, then pitches the course to your answer.
- **Announce the curriculum.** A course runs 6–10 sessions and builds up in order: problem space → vocabulary → mechanisms → application → frontiers → failure modes.
- **Fixed lesson format.** Each session has:
  - a core explanation of 400–600 words
  - a "So what?" paragraph tying it to real-world practice
  - one specific check-in question
  - 2–3 tiered references (*Foundational* / *Verify* / *Go deeper*)
- **Never auto-advance.** Eggman grades each check-in answer. If the answer is wrong, Eggman corrects it and asks a targeted follow-up question. Only then does Eggman ask whether you want to move on or dig in.
- **Keep a durable record.** Each course gets its own `<slug>/` folder with five living docs:

  | Doc | Contents |
  |---|---|
  | `<slug>-course-map.md` | The curriculum, with sessions ticked off as completed |
  | `<slug>-lessons.md` | The lectures exactly as delivered (core material only) |
  | `<slug>-vocabulary.md` | A running glossary, grouped by session |
  | `<slug>-references.md` | The tiered reading list. Nothing invented; anything unverified is flagged |
  | `<slug>-answer-key.md` | Model answers to every check-in, with a *why this matters* line and common traps. Kept separate so re-study starts cold |

  Side-threads and deeper dives go in companion docs, never in the lessons doc.

Courses not yet started are tracked as [issues](https://github.com/oni49/eggman_courses/issues).

## Courses

### [`llm_basics/`](llm_basics/): LLM & GPT (complete)

Eight sessions on how large language models work, from why language is hard to compute through to the attack surface of a deployed model. The through-line is that **the gap between form and meaning is the whole course: it limits capability in Session 1 and becomes an attack surface in Session 8.**

1. **Why Language Modelling Is Hard**: syntax vs. semantics, ambiguity, world knowledge, and why bag-of-words fails
2. **Tokens, Embeddings, and Vector Spaces**: meaning as geometry, Word2Vec, static vs. contextual embeddings
3. **The Transformer Architecture**: self-attention, Query/Key/Value, multi-head attention, and lost-in-the-middle
4. **Pre-training**: next-token prediction, self-supervised learning, base models, and recombination vs. true novelty
5. **Fine-tuning, RLHF, and Alignment**: SFT, reward models, reward hacking, sycophancy, and Constitutional AI
6. **Inference, Temperature, and Sampling**: logits, softmax, temperature, top-k / top-p, and non-reproducibility
7. **Capabilities, Emergent Behaviour, and Failure Modes**: hallucination, confidence ≠ correctness, and why one LLM can't check another
8. **Security Implications**: the collapse of instructions and data, direct vs. indirect prompt injection, agentic escalation, and defending at the blast radius

The four course docs are in the folder. Follow-ons: a rebuilt Session 2 ([#5](https://github.com/oni49/eggman_courses/issues/5)), the Transformer mechanism gaps ([#8](https://github.com/oni49/eggman_courses/issues/8)), and the data pipeline and scaling laws as part of training in practice ([#3](https://github.com/oni49/eggman_courses/issues/3)).

### [`claude_basics/`](claude_basics/): Understanding Claude: Capability, Use & Safety (complete)

Six sessions plus a bonus on how Claude actually works, from "what is this thing" to "how do I threat-model a production deployment of it". The through-line is that **the vulnerability lives in the trust relationships between components, not in the component that happens to be new.**

1. **What Claude Actually Is**: next-token prediction, statelessness, and the context window as Claude's only memory
2. **The Context Window**: lost-in-the-middle, overflow eviction, and handoff summaries
3. **Conversation Chaining**: precedent-setting, drift, and re-seeding precedent
4. **Safety Architecture**: Eggman's Three Layers (trained temperament / system prompt / external classifiers), and why deeper layers win
5. **Where the Legitimate Flexibility Lives**: manipulation vs. clarification, and hard floors
6. **The Assembled System**: tools, retrieval, and search, and how failures compound in the seams between them

**Bonus: Cross-Tenant Leakage at the LLM/SaaS Seam.** Why the leak is usually in the surrounding infrastructure, not the model. Covers the embed/write/read control surface and structural leakage.

The four course docs are in the folder, plus a companion [threat-modeling deep-dive](claude_basics/claude_basics-threat-modeling-safety-layers.md) that covers the Session 4 side-threads and the bonus session. Follow-on seminars: training mechanics ([#2](https://github.com/oni49/eggman_courses/issues/2)) and agentic defenses ([#6](https://github.com/oni49/eggman_courses/issues/6)).

### [`llm-memory/`](llm-memory/): LLM Memory, Context Management & Extended Reasoning (in progress)

Eleven sessions on what a model remembers, how its context is managed, and how it thinks, using Claude and Claude Code as the worked example. The through-line is that **the model is a stateless function and everything it "remembers" is something the harness put back in the window, including its own reasoning.**

1. **The Stateless Machine**: frozen weights, the autoregressive loop, and the harness resending the transcript every call
2. **Anatomy of a Context Window**: tokens, decoder-only transformers, attention, causal masking, and the KV cache
3. **How Models Forget Without Deleting**: attention dilution, lost-in-the-middle, effective context, and drift through in-context learning
4. **Context Management**: pinning, truncation, compaction, tool-result pruning, offloading, and the cache tax
5. **External Memory**: files, RAG, agentic search, and push vs. pull retrieval
6. **Claude's Memory Stack**: when CLAUDE.md, skills, subagents, and tool definitions enter context, and how long each lasts
7. **Thinking in Tokens**: fixed compute per token, the scratchpad, and why reasoning must come before the answer
8. **Trained Reasoners**: RL-trained reasoning, thinking budgets, interleaved thinking, and what is dropped between turns
9. **Long-Horizon Agents**: compounding error, the window as a cache with disk as the truth, hybrid push/pull status, orchestrator–worker, and context resets
10. **Watching the Weights** *(upcoming)*: detecting silent model updates, and what "provably static" can mean
11. **Failure Modes & Open Problems** *(upcoming)*

The five course docs are in the folder, plus a layered [memory diagram](llm-memory/llm-memory-diagram.md) with phone-friendly renders of each layer, and a [deep-dives](llm-memory/llm-memory-deep-dives.md) companion covering push vs. pull, the tool-call loop, and skills vs. subagents.
