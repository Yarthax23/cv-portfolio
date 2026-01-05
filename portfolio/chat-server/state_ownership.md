## Ownership of Client State in an Event-Driven Server

### Context

This project is an event-driven chat server written in C, using a single-threaded `select()` loop. Clients communicate through a line-oriented protocol that supports nicknames, rooms, server events (JOIN / LEAVE / QUIT), and room-scoped messages.

A central design goal was to make **state ownership explicit**, narrow, and auditable. In a low-level, long-running server, unclear ownership of mutable state is one of the fastest ways to introduce desynchronization bugs, ordering errors, and invalid protocol behavior.

---

### The Client as the Single Source of Truth

Each connected client is represented by a single `Client` structure:

```c
typedef struct Client
{
    int socket;          // -1 means unused
    int room_id;         // -1 if not in a room
    int index;           // owned identity
    char username[USERNAME_MAX];
    char inbuf[INBUF_SIZE];
    size_t inbuf_len;
} Client;
```

This structure is **authoritative**:

* Connection state (`socket`)
* Identity (`index`, `username`)
* Room membership (`room_id`)
* Partial protocol input (`inbuf`, `inbuf_len`)

No other subsystem mirrors or caches this information.
If a fact about a client exists, it exists **only here**.

This decision eliminates an entire class of bugs related to duplicated state, stale views, and implicit synchronization.

---

### Clear Ownership Boundaries

Ownership is divided strictly by responsibility:

| Layer     | Owns State? | Responsibility                              |
| --------- | ----------- | ------------------------------------------- |
| Framing   | No          | Accumulate bytes, detect message boundaries |
| Grammar   | No          | Validate syntax, extract intent             |
| Intent    | No          | Describe *what should happen*               |
| Execution | **Yes**     | Mutate client state, enforce invariants     |
| Delivery  | No          | Move bytes to sockets                       |

Only the **execution layer** is allowed to mutate `Client` fields.

---

### Intent Is Explicit and Side-Effect Free

Grammar does not mutate clients.
Instead, it emits a small, explicit action object:

```c
typedef struct
{
    command_result type;
    int room_id;
    const char *payload;
    size_t payload_len;
} command_action;
```

This object answers only one question:

> *“What should the server do next, assuming this command is valid?”*

It does **not** answer:

* *When* the action happens
* *Whether* it is allowed in the current state
* *How* it affects other clients

Those decisions belong exclusively to execution.

---

### Execution Owns Mutation and Ordering

The execution loop consumes `command_action` and performs state transitions explicitly:

```c
command_action action = handle_command(c, msg, msg_len);

switch (action.type)
{
case CMD_JOIN_ROOM:
    if (c->room_id != -1)
        broadcast_leave(c->room_id, c);

    c->room_id = action.room_id;
    broadcast_join(action.room_id, c);
    break;

case CMD_BROADCAST_MSG:
    if (c->room_id != -1)
        broadcast_room(c->room_id, c,
                       action.payload,
                       action.payload_len);
    break;

case CMD_DISCONNECT:
    if (action.room_id != -1)
        broadcast_quit(action.room_id, c);

    client_remove(c);
    break;
}
```

Key properties:

* **Mutation happens before broadcast**
* **Broadcast reflects authoritative state**
* **No helper function mutates clients**
* **Invalid states are unrepresentable by construction**

This guarantees protocol-visible events always correspond to real server state.

---

### Broadcast Helpers Do Not Own State

Broadcast helpers are deliberately limited:

* No mutation
* No policy decisions
* No implicit ordering
* No retries or disconnect logic

They are pure delivery utilities: *fan-out bytes to sockets*.

This prevents subtle bugs where delivery code accidentally becomes a second execution layer.

---

### Invariants Made Explicit

This ownership model enforces several invariants naturally:

* A client cannot receive room messages unless `room_id != -1`
* JOIN / LEAVE events always match actual membership
* QUIT always reflects a real disconnection
* No client can exist “half-joined” or “half-left”

Every invariant is either:

* enforced by execution,
* validated earlier,
* or impossible to express.

---

### Why This Matters

In small servers, it is tempting to let parsing logic “help” with state updates, or to let broadcast helpers infer context. This works—until it doesn’t.

By making ownership explicit and narrow:

* Debugging becomes local
* Reasoning stays linear
* Future refactors remain safe
* The protocol and the implementation cannot silently diverge

This approach favors **correctness and clarity over cleverness**, which is a deliberate tradeoff in a systems context.