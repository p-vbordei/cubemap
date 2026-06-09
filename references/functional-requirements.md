# The Deliverable: Functional Requirements for an AI-Driven Organisation

The client engagement produces a concrete deliverable: a set of **functional
requirements** that describe what the organisation does, the rules it follows, and
which parts AI and automation will run. This file explains how to turn the Macro
map, the written narratives, and the policies into that deliverable — and how to
keep it alive rather than letting it ossify into a document nobody reads.

The crucial idea: the requirements are *the same model* you simulate and build.
You are not writing a spec and then separately designing a system. The
capabilities, events, and policies you mapped at Macro **are** the requirements,
expressed in a form a stakeholder can sign off on. The map, the requirements, the
fakes, and the eventual code are four views of one model.

## What "AI-driven organisation" means here

For each step in a mapped process, you make an explicit decision about *who or
what performs it* in the target state:

- **Human** — judgment, relationship, or accountability that should stay with a
  person (e.g. approving a contentious refund).
- **AI agent** — reading, extracting, drafting, classifying, reconciling: the
  cognitive-but-routine work (e.g. reading receipt K and extracting the figures).
- **Deterministic automation** — fixed rules, integrations, calculations (e.g.
  routing a >€10,000 report to a manager, emailing Z).

The functional requirements record this allocation. They answer, per step: what
must happen, what triggers it, what it produces, what rules constrain it, and
whether a human, an agent, or plain automation owns it. That allocation is itself
a deliverable the client cares about — it's the picture of how their organisation
changes.

## Format: a self-contained HTML document

The requirements ship as **HTML, not Markdown** — `business-models/requirements.html`
(or one file per capability for large engagements). Use semantic HTML (`<section>`
per capability, `<article>` per process) styled in the house palette, with the
process pictures inline as SVG; for the stakeholder-facing walkthrough, build it
as the scrollytelling explainer (`assets/scrollytelling-template.html`). Like
every deliverable, it must be responsive (readable on a phone) and e-mail
sharable (one self-contained file, no external assets) — see
`references/visual-communication.md`. HTML is what lets the requirements carry
their own diagrams, read like the story they came from, and still be structured
enough for an AI coding app to consume.

## Structure of the requirements

Organise by capability (from Macro), and within each capability by process. Use
this template per capability (shown schematically here; render it as the
semantic HTML described above):

```
## Capability: <name>   (core | supporting)
Value it produces: <one line — what the business gets>

### Process: <name>
As-is narrative: <the prose from the Macro session, refined by enrichment>

Trigger: <the domain event or command that starts it>
Steps:
  1. <actor/agent/automation> — <action> — produces <artifact/event>
  2. ...
Outputs: <artifacts and the events they raise>
Recipients: <who/what consumes the outputs>

Rules (policies):
  - <whenever ... then ...>
Edge cases & hotspots:
  - <unreadable input, duplicates, thresholds, failures>

Execution model:
  - Step N: AI agent  (reads/extracts/drafts ...)
  - Step M: human     (approves ...)
  - Step K: automation (routes/calculates/sends ...)

Contracts touched: <openapi/asyncapi operations once Meso is reached>
Acceptance criteria: <observable, testable conditions of "done">
```

Acceptance criteria matter because they double as the assertions your simulation
and tests check. "A report over €10,000 is never approved by Z alone" is both a
requirement and a contract/statechart guard and a test case.

## Turning each input type into requirements

Whatever the client gives you maps into the structure above:

- **A conversation / discovery call** → narratives and policies (Macro). Tell it
  back, write it down, mark hotspots.
- **A description of a manual job** (the receipt example) → a process with steps,
  an execution model where the reading/extraction step is flagged as a candidate
  AI agent.
- **An Excel they export today** → the data shape of an artifact; columns become
  fields, value ranges become constraints, and the way they use the sheet reveals
  rules. Feeds both the contract and the requirements. (Parse with the `xlsx`
  skill.)
- **A receipt / form / report** → an input or output artifact with a field list;
  often reveals an extraction step that becomes an AI agent. (Parse with the `pdf`
  skill / OCR.)
- **Their existing software (X)** → an external system on the map; the integration
  becomes a contract; "what X can and can't do" becomes a constraint.
- **Tacit know-how** ("we never refund electronics after 14 days") → policies and
  acceptance criteria. The most valuable and easiest-to-lose input — capture it
  explicitly.

## Keep it live

The requirements improve on the same enrichment loop as the simulation (see
`references/virtualization-and-enrichment.md`). When a new Excel reveals a field
or a rule, you update the contract, the fake, *and* the requirement together, so
the three never disagree. Version the requirements alongside the contracts in the
repo (`business-models/`). When you can run the simulation and it satisfies the
acceptance criteria, the requirements are validated — not by review, but by a
working system the client has clicked through.

## The handoff: a cube an AI coding app can implement

The requirements are the top sheet of a larger package. When a capability is
fully mapped — requirements signed off, contracts defined, statecharts drawn,
fakes running, evidence registered — that capability is a **cube**: a
self-contained unit that can be handed to an implementer without a single
clarifying meeting. The realistic implementer is increasingly an **AI coding
app** (Claude Code, Lovable, v0, Cursor and the like), and the cube is exactly
the spec such a tool needs. A handoff cube contains:

- `requirements.html` — the functional requirements for this capability,
  including the human / AI / automation split and acceptance criteria.
- The relevant slice of `LEXICON.md` — so the implementation uses the client's
  words in code, UI, and data.
- `contracts/` — the OpenAPI/AsyncAPI specs for every boundary the cube touches.
  These are non-negotiable interfaces, not suggestions.
- `src/machines/` — the XState statecharts: the behaviour, with the business
  policies already encoded as guards.
- `fakes/` + seed data — the running simulation the implementation must be a
  drop-in replacement for, and the neighbours it must coexist with.
- `examples/` + the evidence register entries — the real client artifacts the
  data shapes came from.

The instruction to the AI coding app is then one sentence: *"implement this
capability against these contracts and statecharts; the acceptance criteria are
your tests; do not change either."* Because each cube speaks to the rest of the
system only through its contracts, cubes can be implemented independently, by
different tools, in any order — and the organisation is redeployed, cube by
cube, as an AI-native one.

## Sign-off without a 90-page document

The strongest sign-off isn't a stakeholder reading prose — it's the stakeholder
walking the *running simulation* of their own process and agreeing it's right.
Keep the written requirements tight and structured (the template above), and let
the live fakes carry the weight of demonstrating behaviour. The document records
the decisions; the simulation proves them.
