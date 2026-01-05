# CV & Systems Portfolio — Juan Diego Figari

This repository contains the source materials for my **CV** and **technical portfolio**,
with a focus on **systems programming**, **explicit protocol design**, and
**correctness-first development**.

It is structured as a set of reproducible documents rather than a static artifact dump.

---

## Structure

```
cv/ LaTeX sources for CV (EN / ES)
portfolio/ LaTeX sources and project write-ups
```


### CV
- Language variants: English and Spanish
- Built from LaTeX sources in `cv/`

### Portfolio
- Selected systems-oriented projects
- Includes design documentation, diagrams, and screenshots
- Focuses on *reasoning and tradeoffs*, not feature checklists

---

## Highlighted Project

### Event-Driven Chat Server (C)

A single-process, event-driven, multi-client chat server using UNIX domain
stream sockets and `select()`.

The portfolio focuses on:
- Explicit ownership of server state
- Protocol framing vs grammar vs execution
- Deterministic, debuggable behavior

📂 Design documents:
- `portfolio/chat-server/`

📦 Source repository:
https://github.com/Yarthax23/unix-chat-server

---

## Build

PDFs are generated from LaTeX sources.

Build artifacts are intentionally **not committed**.

---

## Author

**Juan Diego Figari**  
Buenos Aires, Argentina  
GitHub: https://github.com/Yarthax23
