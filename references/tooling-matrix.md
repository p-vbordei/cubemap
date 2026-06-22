# The Verified 2026 Tooling Matrix

The full stack behind Infinite Zoom & Fakes-First, grouped by the job it does, with
selection guidance. All entries verified current as of mid-2026. Don't adopt a tool
before the zoom level calls for it — the matrix is a menu, not a checklist.

## By layer

| Layer | Tool(s) | Role | Notes (2026) |
|-------|---------|------|--------------|
| **Canvas & layout** (the shared map) | React Flow / `@xyflow/react` | Interactive node/edge canvas, viewport, zoom | The map everyone reads; custom nodes host live UI |
| | Zustand | Canvas/global diagram state | Node positions, selections, layout params |
| | dagre / d3-hierarchy / **ELKjs** | Automatic layout | dagre = fast, simple trees; ELKjs = most configurable, async, for serious graphs |
| | Stately Studio | Author & visualise statecharts | Round-trips with XState |
| **Behaviour** (Micro) | **XState v5** | Statecharts + actor model | Every machine is an actor; same formalism for UI flows and backend orchestration |
| | `@xstate/react` | Bind machines to React UI | `useMachine` drives custom nodes |
| **Contracts** (Meso) | **OpenAPI 3.1** | Sync REST contracts | de-facto standard |
| | **AsyncAPI 3.0** | Event/messaging contracts | channels, payloads, brokers |
| | GraphQL SDL / gRPC Protobuf | Graph queries / internal RPC | where they fit |
| | **Spectral** (or Redocly / Vacuum) | Spec style-guide linting in CI | enforce naming/errors/security before sandbox |
| | Bump.sh / Redocly | Auto-published API docs/catalog | transparent contract catalogue |
| **Fakes & simulation** (Meso/virtualization) | **Microcks** | Shared org-level sandbox | ingests all four contract types from Git; **sync-to-async triggers** (≥1.14.0); exposes conformance metrics; emerging MCP server |
| | **Counterfact** | Stateful local TS simulator | typed handlers from the spec (drift caught in-editor), holds state, REPL to inject failures — ideal for the enrichment loop |
| | Prism | Quick OpenAPI dynamic mocks | CLI, Faker-backed stubs |
| | MSW | Browser request interception | frontend dev/tests |
| | WireMock | Heavy third-party HTTP virtualization | for dependencies you don't control |
| | Testcontainers / Docker Compose (devmode) | Local environment parity | spin Microcks + Redpanda/Kafka + in-memory DBs locally |
| | Redpanda / Kafka (Strimzi) | Event broker for async sim | Microcks Async Minion publishes here |
| | Confluent / Apicurio Schema Registry | Avro schema management | keeps async sim faithful to prod serialization |
| **Drift & testing** (continuous) | **Schemathesis** | Property-based fuzzing + stateful | reads OpenAPI, generates adversarial valid requests; stateful chains via links; ~1.4–4.5× more defects found |
| | **PactFlow** BDCT + **Drift** | Bi-directional & spec-conformance | `can-i-deploy` release gating; provider OAS vs consumer Pact |
| | **Specmatic** | Async/Kafka contract testing | AsyncAPI as contract; pulls Avro from Schema Registry |

This row is a one-line index; the drift-detection suite, how the tools fit
together, and the CI gating story are detailed in
`references/contract-drift-and-testing.md`.

## Selection cheatsheet

- **Need the shared, team-wide simulation environment?** → Microcks.
- **Need a local fake that holds state and enforces rules during the enrichment
  loop?** → Counterfact.
- **Need a throwaway stub at one boundary, fast?** → Prism (server) or MSW
  (browser).
- **Virtualizing a messy third-party HTTP dependency?** → WireMock.
- **Simulating "user clicks → event fires on a queue"?** → Microcks sync-to-async
  triggers + AsyncAPI.
- **Quick tree layout?** → dagre. **Large/complex auto-layout?** → ELKjs (accept
  the async complexity).
- **Per-PR "did the code drift from spec?"** → Drift. **"Will this break a live
  consumer?"** → Pact `can-i-deploy`. **"What weird inputs break it?"** →
  Schemathesis. **"Do the events/topics/Avro match?"** → Specmatic.

## The through-line

Notice that one artifact — the contract — feeds the fakes (Microcks/Counterfact),
the type generation (Atomic code), the tests (Schemathesis/Pact/Specmatic), and the
docs (Bump.sh). That's the point: a single source of truth, defined at Meso from
the business map, drives simulation, implementation, validation, and documentation
at once. Adopt tools that consume the contract; avoid tools that ask you to
re-describe your system somewhere else.
