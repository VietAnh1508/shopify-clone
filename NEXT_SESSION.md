# Next Session

> Rewrite this file at the end of each session.
> Last updated: 2026-03-21

---

## Homework

- [ ] **Write ADR-003** — Order state machine design
  - Capture the decision to use intermediate states (`to_pay`, `to_refund`) instead of direct transitions
  - Capture the threshold/auto-cancel pattern for payment retries and refund retries
  - Capture the escalation to `refund_failed` for human intervention
  - Capture why `refund_failed` emits an event rather than alerting directly

- [ ] **Research: Outbox Pattern**
  - Understand why a database table is involved, not just a queue
  - Connect it back to the payment flow you designed this session

---

## Challenge

Before writing any code or scaffolding anything — look at the order state diagram and ask:

> What does the `Order` aggregate look like as a data structure?
> What fields does it need to carry to support every transition in the diagram?

Start with a rough list of fields. Then ask yourself: which fields change on each transition, and which ones never change after the order is placed?
