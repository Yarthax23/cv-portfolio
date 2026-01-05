---

# `framing_streams.md`

## Framing Streams in a Byte-Oriented Protocol

### The Reality of TCP

TCP delivers a **byte stream**, not messages.

There are no guarantees that:

* a command arrives in one `recv`
* commands align with buffer boundaries
* reads correspond to logical protocol units

Any correct server must explicitly handle this reality.

---

### Explicit Framing Layer

This server introduces a dedicated framing step that:

* accumulates incoming bytes into a per-client buffer
* detects complete protocol messages
* preserves partial input across reads

```c
char inbuf[INBUF_SIZE];
size_t inbuf_len;
```

Framing operates strictly at the byte level. It does not understand commands, rooms, or semantics.

---

### Why Framing Is Its Own Responsibility

Separating framing from grammar avoids two common mistakes:

1. **Assuming `recv` boundaries are meaningful**
2. **Mixing byte management with syntax parsing**

By isolating framing:

* buffer management becomes local and testable
* grammar operates on complete messages only
* partial input never leaks into higher layers

---

### Flow of Data

The full pipeline is explicit:

```
recv()
 → byte stream
   → framing
     → complete message
       → grammar
         → intent
           → execution
             → delivery
```

Each stage has a single responsibility and a single data shape.

---

### Handling Partial and Multiple Messages

The framing layer correctly handles:

* partial commands split across reads
* multiple commands in a single read
* leftover bytes after message extraction

This is achieved by:

* shifting unconsumed bytes
* tracking `inbuf_len`
* never assuming message completeness prematurely

No higher layer needs to care about these details.

---

### Failure Modes Are Contained

Framing also acts as a containment boundary:

* malformed byte streams are rejected early
* oversized inputs are handled defensively
* grammar never sees invalid framing states

This reduces the attack surface and simplifies error handling.

---

### Why This Matters in Practice

Many beginner servers “work” under ideal conditions but fail under real network behavior.

By treating framing as a first-class concern:

* the server behaves correctly under stress
* the grammar remains simple and honest
* execution logic never compensates for transport artifacts

This design reflects the reality of production network programming, not idealized examples.