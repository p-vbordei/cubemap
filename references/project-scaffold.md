# Project Scaffold and Governance

The repository layout encodes the methodology's central belief: **the business is
the spec.** So the business model sits at the root, peer to the code — not buried
in a wiki. Anyone opening the repo sees the "why" before the "how."

```text
project/
├── business-models/            # L1 MACRO — the "why" and "what"
│   ├── capabilities.md         #   named business capabilities + value stream
│   ├── event-storm.md          #   domain events in time order
│   ├── policies.md             #   the "whenever ... then ..." rules
│   ├── requirements.md         #   functional requirements (incl. AI/human/automation split)
│   └── narratives/             #   per-process as-is stories, refined by enrichment
│
├── contracts/                  # L2 MESO — the agreements (single source of truth)
│   ├── openapi.yaml            #   sync REST contracts
│   ├── asyncapi.yaml           #   event/messaging contracts
│   └── style/.spectral.yaml    #   the governance ruleset
│
├── examples/                   # raw client inputs feeding enrichment
│   ├── receipts/               #   e.g. the actual "receipt K" files
│   ├── exports/                #   Excel/CSV the client exports from software X today
│   └── reports/                #   sample of report Y
│
├── fakes/                      # L2 VIRTUALIZATION — runnable simulation
│   ├── microcks/               #   shared sandbox config + sample payloads
│   ├── counterfact/            #   stateful local simulators (rules, hot reload)
│   └── data/                   #   synthetic + example-seeded payloads
│
├── src/
│   ├── flow/                   # the React Flow canvas — the shared visual map
│   │   ├── nodes/              #   custom nodes (actors, systems, artifacts, AI agents)
│   │   ├── edges/
│   │   └── vocabulary.ts       #   the shared visual vocabulary (shapes/colours)
│   ├── machines/               # L3 MICRO — XState v5 statecharts (the behaviour)
│   ├── core/                   #   canvas engine: provider, node store, layout (ELK)
│   └── services/               # L4 ATOMIC — real production code (replaces fakes)
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

- **`business-models/` at the root, not in a wiki.** It's the spec; it belongs in
  version control next to the code it governs, reviewed on the same cadence.
- **`examples/` and `fakes/` are separate.** `examples/` is what the client gave
  you (raw, untouched); `fakes/` is what you derived from it. The trail from
  evidence to simulation stays visible — essential for the enrichment loop.
- **`contracts/` is the single source of truth.** Fakes, generated types, tests,
  and docs all consume it. Nothing re-describes the system elsewhere.
- **`machines/` is peer to `services/`.** Behaviour (L3) and implementation (L4)
  are distinct concerns; the statechart is the contract the real code must honour.

## Governance rules

- **Contracts are reviewed like production code.** PRs to `contracts/` require
  frontend, backend, and security eyes. Spectral runs on every change before the
  spec is allowed into the sandbox.
- **The business model leads.** No new feature work begins without a Macro
  narrative and (once at Meso) a contract. Code that has no contract above it is a
  red flag — zoom back up.
- **Conformance gates deploys.** The CI pipeline runs the validation suite
  (`tests/`), and a conformance score below threshold blocks release. See
  `references/contract-drift-and-testing.md`.
- **Examples are append-only evidence.** Never edit a client example to fit the
  model; update the contract to fit the example, and keep the original.

## Local bootstrapping

`docker-compose.devmode.yml` (or Testcontainers) brings up the whole simulated
ecosystem locally — Microcks, a Redpanda/Kafka broker, in-memory databases — so any
developer can run the entire faked system on their machine with no external
dependencies. Environment parity from day one is what keeps the "it works on the
fakes" promise honest when real services land.
