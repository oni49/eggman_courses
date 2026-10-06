# LLM Memory, Context Management & Extended Reasoning: Vocabulary

Running glossary, grouped by session. See the [course map](llm-memory-course-map.md).

## Session 1: The Stateless Machine

- **Weights (parameters):** The numbers learned during training that define the model. They are frozen at inference time.
- **Inference / forward pass:** One run of the model: tokens in, a probability distribution over the next token out.
- **Token:** The unit of text a model reads and writes, typically a word fragment.
- **Vocabulary:** The fixed set of all tokens a model can emit, typically tens of thousands to a couple hundred thousand.
- **Sampling:** Choosing the next token from the model's probability distribution.
- **Temperature:** A sampling setting. Low values pick likely tokens, and high values pick more adventurous ones.
- **Autoregressive generation:** Producing output one token at a time, with each new token appended to the input for the next step.
- **Stop token:** A special token that ends generation.
- **Harness:** The software around the model that builds the input, calls the model, runs tools, and manages the transcript.
- **Transcript:** The full document (system prompt, messages, tool results) that the harness resends on every call.
- **Context window:** The maximum number of tokens a single call can accept.
- **Parametric memory:** Knowledge stored in the weights. It is vast, fuzzy, and frozen after training.
- **Contextual memory:** Knowledge present in the current token sequence. It is exact, temporary, and limited by the window.
- **External memory:** Stores outside the model, such as files, databases, and summaries, that the harness or model brings into context.
- **Push retrieval:** The harness injects content into context before the model runs, and the model has no say.
- **Pull retrieval:** The model calls a tool to fetch content during a turn, because it chose to.
- **Fine-tuning:** Further training of existing weights on new data. It produces a *new* checkpoint and does not mutate the running model.

## Session 2: Anatomy of a Context Window

- **Encoder-decoder transformer:** The original 2017 design, in which an encoder reads the input and a separate decoder writes the output.
- **Decoder-only transformer:** The modern chat-model design: one stack of identical blocks that both reads the prompt and generates the reply.
- **Subword tokenization:** Splitting text into frequent chunks rather than whole words. In English this averages about four characters, or three-quarters of a word, per token.
- **Embedding:** The vector (a list of a few thousand numbers) that a token is converted into before entering the stack.
- **Positional encoding:** Information mixed into each token's vector so the model knows *where* it sits in the sequence.
- **Transformer block:** One layer of the stack, made of an attention step followed by a feed-forward step.
- **Feed-forward layer:** The per-token transformation in each block. Much parametric knowledge appears to live here.
- **Attention:** The mechanism by which each token gathers a relevance-weighted blend of information from other tokens.
- **Query / Key / Value:** The three vectors each token produces per block: "what I'm looking for," "what I contain," and "what I hand over."
- **Softmax:** The normalization that turns attention scores into weights summing to 1. This is the root of attention dilution.
- **Multi-head attention:** Many attention lookups running in parallel, each specializing in a different kind of relationship.
- **Causal masking:** The rule that a token may attend only to earlier tokens, never later ones.
- **KV cache:** The stored keys and values of already-processed tokens. It is memoized working memory in GPU memory, grows linearly with context, and is normally discarded after the call.
- **Prompt caching:** A provider feature that keeps a prompt prefix's KV cache *between* calls, so it isn't recomputed or fully billed again.
- **Prefix property:** Any token's cached computation stays valid only if every token before it is unchanged. Edits invalidate everything downstream.
- **Cache breakpoint:** A boundary the client marks for prompt caching. Reuse happens at breakpoint granularity, up to the last unchanged breakpoint.
