# LLM Memory, Context Management & Extended Reasoning: Claude's Memory Diagram

A diagram of how memory works for an LLM, using Claude (and the Claude Code harness) as the example. It grows by one layer per session. See the [course map](llm-memory-course-map.md).

## Layer 1: The stateless machine (Session 1)

```mermaid
flowchart LR
    subgraph CLIENT["Harness (client side): owns ALL state"]
        T["Transcript array<br/>system prompt<br/>user / assistant turns<br/>tool results"]
        X[("External stores<br/>files, notes, summaries<br/>(Session 5)")]
    end

    subgraph MODEL["Model (server side): stateless"]
        W[["Weights<br/>frozen at inference<br/>= parametric memory"]]
        F(("f(weights, tokens)<br/>→ next-token<br/>distribution"))
        W --- F
    end

    T -- "ENTIRE transcript resent<br/>on every call<br/>= contextual memory" --> F
    F -- "sample 1 token, append,<br/>repeat until stop<br/>(autoregressive loop)" --> F
    F -- "finished reply" --> T
    X -. "push: injected before the call" .-> T
    F -. "pull: tool call requests content" .-> X
```

**Reading it:**
- Nothing persists on the model side between calls. Delete the transcript and the model has never met you.
- *Parametric memory* (the weights) changes only when a **new checkpoint** is trained and deployed, over months for pretraining or hours to days for a fine-tune. It never changes during a conversation.
- *Contextual memory* is whatever the harness put in the array **this call**.
- *External memory* reaches the model only by becoming contextual memory, either through **push** (the harness injects it) or **pull** (the model asks for it through a tool).

## Layer 2: Inside the window (Session 2)

What happens to the transcript once it reaches the model, and where the KV cache sits.

```mermaid
flowchart TB
    subgraph SEQ["Transcript as tokens, left → right = oldest → newest"]
        direction LR
        P1["Tools + system prompt<br/>(stable)"] --> P2["Earlier turns<br/>(stable)"] --> P3["Latest turn<br/>(volatile tail)"] --> NEW["new token being generated"]
    end

    SEQ --> EMB["Embedding lookup + positional info<br/>one vector per token"]
    EMB --> STACK

    subgraph STACK["Decoder-only stack: N identical blocks"]
        direction TB
        A["Attention (causal mask)<br/>query vs. earlier keys → softmax weights sum to 1<br/>→ weighted blend of values"]
        FF["Feed-forward<br/>per-token transform<br/>(much parametric knowledge here)"]
        A --> FF
    end

    KV[("KV cache (GPU memory)<br/>keys + values for every earlier token,<br/>every block · grows linearly · discarded after call")]
    A <-->|"read earlier K,V<br/>write new K,V"| KV
    STACK --> OUT["Next-token distribution → sample → append (Layer 1 loop)"]

    PC[("Prompt cache (provider-side, between calls)<br/>keeps the KV cache of an unchanged PREFIX")]
    KV -. "persist prefix up to a cache breakpoint" .-> PC
    PC -. "cache hit: skip recomputing the prefix" .-> KV
```

**Reading it:**
- **Causal masking** means each token's keys and values depend only on the tokens before it. That's why the KV cache can be reused at all.
- **The prefix property:** edit token *k* and everything from *k* onward must be recomputed. Edit turn 10 of 20 and turns 1–9 (up to the last cache breakpoint before the edit) are reused, while 10–20 are recomputed.
- **Design consequence:** stable content goes first and volatile content last. A timestamp at the top of the system prompt would make every call a full cache miss.

## Layer 3: Where attention goes, and how rules fade (Session 3)

The same transcript, annotated with how strongly the model tends to *use* each region. Nothing is deleted. Some regions just get quieter.

```mermaid
flowchart LR
    subgraph WIN["Context window (oldest → newest)"]
        direction LR
        S["🔥 START<br/>system prompt, rules<br/>primacy + attention sinks"]
        M["🧊 MIDDLE<br/>old turns, pasted docs,<br/>stale tool output<br/>lost-in-the-middle trough"]
        E["🔥 END<br/>latest turns, reminders<br/>recency"]
        S --- M --- E
    end
    GEN(("next token"))
    E ==>|"strongest pull"| GEN
    S -->|"strong pull"| GEN
    M -.->|"weak pull, worse with<br/>similar-looking distractors"| GEN

    GEN -->|"output appended<br/>becomes an EXAMPLE"| E
    E -.->|"in-context learning:<br/>uncorrected drift teaches more drift"| GEN

    H["Harness"] -. "reminder injection:<br/>restate rules near the end" .-> E
    H -. "true deletion: truncation /<br/>summarization (Layer 4)" .-> M
```

**Reading it:**
- **Attention budget:** every token in the window competes for weights that sum to 1. Similar-looking distractors cost the most.
- **Position:** the start and end are hot and the middle is cold. The *effective* window is smaller than the advertised one.
- **Feedback loop:** each reply is appended and becomes an example for the next. Correct drift early, or edit it out of the history (which costs a cache miss from that point, per Layer 2).
- **Debugging order:** first ask whether the harness deleted it (Layer 4). Then ask whether it was diluted, badly placed, or outvoted by examples.

## Layer 4: Context management, where the harness deletes on purpose (Session 4)

![Layer 4, phone-friendly render](diagram-layer4.png)

```mermaid
flowchart LR
    subgraph WIN["Context window, before compaction (~180K / 200K)"]
        direction LR
        PIN["📌 PINNED<br/>tools + system prompt<br/>(+ CLAUDE.md, Layer 6)<br/>never trimmed · cached"]
        MID["MANAGED REGION<br/>old turns · decisions ·<br/>bulky tool output"]
        HOT["🔥 RECENT<br/>last N turns<br/>kept verbatim"]
        PIN --- MID --- HOT
    end

    MID -->|"1 truncate: drop oldest<br/>(blind to importance)"| GONE["🗑️ gone"]
    MID -->|"2 summarize / compact<br/>(lossy, model chooses detail)"| SUM["summary block"]
    MID -->|"3 prune tool results<br/>(stub, re-fetchable)"| STUB["'[read config.py]' stub"]
    MID -->|"4 offload<br/>(decisions, progress)"| EXT[("External files<br/>(Layer 5)")]

    subgraph AFTER["After one compaction step (~60K)"]
        direction LR
        PIN2["📌 PINNED<br/>✅ cache HIT"] --- NEWMID["summary + stubs<br/>(+ pushed decisions)<br/>❌ cache MISS from here"] --- HOT2["🔥 RECENT<br/>recomputed once"]
    end
    SUM --> NEWMID
    STUB --> NEWMID
    EXT -. "push (pinned) or pull (tool)" .-> NEWMID
```

**Reading it:**
- **The cache tax:** any edit below the pinned prefix is a cache miss from that point down. So compaction happens rarely and **in one batch**: one miss on a much smaller context, then cheap appends again.
- **Durable state goes in pinned or external memory.** Conversational state can be allowed to fade. A decision's *reason* ("because JSONB") is exactly what summaries tend to drop.
- **Stale context is worse than missing context.** Pruning old file reads forces fresh re-reads that match the current file.

## Layer 5: External memory and retrieval paths (Session 5)

![Layer 5, phone-friendly render](diagram-layer5.png)

```mermaid
flowchart TB
    subgraph EXT["External memory: outside the window, usable only as tokens"]
        direction TB
        F[("📄 Files / notes<br/>CLAUDE.md · decision logs · memory dir<br/>exact · name-addressed")]
        V[("🔎 Vector index (RAG)<br/>chunks → embedding model → vectors<br/>similarity search only")]
        R[("🗂️ Raw corpus / repo<br/>searched with grep · find · read")]
        P[("💬 Past chats<br/>summaries · extracted facts")]
    end

    subgraph H["Harness: PUSH (code decides, before the model runs)"]
        RT{"router / always-retrieve<br/>+ query rewrite"}
    end

    subgraph WIN["Context window (tokens only)"]
        PIN["📌 pinned: CLAUDE.md, user prefs"]
        RET["retrieved chunk TEXT"]
        TOOL["tool results"]
    end

    M(("Model"))

    F -- "push at session start" --> PIN
    P -- "push summary / facts" --> PIN
    RT -- "embed query → top-k" --> V
    V -- "chunk text, not vectors" --> RET
    M -- "PULL: tool call<br/>(model decides, can iterate)" --> R
    M -- "PULL: search_past_chats" --> P
    R -- "matching lines / file text" --> TOOL
    WIN --> M
```

**Reading it:**
- **Push vs. pull is about *who decides*, not how much.** Classic RAG is push (harness code decides). Agentic search is pull (the model decides, and can retry after seeing a wrong result).
- **Vectors never enter the window.** Retrieval embeddings exist only for search. The model receives the chunk's text and re-tokenizes it.
- **Failure points:** retrieval miss (wrong chunk), chunking damage (fact separated from its heading), staleness (old index), and poisoning (retrieved text carries instructions).
