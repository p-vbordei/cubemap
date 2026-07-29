# Meso (L2): Contracts and Fakes

At the Meso level you double-click a business capability from the Macro map and
reveal the **systems** inside it (the C4 container view) and the **contracts**
that govern how those systems talk. Then you immediately stand up **fakes** that
obey those contracts, so the whole thing is runnable before anything is built.

This is the level where business intent becomes a concrete, machine-checkable
agreement, without yet committing to an implementation.

## Reveal the systems (C4 containers)

A capability like *Reporting & Approval* expands into containers: the
deployable software chunks that realise it: a Reporting Service, a
Notification Gateway, a web app the operator uses. Draw them as nodes on the
canvas inside the capability's boundary. Stay at container granularity (C4
Level 2); you are not yet decomposing into components or code. Each arrow
between containers is a place that needs a contract.

C4 gives you the clean static decomposition, context, containers, components,
code, but it's deliberately static. The dynamic behaviour (how a container
reacts over time) is handled at Micro with statecharts. Keep the two separate:
C4 says *what the pieces are*, statecharts say *how a piece behaves*.

## Contracts: the four kinds

Every boundary is governed by a formal contract. Pick the contract type by
interaction style:

| Interaction | Contract standard | Use when |
|-------------|-------------------|----------|
| Synchronous request/response (REST) | **OpenAPI 3.1** | A caller needs an answer now: create order, fetch report |
| Asynchronous events | **AsyncAPI 3.0** | Something happened and others react: `OrderCreated`, `ReceiptReceived` |
| Graph queries over many resources | **GraphQL SDL** | A client composes its own view across entities |
| High-throughput service-to-service RPC | **gRPC / Protobuf** | Internal, performance-sensitive calls |

Contracts are first-class artifacts, stored in Git and reviewed like
production code: naming consistency, error shapes, pagination, security.
Enforce a style guide automatically with **Spectral** (or Redocly/Vacuum
rules) in CI so specs can't drift from your conventions before they hit the
sandbox. "Schema valid" is weaker than "integration compatible": a valid
schema can still break a consumer, which is why drift testing (see
`references/contract-drift-and-testing.md`) exists.

A contract should carry **examples**, not just types. Examples are what make a fake
realistic, and they're where client-supplied data lands during enrichment.

## Fakes: making it runnable now

Once a contract exists, generate a fake from it. This is what lets frontend,
design, and stakeholders work in parallel with no backend. Choose the fake by job:

- **Microcks**: the shared, org-level sandbox. Ingests OpenAPI/AsyncAPI/GraphQL/
  gRPC straight from Git and serves live mock endpoints plus simulated event
  streams. Use it as the team's common simulation environment, and for its
  **sync-to-async triggers** (below). Examples in the contract become the mock
  responses.
- **Counterfact**: a stateful, hot-reloading TypeScript simulator. Point it at
  an OpenAPI doc and get typed handlers for every endpoint; it holds state
  across calls, has a REPL to inject failures and inspect state, and, because
  handlers are typed from the spec, drift is caught in your editor before
  runtime. Ideal for the enrichment loop and for simulating business *rules*,
  not just shapes.
- **Prism**: quick, CLI, OpenAPI-native dynamic mocks (Faker-backed). Good for a
  fast local stub at a boundary.
- **MSW**: intercepts requests in the browser; great for frontend-only dev and
  tests.
- **WireMock**: heavy-duty virtualization of third-party HTTP dependencies you
  don't control.

Rule of thumb: **Microcks for the shared sandbox, Counterfact for stateful local
simulation, Prism/MSW/WireMock at the edges.** Full matrix in
`references/tooling-matrix.md`.

## The sync-to-async trigger (the key virtualization trick)

Real systems constantly turn a synchronous user action into an asynchronous event:
the user clicks "Place order," and somewhere a `OrderCreated` event fires onto a
queue that a billing worker consumes. Simulating that link normally means writing
glue code. You don't have to.

Microcks (since 1.14.0) supports **sync-to-async triggers**: when a mocked
REST call comes in, Microcks captures the request/response context and
publishes a contextualised event onto the messaging channel (Kafka, etc.)
defined in your AsyncAPI spec, using template expressions to inject the live
values. No custom simulation code. You annotate the OpenAPI operation with the
AsyncAPI operation it should trigger, and the event payload pulls fields
straight from the request and response.

This means you can demonstrate a complete sync→async business flow (order
placed → event published → worker reacts) entirely on fakes, before any
service exists.

```yaml
# OpenAPI operation annotated to fire an async event
paths:
  /orders:
    post:
      operationId: createOrder
      x-microcks-operation:
        triggers:
          - 'E-Commerce Events API:1.0.0:SUBSCRIBE orders/new'
      # ...request/response as normal
```

```yaml
# AsyncAPI message whose payload is filled from the sync call's context
examples:
  - name: contextualized_trigger
    payload:
      id:    '{{ response.body/orderId }}'
      total: '{{ request.body/totalAmount }}'
      client:'{{ request.body/customerId }}'
      generatedAt: '{{ now() }}'
```

For asynchronous flows that use Avro, point Microcks at a Schema Registry
(Confluent/Apicurio) so it registers schemas as it publishes, keeping the
async simulation faithful to production serialization.

## What you leave Meso with

- A C4 container view per capability on the canvas.
- A reviewed, style-checked contract on every boundary, carrying examples.
- A live sandbox (Microcks) and/or local simulators (Counterfact) serving those
  contracts, including sync→async flows.
- A system the client can click through end-to-end (on fakes) to validate the
  design before Micro and Atomic work begins.
