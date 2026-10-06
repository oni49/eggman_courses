# LLM Automation: The Lessons
*Professor Eggman's Seminar — core lectures only*

The taught lessons from the LLM Automation course, each as delivered: core explanation, "So what?", and check-in question. Only Session 1 has been delivered so far.

---

## Session 1 — The Automation Problem

Everything from your last two courses adds up to one inconvenient fact: a raw LLM automates **nothing**. Not *won't* — *can't*.

Think of an inference call the way you'd think of a **pure function**. Same input, same output distribution. No memory between calls, no side effects. It reads a block of text, emits a block of text, and forgets it ever happened. Beautiful, and utterly inert. A pure function *computes*; it does not *do*.

To automate, you engineer the impure system *around* that pure function. And a raw model has exactly three limits you must build past — all three of which you already know under other names.

**Limit one: it's sealed in the window.** It knows only what's on the whiteboard in front of it. It cannot reach out and pull in a document, a database row, today's date. If the fact isn't in the context, it doesn't exist. → The model needs **eyes**. That's retrieval. That's RAG.

**Limit two: it only produces text.** Generation is its one and only operation. It cannot run a query, send an email, hit an API, move a file. It can produce a beautifully-worded *description* of sending an email — which sends precisely zero emails. Text is inert. → The model needs **hands**. That's tools: function-calling, and later MCP.

**Limit three: it runs once and stops.** One forward pass, one output, done. No way to do X, inspect the result, then decide to do Y. No iteration, no persistence, no "keep going until finished." → The model needs a **loop**. That's the harness.

Eyes, hands, a loop. Retrieval, tools, orchestration. RAG, MCP, harness.

So here's the reframe I set you up for last turn: these aren't three gadgets. They're three prosthetics bolted onto the same brilliant brain-in-a-jar — three answers to the single fact that the model is a *stateless text-predictor that can only think, never act, and only once*. The model is going to turn out to be the *least* interesting part of the system you're about to build.

**So what?** This is why "just use ChatGPT to automate my job" quietly disappoints everyone. The model is *one component* — the reasoning core. The reliability, the value, every dollar of real engineering lives in the scaffolding: what you retrieve, which tools you expose, how tightly you govern the loop. And the kicker you'll spend Session 7 on — every prosthetic that lets the model reach *out* is a channel for something to reach *in*. You gave it eyes: now it can read a poisoned document. You gave it hands: now an injection can *act*. Capability and vulnerability are the same surface, viewed from two chairs. You already know that in theory. Now you'll watch it get built.

**Check-in.** Two parts. First, the easy half: map each of your three terms onto one of my three limits — eyes, hands, loop. Then the real one: of those three prosthetics, which single one turns a prompt-injection from an *embarrassing sentence* into an actual *incident* — and why that one specifically?
