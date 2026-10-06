# LLM Memory, Context Management & Extended Reasoning: Course Map

Professor Eggman's seminar. Eleven sessions, about 10 minutes each.

**Companion docs**
- [Lessons](llm-memory-lessons.md): the lectures as delivered
- [Vocabulary](llm-memory-vocabulary.md): running glossary, grouped by session
- [References](llm-memory-references.md): sources, tiered (Foundational / Verify / Go deeper)
- [Diagram](llm-memory-diagram.md): Claude's memory, built up one layer per session (added at the student's request)

## Student calibration
BSc CS, 15 years out. Has read *The Illustrated Transformer*. Already holds these models: weights are frozen at runtime, the harness feeds "memory" back in each turn, context behaves like an append-only list where attention gets diluted, and chain of thought works like self-reprompting. Observed failure: rules and framing get forgotten in long sessions.

## Curriculum

| # | Session | Scaffold stage | Diagram layer | Status |
|---|---------|----------------|---------------|--------|
| 1 | **The Stateless Machine.** Why a model has no memory, and the autoregressive loop that fakes it | Problem space | Frozen weights + harness + resend loop | ☐ |
| 2 | **Anatomy of a Context Window.** Tokens, decoder-only transformers, attention, the KV cache | Vocabulary | Inside the window: token stream, KV cache | ☐ |
| 3 | **How Models Forget Without Deleting.** Attention dilution, lost-in-the-middle, positional limits, why your rules fade | Mechanisms | Attention "hot" and "cold" zones | ☐ |
| 4 | **Context Management.** System prompts, truncation, summarization/compaction, prompt caching | Mechanisms | System prompt, cache boundary, compaction | ☐ |
| 5 | **External Memory.** Embeddings, retrieval-augmented generation, memory files and tools | Application | Out-of-window stores and retrieval paths | ☐ |
| 6 | **Claude's Memory Stack.** When CLAUDE.md, skills, agent files and tool definitions enter context, and what is resent every turn | Application | Full Claude Code assembly (capstone) | ☐ |
| 7 | **Thinking in Tokens.** Chain of thought: why generated text *is* computation | Mechanisms | Thinking blocks in the stream | ☐ |
| 8 | **Trained Reasoners.** Reinforcement-learned reasoning, thinking budgets, interleaved thinking with tools | Frontiers | Thinking across tool calls, and what is dropped between turns | ☐ |
| 9 | **Long-Horizon Agents.** Subagents, notes, compaction loops | Application | Subagent contexts as separate windows | ☐ |
| 10 | **Watching the Weights.** Detecting silent model updates, and what "provably static" can and can't mean | Failure modes / verification | Version pin and fingerprint probe | ☐ |
| 11 | **Failure Modes & Open Problems.** Context rot, unfaithful reasoning, long context vs. retrieval | Failure modes | Annotated failure points | ☐ |
