# Atomic (L4): Round-Trip Sync and DDD Guardrails

At the Atomic level the fake is replaced by real production code, written inside
the node and stored in the project's Git repo. Who writes that code is an open
choice: an engineer, or — increasingly the default — an **AI coding app** handed
the capability's cube (requirements, contracts, statecharts, fakes, lexicon,
evidence register; see `references/functional-requirements.md`). The handoff
instruction is one sentence: *implement against these contracts and statecharts;
the acceptance criteria are your tests; change neither.* Everything in this file
applies to both kinds of implementer — an AI agent drifts from the model at
least as easily as a junior developer does, so the sync machinery and the
guardrails below are what keep either honest.

Two problems appear the moment code and model coexist: keeping them in sync
without clobbering hand-written code, and stopping implementers from quietly
violating the architecture agreed higher up. This file covers both.

## Keeping model and code in sync (round-trip engineering)

The canvas (model) and the source code must stay consistent in both directions
without either destroying the other's work.

### Model → Code (incremental, surgical)
When a visual change is made — renaming a state, adding a contract field — don't
regenerate the whole file (that erases hand-written code). Instead, use the
TypeScript Compiler API to locate the exact AST node affected and inject only the
new values, then save. This incremental transformation (a Vitruvius-style approach)
preserves everything the developer wrote around it.

### Code → Model (discovery and grouping)
When code is edited by hand, a watcher reparses it. Because one logical entity
(say a business component) can have its types, interfaces, and logic spread across
several files or AST nodes, run a **discovery-and-grouping pass** (inspired by the
ts2cpp compiler): walk the AST, collect all fragments belonging to the same entity,
consolidate them into one metadata structure, then diff that against the canvas via
a "domain mirror" and update node geometry/topology — without disturbing the
existing spatial arrangement the team is used to.

## Don't destroy comments and formatting

Naïve code generators strip comments, collapse intentional whitespace, and wreck
indentation — which makes engineers distrust and abandon the tool. Two techniques
prevent that:

**Full-fidelity parsing with trivia (Roslyn-style).** Treat every non-syntactic
character — comments, whitespace — as *trivia* attached to a real token. Each token
carries *leading trivia* (before it) and *trailing trivia* (after it). After
building the new AST during a refactor, run a pre-order and a post-order traversal
to re-attach each comment to the nearest node by character distance, so comments
land back exactly where the developer wrote them.

**Isolated sync regions (EGL-style).** For blocks the model owns, fence them with
anchor comments carrying the model node's unique ID:

```ts
export class TicketProcessor {
  private logger = new Logger('Tickets');   // hand-written, never touched

  public validateTicket(ticketId: string): boolean {
    //sync _bfpnGUbFEeqXnfGWlV2_8A, behavior
    // The model owns ONLY the lines between the anchors.
    if (ticketId === '') return false;
    return true;
    //endSync
  }
}
```

On write, the engine replaces only the text between `//sync <id>` and `//endSync`.
If a developer edits inside the region, the reverse process reads it back and
updates the model. Everything outside the anchors is sacrosanct.

## DDD guardrails: stop architectural drift at the source

The deeper risk is semantic: a developer at Atomic accidentally violating a design
rule set at Macro/Meso. Domain-Driven Design tactical patterns —
**Aggregate, Aggregate Root, Entity, Value Object** — become first-class modelling
primitives with rules attached, not just labels. A constraint engine checks every
mutation (visual change or code change, via its AST) against the rules in real
time.

Example rule: *an Entity inside one Aggregate may not hold a direct reference to an
Entity in another Aggregate; it must reference the other Aggregate Root by its
global ID.* If a developer writes code (or draws an edge) that creates an illegal
cross-aggregate reference, the engine:

1. rejects the mutation on the canvas,
2. raises a compile-time error in the editor, and
3. suggests a fix (e.g. convert the direct link into a Value Object holding the ID).

Other guardrails worth encoding: no public setters on Entities (mutate through
behaviour), invariants enforced at the Aggregate Root, Value Objects immutable. The
2026 research direction here ("constraint-based round-trip engineering for tactical
DDD") is explicitly about embedding expert architectural judgement into the tool so
that junior developers can't accidentally erode the design — the system teaches and
enforces the rules as you work.

## Why this matters to the whole method

Atomic is where all the upstream agreement could quietly unravel. Round-trip sync
keeps the picture honest as code is written; DDD guardrails keep the code honest
against the picture. Together they guarantee the property the whole methodology
promises: you can always trace a line of production code back up to the business
capability it serves, and nothing can drift away from that without the system
objecting.
