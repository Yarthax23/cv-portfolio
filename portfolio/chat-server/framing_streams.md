## Intent vs Execution: Separating Meaning from Effect

### The Problem

In protocol-driven servers, it is easy to blur the line between:

* understanding *what a client means*, and
* deciding *what the server does about it*.

When parsing logic begins to mutate state, or execution logic begins to infer intent, systems become fragile. Ordering bugs, duplicated validation, and protocol drift follow naturally.

This project enforces a strict separation between **intent** and **execution**.

---

### Intent Is Declarative

After framing and grammar validation, the server produces an **intent**:

```c
typedef struct
{
    command_result type;
    int room_id;
    const char *payload;
    size_t payload_len;
} command_action;
```

An intent describes:

* **What kind of operation** the client requested
* **Which logical entity** it targets (room, payload)
* **Nothing else**

It deliberately does *not* describe:

* whether the action is currently valid
* when it should occur
* what side effects it triggers
* how other clients are affected

Intent is *descriptive, not imperative*.

---

### Execution Is Authoritative

The execution layer consumes intent and decides:

* whether the action is applicable
* how it mutates server state
* which broadcasts must occur
* in what order effects are visible

For example, a `CMD_JOIN_ROOM` intent does not imply:

> “The client has joined a room”

It means:

> “The client requested to join a room”

Only execution can make that true.

---

### Why This Boundary Matters

This separation creates several guarantees:

1. **Grammar is deterministic**

   * Same input → same intent
   * Independent of server state

2. **Execution is contextual**

   * Same intent can result in different effects
   * Depends on client state, room membership, lifecycle

3. **State mutation is centralized**

   * All mutations occur in one place
   * All ordering decisions are explicit

This drastically reduces reasoning complexity.

---

### Preventing Hidden Coupling

A common anti-pattern in small servers is allowing grammar to “help” execution:

* updating `room_id` during parsing
* emitting JOIN events directly
* assuming current state while parsing

This creates hidden coupling between syntax and semantics.

In this project:

* Grammar cannot observe global state
* Grammar cannot mutate clients
* Grammar cannot trigger I/O

This makes the system easier to extend and audit.

---

### Intent Is Stable, Execution Evolves

One key benefit of this design is **change isolation**.

The protocol grammar and intent model can remain stable while:

* execution logic evolves
* ordering rules are refined
* edge cases are tightened
* delivery mechanisms change

This is especially valuable in C, where implicit coupling is costly to undo.

---

### Mental Model

A useful way to reason about the system is:

> Grammar answers *“What did the client ask for?”*
> Execution answers *“What actually happens?”*

This distinction is subtle, but enforcing it consistently prevents entire classes of bugs before they exist.