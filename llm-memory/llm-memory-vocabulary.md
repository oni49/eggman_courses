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

## Session 3: How Models Forget Without Deleting

- **Attention budget:** The intuition that attention weights sum to 1, so every added token competes with existing ones for a share.
- **Attention dilution:** A rule or fact receiving a smaller share of attention as context grows, without being removed.
- **Distractor:** Context content that *resembles* the relevant information and therefore draws attention away from it. Similar text is far more harmful than unrelated text.
- **Lost in the middle:** The U-shaped recall curve. Information at the start and end of a long context is used best, and information in the middle worst.
- **Primacy / recency bias:** The tendency to weight content near the start (primacy) and near the end (recency) of the context more heavily.
- **Attention sink:** Early tokens that attention heads use as a default "parking spot," which helps give the start of the context outsized attention.
- **Advertised vs. effective context:** The maximum tokens a model *accepts* versus the length over which it *reliably uses* information. Effective is smaller.
- **Needle-in-a-haystack test:** A benchmark that plants one fact in a long context and asks for it back. It's easy compared with tasks that combine several scattered facts.
- **In-context learning:** The model's ability to pick up and continue patterns shown in its context, without any weight change.
- **Drift:** Gradual departure from an instruction. It becomes self-reinforcing when uncorrected outputs remain in the transcript as examples.
- **Reminder injection:** A harness restating key rules late in the context, near the generation point, to exploit recency.

## Session 4: Context Management

- **Context engineering:** Deciding, on every call, what goes into the window. Treats context as a scarce budget.
- **Pinned region:** Content the harness never trims (tools, system prompt), kept at the stable, cached start of the window.
- **Managed region:** The middle of the context (conversation history, tool output) where trimming strategies apply.
- **Truncation / sliding window:** Dropping the oldest turns past a limit. It's cheap and doesn't care what it drops.
- **Summarization / compaction:** Replacing old history with a model-written summary. Lossy, and a model decides what counted as detail.
- **Auto-compact:** Claude Code automatically compacting the conversation as the window approaches its limit.
- **`/compact` vs. `/clear`:** Claude Code commands. `/compact` summarizes history (lossy), and `/clear` discards it entirely (full truncation).
- **Tool-result pruning:** Replacing bulky, stale tool output with a short stub that records the action happened. It's recoverable by re-running the tool.
- **Offloading:** Writing state (decisions, progress, to-dos) to external storage and reading it back on demand.
- **Cache tax:** The cache miss that every history edit causes from that point onward. Do edits in rare, large batches.
- **Durable vs. conversational state:** Information that must survive compaction (put it in pinned or external memory) versus information that can fade with the chat.
- **Staleness:** Old context that no longer matches reality, such as a file read from before the file was edited. It's worse than no context.
