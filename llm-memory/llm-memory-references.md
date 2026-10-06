# LLM Memory, Context Management & Extended Reasoning: References

Tiers: **Foundational** (landmark works), **Verify** (check a claim from a session), **Go deeper** (self-study).
Caveat: sources are named from the instructor's training knowledge. Verify URLs and editions before relying on them.

## Session 1: The Stateless Machine

- *Foundational:* Vaswani, A. et al. "Attention Is All You Need." NeurIPS 2017. The original transformer paper, with the encoder-decoder design shown in *The Illustrated Transformer*.
- *Verify:* Anthropic, Claude API documentation, Messages API ("Working with the Messages API"). Confirms the API is stateless and that the client sends the full conversation history on each request. Doc paths have moved over time, so search the Anthropic docs site for "Messages API."
- *Go deeper:* Jay Alammar, "The Illustrated GPT-2 (Visualizing Transformer Language Models)," 2019. https://jalammar.github.io/illustrated-gpt2/. The decoder-only sequel to the article you've already read, which shows the autoregressive loop step by step.
