# Continuous Validation: Contract Drift and Testing

Fakes-First only works if the real code, once written, actually honours the
contracts the fakes were built from. The risk is **drift**: the YAML spec and the
running production code slowly diverge until the agreed design is fiction. A spec
file can't fix a backend bug on its own, so contracts have to become *executable*
checks that run continuously in CI and block incompatible changes from shipping.

"Schema valid" is a weaker guarantee than "integration compatible." Strict JSON
validation won't catch a polymorphic mismatch (`oneOf`/`anyOf`) or a
state-specific business rule that one side violates. So you layer several
purpose-built tools rather than relying on one.

## The drift-detection suite

| Tool | Testing style | Protocols | Where it runs | What it's best at |
|------|---------------|-----------|---------------|-------------------|
| **Schemathesis** | Property-based fuzzing (+ stateful) | OpenAPI, GraphQL | Regression/fuzz runs in CI | Generates thousands of schema-valid requests against the real server; finds 500s, validation gaps, and (via OpenAPI links) multi-step *stateful* bugs others miss |
| **PactFlow Drift** | Spec-conformance | OpenAPI (REST) | Fast, per-PR | Deterministic check that the provider's implementation matches its OAS; plug-and-play CLI |
| **Pact / BDCT** | Consumer-driven / bi-directional | REST, gRPC, messaging | Release gating | Guarantees a provider doesn't drop or change a field a live consumer actually uses, without sharing code |
| **Specmatic** | Async contract testing | AsyncAPI, Kafka, JMS | Consumer/producer gating | Spins a local broker, replays AsyncAPI-defined events, pulls Avro schemas from the Schema Registry, asserts topics/serialization match the contract |

### Schemathesis: find what testers wouldn't think to try
Property-based fuzzing built on Hypothesis: it reads the OpenAPI contract and
generates structurally-valid-but-adversarial inputs (boundary values, unicode
edge cases, nulls where they shouldn't be) and fires them at the real backend.
Its **stateful** mode chains operations (create → get → delete) using OpenAPI
links, which is where the nastiest bugs hide. Independent evaluation found it
catches 1.4–4.5× more defects than comparable tools. Actively maintained.

### PactFlow BDCT + Drift: release gating with `can-i-deploy`
Bi-directional Contract Testing compares two artifacts statically: the consumer's
Pact (what it actually needs) and the provider's OpenAPI (what it actually
offers). **Drift** makes the provider's OAS enforceable against its real
implementation. At release time, `can-i-deploy` cross-validates the provider's
spec against every active consumer's contract and tells you, immediately, whether
it's safe to ship, without the teams coordinating directly.

### Specmatic: the same discipline for events
For the async half of the system, Specmatic treats the AsyncAPI document as the
definitive contract, fetches Avro schemas straight from the Schema Registry, and
verifies that messages flow on the right topics with the right serialization. It
catches "message published to the wrong topic" and schema mismatches during
development instead of in production.

## Conformance metrics worth dashboarding

The virtualization platform (Microcks) exposes two metrics that make drift
visible and gate-able:

- **Conformance Index**: how well the contract's attached examples *cover* the
  spec; i.e. how testable the API is in principle.
- **Conformance Score**: the live result of running the conformance suite; how
  aligned the current code actually is with the agreed contract.

Put both on a shared dashboard. Make a drop in conformance score below a threshold
(e.g. 95%) automatically block deploys. This turns "don't drift" from a hope into
a pipeline rule.

## How it fits the pipeline

1. Spec changes are linted (Spectral/Redocly) on every PR (naming, errors,
   security, style) before they reach the sandbox.
2. Drift/Pact run fast per-PR conformance checks.
3. Schemathesis runs deeper fuzz/regression passes.
4. Specmatic gates the async services.
5. `can-i-deploy` is the final release gate; conformance score gates deploys.

The result: the system you ship can never silently diverge from the picture the
client signed off on at Macro. The contract is the through-line from the business
map all the way to production.
