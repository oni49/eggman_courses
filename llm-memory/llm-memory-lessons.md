# LLM Memory, Context Management & Extended Reasoning: Lessons

The lectures as delivered. See the [course map](llm-memory-course-map.md).

---

# Session 1: The Stateless Machine

Let's start with the most important fact in this course, and the most often forgotten: **the model remembers nothing.** It doesn't remember a little. It remembers nothing at all.

**The model is a pure function.**

Take away the chat interface, the branding, and the friendly tone, and what's left looks like this:

```
next_token_probabilities = f(weights, tokens_so_far)
```

You give it a sequence of tokens. It returns a probability for every possible next token in its vocabulary, typically tens of thousands to a couple hundred thousand of them. That's all one call does. It doesn't produce sentences or answers. It produces one probability distribution.

The weights are fixed, as you said. Given the same inputs, the function gives the same distribution. *(Simplification flag: in practice, hardware introduces small numerical noise. That matters a lot in Session 10, when we try to catch weights changing.)*

**Generation is a loop around that function.**

To get a sentence, the surrounding code does this:

1. Call `f` and get the distribution.
2. **Sample** one token from it. The *temperature* setting controls how adventurous that choice is.
3. Append the token to the sequence.
4. Repeat until a stop token appears.

This is called **autoregressive generation**: each output is fed back in as input. A 500-word reply takes about 700 trips around this loop. The model never plans the whole paragraph in one step. It commits to one token at a time, and the paragraph emerges.

Keep that in mind. It's why chain of thought works, and we'll come back to it in Session 6.

**The conversation is a document the client rebuilds.**

So where does "the conversation" live? On your side of the wire.

Your API experiments were correct. Every turn, the harness, meaning the code around the model, sends the *entire transcript* again: system prompt, every message, every tool result. The model reads it as one fresh document. It's like a stateless HTTP server where the client resends the full session in every request, because the server has no session table.

This is where I'd adjust your "queue" picture. A queue suggests the model holds the structure. It doesn't. The client builds the array and the model reads a snapshot. Nothing gets popped out unless the harness removes it.

It also means the model can't tell whether an earlier "assistant" turn was really written by it. Those turns are text with a label. Edit one through the API and the model will carry on as though it said it. That's a strange property, and it's useful once you know about it.

**Two kinds of memory, and soon a third.**

That leaves two places knowledge can live:

- **Parametric memory** is in the weights. It's learned during training and frozen afterward. It's vast but fuzzy, a lossy compression of the training data. Think of it as ROM with an attitude.
- **Contextual memory** is in the token sequence. It's exact, temporary, and limited by the **context window**, the maximum number of tokens one call can accept.

Everything people call "memory" in products, such as ChatGPT remembering your dog's name or Claude reading your project notes, is the harness choosing what to put into the context. That's the third kind, external memory, and we'll get to it in Session 5. The weights stay out of it.

## So what?

Every memory bug you've seen is really a question about what the harness sent. If a model "forgot" a rule, either the rule wasn't in the context, or it was there and lost out to everything else competing for attention. Those two failures have completely different fixes. When you debug these systems, the first question is always the same: *what exactly was in the window when it made that choice?* Once you think of the model as a stateless function with a smart client, half of the magic turns out to be plumbing, and plumbing can be inspected.

## Check-in question

Suppose you're using the API directly. In turn 3 you tell the model *"My server is called Barnaby."* In turn 10 you ask *"What's my server called?"* and it answers correctly.

1. At the moment it answers in turn 10, **where physically is "Barnaby,"** and how did it get there?
2. Tomorrow you open a brand-new conversation and ask the same question. Name **two different mechanisms** that could make it answer correctly. For each, say whether the **weights** were involved.

---

# Session 2: Anatomy of a Context Window

**Recap:** Last time we established that the model is a stateless function, and that the harness resends the entire transcript on every call. **Today** we look inside that function: what a "context window" physically is while the model is running.

**First, the correction I promised.** The Illustrated Transformer describes the 2017 design: an encoder reads the input, and a decoder writes the output. Claude, GPT, and nearly every modern chat model drop the encoder. They're **decoder-only**: one stack of identical blocks, used for both reading and writing. So forget the two-towers picture. What you have is a single tall stack that the token sequence flows up through.

**Tokens become vectors.** Text is split into **tokens**, which are subword chunks. In English a token averages roughly four characters, or three-quarters of a word. Each token is looked up in an embedding table and becomes a vector: a list of a few thousand numbers. The model also mixes in information about *position*, so it can tell "dog bites man" from "man bites dog." *(How position is encoded matters for forgetting, so it comes back in Session 3.)*

That column of vectors, one per token, rises through dozens of blocks. Each block does two things:

1. **Attention:** each token gathers information from other tokens.
2. **Feed-forward:** each token is transformed on its own. This is where much of the parametric knowledge from Session 1 seems to live. *(A simplification, and still an active research question.)*

**Attention is a soft database lookup.** At every block, each token produces three vectors:
- a **query**: "what am I looking for?"
- a **key**: "what do I contain?"
- a **value**: "what do I hand over if you pick me?"

Each token's query is compared against the keys of the other tokens. The scores are normalized into weights that sum to 1, and the token receives a weighted blend of their values. Think of it as a hash map that returns *everything at once*, weighted by relevance, instead of one exact match. **Multi-head** attention runs many of these lookups in parallel, each learning to look for something different: syntax, coreference, "the instruction I'm following," and so on.

That "weights sum to 1" detail matters. It's the mechanism behind the dilution hunch you mentioned in our first conversation.

**Causal masking makes it one-way.** In a decoder, a token may attend only to tokens *before* it, never after. Token 50 cannot see token 80. Your instinct that it's "a one-way, forward operation" was correct. This is the rule that enforces it.

**The KV cache.** Causal masking has a useful consequence. Token 50's keys and values depend *only on tokens 1 through 50*. Appending token 51 can't change them. So the server computes them once and stores them in GPU memory. That's the **key-value (KV) cache**. It's memoization, applied to attention.

When generating, each new token computes only *its own* query, key, and value, then attends over the cache. Processing your prompt once is expensive, because every token attends to every earlier one and the cost grows quadratically with length. After that, each output token is comparatively cheap.

The KV cache is the closest thing the model has to working memory. It lives in GPU memory, it grows linearly with context, it's private to your request, and it's normally **discarded when the call ends**. The context window limit is partly a training limit and partly the physical cost of this cache.

## So what?

The context is effectively an **append-only log with a prefix-keyed cache**. Appending is cheap. Editing anything early invalidates every cached entry after it, like changing the first line of a file that a build system has hashed. This drives real design decisions. Harnesses put stable material (system prompt, tool definitions, project instructions) *first* and volatile material *last*. Providers sell **prompt caching**, which keeps that prefix's KV cache between calls so you don't pay to recompute it. Long contexts cost money and latency in proportion to their size, which is why context management (Session 4) is an engineering discipline and not a matter of tidiness.

## Check-in question

On turn 20 of a long conversation, your harness has two choices:

- **(A)** fix a one-word typo in the system prompt at the very *start* of the transcript, or
- **(B)** append a one-line note to the very *end*.

Using **causal masking** and **what each token's keys and values depend on**, explain which change forces more recomputation on the next call, and *why*. Then: if prompt caching were in use, what happens to the cached prefix in each case?
