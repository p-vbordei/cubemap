# Project Scaffold and Governance

The repository layout encodes the methodology's central belief: **the business is
the spec.** So the business model sits at the root, peer to the code, not buried
in a wiki. Anyone opening the repo sees the "why" before the "how."

```text
project/
├── business-models/            # L1 MACRO · the "why" and "what" (HTML deliverables: self-contained, responsive, e-mail sharable)
│   ├── capabilities.html       #   named business capabilities + value stream
│   ├── event-storm.html        #   domain events in time order
│   ├── policies.html           #   the "whenever ... then ..." rules
│   ├── requirements.html       #   functional requirements (incl. AI/human/automation split + per-process PROOF blocks)
│   ├── decisions.html          #   the DECISION LOG: every model-changing decision: what, who, why, what it rippled to
│   ├── closeout.html           #   the as-built dossier: produced by the closeout pass when a cube ships or the engagement wraps
│   └── narratives/             #   per-process as-is stories (one .html each), refined by enrichment
│
├── contracts/                  # L2 MESO · the agreements (single source of truth)
│   ├── openapi.yaml            #   sync REST contracts
│   ├── asyncapi.yaml           #   event/messaging contracts
│   └── style/.spectral.yaml    #   the governance ruleset
│
├── examples/                   # raw client inputs feeding enrichment
│   ├── register.html           #   the EVIDENCE REGISTER: every artifact documented (see below)
│   ├── receipts/               #   e.g. the actual "receipt K" files
│   ├── exports/                #   Excel/CSV the client exports from software X today
│   ├── templates/              #   blank forms, invoice/report templates the client works with
│   └── reports/                #   sample of report Y
│
├── fakes/                      # L2 VIRTUALIZATION · runnable simulation
│   ├── microcks/               #   shared sandbox config + sample payloads
│   ├── counterfact/            #   stateful local simulators (rules, hot reload)
│   └── data/                   #   synthetic + example-seeded payloads
│
├── src/
│   ├── flow/                   # the React Flow canvas: the shared visual map
│   │   ├── nodes/              #   custom nodes (actors, systems, artifacts, AI agents)
│   │   ├── edges/
│   │   └── vocabulary.ts       #   the shared visual vocabulary (shapes/colours)
│   ├── machines/               # L3 MICRO · XState v5 statecharts (the behaviour)
│   ├── core/                   #   canvas engine: provider, node store, layout (ELK)
│   └── services/               # L4 ATOMIC · real production code (replaces fakes)
│
├── tests/                      # continuous validation
│   ├── schemathesis/           #   property-based fuzzing
│   ├── pact/                   #   BDCT / can-i-deploy
│   └── specmatic/              #   async/Kafka contract tests
│
├── docker-compose.devmode.yml  # local parity: Microcks + Redpanda + in-memory DBs
└── package.json
```

## Why this shape

- **`business-models/` at the root, not in a wiki.** It's the spec; it belongs
  in version control next to the code it governs, reviewed on the same
  cadence. Its documents are **self-contained HTML** (responsive, e-mail
  sharable, see `references/visual-communication.md`), so any of them can be
  sent to a stakeholder as-is and read on a phone.
- **`examples/` and `fakes/` are separate.** `examples/` is what the client
  gave you (raw, untouched); `fakes/` is what you derived from it. The trail
  from evidence to simulation stays visible, essential for the enrichment
  loop.
- **`examples/register.html` is the evidence register.** One entry per
  artifact the client provides, template, export, receipt, report, screenshot:
  what it is, who gave it and when, which process and step it belongs to, what
  structure was extracted, and what it changed (contract field, policy, seed
  data, requirement). Nothing the business hands over is absorbed silently;
  the register is how every part of the model traces back to its evidence.
  Start from the shipped `assets/evidence-register-template.html` (house
  style, responsive, self-contained).
- **`contracts/` is the single source of truth.** Fakes, generated types, tests,
  and docs all consume it. Nothing re-describes the system elsewhere.
- **`machines/` is peer to `services/`.** Behaviour (L3) and implementation (L4)
  are distinct concerns; the statechart is the contract the real code must honour.

## Governance rules

- **Contracts are reviewed like production code.** PRs to `contracts/` require
  frontend, backend, and security eyes. Spectral runs on every change before the
  spec is allowed into the sandbox.
- **The business model leads.** No new feature work begins without a Macro
  narrative and (once at Meso) a contract. Code that has no contract above it
  is a red flag, zoom back up.
- **Conformance gates deploys.** The CI pipeline runs the validation suite
  (`tests/`), and a conformance score below threshold blocks release. See
  `references/contract-drift-and-testing.md`.
- **Examples are append-only, registered evidence.** Never edit a client
  example to fit the model; update the contract to fit the example, and keep
  the original. Every example gets a row in `examples/register.html` the day
  it arrives: an unregistered artifact is treated as if it doesn't exist.
- **Decisions and proof are recorded the day they happen.** A model-changing
  decision goes in `business-models/decisions.html` with its business reason; a
  walkthrough, sign-off, or passing run goes in the proof block beside the
  acceptance criterion it proves. A session never ends with the model ahead of
  the documents (see `references/living-documentation.md`).
- **A capability ships as a cube.** When a capability's requirements, contracts,
  statecharts, fakes, and evidence are complete and consistent, that slice of
  the repo is a self-contained handoff package an AI coding app (or a team) can
  implement independently (see `references/functional-requirements.md`).
- **Nothing ends without a closeout.** A cube going live or the engagement
  wrapping triggers the closeout pass: artifacts trued to as-built, proof
  statuses final, and `business-models/closeout.html` produced (see
  `references/project-closeout.md`).

## Local bootstrapping

`docker-compose.devmode.yml` (or Testcontainers) brings up the whole simulated
ecosystem locally (Microcks, a Redpanda/Kafka broker, in-memory databases) so any
developer can run the entire faked system on their machine with no external
dependencies. Environment parity from day one is what keeps the "it works on the
fakes" promise honest when real services land.
