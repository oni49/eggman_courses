# LLM Memory, Context Management & Extended Reasoning: References

Tiers: **Foundational** (landmark works), **Verify** (check a claim from a session), **Go deeper** (self-study).
Caveat: sources are named from the instructor's training knowledge. Verify URLs and editions before relying on them.

## Session 1: The Stateless Machine

- *Foundational:* Vaswani, A. et al. "Attention Is All You Need." NeurIPS 2017. The original transformer paper, with the encoder-decoder design shown in *The Illustrated Transformer*.
- *Verify:* Anthropic, Claude API documentation, Messages API ("Working with the Messages API"). Confirms the API is stateless and that the client sends the full conversation history on each request. Doc paths have moved over time, so search the Anthropic docs site for "Messages API."
- *Go deeper:* Jay Alammar, "The Illustrated GPT-2 (Visualizing Transformer Language Models)," 2019. https://jalammar.github.io/illustrated-gpt2/. The decoder-only sequel to the article you've already read, which shows the autoregressive loop step by step.

## Session 2: Anatomy of a Context Window

- *Foundational:* Radford, A. et al. "Improving Language Understanding by Generative Pre-Training." OpenAI technical report, 2018 (GPT-1). The paper that established the decoder-only, predict-the-next-token recipe that modern chat models descend from.
- *Verify:* Anthropic, Claude API documentation, "Prompt caching." Confirms prefix-based caching, the fixed prefix order (tools → system → messages), and that a change invalidates the cache from that point onward.
- *Go deeper:* Andrej Karpathy, "Let's build GPT: from scratch, in code, spelled out." YouTube, 2023. Two hours of building a decoder-only transformer by hand, including causal masking. The best way to make query, key, and value concrete.

## Session 3: How Models Forget Without Deleting

- *Verify:* Liu, N. F. et al. "Lost in the Middle: How Language Models Use Long Contexts." *Transactions of the ACL*, 2024 (arXiv 2307.03172). The source of the U-shaped recall curve.
- *Go deeper:* Hsieh, C.-P. et al. "RULER: What's the Real Context Size of Your Long-Context Language Models?" COLM 2024 (arXiv 2404.06654). Shows effective context falling well short of advertised context once tasks go beyond single-needle retrieval.
- *Go deeper:* Xiao, G. et al. "Efficient Streaming Language Models with Attention Sinks." ICLR 2024 (arXiv 2309.17453). Introduces attention sinks, the reason the first tokens soak up disproportionate attention.
