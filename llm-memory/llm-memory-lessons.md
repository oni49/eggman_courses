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
