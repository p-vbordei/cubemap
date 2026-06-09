# Virtualizing Without Complete Data, and the Enrichment Loop

The defining move of this methodology: **you make the system runnable before it
is real, and before you have all the data.** A contract plus a fake gives you a
clickable, end-to-end flow on day one. Then, as real examples trickle in, you fold
them in and the simulation converges on production reality. The client watches the
thing get truer every time they hand you something.

This file explains how to bootstrap a simulation from nothing but the model, and
how to run the enrichment loop well.

## Why you must not wait for the examples

The instinct — "we can't build the mock until we know exactly what the data looks
like" — is what kills momentum. It blocks frontend, design, and stakeholder
review behind a data-gathering exercise that can take weeks. The fix: a contract
defines the *shape* of the data, and that shape alone is enough to generate
plausible synthetic data and serve it from a fake. You can demonstrate the whole
refund flow, the whole receipt-to-report flow, the whole checkout — with not one
real record and not one real backend service running.

Coarse and synthetic on day one is fine. The simulation's job early on is to make
the *flow* tangible and to surface what's missing, not to be accurate.

## Stage 0 → Stage 3: how a fake matures

Think of each simulated node as moving through stages. Different parts of the
system will be at different stages at the same time — that's expected.

| Stage | What drives the fake | What it's good for |
|-------|----------------------|--------------------|
| **0 — Schema-only** | Synthetic data generated from the contract's types/constraints (e.g. Faker, Schemathesis-style generation) | Proving the flow connects; clicking through screens; finding missing steps |
| **1 — Example-seeded** | Real examples pasted into the contract as named `examples`; Microcks/Counterfact serve them | Realistic demos; catching "that's not how our data looks" early |
| **2 — Stateful simulation** | A simulator (Counterfact) holds state across calls and applies the business rules | Multi-step flows, error cases, "what if it's a duplicate receipt" |
| **3 — Real implementation** | Production code, contract-tested against the same spec | Going live, one node at a time |

The enrichment loop is mostly about walking nodes from Stage 0 toward Stage 2,
then quietly swapping the ready ones to Stage 3 — without ever breaking the
running flow, because the contract never changed underneath them.

## The enrichment loop

Every time the client hands over something real — *"here's an actual receipt,"*
*"here's the Excel we export from software X today,"* *"here's the report template
we email to Z"* — run this loop:

0. **Register the artifact first.** Store the raw file untouched in `examples/`
   and add an entry to the evidence register (`examples/register.html`): what it
   is, who provided it and when, and which process/step of the map it belongs
   to. Templates count as much as filled-in examples — a blank invoice template
   defines the artifact's shape; a filled-in one adds real values and edge
   cases. You'll complete the entry's "what it changed" field at the end of the
   loop. Nothing the business hands over is absorbed silently. (No register in
   the project yet? Create one from
   `assets/evidence-register-template.html`.)
1. **Read the example and extract structure.** An Excel becomes columns, types,
   value ranges, and a few representative rows. A receipt PDF becomes fields
   (vendor, date, line items, VAT, total). A report becomes its sections and the
   data each one needs. (Spreadsheets: use the `xlsx` skill to parse; PDFs/scans:
   use the `pdf` skill / OCR.)
2. **Reconcile with the contract.** Does the real example fit the current schema?
   Usually not exactly — there's a field nobody mentioned, a currency, a status
   you didn't know about. Update the OpenAPI/AsyncAPI contract to match reality.
   This is contract refinement driven by evidence, not guesswork.
3. **Seed the fake.** Turn representative rows into named `examples` in the
   contract (Microcks serves them directly) or into seed data / handlers in the
   Counterfact simulator. The mock now returns data that looks like *their* data.
4. **Sharpen the written process.** Update the prose narrative from the Macro
   session: the new field, the edge case the example revealed, the rule you
   inferred ("ah — receipts from this vendor never have VAT"). The write-up and
   the simulation improve together.
5. **Re-walk the flow with the client.** Show them the now-truer simulation.
   New examples and corrections fall out. Repeat.

This is a *standing loop*, not a phase. The system is never "spec'd then built";
it is continuously made more accurate until enough of it is at Stage 2 that
building the real thing (Stage 3) is low-risk and obvious.

## Feeding examples to the AI

Because the examples arrive as files — spreadsheets, receipts, exported reports,
screenshots of legacy screens — the practical workflow is: hand the file to the
AI, and have it do steps 1–4 above. Concretely the AI should:

- Register the file in `examples/register.html` (provenance, process, step)
  before doing anything else with it.
- Parse the file and report the structure it found (don't just absorb it silently —
  show the client the columns/fields you extracted so they can correct you).
- Propose contract changes as a diff, with a one-line reason for each.
- Generate or update the synthetic/seed data so the fake reflects the example.
- Update the written process narrative and flag any new hotspot or rule.
- Close the register entry: record what this artifact changed — which contract
  fields, which policy, which seed data, which requirement.

Keep raw client examples in `examples/` in the repo and the derived seed data in
`fakes/`, with the register as the index over both — a clear, auditable trail
from "what the client gave us" to "what the simulation serves." When a
capability is later handed off as a cube for implementation, the register
travels with it: the implementer (human or AI coding app) can see the real
evidence behind every data shape.

## Worked illustration: the receipt-to-report process

Using the running example — *employee reads receipt K, processes it in software X,
produces report Y, emails Z*:

- **Day 1, Stage 0.** You have only the Macro narrative. Write an OpenAPI contract
  with `POST /receipts` (a receipt shape: vendor, date, total) and
  `GET /reports/{id}` (a report shape). Stand up Microcks/Counterfact with
  synthetic data. The frontend can already show a "submit receipt → see report"
  flow. Nothing is real, but everyone can see the process move.
- **Day 3, Stage 1.** The client emails three actual receipts and one exported
  report. You extract fields, discover receipts can be multi-currency and reports
  have a "needs manager approval" flag you didn't know about. Update the contract;
  seed the fakes with the real values. The demo now uses their vendors, their
  amounts.
- **Day 8, Stage 2.** You add a Counterfact simulator that holds receipts in
  state, rejects duplicates, and routes anything over €10,000 to manager approval —
  encoding the policy from the Macro session. Now you can demo the *rules*, not
  just the happy path.
- **Day 15, Stage 3.** The Reporting service is implemented for real, contract-
  tested with Schemathesis and PactFlow against the exact same spec. It drops into
  the running canvas in place of the fake; the rest of the system, still partly
  faked, doesn't notice.

At no point did the team stop and wait. The simulation was always live, and it got
truer with every example.
