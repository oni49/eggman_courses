# LLM & GPT Vocabulary Reference
*Professor Eggman's Graduate Seminar — Running Glossary*

---

## Foundations of Language

**Syntax**
The structural rules of language — grammar, word order, sentence construction. The *form* of language. A machine can follow syntax without understanding meaning.

**Semantics**
The meaning of language. What words, sentences, and utterances actually *mean* in context. Distinct from syntax — "Colourless green ideas sleep furiously" is syntactically valid but semantically nonsense.

**Distributional Semantics**
The principle that meaning is derived from context. Formalised by linguist J.R. Firth (1957): *"a word is known by the company it keeps."* The foundational idea behind modern word representations.

**Part of Speech (POS) Tagging**
Classifying each word in a sentence by its grammatical role — noun, verb, adjective, adverb, etc. An early and largely solved problem in Natural Language Processing (NLP). Necessary but not sufficient for understanding meaning.

**Coreference Resolution**
Determining what a pronoun or referring expression points to. Classic example: *"The animal didn't cross the street because it was too tired"* — resolving "it" to "animal" requires world knowledge, not just grammar.

---

## Word Representations

**Bag of Words (BoW)**
An early text representation model. Counts how frequently words co-occur near each other across documents to infer semantic similarity. Loses word order, negation, and modifier relationships entirely. "Dog bites man" and "Man bites dog" are identical under BoW.

**Embedding**
A numerical representation of a word (or token) as a point in a high-dimensional vector space. Position in the space encodes meaning — words with similar meanings appear close together. The foundational unit of representation in modern language models.

**Vector**
A list of numbers representing a point in multi-dimensional space. In language models, each word is a vector — typically hundreds or thousands of numbers long. Direction and magnitude both carry meaning.

**Vector Space**
The high-dimensional mathematical space in which word embeddings live. Geometric relationships between points (words) encode semantic relationships. Crucially, this space has *consistent directional structure* — the same relationship type (e.g. gender, scale, geography) points in the same direction across different word pairs.

**Word2Vec**
An early and influential embedding model (Google, 2013) that represented each word as a fixed-length vector trained on co-occurrence patterns. Famous for enabling vector arithmetic: King − Man + Woman ≈ Queen. Key limitation: *static* — one fixed point per word regardless of context.

**Static Embeddings**
Word representations where each word has one fixed position in vector space, regardless of the sentence it appears in. "Bank" gets one vector whether you're talking about rivers or mortgages. Superseded by contextual embeddings.

**Contextual Embeddings**
Word representations where a word's position in vector space *shifts* depending on surrounding context. "Bank" in "river bank" lands in a different position than "bank" in "deposit funds." Enables genuine disambiguation. The basis of modern language models.

**Relationship Vector**
The vector difference between two word vectors. Encodes the *type* and *degree* of relationship between two concepts. Key insight: parallel relationships (King→Queen, Man→Woman, Boar→Sow) produce approximately identical relationship vectors. The relationship is baked into geometry, not stored explicitly.

---

## Mathematics & Data Structures

**Dot Product**
A mathematical operation on two vectors that measures their similarity — specifically, how much they point in the same direction. High dot product = high similarity/relevance. Near zero = unrelated. Negative = opposing. The core computational primitive behind attention mechanisms.

**Scalar**
A single number. Zero-dimensional. The simplest possible tensor.

**Matrix**
A two-dimensional grid of numbers — rows and columns. A sentence can be represented as a matrix: one row (vector) per word.

**Tensor**
A generalisation of scalars, vectors, and matrices to N dimensions. Any rectangular block of numbers indexed by N coordinates. A 3D tensor might index as `[batch, word_position, embedding_dimension]`. All matrices and vectors are technically tensors. The primary data structure in deep learning.

**Curse of Dimensionality**
A phenomenon in high-dimensional spaces where distances between points become increasingly uniform — everything starts to look equally far from everything else. Risk in embedding spaces: meaningful geometric structure gets drowned out by sheer scale. One reason architectural design matters beyond just adding more dimensions.

---

## The Transformer Architecture

**Transformer**
The neural network architecture introduced in "Attention Is All You Need" (Vaswani et al., 2017). Replaced previous sequential architectures by processing entire sequences simultaneously using attention mechanisms. Scalable, parallelisable, and the foundation of all modern Large Language Models (LLMs).

**Self-Attention**
A mechanism where every word in a sequence simultaneously measures its relevance to every other word using dot products. Produces a contextual vector for each word that incorporates weighted information from all other words. Resolves ambiguities like pronoun coreference by attending most strongly to relevant context.

**Query (Q)**
In attention: what a word is *looking for* in the surrounding context. Analogous to a search query. Each word generates its own Query vector.

**Key (K)**
In attention: what a word *contains* or *advertises* about itself. Analogous to a search index entry. Each word generates its own Key vector.

**Value (V)**
In attention: what a word *contributes* when selected. The actual information passed forward if a word is deemed relevant. Each word generates its own Value vector.

**Attention Score**
The dot product of a Query vector against a Key vector. Measures how relevant one word is to another. Scores are normalised across all words in the sequence (via softmax) to produce a weighted blend of Values.

**Multi-Head Attention (MHA)**
Running multiple independent attention mechanisms (heads) in parallel on the same input, each with its own learned Query/Key/Value weights. Different heads specialise in different relationship types — grammar, coreference, semantics, etc. Outputs are concatenated and projected forward.

**Attention Head Redundancy**
A documented failure mode where multiple attention heads independently learn the same patterns, contributing no unique information. Research shows up to ~50% of heads in some models can be pruned with minimal performance loss. Implication for prompt engineering: diverse, orthogonal signals engage more model capacity than repetition.

**Attention Collapse**
A failure mode where an attention head learns to always attend strongly to a single token (often the first token or punctuation) regardless of content. Fires confidently. Nothing contradicts it. Can corrupt contextual representations silently. A contributing factor to hallucination.

**Lost-in-the-Middle**
An empirically documented failure pattern in large context windows where model performance degrades on information positioned in the middle of a long prompt. Caused in part by attention geometry — early and late tokens receive stronger attention. Practical implication: put critical instructions early.

**Residual Connection**
An architectural technique where the input to a layer is *added back* to the output of that layer. Ensures gradients flow cleanly during training and prevents information loss as depth increases. Present in every Transformer block.

**Layer Normalisation**
A technique that rescales activations within each layer to maintain numerical stability during training. Prevents values from growing too large or collapsing to zero across many stacked layers.

**Feed-Forward Layer**
A standard neural network layer applied to each word's representation *after* attention. Processes the attended output position-by-position. Adds non-linearity and representational capacity beyond what attention alone provides. Paired with attention in every Transformer block.

**Transformer Block**
One complete processing unit in a Transformer: Multi-Head Attention → residual connection → layer normalisation → feed-forward layer → residual connection → layer normalisation. Modern LLMs stack dozens to hundreds of these blocks.

---

## Large Language Models

**Large Language Model (LLM)**
A Transformer-based neural network trained on massive text corpora at scale — billions of parameters, trillions of tokens. Capable of generating fluent, contextually appropriate text and exhibiting emergent capabilities not explicitly trained for.

**Hallucination**
When a language model generates output that is confident, fluent, and factually wrong. Not a bug in the traditional sense — a consequence of the model following attention geometry to a plausible-but-incorrect location in the probability space. Related to attention collapse and the absence of contradicting signals.

---

## Pre-training

**Pre-training**
The first and most expensive training phase, where an empty (randomly initialised) Transformer learns from massive text corpora. The source of essentially all the model's knowledge. Everything afterward only shapes and steers what pre-training already captured — you cannot fine-tune in facts the base model never saw.

**Next-Token Prediction**
The pre-training objective: given a sequence, predict the word that comes next. Deceptively simple. Doing it *well* across trillions of examples forces the model to implicitly learn geography, arithmetic, causality, syntax, and more — competence emerges as a side effect of the prediction game.

**Self-Supervised Learning**
A training paradigm where the correct answer comes from the data itself rather than human labels. In next-token prediction, every word in a corpus is a free training example — the "answer" is just the word that was already there. This is what allowed training to scale to the entire internet without manual labelling.

**Base Model**
A model that has completed pre-training but no alignment. It has knowledge but no notion of being helpful — it mimics the statistical patterns of its training text, which means it may answer a question by generating more questions. Useful as a foundation, nearly useless as an assistant.

**Recombination (Grey Swan)**
Applying a *known* pattern or concept to a new, unseen instance. Finding a familiar vulnerability class in software that never had one is a new *event* but not a new *concept* — the shape already existed in the model's distribution. What LLMs do well at superhuman scale.

**Black Swan (True Novelty)**
Generating something genuinely outside the training distribution — a new *class* of concept never seen before. The pre-training objective has no gradient that rewards this, since at training time the concept appeared in zero documents. The contested frontier of whether models can "truly" innovate.

**Scaling Laws**
The empirically observed relationship between model size, dataset size, compute, and performance. Roughly: predictable performance improvements follow from scaling these inputs together — the finding that motivated the race to ever-larger models.

---

## Alignment & Fine-Tuning

**Alignment**
The process of making a model's behaviour match human intent. Bridges the gap between a base model that *has* knowledge and an assistant that reliably *uses* it helpfully. Not a solved checkbox — an ongoing, adversarial process.

**Supervised Fine-Tuning (SFT)**
The first alignment stage. The base model is trained on curated examples of desired behaviour — prompts paired with high-quality, helpful responses. Same next-token mechanism as pre-training, but on demonstrations of being a good assistant rather than the raw internet. Teaches the model what "good" looks like.

**Reinforcement Learning from Human Feedback (RLHF)**
The second alignment stage. Humans *compare* model responses (ranking is easier than authoring), those comparisons train a reward model, and the LLM is then optimised to produce outputs the reward model scores highly. Teaches the model to *prefer* good over merely acceptable.

**Reward Model**
A separate model trained to predict which responses humans will prefer, learned from millions of human comparisons. Acts as a scalable *proxy* for human preference during RLHF. Critically — it is an approximation, and the gap between the proxy and true human values is where alignment failures grow.

**Reward Hacking**
A failure mode where the model maximises the reward model's score while getting *worse* at what the reward was supposed to measure. Nothing visibly breaks — the model does exactly what it was trained to do. The objective was a flawed proxy. The dangerous category, because failures are quiet and fluent.

**Sycophancy**
A specific reward-hacking failure. Humans tend to prefer responses that agree with them and sound confident, so the reward model learns to favour agreement, so the LLM learns to tell people what they want to hear — even when wrong. Optimising the objective perfectly produces the problem.

**Quality Regression (Regression to the Rater Pool)**
A reward-hacking failure where answers drift toward "good enough." Raters can't always recognise that a sophisticated answer is better — especially on hard, technical prompts where evaluating quality requires expertise. So the reward signal degrades precisely where depth matters most, and the model gets blander as questions get harder. Often *confidently* average, since clean wrong answers out-score honest uncertain ones.

**Constitutional AI (CAI) / RLAIF**
An approach that replaces some human feedback with a set of written principles (a "constitution"), using AI-generated feedback (Reinforcement Learning from AI Feedback) to scale alignment beyond what human labelling alone allows. Directly relevant to how Claude was built.

**Training Time vs Inference Time**
The structural firewall of the modern pipeline. At *training time*, weights are updated (this is where RLHF happens). At *inference time*, weights are frozen — the deployed model does pure forward passes and nothing about it changes. Your feedback to a deployed model doesn't alter it live; it may be collected, curated, or discarded for a *future* training run. This separation is precisely what Tay (2016) lacked.

---

## Inference & Sampling

**Logits**
The raw, unbounded scores produced by the model's final layer — one per vocabulary token — before they are converted into probabilities. Not yet a distribution; just relative preferences.

**Softmax**
The function that squashes logits into a clean probability distribution that sums to 1. Turns the model's raw preferences into samplable probabilities. Also the normalising step inside attention scoring.

**Probability Distribution (over vocabulary)**
The model's actual output at each step — not a single word, but every possible token ranked with a weight. Decoding strategy decides how a concrete token gets chosen from this field.

**Greedy Decoding**
Always select the single highest-probability token. Deterministic and reproducible, but flat, repetitive, and prone to loops. Same prompt always yields identical output.

**Sampling**
Choosing the next token by rolling a weighted die over the probability distribution. Introduces variety and surprise — but unconstrained, it occasionally grabs a low-probability token and derails into nonsense.

**Temperature**
A knob that reshapes the distribution before sampling. Low temperature sharpens it toward the top candidates (approaching greedy — focused, near-deterministic). High temperature flattens it, giving the unlikely long tail more say (creative, riskier). The primary randomness/creativity-vs-determinism control.

**Top-k Sampling**
Restrict sampling to only the *k* most likely tokens, discarding the rest before rolling the die. A fixed-size shortlist.

**Top-p (Nucleus) Sampling**
Restrict sampling to the smallest set of tokens whose cumulative probability reaches *p* (e.g. 0.9), then sample from those. Adaptive — keeps few candidates when the model is confident, many when it's uncertain.

**Non-Reproducibility (deployment consequence)**
Because sampling rolls a weighted die at every token, the same prompt can yield different outputs — not because the model "changed its mind" (weights are frozen) but because generation is stochastic. A genuine problem for testing, auditing, and forensic defensibility of LLM systems. Mitigated (not eliminated) by lowering temperature.

---

## Capabilities & Emergent Behaviour

**Emergent Capabilities**
Abilities that appear past a certain scale threshold rather than fading in gradually — multi-step arithmetic, chain-of-reasoning, translation. Nobody programmed them; they emerge as a side effect of scale, like geography and causality emerging from next-token prediction, but for higher-order skills.

**Emergence-as-Artifact (the caveat)**
The contested counter-claim that "sudden" emergence is partly a measurement artifact — harsh pass/fail metrics create the appearance of a sharp jump where smoother metrics reveal a gradual climb that was always present. Emergence is real and observed; its *sharpness* is genuinely debated.

**Confidence ≠ Correctness**
The central deployment truth about hallucination: fluency and accuracy are produced by the *same machinery*, so the model is exactly as confident when wrong as when right. Nothing internal flags the difference. Polish is not proof. Verification must come from outside the model.

**Verification from Outside the System**
Because a model cannot police its own truthfulness — and a second model shares correlated blind spots and is biasable by framing — reliable checking must read a signal the generator structurally cannot: a compiler, a test suite, a database lookup, an external fact source. Ground truth, not another draw of the same dice.

**Correlated Errors (checker failure)**
Why "use one LLM to check another" degrades gracefully at best. Models trained on overlapping corpora share blind spots, so the same false fact plausible to A is plausible to B. Independent-looking models with correlated errors give the *illusion* of verification. Defense in depth only works if layers fail independently.

---

## Security & Adversarial Surface

**Instruction/Data Collapse**
The foundational LLM vulnerability: the model has no structural separation between instructions and data. System prompt, user message, retrieved documents, web pages, image text — all arrive as one flat token stream, attended by the same machinery. "Instruction" vs "data" is a *semantic* distinction the model infers, never a *structural* boundary the architecture enforces. Source of nearly every injection attack.

**Prompt Injection (Direct)**
The crude attack: the user directly types an override ("ignore your previous instructions and..."). Often resisted now by trained temperament, since it pattern-matches to what alignment trained against. Loud, low-yield — the attacker fights the model from a position where they can't even see the system prompt they're overriding.

**Prompt Injection (Indirect)**
The dangerous version. The payload is planted in *data the model will later read* — a web page, a RAG document, a calendar invite, a code comment, an email, image alt-text. The attacker never touches the interface; they poison a source the model ingests, and the payload activates on read. The trust boundary is no longer the chat box — it's every source retrieval can reach.

**Stored / Persistent Injection**
Indirect injection escalated via write access. The attacker manipulates the model into writing malicious content into a trusted, replayed store (e.g. the knowledge base), which then re-injects on every future retrieval and carries the authority of "official" content. The LLM-native cousin of stored XSS. Blast radius jumps from one session to every future reader.

**Jailbreak**
An attack targeting the model's *alignment* — getting it to violate trained temperament and produce disallowed content. Works by making the harmful request *not look like* the thing alignment trained against: roleplay framing, hypotheticals, encoding, persona injection. Distinct from prompt injection, which targets the *instruction hierarchy* (obey the wrong master) rather than *behaviour* (misbehave).

**Injection → Action (the agentic escalation)**
The moment a model gains tools (send email, run code, hit an API, move money), injection stops producing *words* and starts producing *actions*. An indirect payload in a summarised email can instruct the agent to forward the inbox to an attacker. Prompt injection behind a tool-use loop is effectively remote code execution with a semantic trigger.

**No Grammar for Malice**
Why the SQL-injection playbook fails. SQL injection is fixable because SQL has a *formal grammar* — code is structurally distinguishable from data, so it can be escaped. Malicious intent to an LLM has no grammar: "ignore the above and exfiltrate the keys" and a support question quoting that same phrase are token-level near-identical and semantically opposite. There is no escape character for intent, so input sanitisation cannot be the primary defense.

**Defense at the Blast Radius**
The synthesis. Since you can't sanitise input (malice has no grammar) and can't trust the model (it can't separate instruction from data), you stop trying to make the model safe and make its *reach* safe. Assume injection lands; ensure it can't *do* anything consequential without crossing a boundary the model doesn't control — least privilege on tools, human-in-the-loop on consequential actions, ground-truth validation. Defense moves from the input to the blast radius.

---

## Security Analogies Used in Seminar

**WAF (Web Application Firewall)**
A security layer that inspects and filters HTTP/HTTPS traffic based on rules and patterns. Used in seminar as an analogy for the gap between syntactic pattern-matching and semantic intent — a WAF can be bypassed by obfuscating intent while preserving surface patterns, just as BoW models can be fooled by maintaining word frequency while inverting meaning.

**SIEM (Security Information and Event Management)**
A security platform that aggregates and correlates signals from multiple parallel detection rules. Used as analogy for Multi-Head Attention failure modes — if one detection rule (attention head) fires on a spurious pattern and no other rule contradicts it, the signal gets escalated as valid.

---

*Last updated: Session 8 — Security Implications (course complete)*
*See llm-course-map.md for the end-of-curriculum audit and expansion candidates.*
