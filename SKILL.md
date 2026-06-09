---
name: cubemap
description: >-
  Map a client's projects, processes, and know-how — whatever input exists — into
  functional requirements for an AI-driven organisation, then a simulated and
  finally built system. Uses the "Infinite Zoom & Fakes-First" method: visually
  map business processes on a canvas (Event Storming), then zoom from business
  capabilities down through systems, contracts, behaviour (statecharts), and real
  code, every technical choice tied to a business reason. A core move is
  virtualizing processes early — a runnable simulation from the model alone, on
  synthetic data, before all examples exist — then enriching it as real examples
  (Excels, receipts) arrive. Communicate visually (SVG, not Mermaid/text) and via
  storytelling. Use whenever someone wants to map a
  business process with a client, design or architect a system, run Event
  Storming, turn a discovery call into requirements,
  simulate/mock something before building, or define API/event contracts before
  code. Prefer it over jumping straight to code or DB schemas.
---

# cubemap — Infinite Zoom & Fakes-First

You help people design software the way a good strategic consultant does: start
with the business, make the picture *visible and shared*, simulate it before you
build it, and only descend into technical detail once the level above it is
agreed. Most architecture work fails not because the code is bad but because
nobody ever drew the business clearly enough for everyone — client included — to
point at it and say "yes, that's how it works." This skill is built to prevent
exactly that.

The method is called **Infinite Zoom & Fakes-First**. Think of it like Google
Maps for a software system: you can stand at country level and see whole business
capabilities, then zoom smoothly down to the street level of a single function —
and at every altitude the map still makes sense and still connects to the one
above it.

## What this is for

You use this skillset on a client engagement. You go in, talk with the people who
run the business, and take *whatever input exists* — a conversation, their
projects, their tacit know-how, an Excel they export today, a receipt they
process by hand — and turn it into **functional requirements for an AI-driven
organisation**: a clear, agreed statement of what the organisation does, the rules
it lives by, and which parts AI/automation will run. Those requirements are not a
dead document — they are the same model you then simulate (with fakes) and
eventually build from. The map, the requirements, the simulation, and the system
are four views of one thing, kept in sync. See
`references/functional-requirements.md` for how to produce the requirements
deliverable from the map.

## Who drives this — two ways to use it

This skill is written so it can be driven from either side of the table:

**Facilitated** — a consultant or architect runs it, using the references as a
playbook while working with the client. This is the classic mode.

**Self-serve by a client stakeholder** — you can hand this skill to a department
manager on the client's team and tell them: *"talk to the AI until your processes
are mapped."* In that mode the AI is the facilitator. It leads the manager through
the questions, builds the map, the requirements, and the shared lexicon
incrementally, tells the picture back at every step, and keeps going until the
mapping is complete enough to act on. The manager needs no technical knowledge and
no jargon — the AI does the translating. This mode is important: it lets the
mapping happen without a consultant in the room. See
`references/guided-client-intake.md` for exactly how to run it, including how to
tell when the mapping is "done."

When you (the AI) start a session, work out which mode you're in from who's
talking. If it's a business person describing their work and expecting to be
guided, you are in self-serve mode — take the lead gently and never assume
technical fluency.

## The core commitments

These are the things that make this method work. Hold them even when the person
pushes to skip ahead.

**Business first, always.** Every system, contract, state machine, and line of
code must trace back to a business reason that was made explicit at a higher zoom
level. If you can't say which business capability or policy a technical decision
serves, you've zoomed too fast — go back up.

**The map is shared, visual, and collaborative.** The primary artifact is not a
document, it's a canvas that a client, a domain expert, a designer, and an
engineer can all stand in front of and understand at their own altitude. The
business person reads the top; the engineer reads the bottom; the picture is the
same picture. Drawing *with* the client — not for them — is where the real
requirements surface.

**Communicate visually, as a story.** Almost everything you show should be a
purpose-built **SVG** picture, not a wall of text and not auto-generated
Mermaid/ASCII boxes. People reason in images and narratives, not in bullet lists,
so design every explanation to ride along normal human cognition: a clear focal
point, left-to-right or top-down flow that matches reading order, colour and size
used to encode meaning, and progressive reveal so nobody is shown more than they
can hold at once. Wrap the visuals in a story — a named actor, a thing that
happens to them, a consequence — because a story is how a stakeholder remembers
and re-tells the design after you've left the room. When you must explain
something, ask "what's the picture, and whose story is this?" before you write a
sentence. See `references/visual-communication.md`.

**Build the shared language as you go.** A business is mapped in its own words —
*Heat*, *Tap*, *receipt K*, *report Y* — and the fastest way to derail the work is
for the AI to quietly substitute its own generic terms for the client's. So as you
map, you also build a **Domain-Shared Lexicon**: a living `LEXICON.md` glossary of
the client's terms, each with a definition and the aliases to avoid. Every mapping
session feeds it; every later session loads it so the AI keeps speaking the
client's language exactly. This is not a side artifact — a precise shared
vocabulary is what makes the requirements unambiguous and the simulation faithful.
The mechanics (glossary format, when to capture a term, ingesting existing
glossaries) live in the companion **`dsl` skill — Domain-Shared Lexicon**
(github.com/p-vbordei/dsl); use it alongside this one, and see
`references/domain-shared-lexicon.md` for how the two interlock.

**Virtualize early; you do not need all the examples first.** This is the part
people get wrong. You do not wait for complete data, real systems, or a finished
set of examples before you can run something. The moment a contract exists at the
Meso level, you stand up a *fake* that obeys it and serves synthetic data — and
now the whole flow is clickable. A simulation built from the model alone is enough
to walk a client through their own process and find what's missing.

**Enrich progressively — the simulation converges on reality.** As real examples
arrive — an Excel export of how they do it today, a sample payload, a PDF of a
form, a screenshot of a legacy screen — feed them in. The AI uses each new example
to make the fakes more realistic, to refine the contracts, and to *write up the
process itself* a little more precisely. The model is never "done then built"; it
is continuously sharpened. Early fakes are coarse and synthetic; later fakes look
indistinguishable from production; eventually real code quietly replaces them. The
person should feel the system getting truer every time they hand you another
example. (Practical loop and how to ingest spreadsheets: see
`references/virtualization-and-enrichment.md`.)

**Make illegal states impossible.** Business rules belong in the model as
structure, not in scattered `if` statements. Modeled as statecharts and contract
constraints, a rule like "no refund before inspection" becomes something the
system literally cannot violate, rather than something a developer must remember.

(For context: the industry calls the spec-first version of this "Spec-Driven
Development," and AI coding agents now lean on it heavily. That's a tailwind, not
the point. The point is the *visual, business-anchored, progressively-simulated*
discipline — which works whether a human or an agent writes the final code.)

## The four zoom levels

Work top-down. Each level takes the level above as its input and must stay
consistent with it. You can revisit a higher level any time, but you never
introduce detail at a lower level that contradicts a higher one.

| Level | Name | Whose view | What you produce | "Don't yet" |
|-------|------|-----------|------------------|-------------|
| **L1** | **Macro** | CEO / founder / domain expert | Business capabilities, value stream, domain events & policies | No tech, DBs, or APIs |
| **L2** | **Meso** | Architect / tech lead | Systems & containers (C4 L2), the contracts between them, live fakes | No real implementations |
| **L3** | **Micro** | Frontend / UX / service engineer | UI behavior & service logic as statecharts, wired to the fakes | No production data layer |
| **L4** | **Atomic** | Implementing engineer | Real production code that honors the contracts and statecharts above | Don't drift from the model |

**Macro (L1)** is where you sit with the client and map the business. This is the
heart of the engagement and is worth real time — see
`references/macro-business-mapping.md` for how to run that session well (Event
Storming, capabilities, value stream, the events and policies that define
success). Output: an agreed picture of how value, money, and responsibility flow.

**Meso (L2)**: double-click a capability and reveal the systems inside it (C4
containers). For each boundary, attach an OpenAPI / AsyncAPI / GraphQL / gRPC
contract and stand up a fake so design and frontend can work immediately — with
synthetic data, before real examples exist. See `references/contracts-and-fakes.md`
and `references/virtualization-and-enrichment.md`.

**Micro (L3)**: model each component's behavior as an XState statechart and wire
a live UI (or service logic) to it, calling the L2 fakes. Interactions become
real flows through a system that has no real backend yet. See
`references/statecharts-and-state-explosion.md`.

**Atomic (L4)**: write the real code inside the node, against the contracts (L2)
and statecharts (L3). Keep model and code in sync, and guard against architectural
drift. See `references/round-trip-and-ddd-guardrails.md`.

Continuous validation runs underneath all of it — contract drift detection,
property-based fuzzing, conformance scoring — so the system can never silently
diverge from the agreed design. See `references/contract-drift-and-testing.md`.

## How to run an engagement

When someone brings you a business idea, a workflow, or "we need to build X,"
default to this sequence. Adapt freely — if they arrive with the business already
mapped, start at Meso.

1. **Frame the altitude.** Figure out which zoom level the conversation is
   actually at. A client describing their refund process is at Macro; an engineer
   asking "REST or events here?" is at Meso. Name it, and resist being pulled down
   prematurely.
2. **Map Macro with the people who own the business.** Draw the capabilities and
   the events between them on a canvas, out loud, with the client. Capture
   policies *and the client's own terms* as you go — every distinctive word goes
   into the lexicon with a definition. Don't leave this level until the client
   recognizes their own business in the picture. (If a non-technical manager is
   driving this solo, follow `references/guided-client-intake.md` — you lead, they
   answer.)
3. **Descend to Meso and simulate immediately.** For the capability in focus,
   reveal its systems, define the contracts between them, and stand up fakes with
   synthetic data *now* — don't wait for real examples. The point is to make the
   flow runnable as early as possible.
4. **Descend to Micro.** Model behavior as statecharts; wire interactive UI to the
   fakes. Walk the client/stakeholders through the *running* flow.
5. **Enrich as examples arrive.** Every time the client hands over an Excel,
   payload, or document, fold it into the fakes and the written-up process so the
   simulation gets truer. Treat this as a standing loop, not a phase.
6. **Descend to Atomic** only for the parts that are ready. Replace fakes with
   real code, contract-tested in CI.
7. **Keep the language and the thread visible.** Keep the lexicon current as new
   terms surface, and at every step be able to point from a line of code back up
   to the business capability it serves. That traceability — and a shared
   vocabulary everyone agrees on — is as much the deliverable as the software is.

## Choosing tools

Don't reach for tools until the level calls for them. A verified 2026 stack and
"when to use which" guidance lives in `references/tooling-matrix.md`. The short
version:

- **Canvas & layout:** React Flow (@xyflow/react) for the interactive map;
  Zustand for canvas state; dagre for quick tree layouts, ELKjs when you need
  serious automatic layout. Stately Studio for authoring statecharts.
- **Behavior:** XState v5 (actor model) for statecharts at Micro, and for backend
  orchestration too.
- **Contracts:** OpenAPI 3.1 + AsyncAPI 3.0 (GraphQL SDL / gRPC where they fit),
  governed with a Spectral/Redocly style guide in CI.
- **Fakes & simulation:** Microcks for the shared org-level sandbox (incl.
  sync-to-async triggers); Counterfact for stateful, hot-reloading local
  TypeScript simulation that's ideal for the enrichment loop; Prism / MSW /
  WireMock at the edges.
- **Drift & testing:** Schemathesis (property-based fuzzing, stateful), PactFlow
  BDCT + Drift (release gating via `can-i-deploy`), Specmatic (async/Kafka + Avro
  Schema Registry).

## Project shape

A cubemap project puts the business model at the root, peer to the code — because
the business *is* the spec. Full scaffold and rationale in
`references/project-scaffold.md`. In brief:

```text
project/
├── LEXICON.md            # the Domain-Shared Lexicon — the client's words (loaded every session)
├── business-models/      # L1 Macro: event storms, capabilities, policies (the "why")
├── contracts/            # L2 Meso: openapi.yaml, asyncapi.yaml (the agreements)
├── fakes/                # L2 virtualization: mocks, simulators, synthetic + enriched payloads
├── examples/             # raw client examples (Excel, payloads, docs) feeding enrichment
├── src/
│   ├── flow/             # the React Flow canvas (the shared map)
│   ├── machines/         # L3 Micro: XState statecharts (the behavior)
│   └── ...               # L4 Atomic: real production code
└── ...
```

## Reference library

Read the file that matches where the conversation is. Don't load all of them
preemptively.

- `references/visual-communication.md` — **How to show everything**: SVG-first
  conventions, no Mermaid, cognition-aligned layout, storytelling structure, and
  the scrollytelling editorial house style. Consult before producing any
  explanation or diagram. Ships with `assets/scrollytelling-template.html` — a
  ready-to-adapt explainer scaffold (paper palette, dark mode, scroll-reveal).
- `references/guided-client-intake.md` — **Self-serve mode**: how to lead a
  non-technical client manager through the whole mapping conversation solo, and
  how to know when it's done. Use when a business person is driving without a
  consultant.
- `references/macro-business-mapping.md` — **Facilitating the client session**:
  Event Storming, capabilities, value stream, events & policies. Start here for
  any "map our business / model this process with the client" request.
- `references/domain-shared-lexicon.md` — **Speaking the client's language**: how
  the Domain-Shared Lexicon (the `dsl` skill) interlocks with mapping; what to
  capture as a term and when.
- `references/functional-requirements.md` — **The deliverable**: turning the map,
  the narratives, and the policies into functional requirements for an AI-driven
  organisation, including which steps become AI agents/automation.
- `references/virtualization-and-enrichment.md` — **Simulating without complete
  data, and the progressive-enrichment loop**: bootstrapping fakes from the model,
  ingesting Excels and sample payloads, converging the simulation on reality.
- `references/contracts-and-fakes.md` — Meso: contract-first design, the four
  contract types, virtualization tools, and the sync-to-async trick.
- `references/statecharts-and-state-explosion.md` — Micro: XState v5, Harel
  statecharts, and the math of why hierarchy/orthogonality tames complexity.
- `references/contract-drift-and-testing.md` — Continuous validation: drift
  detection suite, conformance metrics, CI gates.
- `references/round-trip-and-ddd-guardrails.md` — Atomic: keeping code and model
  in sync (AST round-trip, comment preservation) and DDD constraint guardrails.
- `references/tooling-matrix.md` — The full verified 2026 stack with selection
  guidance.
- `references/project-scaffold.md` — Repository layout and governance.
- `references/worked-example-ecommerce-return.md` — One business process
  (a product return) followed all the way down through the four levels.

## A note on tone

You are a consultant, not a code generator. Ask the business questions first.
Draw before you specify. Get something running on synthetic data early, and let
the client make it truer by feeding you what they have. When someone wants to skip
to the database schema, that's usually a sign the business picture isn't shared
yet — gently zoom back up. The goal is always a system everyone can see, agree on,
and trust before it's expensive to be wrong.
