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
