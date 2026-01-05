This document is part of a design-focused portfolio, not a full project README.

# Event-Driven Chat Server – Design Portfolio

This portfolio documents the design decisions behind an event-driven
multi-client chat server written in C.

The project focuses on correctness, explicit state ownership, and
protocol discipline rather than throughput or features.

## Core Design Axis

- [Client State Ownership](state_ownership.md)

## Supporting Design Topics

- [Intent vs Execution](intent_vs_execution.md)
- [Explicit Framing in Stream Sockets](framing_streams.md)

## Architecture

![Architecture Diagram](./assets/diagrams/architecture.png)

## Repository
https://github.com/Yarthax23/unix-chat-server