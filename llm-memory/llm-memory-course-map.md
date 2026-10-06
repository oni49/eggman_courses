# LLM Memory, Context Management & Extended Reasoning: Course Map

Professor Eggman's seminar. Nine sessions, about 10 minutes each.

**Companion docs**
- [Lessons](llm-memory-lessons.md): the lectures as delivered
- [Vocabulary](llm-memory-vocabulary.md): running glossary, grouped by session
- [References](llm-memory-references.md): sources, tiered (Foundational / Verify / Go deeper)

## Student calibration
BSc CS, 15 years out. Has read *The Illustrated Transformer*. Already holds these models: weights are frozen at runtime, the harness feeds "memory" back in each turn, context behaves like an append-only list where attention gets diluted, and chain of thought works like self-reprompting. Observed failure: rules and framing get forgotten in long sessions.

## Curriculum

| # | Session | Scaffold stage | Status |
|---|---------|----------------|--------|
| 1 | **The Stateless Machine.** Why a model has no memory, and the autoregressive loop that fakes it | Problem space | ☐ |
| 2 | **Anatomy of a Context Window.** Tokens, decoder-only transformers, attention, the KV cache | Vocabulary | ☐ |
| 3 | **How Models Forget Without Deleting.** Attention dilution, lost-in-the-middle, positional limits, why your rules fade | Mechanisms | ☐ |
| 4 | **Context Management.** System prompts, truncation, summarization/compaction, prompt caching | Mechanisms | ☐ |
| 5 | **External Memory.** Embeddings, retrieval-augmented generation, memory files and tools | Application | ☐ |
| 6 | **Thinking in Tokens.** Chain of thought: why generated text *is* computation | Mechanisms | ☐ |
| 7 | **Trained Reasoners.** Reinforcement-learned reasoning, thinking budgets, interleaved thinking with tools | Frontiers | ☐ |
| 8 | **Long-Horizon Agents.** Memory, context and reasoning combined: subagents, notes, compaction loops | Application | ☐ |
| 9 | **Failure Modes & Open Problems.** Context rot, unfaithful reasoning, long context vs. retrieval | Failure modes | ☐ |
