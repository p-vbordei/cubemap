# Worked Example: A Product Return, From Business to Code

One business process — a customer returning a product — followed all the way down
the four zoom levels. Read it as a template for how an engagement actually moves,
and notice that each level opens with a *story* and would, in practice, be
presented as an *SVG picture* (see `references/visual-communication.md`), not the
text shown here.

> **The story.** Dana bought a coffee machine, it arrived scratched, and she wants
> her money back. From the business's side, that single human moment kicks off a
> chain that touches customer service, finance, and logistics — and the company
> cares enormously that the refund is correct, auditable, and not paid out before
> the damaged item is accounted for.

## Zoom 1 — Macro (business capabilities)

Sitting with the client, you map only capabilities and the events between them. No
tech.

- Capabilities: **Customer Portal** → **Order Management (OMS)** → **Fulfillment
  Logistics**.
- The flow: *Return Initiated* (in the portal) → OMS validates → *AWB Generated*
  (logistics produces a shipping label).
- A policy surfaces from the client's know-how: *"if damage is reported, block the
  refund until the item is inspected."* That sentence is worth more than any
  diagram — capture it.

Output: an agreed picture of how a return moves through the business, the events
that mark progress, and the inspection policy. (Present as an SVG: three big
capability blocks, orange event arrows, the policy as a callout on the validation
step, Dana as the focal actor on the left.)

## Zoom 2 — Meso (systems + contracts + fakes)

Double-click **Order Management**. Inside, three systems appear:

- **Return Validation Service** — decides eligibility against the return policy.
- **Refund Ledger Service** — handles the money and credit notes.
- **Customer Communication Gateway** — notifies Dana.

The boundary between Return Validation and Refund Ledger gets an OpenAPI contract:
`POST /refunds` with `{ "returnId": "RET-2026-9912", "amount": 450.00, "currency":
"RON" }`. None of these services exist yet — so you point **Counterfact** (or
Microcks) at the spec and instantly have a mock that validates the payload and
responds. The frontend team starts building against it the same afternoon, on
**synthetic data**, before any backend code is written. As the client sends real
return records, you seed the fake with them (enrichment loop).

## Zoom 3 — Micro (behaviour + live UI on fakes)

> **The story continues.** A support operator, Marcus, opens the return. He sees
> Dana's case, the photos of the scratch, and two buttons: Approve and Reject.

Double-click **Return Validation Service** and model its behaviour as an XState v5
statechart:

```
idle → verifyingEligibility → awaitingOperatorApproval → approved (final)
                            ↘ rejectedBySystem (final)   ↘ rejectedByOperator (final)
```

`verifyingEligibility` `invoke`s `checkPolicyContract` — which calls the Meso fake.
A React Flow custom node renders Marcus's actual panel (an HTML fake) bound to the
machine via `@xstate/react`. While the machine sits in `awaitingOperatorApproval`,
the panel shows the two buttons. Marcus clicks **Approve** → `APPROVE` event → the
machine moves to `approved` and runs `triggerRefundEvent`, which POSTs to the Meso
mock. The entire flow — portal to operator decision to refund call — runs live, on
fakes. Stakeholders watch Dana's return get approved on screen before a single real
service exists.

The inspection policy from Macro is encoded as a **guard**: the transition to
`approved` simply isn't available while an inspection is outstanding. The rule is
structurally enforced, not remembered.

## The sync-to-async moment

When Marcus approves, a synchronous REST call (`POST /refunds`) must spawn an
asynchronous event the billing worker consumes. Using Microcks **sync-to-async
triggers**, the mocked REST call captures its context and publishes a
contextualised `RefundApproved` event onto the Kafka topic defined in the AsyncAPI
spec — no glue code. The faked billing worker reacts. You've demonstrated a
complete sync→async business flow entirely in simulation.

## Zoom 4 — Atomic (real code)

By now **Return Validation** is a complete cube: its story and policies (L1),
its OpenAPI contract and running fake (L2), its statechart (L3), and the real
return records the client shared, each one in the evidence register. That cube
is handed to an implementer — here, an AI coding app, with the one-sentence
brief: *implement this service against this contract and this statechart; the
acceptance criteria are your tests; change neither.*

The Return Validation Service comes back implemented in TypeScript, honouring
the exact OpenAPI contract (L2) and statechart (L3). It's contract-tested with
**Schemathesis** (fuzzing) and gated with **PactFlow `can-i-deploy`** before
release. DDD guardrails ensure the generated code doesn't, say, let the refund
Entity reach directly into the Order Aggregate. When it's ready, it drops into
the running canvas in place of the fake — and the rest of the system, still
partly simulated, never notices, because the contract never changed. The Refund
Ledger and Communication Gateway cubes can follow later, implemented by
different tools or teams, in any order.

## What the example demonstrates

- The business picture (Macro) is mapped *with the client* and drives everything.
- The system is **runnable on fakes from day one**, with synthetic data, and gets
  truer through enrichment.
- A business **policy** becomes a statechart **guard** — illegal states made
  impossible.
- One **contract** drives the fake, the UI, the real code, the tests, and the
  sync→async event.
- Real code replaces fakes **one node at a time**, never breaking the running whole.
- A fully mapped capability is a **cube** — self-contained enough to hand to an
  AI coding app for implementation, and to redeploy independently of its
  neighbours.
- Every artifact traces back up to "Dana wants her money back" — the business
  reason at the top of the zoom.
