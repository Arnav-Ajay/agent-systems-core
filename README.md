# Agent Systems: Control, Separation, State, and Generation

**A systems-level decomposition of decision-making, execution, memory, and generation in agent architectures**

---

## What This Repository Is

This repository consolidates a sequence of **agent system primitives** that are frequently collapsed into a single opaque loop in modern LLM applications:

* **Control** — deciding *whether* to act
* **Separation** — deciding *what* should happen vs *how* it happens
* **State** — deciding *what persists* across executions
* **Generation** — deciding *whether the system should speak at all*

Rather than presenting a “smart agent,” this work decomposes agents into **independent, inspectable mechanisms** with explicit contracts.

The goal is not to improve answers.
The goal is to make **agent behavior and failure legible**.

---

## The Core Problem

Most agent systems conflate:

* reasoning
* tool invocation
* execution
* memory
* generation
* logging

into a single loop.

When this happens:

* failures cannot be attributed
* decisions cannot be audited
* memory contaminates behavior silently
* generation appears inevitable rather than conditional
* observability collapses into post-hoc guesswork

This repository demonstrates that **agents only become debuggable once these concerns are separated into first-class system layers**.

---

## System Regimes Implemented

Each included repository introduces **exactly one new capability**, while preserving prior invariants.

---

### 1️⃣ Retrieval as a Decision

**Repo:** [`agent-tool-retriever`](https://github.com/Arnav-Ajay/agent-tool-retriever)

**Problem addressed**
Retrieval is usually injected by default, even when unnecessary or harmful.

**Mechanism introduced**

* Retrieval becomes an **explicit binary decision**
* The agent may choose:

  * `noop` (parametric answer), or
  * `retrieve` (tool invocation)
* Every decision is logged with rationale

**Key outcome**

> Many RAG failures are **control failures**, not retrieval failures.

---

### 2️⃣ Planning vs Execution Separation

**Repo:** [`agent-planner-executor`](https://github.com/Arnav-Ajay/agent-planner-executor)

**Problem addressed**
Monolithic agent loops make it impossible to tell whether failures arise from reasoning or execution.

**Mechanism introduced**

* **Planner** decides *what should happen*
* **Executor** performs *exactly what was planned*
* **Runtime** orchestrates without reasoning

**Invariant enforced**

> Reasoning must be inspectable without running tools.
> Execution must be debuggable without re-reasoning.

**Key outcome**

> Observability improves **without changing system behavior**.

---

### 3️⃣ Memory as a Governed Mechanism

**Repo:** [`agent-memory-systems`](https://github.com/Arnav-Ajay/agent-memory-systems)

**Problem addressed**
“Agent memory” is often treated as accumulated context rather than controlled state.

**Mechanism introduced**

Three explicit memory types:

| Memory Type | Scope         | Persistence | Purpose             |
| ----------- | ------------- | ----------- | ------------------- |
| Working     | Session-local | None        | Intra-run reasoning |
| Episodic    | Cross-session | Append-only | Event trace         |
| Semantic    | Cross-session | Gated       | Stable abstractions |

All memory access is routed through a **Memory Router** with:

* explicit reads
* explicit writes
* explicit forgetting
* full traceability

**Key outcome**

> Memory can exist **without being helpful** — and that distinction matters.

---

### 4️⃣ Generation as a Decision (Not an Output)

**Repo:** [`llm-generation-control`](https://github.com/Arnav-Ajay/llm-generation-control)

**Problem addressed**
Most systems treat generation as an inevitable effect of retrieval.

This hides an essential question:

> **Given evidence, should the system speak at all?**

**Mechanism introduced**

Generation is elevated to a **policy-controlled decision layer**.

Every run produces exactly one outcome:

* **ANSWER** — evidence judged sufficient
* **HEDGE** — evidence judged conflicting
* **REFUSE** — evidence judged insufficient

There are **no implicit fallbacks** and no partial states.

**Key properties**

* Generation policy does **not**:

  * access retrieval internals
  * access memory
  * call an LLM
* Generation text (if any) is strictly downstream of the decision
* All decisions are logged as first-class artifacts

**Key outcome**

> Hallucination becomes a **policy failure**, not a mystery.

---

## Architectural Invariants (Across All Systems)

The following constraints hold across every repository:

* No implicit retrieval
* No hidden tool calls
* No planner access to execution artifacts
* No executor reinterpretation of plans
* No memory access without logging
* Logs are **never** treated as memory
* Memory is **never** reconstructed from logs
* Generation is **never guaranteed**

These invariants matter more than performance.

---

## What This Repository Establishes

Across these systems, this work establishes that:

* Agent behavior can be decomposed into independent layers
* Control decisions are a distinct failure surface
* Planning and execution must be separated to be observable
* State persistence does not imply usefulness
* Policy enforcement measurably alters behavior
* Generation must be governed, not assumed
* Debuggability improves when architecture — not prompts — is the focus

---

## What This Repository Does NOT Claim

This repository makes **no claim** that:

* answers are more accurate
* hallucinations are eliminated
* memory improves quality
* retrieval improves correctness
* these systems are production-ready
* intelligence increases with complexity

Those claims require **failure analysis**, not feature accumulation.

---

## Relationship to Foundational RAG Systems

All agent systems here build on frozen RAG foundations consolidated in:

* **[`rag-systems-foundations`](https://github.com/Arnav-Ajay/rag-systems-foundations)**
  (chunking, retrieval, reranking, representability)

No retrieval or ranking logic is modified.
All progress here is **architectural**, not algorithmic.

---

## Why This Matters

In real systems:

* compilers are separate from runtimes
* planners are separate from operators
* state is governed by policy, not accumulation
* output is conditional, not assumed

Agent systems should follow the same discipline.

This repository treats agents as a **systems engineering problem**, not a prompt-engineering exercise.

---

## What Comes Next

With:

* explicit control
* planner–executor separation
* governed memory
* generation policy

…the remaining questions are no longer about features, but about **failure**:

* Where do these layers interact pathologically?
* Which abstractions fail under pressure?
* How do failures compound across layers?
* How should they be observed in real systems?

Those questions are addressed in downstream work focused on:

* failure-first analysis
* cross-layer failure taxonomy
* observability and causal tracing

---

## Intended Audience

This repository is for readers who care about:

* architectural clarity over demos
* debuggability over novelty
* failure analysis over benchmarks

If you want a chatbot, this is not for you.
If you want to understand **how agent systems actually break**, this is the right place.

---

### Closing Statement

This repository does not make agents smarter.

It makes them **legible**.

That is the prerequisite for everything that follows.

---
