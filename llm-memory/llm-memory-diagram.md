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
