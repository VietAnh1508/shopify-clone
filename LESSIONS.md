# Lessons Learned

> A personal engineering journal for this project.
> Raw, honest, and retrospective. Write here when your thinking shifts — not just when things go right.

---

## Entry Template

```
## [YYYY-MM-DD] — [One line summary of the insight]
**Phase:** Phase N — Name

What happened or what I was working on:

What I thought before:

What I now understand differently:

How this changes how I'd approach it next time:
```

---

<!-- Start writing entries as you progress through the project. -->
<!-- No entry is too small. A shifted assumption is worth recording. -->

## [2026-03-21] — Each service owns its own state

**Phase:** Phase 1 — Monolith First

_What I was working on:_ Designing order states and deciding which service can change them.

_What I thought before:_ The Payment service could directly update the order state to `paid` after a successful charge.

_What I now understand:_ A service owns its own state. Service A should notify what happened; service B decides how to react. No service should write another service's state.

_How this changes my approach:_ Always ask "who owns this state?" before deciding which service drives a transition.

## [2026-03-21] — A queue publish alone is not a durability guarantee

**Phase:** Phase 1 — Monolith First

_What I was working on:_ Designing the payment flow — specifically how the Payment service recovers if it crashes mid-payment.

_What I thought before:_ Publish a `pay_in_progress` message to the queue before redirecting to the gateway. If the service crashes, it can pick up the message on recovery.

_What I now understand:_ Initiating payment involves two writes — one to the queue, one to start the redirect. Neither is atomic. If the service crashes between them, the system ends in an inconsistent state. This is the problem the Outbox Pattern solves.

_How this changes my approach:_ Before publishing events in critical flows, ask whether the write to the queue and the write to the DB are atomic. If not, investigate the Outbox Pattern.
