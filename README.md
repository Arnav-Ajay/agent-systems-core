# Agent Systems: Control, Separation, and State

**A systems-level exploration of decision-making, execution, and memory in agent architectures**

---

## What This Repository Is

This repository consolidates a set of **agent system primitives** that are often conflated in modern LLM applications:

* **Control** — deciding *whether* to act (e.g., retrieve or not)
* **Separation** — deciding *what* to do vs *how* to do it
* **State** — deciding *what persists* across executions

Rather than presenting a single “smart agent,” this work decomposes agents into **independent, inspectable mechanisms**.

The goal is not to build a better chatbot.
The goal is to make **agent failure modes legible**.

---

## The Core Problem

Most agent systems collapse all of the following into a single loop:

* reasoning
* tool invocation
* execution
* memory
* logging

When that happens:

* failures cannot be attributed
* reasoning cannot be evaluated independently
* memory contaminates decision-making silently
* observability becomes retrospective guesswork

This repository demonstrates that **agents only become debuggable once these concerns are separated**.

---

## System Regimes Implemented

This repository documents three agent regimes, each adding *one* capability while preserving prior invariants.

---

### 1. Retrieval as a Decision

**[`agent-tool-retriever`](https://github.com/Arnav-Ajay/agent-tool-retriever)**

**Problem addressed:**
Retrieval is usually injected by default, even when unnecessary or harmful.

**Mechanism introduced:**

* Retrieval is a **binary, explicit decision**
* The agent may choose:

  * `noop` (parametric answer), or
  * `retrieve` (tool invocation)
* Every decision is logged with rationale

**Key outcome:**

> Many RAG failures are **control failures**, not retrieval failures.

---

### 2. Planning vs Execution Separation

**[`agent-planner-executor`](https://github.com/Arnav-Ajay/agent-planner-executor)**

**Problem addressed:**
Monolithic agent loops make it impossible to tell whether errors come from bad reasoning or bad execution.

**Mechanism introduced:**

* **Planner** decides *what should happen*
* **Executor** performs *exactly what was planned*
* **Runtime** orchestrates without reasoning

**Invariant enforced:**

> Reasoning must be inspectable without running tools.
> Execution must be debuggable without re-reasoning.

**Key outcome:**

> Observability improves **without changing system behavior**.

---

### 3. Memory as a Mechanism

**[`agent-memory-systems`](https://github.com/Arnav-Ajay/agent-memory-systems)**

**Problem addressed:**
“Agent memory” is often treated as accumulated context, not controlled state.

**Mechanism introduced:**

Three explicit memory types:

| Memory Type | Scope         | Persistence | Purpose                 |
| ----------- | ------------- | ----------- | ----------------------- |
| Working     | Session-local | None        | Intra-run reasoning     |
| Episodic    | Cross-session | Append-only | Event trace             |
| Semantic    | Cross-session | Gated       | Long-lived abstractions |

All memory access is routed through a **Memory Router** with:

* explicit reads
* explicit writes
* explicit forgetting policies

**Key outcome:**

> Memory can exist **without being helpful** — and that distinction matters.

---

## Architectural Invariants (Across All Systems)

The following rules are enforced consistently:

* No implicit retrieval
* No hidden tool calls
* No planner access to execution artifacts
* No executor reinterpretation of plans
* No memory access without logging
* Logs are **never** treated as memory
* Memory is **never** reconstructed from logs

These invariants are more important than performance.

---

## What This Repository Establishes

Across the included systems, this work establishes that:

* Agent behavior can be decomposed into independent mechanisms
* Control decisions are a distinct failure mode
* Planning and execution must be separable to be observable
* State can persist across sessions without improving correctness
* Policy enforcement measurably alters agent behavior
* Debuggability improves when architecture, not prompts, is the focus

---

## What This Repository Does NOT Claim

This repository makes **no claim** that:

* agents are more accurate
* memory improves quality
* retrieval improves answers
* these systems are production-ready
* intelligence increases with complexity

Those claims require **failure analysis**, not feature addition.

---

## Relationship to Earlier Systems

This work builds on foundational RAG components consolidated in:

* **[`rag-systems-foundations`](https://github.com/Arnav-Ajay/rag-systems-foundations)**
  (chunking, retrieval, reranking, representability)

All retrieval and ranking logic is reused unchanged.
All improvements here are **architectural**, not algorithmic.

---

## Why This Matters

In real systems:

* compilers are separate from runtimes
* planners are separate from operators
* state is governed by policy, not accumulation

Agent systems should follow the same discipline.

This repository treats agents as a **systems engineering problem**, not a prompt-engineering exercise.

---

## What Comes Next

With control, separation, and state established, the remaining questions are no longer about features, but about **failure**:

* How do these systems break under pressure?
* Which abstractions mislead builders?
* Where does observability still collapse?
* What failure patterns repeat across regimes?

Those questions are addressed in subsequent repositories focused on:

* failure-first analysis
* observability
* system-level synthesis

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
