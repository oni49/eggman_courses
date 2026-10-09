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

## Session 4: Context Management

- *Verify:* Anthropic, Claude Code documentation, "Manage Claude's memory" (CLAUDE.md files) and the slash-commands reference (`/compact`, `/clear`). Confirms how project memory is loaded and how compaction is triggered.
- *Go deeper:* Anthropic Engineering blog, "Effective context engineering for AI agents," 2025. Covers the attention budget, compaction, structured note-taking, and tool-result clearing from a practitioner's view. *(Title and date from training knowledge, so verify before citing.)*
- *Go deeper:* Packer, C. et al. "MemGPT: Towards LLMs as Operating Systems." arXiv 2310.08560, 2023. Frames context management as virtual memory: paging between a small "main memory" window and external storage.

## Session 5: External Memory

- *Foundational:* Lewis, P. et al. "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." NeurIPS 2020 (arXiv 2005.11401). The paper that named RAG.
- *Go deeper:* Anthropic, "Introducing Contextual Retrieval," Anthropic blog/engineering post, September 2024. Directly addresses chunking damage by prepending chunk-specific context before embedding, and combines embeddings with keyword (BM25) search, which is hybrid search in practice.
- *Verify:* Greshake, K. et al. "Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection." ACM AISec 2023 (arXiv 2302.12173). The foundational demonstration that retrieved content can carry attacker instructions.

## Session 6: Claude's Memory Stack

- *Verify:* Anthropic, Claude Code documentation: "Manage Claude's memory" (CLAUDE.md hierarchy and imports), "Agent Skills," and "Subagents." Confirms load timing and the separate context window for subagents. The exact injection details change between releases, so check the current pages.
- *Go deeper:* Anthropic Engineering blog, "Equipping agents for the real world with Agent Skills," 2025. Explains progressive disclosure: metadata first, body on demand, supporting files as needed. *(Title and date from training knowledge, so verify.)*
- *Go deeper:* Anthropic Engineering blog, "How we built our multi-agent research system," 2025. Subagents in practice: separate contexts and condensed results, plus the token-cost tradeoffs.

## Session 7: Thinking in Tokens

- *Foundational:* Wei, J. et al. "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models." NeurIPS 2022 (arXiv 2201.11903). The paper that named chain-of-thought prompting.
- *Verify:* Kojima, T. et al. "Large Language Models are Zero-Shot Reasoners." NeurIPS 2022 (arXiv 2205.11916). The "Let's think step by step" result.
- *Go deeper:* Merrill, W. & Sabharwal, A. "The Expressive Power of Transformers with Chain of Thought." ICLR 2024 (arXiv 2310.07923). The theory behind "tokens are computation": intermediate steps provably extend what a fixed-depth transformer can compute.
