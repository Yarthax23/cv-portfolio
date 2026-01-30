# CV & Systems Portfolio — Juan Diego Figari

This repository contains the source materials for my **CV** and **technical portfolio**,
with a focus on **systems programming**, **explicit protocol design**, and
**correctness-first development**.

It is structured as a set of reproducible documents rather than a static artifact dump.

---

## Current Role

**Junior Engineer @ Urbanly** (Jan 2026–present)

Working on geospatial simulation systems for urban planning — agent-based modeling,
accessibility calculations, and spatial data infrastructure. Tech stack includes
C++ services (Carpincho, Bigfoot), TypeScript/React (CityCompass), and PostgreSQL/PostGIS.

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

## Highlighted Projects

### Event-Driven Chat Server (C)

A single-process, event-driven, multi-client chat server using UNIX domain
stream sockets and `select()`.

The portfolio focuses on:
- Explicit ownership of server state
- Protocol framing vs grammar vs execution
- Deterministic, debuggable behavior

📂 Design documents: `portfolio/chat-server/`
📦 Source: https://github.com/Yarthax23/unix-chat-server

### Urbanly Subway Map (TypeScript)

Interactive subway network visualization built during Urbanly's hiring process.
Demonstrates frontend architecture decisions, state management, and accessible UI design.

📂 Design documents: `portfolio/urbanly-subte-map/`

---

## Build

PDFs are generated from LaTeX sources.

Build artifacts are intentionally **not committed**.

---

## Author

**Juan Diego Figari**  
Buenos Aires, Argentina  
GitHub: https://github.com/Yarthax23
