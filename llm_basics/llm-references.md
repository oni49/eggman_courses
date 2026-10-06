# LLM & GPT Course — Reference List
*Professor Eggman's Graduate Seminar — References by Session*

References are tiered:
- **Verify** — check a specific claim made during the session
- **Go deeper** — motivated self-study beyond the lesson
- **Foundational** — landmark papers or books, used sparingly

---

## Session 1 — Why Language Modelling Is Hard

- **Foundational** — J.R. Firth, "A Synopsis of Linguistic Theory, 1930–1955," in *Studies in Linguistic Analysis*, Blackwell 1957. Origin of the distributional principle — "a word is known by the company it keeps" — that underpins every word representation that followed.
- **Go deeper** — Noam Chomsky, *Syntactic Structures*, Mouton 1957. Source of "Colourless green ideas sleep furiously" and the formal separation of syntax from semantics.

---

## Session 2 — Tokens, Embeddings, and Vector Spaces

- **Foundational** — Mikolov et al., "Efficient Estimation of Word Representations in Vector Space" (the Word2Vec paper), 2013. The model behind King − Man + Woman ≈ Queen and the popularisation of vector arithmetic over word embeddings.
- **Go deeper** — Mikolov et al., "Distributed Representations of Words and Phrases and their Compositionality," NeurIPS 2013. The companion paper detailing the training improvements that made Word2Vec practical at scale.

---

## Session 3 — The Transformer Architecture

- **Verify** — Vaswani et al., "Attention Is All You Need," NeurIPS 2017. The original Transformer paper. Abstract and introduction are readable without a deep maths background.
- **Go deeper** — Jay Alammar, "The Illustrated Transformer," jalammar.github.io. Visual walkthrough. Exceptional clarity.
- **Foundational** — Bahdanau et al., "Neural Machine Translation by Jointly Learning to Align and Translate," ICLR 2015. The attention precursor that set the stage.

---

## Session 4 — Pre-training: Objectives, Data, and Scale

- **Verify** — Radford et al., "Language Models are Unsupervised Multitask Learners" (the GPT-2 paper), OpenAI 2019. Where the "next-token prediction scales to general capability" thesis went mainstream.
- **Go deeper** — Kaplan et al., "Scaling Laws for Neural Language Models," OpenAI 2020. The empirical relationship between data, compute, parameters, and performance.
- **Foundational** — Brown et al., "Language Models are Few-Shot Learners" (the GPT-3 paper), NeurIPS 2020. The demonstration that scale alone unlocks emergent capability.

---

## Session 5 — Fine-tuning, RLHF, and Alignment

- **Verify** — Ouyang et al., "Training Language Models to Follow Instructions with Human Feedback" (the InstructGPT paper), OpenAI 2022. The canonical SFT-then-RLHF recipe, described end to end.
- **Go deeper** — Christiano et al., "Deep Reinforcement Learning from Human Preferences," NeurIPS 2017. The original idea of learning a reward model from human comparisons, predating its use in LLMs.
- **Foundational** — Bai et al., "Constitutional AI: Harmlessness from AI Feedback," Anthropic 2022. An approach to replacing some human feedback with a set of written principles — directly relevant to how Claude was built.

---

## Session 6 — Inference, Temperature, and Sampling

- **Verify** — Holtzman et al., "The Curious Case of Neural Text Degeneration," ICLR 2020. Introduced nucleus (top-p) sampling and showed *why* pure likelihood-maximisation produces bland, repetitive text.
- **Go deeper** — Jay Alammar, "The Illustrated GPT-2," jalammar.github.io. Clear visual walkthrough of how logits become sampled tokens.

---

## Session 7 — Capabilities, Emergent Behaviour, and Failure Modes

- **Verify** — Wei et al., "Emergent Abilities of Large Language Models," TMLR 2022. The paper that popularised the "abilities switch on past a scale threshold" framing.
- **Go deeper** — Schaeffer et al., "Are Emergent Abilities of Large Language Models a Mirage?", NeurIPS 2023. The counter-argument that emergence is partly an artifact of harsh, discontinuous metrics. Read alongside Wei et al. for the live debate.
- **Foundational** — Ji et al., "Survey of Hallucination in Natural Language Generation," ACM Computing Surveys 2023. A broad map of what hallucination is, why it happens, and how it's categorised.

---

## Session 8 — Security Implications

- **Verify** — Greshake et al., "Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection," AISec 2023. The paper that formalised indirect prompt injection as a class.
- **Go deeper** — OWASP, "Top 10 for Large Language Model Applications." The current industry-standard catalogue of LLM/agent risks — prompt injection, insecure output handling, excessive agency. Pull the latest version directly from owasp.org, as it revises.
- **Foundational** — Perez & Ribeiro, "Ignore Previous Prompt: Attack Techniques For Language Models," NeurIPS ML Safety Workshop 2022. An early systematic treatment of prompt-based attacks.

---

*Last updated: Session 8 — Security Implications (course complete)*
*This reference list is complete for the core curriculum.*
