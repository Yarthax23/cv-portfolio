# Screenshots & Diagrams – Chat Server Portfolio

This directory contains visual material used in the PDF portfolio and CV
to illustrate the design and execution of the UNIX chat server.

The goal of these assets is not UI polish, but to demonstrate:
- Protocol design
- Separation of responsibilities
- Correct server-side event handling
- Reproducible behavior

---

## Files

### `server-terminal.png`
Screenshot of the server terminal during a live session.

**What it shows**
- Single-process, event-driven loop using `select()`
- Incoming commands parsed and executed in order
- Server-authored events: `JOIN`, `LEAVE`, `QUIT`
- Clear distinction between client input and server-visible behavior

**Why it matters**
Demonstrates that the server owns state, execution order, and broadcasts,
rather than mirroring client actions blindly.

---

### `client-toad.png`
Screenshot of a spectator client (“Toad”).

**What it shows**
- Passive observation of a room
- Server events and user messages as delivered by the protocol
- A client leaving and rejoining the room
- Late nickname assignment (`Client N` → `Peach`)

**Why it matters**
Shows protocol correctness from the client perspective and validates that:
- Server events are authoritative
- Nicknames are not assumed
- Re-joins are handled cleanly

---

### `dataFlow.pdf`
Architecture and data-flow diagram (exported from draw.io).

**What it shows**
- Strict pipeline:

```
recv → byte stream → framing → grammar → intent → execution → delivery → send
```

- Clear separation between:
- Framing (byte stream handling)
- Grammar (pure validation, no I/O)
- Server execution (state mutation + broadcasts)

**Key design rule**
`grammar.c` never performs I/O or mutates state.
It only validates input and returns intent.
All observable behavior is owned by the server.

---

## Export & Formatting Notes (Important)

### Diagrams
- Export from draw.io as **PDF**
- Enable **Crop** on export to avoid oversized bounding boxes
- Do NOT resize diagrams inside draw.io for layout purposes
- Let LaTeX handle scaling

### Screenshots
- Use PNG
- Target width: ~800–1000 px
- Avoid very tall images that waste page space

### LaTeX Layout
- Images and captions are treated as a single visual unit
- Captions are:
- Centered as a block
- Left-aligned inside a box matching the image width
- Image height is explicitly limited to avoid empty pages

---

## Intent

These assets are meant to support a technical narrative:
**design → execution → observability**.

They are not exhaustive documentation, but evidence of:
- Intentional protocol design
- Deterministic behavior
- Engineering discipline

If something here looks “simple”, it is simple on purpose.
