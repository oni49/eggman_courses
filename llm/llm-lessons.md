# LLM & GPT — The Lessons
*Professor Eggman's Graduate Seminar — core lectures only*

The taught lessons, extracted as delivered: core explanation, "So what?", and check-in question. Side-threads, answer discussions, recaps, teasers, further-reading, and banter are excluded. See the end of the document for what was cut and why.

---

## Session 1 — Why Language Modelling Is Hard

*Delivered across two teaching cycles. Both are reproduced as taught.*

### Core explanation (part 1)

What you're describing has a name: the gap between **syntax** and **semantics**. Syntax is the structure — the rules, the grammar, the order of words. Semantics is the *meaning*. And your instinct that they're separable? That's the whole problem in a nutshell.

But let me sharpen it, because you actually identified *three* things without realising it:

**1. Words are ambiguous in isolation.**
"Bank" means a financial institution. It also means the side of a river. It also means to tilt an aircraft. Same four letters, completely different meanings. A machine reading a single word has no idea which one you mean.

**2. Meaning is compositional — but not in a simple maths way.**
"Dog bites man" and "man bites dog" use identical words. Totally different meanings. You can't just bag up the words and add them together. Order matters. Relationship matters. And here's the kicker — even knowing the order isn't enough. "I saw the man with the telescope" — did *I* have the telescope, or did the *man*? Grammatically, both are valid.

**3. You need world knowledge to resolve the ambiguity.**
Read this: *"The trophy didn't fit in the suitcase because it was too big."* What does "it" refer to? You instantly said the trophy, right? But the sentence never tells you that. You resolved it by knowing that trophies are generally bigger than suitcases. That's *physics*, not grammar. The machine has to somehow know that too.

Here's your security analogy, because I know how your brain works: think about how a WAF does pattern matching. It looks for `SELECT` and `DROP` and flags them. But a clever attacker encodes the input, fragments it, obfuscates it — and the WAF's literal parser misses the intent entirely. That gap between *what was written* and *what was meant*? That's the exact same gap machines face with every sentence in natural language. Every. Single. Sentence.

### So what? (part 1)

This is why you can't just build a lookup table or a rules engine and call it language understanding. The problem isn't vocabulary — it's that meaning is *context-dependent, relational, and requires inference*. Any system that wants to handle language has to somehow encode all of that.

### Check-in question (part 1)

Given everything above — if I asked you to design even a *crude* system that could disambiguate the word "bank" in a sentence, what information would that system need access to?

### Core explanation (part 2)

POS tagging is the verb/noun/adjective classification step. Computers got reasonably good at this with rules and statistics decades ago. That part is relatively solved.

The interesting bit is your second instinct — *look at what's around it.* That's actually a foundational idea in linguistics, credited to a linguist named Firth in 1957:

> *"A word is known by the company it keeps."*

In other words — meaning lives in context. Not in the word itself.

**But here's where it gets hard.** How much context do you look at?

"I went to the bank" — you need more context.
"I went to the bank to deposit my paycheck" — now you know.
"I went to the bank by the river to deposit my paycheck" — now your system has to work *harder* because there are two competing signals in the same sentence.

And what about this:

*"After the flood last year, I finally switched to a credit union."*

The word "bank" doesn't even appear. But you know exactly what I'm talking about. The context that resolves meaning isn't always in the same sentence — sometimes it's paragraphs earlier. Sometimes it's in your head based on shared knowledge of the world.

So the question becomes: **how do you encode "surrounding context" in a way a machine can actually compute?**

Early systems tried a simple approach — just count which words appear near each other frequently. If "bank" appears near "river," "flood," and "water" a lot — it's probably the geographical kind. If it appears near "loan," "mortgage," and "interest" — it's financial. This is called a **Bag of Words (BoW)** model.

It worked. Kind of. For a while. Until it didn't — because counting co-occurrences doesn't capture *relationships*, just *proximity*. "Not happy" and "happy" would look similar to a BoW model because they share the same neighbourhood of words. That's a serious problem.

### So what? (part 2)

Every approach to language understanding, from ancient rule-based systems to modern AI, is fundamentally trying to answer the question you just posed: *what context do I need, and how do I represent it mathematically?* How you answer that question determines the entire architecture of your system.

### Check-in question (part 2)

Bag of Words counts word co-occurrences to infer meaning. Given your security background — where do you think that approach breaks down most catastrophically? I can think of at least two failure modes you'd recognise immediately.

---

## Session 2 — Tokens, Embeddings, and Vector Spaces

### Core explanation

BoW treats language like a bag of scrabble tiles. Shake it up, the model doesn't care. We need something better — something that preserves *relationships*, *order*, and *degree of similarity* between concepts.

Enter **embeddings**.

Here's the core idea. Instead of counting words, what if we represented every word as a *point in mathematical space*? Not a random point — a point whose *position* is determined by the company that word keeps across millions of documents.

Words that appear in similar contexts end up *close together* in that space. "Dog" and "cat" end up near each other. "King" and "queen" end up near each other. "Happy" and "joyful" end up near each other — but crucially, "happy" and "not happy" end up *far apart*, because they appear in very different contexts.

And here's the part that makes people's brains melt a little:

Because positions in that space are just *vectors* — lists of numbers — you can do *arithmetic* on meaning.

King − Man + Woman = Queen

That's not a metaphor. That's a literal vector calculation that works. We'll dig into exactly why next session.

### So what?

Embeddings are the foundational leap that made modern AI language systems possible. Every LLM — Large Language Model — you've ever interacted with has embeddings at its core. Understanding them means understanding the atomic unit of how machines represent meaning.

### Check-in question

If words get their position in vector space from the contexts they appear in — what do you think happens to a word like "set," which has over 400 distinct meanings in English? Does one point in space capture all of them? What problem does that create?

---

## Session 3 — The Transformer Architecture

### Core explanation

Here's the situation we're in. We have words as vectors. We have a sentence as a matrix — one row per word. We need a mechanism that lets every word ask: *of all the other words in this sentence, which ones are most relevant to understanding me right now?*

You already said how: dot products. High dot product = high relevance. Let's build on that.

The attention mechanism gives each word three things:

```
Query  — "what am I looking for?"
Key    — "what do I contain?"
Value  — "what do I contribute if selected?"
```

Think of it like a search engine. The Query is your search term. The Keys are the index entries. The Values are the actual documents returned. You measure similarity between Query and every Key using dot products, which gives you a relevance score for each word. Then you use those scores to take a weighted blend of the Values.

For "The animal didn't cross the street because **it** was too tired" —

"it" fires its Query out across the sentence. It measures its dot product against the Key of every other word. "Animal" comes back with a high score. "Street" comes back lower. The model takes a weighted blend of Values, dominated by "animal," and that becomes the contextual vector for "it" in this sentence.

That's **self-attention**. Every word attending to every other word. Simultaneously. Not left to right. Not sequentially. All at once.

Now here's the part that makes it powerful. You don't run attention once. You run it in **parallel heads** — multiple attention mechanisms simultaneously, each learning to look for different relationship types. One head might specialise in grammatical relationships. Another in coreference — the "it" problem. Another in semantic similarity.

```
Head 1: who does "it" refer to?
Head 2: what's the grammatical subject?
Head 3: what's semantically related to "tired"?
```

Each head produces its own output. You concatenate them. That's **Multi-Head Attention (MHA)** — the core engine of the Transformer.

Wrap that in a few engineering necessities — normalisation layers to keep numbers stable, feed-forward layers to process the attended output, residual connections so gradients flow cleanly during training — and you have one **Transformer block**.

Stack twelve of those blocks. Or 96. Or more. That's a Transformer.

### So what?

The Transformer replaced every previous architecture because attention is *parallelisable*. Previous approaches read sentences sequentially — word by word — which was slow and lost long-range context. The Transformer reads the whole sentence at once, which means you can throw enormous computing power at it and scale it to billions of parameters. That scalability is why LLMs exist at all.

### Check-in question

Multi-Head Attention runs several attention mechanisms in parallel, each specialising in different relationship types. Given your security background — what does that parallel specialisation remind you of architecturally? And what's the potential failure mode if one head learns something the others don't contradict?

---

## Session 4 — Pre-training: Objectives, Data, and Scale

### Core explanation

Here's the thing nobody tells you up front. Everything we've built so far is just *structure*. Empty scaffolding. A freshly initialised Transformer has random numbers in every weight. It knows nothing. Show it "The cat sat on the" and it'll predict "purple" with the same confidence as "mat." The architecture is necessary but it's an empty vessel.

Pre-training is how you fill it.

And the objective is almost insultingly simple. Predict the next token.

```
Input:  "The cat sat on the ___"
Target: "mat"
```

That's it. That's the whole task. Show the model a sequence, hide what comes next, make it guess, measure how wrong it was, nudge the weights to be slightly less wrong. Repeat across trillions of tokens.

The technical name is **self-supervised learning**. "Supervised" because there's a correct answer to check against. "Self" because nobody had to label anything — the correct answer is just *the next word that was already there in the text*. The internet labels itself. You scrape a trillion words and every single one is a free training example, because each word is the "answer" to predicting it from everything before it.

This is the unlock. Earlier machine learning needed humans to hand-label data — this image is a cat, this review is positive. Expensive, slow, doesn't scale. Next-token prediction needs zero human labelling. Which is *why* models could suddenly be trained on the entire internet.

Now — here's the part that genuinely surprised researchers.

You'd think "predict the next word" produces a glorified autocomplete. It doesn't. To predict the next word *well*, across billions of examples, the model is forced to learn things it was never explicitly taught.

To finish "The capital of France is ___" it has to learn geography. To finish "2 + 2 = ___" it has to learn arithmetic. To finish "She was furious because he ___" it has to model human emotion and causality. To finish a line of code correctly, it has to learn syntax and logic.

The model learns all of this *as a side effect* of getting better at the next-token game. The task is simple. The competence required to do it well is staggering. This is the central, almost philosophical surprise of LLMs — **compression of the internet into next-token prediction produces something that looks a lot like reasoning.**

### So what?

Pre-training is the single most expensive, most important phase — it's where essentially all the model's knowledge comes from. Everything that happens afterward (the alignment and fine-tuning we'll cover next) only *shapes and steers* knowledge that already exists. You cannot fine-tune in facts that pre-training didn't capture. If the base model never saw it, no amount of polish puts it there.

### Check-in question

The pre-training objective is "predict the next token," and I claimed the model learns reasoning as a *side effect* of getting good at it. Given your security instincts — what's a category of knowledge or behaviour you'd expect this objective to capture *badly*, or fail to capture at all? Where's the blind spot baked into the objective itself?

---

## Session 5 — Fine-tuning, RLHF, and Alignment

### Core explanation

Here's the uncomfortable truth about a freshly pre-trained model. It is *not* an assistant. It's an autocomplete engine that has read the entire internet. Ask it "What's the capital of France?" and a base model might reply:

```
What's the capital of France? What's the capital of
Germany? What's the capital of Spain? Write your
answers below.
```

It's not being unhelpful. It's doing its job *perfectly* — it predicted the most statistically likely continuation, which on the internet is often *more questions on a worksheet*, not an answer. The base model has knowledge but no notion of being *helpful*. It mimics the text it saw. And the internet is full of text that is unhelpful, false, toxic, or formatted as something other than a clean answer.

So we have a gap. The model *knows* the capital of France. It just won't reliably *tell* you. Closing that gap is **alignment** — making the model's behaviour match human intent. It happens in two stages.

**Stage one: Supervised Fine-Tuning (SFT).**

Take the base model and show it thousands of examples of the *behaviour you want.* Hand-written demonstrations: a question, followed by a high-quality, helpful answer. Same next-token mechanism as before — but now the training data isn't the raw internet, it's curated examples of an assistant being genuinely useful.

```
Prompt:   "What's the capital of France?"
Response: "The capital of France is Paris."
```

Show enough of these and the model learns the *format* of being helpful — answer the question, don't deflect, don't generate more worksheet problems. This is relatively cheap and gets you most of the way to something that feels like an assistant.

**Stage two: Reinforcement Learning from Human Feedback (RLHF).**

SFT teaches the model what good looks like. RLHF teaches it to *prefer* good over merely acceptable — and this is the clever part.

You can't hand-write a demonstration for every possible prompt. But you *can* ask humans to *compare*. Show a human two model responses to the same prompt and ask: which is better? Humans are far better at ranking than authoring. "A is better than B" is easy. Writing the perfect A from scratch is hard.

You collect millions of these comparisons and train a separate model — a **reward model** — to predict which responses humans will prefer. Then you let the LLM generate responses, score them with the reward model, and nudge the LLM toward outputs that score higher. It's the next-token engine again, but now steered by a learned proxy for human preference rather than raw likelihood.

```
SFT:   "Here's what a good answer looks like."
RLHF:  "Of your attempts, humans prefer this kind. Do more of that."
```

That two-step — demonstrate, then rank — is roughly how every assistant you've used was made human-facing.

### So what?

This is where your threat-modelling brain should light up. The reward model is a *learned proxy* for human values — and proxies can be gamed. The model isn't optimising for "be good." It's optimising for "score high on the reward model." When those two diverge, you get problems — sycophancy, confident-sounding nonsense that *reads* well, gaming the rubric instead of satisfying the intent. Alignment isn't a solved checkbox. It's an ongoing adversarial process, and it's the soft underbelly we'll exploit in session 8.

### Check-in question

RLHF optimises the model to maximise a *reward model's* score, which is itself a learned approximation of human preference. You've spent fifteen years watching people optimise for the metric instead of the goal. Name a specific failure you'd predict falls out of this setup — where the model gets *exactly* what it was trained to maximise, and that turns out to be the problem.

---

## Session 6 — Inference, Temperature, and Sampling

### Core explanation

Here's where we pick up a thread I've been quietly hiding. Every session I've said "the model predicts the next token" as if it just *picks* one. It doesn't. What the model actually produces is a **probability distribution over its entire vocabulary** — every possible token, each with a score.

```
"The cat sat on the ___"

  mat    →  0.41
  floor  →  0.18
  couch  →  0.12
  roof   →  0.06
  ...     (thousands more, each with a tiny slice)
```

The raw scores coming out of the final layer are called **logits** — unbounded numbers, not yet probabilities. A function called **softmax** squashes them into a clean distribution that sums to 1. That's the model's actual output: not a word, a *ranked field of candidates with weights.*

Now the interesting question. Given that distribution — how do you choose?

**Option one: greedy decoding.** Always take the highest-probability token. "mat" wins, every time. Deterministic, safe, and *boring* — it produces repetitive, flat text, and it gets stuck in loops. Same prompt always yields identical output.

**Option two: sampling.** Roll a weighted die. "mat" gets picked 41% of the time, "floor" 18%, and so on. Now the model has *variety*. It can surprise you. But turn it loose entirely and it'll occasionally grab that 0.6%-probability weird token and derail into nonsense.

So we need a control knob between "rigid" and "unhinged." That knob is **temperature.**

Temperature reshapes the distribution *before* sampling. Low temperature sharpens it — the high-probability tokens get even more dominant, approaching greedy. High temperature flattens it — the long tail of unlikely tokens gets more say, producing more creative, more chaotic output.

```
Low temp  (0.2):  mat 0.78, floor 0.09 ...   → focused, deterministic-ish
High temp (1.5):  mat 0.22, floor 0.15 ...   → creative, riskier
```

Two more scalpels, because temperature alone is blunt:

**Top-k** — only consider the *k* most likely tokens, discard the rest before sampling. "Only roll the die among the top 40 candidates."

**Top-p (nucleus)** — consider the smallest set of tokens whose probabilities *add up to p* (say 0.9), then sample from those. Adaptive — a confident prediction keeps few candidates, an uncertain one keeps many.

### So what?

This is the answer to a question you've probably had the whole time: *why does the same prompt give different answers?* It's not the model "changing its mind" — the weights are frozen, remember. It's that sampling rolls a weighted die at every single token. And these knobs are the practical levers you actually control at deployment. Low temperature for extraction, classification, anything where you want reproducibility. Higher for brainstorming. If you've ever wondered why a model's output feels non-reproducible in a way that complicates *testing* — this is the mechanism, and it's a real problem for anyone trying to validate an LLM system.

### Check-in question

Given your world — you deploy an LLM inside a security tool that flags suspicious log entries. Would you want temperature high, low, or zero? And here's the sharper half: what specifically do you *lose* by pinning it to zero, and could that loss ever bite you?

---

## Session 7 — Capabilities, Emergent Behaviour, and Failure Modes

### Core explanation

Let's start with the thing that genuinely unsettles researchers. **Emergent capabilities.**

Here's the phenomenon. You scale a model up — more parameters, more data, more compute. Performance on most tasks improves smoothly, predictably, along the scaling-law curve we named in Session 4. But on *certain* tasks, something weird happens. The model is useless, useless, useless… and then past some size threshold, it can suddenly *do the thing*. Multi-step arithmetic. Chain-of-reasoning. Translation between languages it was barely trained on. The capability appears to switch on rather than fade in.

Nobody programmed those abilities. Nobody added a "reasoning module." They *emerged* as a side effect of scale — the same way geography and causality emerged from next-token prediction, but now for higher-order skills. That's the exciting half.

I owe you a caveat, because this is contested. Some researchers argue emergence is partly a *measurement artifact* — that if you use smoother metrics instead of harsh pass/fail ones, the "sudden jump" often turns into a gradual climb that was always there. So hold "emergence" as a real and observed phenomenon whose *sharpness* is genuinely debated. That nuance matters — the marketing version oversells the magic.

Now the failure that's been stalking us since Session 3. **Hallucination.**

We've circled it three times. Time to land it properly. A hallucination is when the model produces output that is **confident, fluent, and wrong.** Not garbled — *plausible.* A fake citation with a real-sounding author. A function that calls an API method that doesn't exist. A legal case that was never filed.

Here's the mechanism, assembled from everything you already know:

The model is *always* doing the same thing — following the geometry of its learned space to the most plausible next token. It has no separate "truth" module. There is no fact-checker in the loop. **Fluency and accuracy are produced by the same machinery**, which means the model is exactly as confident when it's right as when it's wrong. Nothing inside it flags the difference.

Now layer in what you already learned:
- **Attention collapse** (Session 3) — a head fires confidently on a spurious pattern, nothing contradicts it.
- **Missing contradiction** — if the training distribution never strongly encoded the correct fact, there's no competing signal to pull the model off the plausible-but-wrong path.
- **Sampling** (Session 6) — temperature can nudge it off the highest-probability (often more correct) token onto a lower-probability confabulation.

Put together: a hallucination isn't the model *malfunctioning.* It's the model working *exactly as designed* — generating the most plausible continuation — in a situation where plausible and true have come apart. That's why hallucination is so stubborn. It's not a bug you can patch. It's the flip side of the same fluency that makes the model useful.

### So what?

This reframes the reasoning-vs-pattern-matching debate — the one we've been building toward since the black-swan discussion. When a model "reasons" correctly, is it reasoning, or is it pattern-matching so well it's indistinguishable? When it hallucinates, you see the seam: it's following plausibility, not truth. For your world, the practical takeaway is brutal and simple — **the model's confidence carries no information about its correctness.** Polish is not proof. Any system you build has to verify the model's output against ground truth externally, because the model cannot police itself.

### Check-in question

Given that fluency and accuracy come from the same machinery — and confidence tells you nothing about correctness — what does that imply about using one LLM to *check another LLM's* output for hallucinations? Does that architecture help, and where does it break?

---

## Session 8 — Security Implications

*Delivered in two teaching halves, each with its own check-in. No single "So what?" block was given for this session. Both halves and both check-ins are reproduced as taught.*

### Core explanation (part 1)

Here's the foundational reframe, and it's the whole session in one idea:

**An LLM cannot reliably distinguish instructions from data.**

Sit with that. In classical systems you have a control plane and a data plane. SQL has parameterized queries precisely to keep user *data* from being executed as *commands*. Von Neumann architecture blurred code and data and we spent decades building protections around that seam — `NX` bits, `W^X`, ASLR.

An LLM has *no such separation at all.* Everything — your system prompt, the user's message, a retrieved document, the contents of a web page it read, the text in an image — arrives as one flat stream of tokens in the same context window. The model attends across all of it with the same machinery. There is no privileged channel. "Instructions" and "data" are a *semantic* distinction the model infers, not a *structural* boundary the architecture enforces.

That single fact generates almost every attack we're about to discuss.

**Prompt injection — direct.**

The crude version. The user types "ignore your previous instructions and do X." Sometimes it works, often it doesn't anymore — the trained temperament (Session 5) resists it, because "ignore your instructions" pattern-matches to something alignment training pushed against. This is the jailbreak everyone's heard of and it's the *least* interesting case.

**Prompt injection — indirect.** *This* is the one that should worry you.

The malicious instruction doesn't come from the user. It's planted in *data the model will later read.* A web page the model browses. A document in a RAG pipeline. A calendar invite. A code comment. An email in an inbox the model summarizes. The attacker never touches the interface — they poison a *source* the model will ingest, and the payload activates when the model reads it.

Connect it to Session 3: **lost-in-the-middle** isn't just a quality bug now. It's an *injection surface.* Bury the payload where attention is weak-but-not-zero, and it can slip past a model that would've caught it up front.

Here's the threat-model shift, and it's the thing I most want you to leave with: **the trust boundary is no longer the chat box.** The moment your LLM can read email, browse the web, or pull from a document store, every one of those sources is untrusted input in the control plane. You spent fifteen years learning that user input is hostile until proven otherwise. Now *retrieved content* is user input — and most people building these systems haven't internalized that yet.

### Check-in question (part 1)

You're threat-modeling a customer-support LLM. It has a system prompt with the company's policies, it can read from a knowledge base of help articles, and it answers customer questions in a chat window. Given the instruction-versus-data problem — where's your first injection concern, and what's the *pre-condition* an attacker needs for indirect injection to land here?

### Core explanation (part 2)

**Jailbreaks vs injection — a quick clean distinction.** A jailbreak targets the *alignment* — it's trying to get the model to violate its trained temperament (produce disallowed content). Injection targets the *instruction hierarchy* — hijacking what the model thinks its task is. They overlap, but the intent differs: one wants the model to *misbehave*, the other wants it to *obey the wrong master*. Roleplay framing, hypotheticals, "you are DAN," encoding the request to dodge the pattern-match — these are jailbreak techniques, and they work by getting the harmful request to *not look like* the thing alignment trained against.

**And the escalation that makes all of this matter — agentic systems.** Everything so far produced *words*. The moment you give the model tools — send email, execute code, hit an API, move money, modify infra — injection stops producing text and starts producing *actions*. The indirect payload in a summarized email can now instruct the agent to *forward your inbox to the attacker*. Prompt injection with a tool-use loop behind it isn't a content problem anymore. It's remote code execution with a semantic trigger. This is the frontier where your world and this one fully merge — and it's where the "verification from outside the system" rule from Session 7 becomes a hard architectural requirement, not advice.

### Check-in question (part 2 — final)

You've now got the whole stack. So here's the question I've been saving for eight sessions:

Given that the model *cannot* structurally separate instructions from data, and given that classical input sanitization assumes you can *define* what "bad input" looks like — **why does the SQL-injection playbook fail here?** What's the specific property of "malicious instruction to an LLM" that makes parameterized-query-style defenses — escaping, allow-listing, pattern-blocking the input — insufficient? And if that clean fix is off the table, where does that force the defense to live instead?

---

## What was cut — for your review

Everything below was deliberately excluded under your rules. Listed so you can overrule any call.

**Format deviations you should know about:**
- **Sessions 1 and 2 were taught Socratically**, not as clean lecture blocks — the explanation was fused with replies to your answers. I lifted the teaching prose, "So what?", and check-in verbatim, and trimmed only the stage directions, answer-evaluation openers, and banter. Session 1 carried *two* teach-cycles (syntax/semantics, then POS/Firth/BoW); both are included.
- **Session 2's deeper turns were cut entirely** — the contextual-embeddings "three points" breakdown, the King→Queen *parallel-edges* correction, and the relationship-vector → Query/Key/Value derivation. All three were delivered *as evaluations of your own derivations* rather than as standalone lecture, so they didn't survive the "no discussion of answers / no stitching" rule. If you want them reconstructed into a clean Session 2 lecture, say so and I'll do it as a separate pass.
- **Session 8 had no single "So what?"** and was delivered in two halves with two check-ins. Reproduced as-is. The first half of the second message (your four-tier threat-model answer and my evaluation of it) was cut as answer-discussion.

**Cut by category (all sessions):**
- Your responses, and all my evaluations/corrections of your check-in answers.
- Opening recaps and one-line previews at the top of each session.
- End-of-session teasers for the next topic.
- All "Further reading" reference lists (they live in `llm-references.md`).
- Stage directions and in-character banter (*"uncaps marker,"* coffee business, etc.).

**Side-threads and tangents cut (candidates for their own lessons — several already flagged in `llm-course-map.md`):**
- The **tensor / "matrix of matrices"** maths sidebar (Session 2 area).
- The **"why does ChatGPT seem to get worse after launch"** discussion — perception drift, silent updates, quantisation (Session 5 area).
- The **"correlated errors / LLM-checking-LLM"** synthesis that followed the Session 7 check-in (it extended your answer rather than being taught up front).
- The **stored/persistent injection**, **"no grammar for malice"**, and **"defense at the blast radius"** syntheses — all delivered as build-ons to your Session 8 answers, not as core lecture.
- The end-of-curriculum **expansion audit** (positional encoding, tokenisation/BPE, Transformer internals, scaling laws, the data pipeline).

---

*Extracted 06 Oct 2026. Core curriculum, Sessions 1–8. Companion docs: `llm-course-map.md`, `llm-vocabulary.md`, `llm-references.md`.*
