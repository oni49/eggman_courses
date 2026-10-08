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

---

# Session 3: How Models Forget Without Deleting

**Recap:** The context is an append-only log. The model reads all of it on every call, with cached keys and values making that cheap. **Today:** if nothing is ever deleted, why did your rules fade?

Here's the paradox. In a 150-turn conversation, your rule from turn 1 is *still in the window*, byte for byte. The model can see it. It just stops *acting* on it. There are four separate mechanisms behind that, and they add up.

**1. The attention budget.**
Remember: attention weights in each head sum to 1. Every token added is another candidate competing for a share. A rule that took 5% of a head's attention in a short conversation might get 0.2% in a long one. Nothing was removed. It's just quieter.

What hurts most isn't *more* text. It's *similar* text. Fifty paragraphs that look a bit like your rule pull attention away much more than fifty paragraphs about something unrelated. Engineers call these **distractors**.

*(Simplification flag: some heads stay very sharp at long range, so this isn't a strict zero-sum budget. It's still the right intuition.)*

**2. Position matters: lost in the middle.**
Models don't attend evenly across the window. Researchers who planted a fact at different depths in long contexts found a U-shaped curve: recall is best near the **start** and the **end**, and worst in the **middle**. The end benefits from recency. The start benefits from training habits and from **attention sinks**, early tokens that heads use as a default parking spot. Your rule, mentioned in passing in turn 40, sits in the trough.

**3. Advertised versus effective context.**
A model may *accept* a million tokens, but it was trained mostly on much shorter sequences, and the positional encoding becomes less reliable at distances it rarely saw in training. "Needle in a haystack" tests, where you retrieve one planted sentence, look impressive. Tasks that require *combining* several facts spread across a long context degrade much earlier. The usable window is smaller than the one on the box.

**4. The transcript teaches by example.** This is the subtle one, and it's probably the real culprit in your experience.
Models are extraordinarily good at **in-context learning**: they continue whatever pattern the document shows. Your rule is one abstract instruction. The transcript is 150 *concrete examples* of how this conversation goes. If a few replies drifted from the rule and nobody objected, those replies now count as evidence of "how we do things here." Each drift makes the next one more likely. The conversation becomes its own prompt, and when an instruction conflicts with examples, the examples usually win.

**And yes, sometimes it really was deleted.** Harnesses do trim or summarize old turns when they run out of room. That's genuine forgetting, and it's Session 4's subject. Always check that first, because the fix is completely different.

## So what?

This gives you a debugging checklist and a set of countermeasures. **Placement:** put critical rules at the start *and* restate them near the end. That's why harnesses like Claude Code inject short reminders late in the context. **Hygiene:** fewer distractors, and less stale tool output. **Correct drift immediately**, before bad examples pile up. **Start fresh** when the history has become mostly noise. "The model forgot" is rarely one bug. Usually it's dilution, position, and self-reinforcing examples together.

## Check-in question

Your system prompt says *"Always use British spelling."* By turn 150 the model writes "color" and "optimize." The transcript contains:
- a 20,000-token pasted American technical document around turn 70
- about a dozen earlier replies that already slipped into American spelling, with no correction

**Identify which of the four mechanisms are at work, and point to the specific evidence for each.** Then propose **two fixes that target *different* mechanisms**, and say which mechanism each one addresses.

---

# Session 4: Context Management

**Recap:** Rules fade without being deleted, through dilution, position effects, and the transcript teaching by example. **Today:** the harness starts deleting things *deliberately*, and we look at how to do that without damaging the conversation.

**The harness is a budget allocator.** On every call it has to answer one question: *of everything this conversation has ever produced, what goes into the window this time?* The field has started calling this **context engineering**. That's a grand name for an old problem. You have a scarce, expensive resource, and every token in it takes attention away from every other token.

**The layout comes first.** The previous sessions give the standard shape:

```
[ tools + system prompt ]  [ conversation history ]  [ latest turn + reminders ]
   stable, pinned, cached        managed region            volatile, hot
```

The stable prefix is **pinned**, meaning it's never trimmed, and it's cached. Everything interesting happens in the middle.

**Four strategies for the middle, from bluntest to cleverest:**

**1. Truncation (sliding window).** Drop the oldest turns once you hit a limit. It's cheap and predictable, and it doesn't care what it throws away. The decision from turn 12 that explained *why* you chose Postgres goes off the edge just like small talk does. The usual safeguard is that only the conversation slides. The system prompt stays pinned.

**2. Summarization (compaction).** Ask a model to rewrite the old history as a short summary, then replace the history with that summary. Claude Code does this automatically as the window fills, and on demand with `/compact`. It keeps the *gist* and discards *detail*, and **a model decides what counted as detail**. It's a JPEG of your conversation: fine at a glance, and full of artifacts when you zoom in. If Claude seems to forget a specific detail after compaction, it probably isn't attention at all. The detail just didn't make it into the summary.

**3. Pruning tool results.** In agent work, most of the bulk isn't conversation. It's *tool output*: file contents, logs, search results. A 3,000-line file read 40 turns ago is pure distractor now. Pruning replaces it with a stub like "[read config.py: 3,000 lines]" and keeps the *fact* that the read happened. If needed, the model can read the file again. This is lossless in a useful sense: the information still exists outside the window, and you can get it back.

**4. Offloading.** Have the model write important state, such as decisions, progress, and to-do lists, into files *outside* the window, and read them back when needed. That turns contextual memory into external memory, which is Session 5's territory.

**The cache tax.** Every one of these strategies *edits the history*. By Session 2's prefix rule, every edit is a cache miss from that point onward. So well-built harnesses don't trim a little every turn. They let context accumulate, cache-friendly, and then compact in one large step when a threshold is reached. One cache miss on a much smaller context, then cheap appends again. It's the same tradeoff as log compaction in a database: let the log grow, then occasionally merge it into a compact form.

## So what?

Decide what's **durable** and what's **conversational**. In Claude Code, a decision you typed in chat lasts only as long as it survives compaction. The same decision written in **CLAUDE.md** is loaded fresh into the pinned region, so it survives. More on exactly how in Session 6. And `/clear` versus `/compact` isn't a matter of taste. One is truncation of everything, the other is lossy summarization, and you should choose deliberately.

## Check-in question

A Claude Code session is at **180K tokens** of a **200K** window. The context contains:
- the system prompt and tool definitions (about 15K)
- in turn 12, your decision: *"Use Postgres, not MySQL, because we need JSONB."*
- **60 file-read results** totalling about 120K tokens
- the **last 10 turns** of active debugging (about 30K)

**Design a policy:** which strategy (truncate, summarize, prune, offload, or leave alone) do you apply to *each* of the four regions, and what's the specific risk of each choice? Then: **where does the cache miss happen**, and why does it make sense to do all of this in **one step** rather than a little each turn?
