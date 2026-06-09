# cubemap — Infinite Zoom & Fakes-First

> A Claude skill for going to a client, mapping how their business actually works,
> and turning it into functional requirements for an AI-driven organisation — then
> a runnable simulation, then real code. Business first, visual throughout.

Most software work fails not because the code is bad, but because nobody ever drew
the business clearly enough for everyone — client included — to point at it and
say *"yes, that's how it works."* cubemap is built to prevent exactly that.
It guides a conversation from business strategy down to running code, like Google
Maps for a system: zoom out to whole business capabilities, zoom in to a single
function, and at every altitude the picture still makes sense and still connects to
the one above it.

## The idea in one minute

- **Map the business with the people who run it.** Event Storming on a shared
  canvas: capabilities, the events between them, and the rules they live by — in
  the client's own words.
- **Virtualize early — you don't need all the data first.** The moment a contract
  exists, stand up a *fake* that obeys it and serves synthetic data. The whole flow
  is clickable before anything is built.
- **Enrich progressively.** As real examples arrive (an Excel export, a receipt, a
  report), feed them in; the simulation and the written-up process converge on
  reality, iteration by iteration.
- **Make illegal states impossible.** Business rules become statechart guards and
  contract constraints — something the system *cannot* violate, not something a
  developer must remember.
- **Keep one source of truth for meaning and structure.** A shared lexicon of the
  client's vocabulary + a single contract per boundary keep business and code
  reading the same model.

## The four zoom levels

| Level | Name | Whose view | Produces |
|-------|------|-----------|----------|
| **L1** | Macro | CEO / domain expert | Business capabilities, events, policies — the "why" |
| **L2** | Meso | Architect | Systems (C4), contracts, **live fakes** on synthetic data |
| **L3** | Micro | Engineer / UX | Behaviour as statecharts, wired to the fakes |
| **L4** | Atomic | Engineer | Real code that honours the contracts & statecharts above |

## Two ways to use it

- **Facilitated** — a consultant or architect runs it as a playbook with the client.
- **Self-serve by a client manager** — hand the skill to a non-technical department
  manager and say *"talk to the AI until your processes are mapped."* The AI becomes
  the facilitator: it leads, they answer, and it keeps going until the map, the
  requirements, and the lexicon are complete. See
  [`references/guided-client-intake.md`](references/guided-client-intake.md).

## Communicate visually, as a story

Explanations are **purpose-built SVG**, not Mermaid or walls of text, designed to
match how people actually read — one idea, one picture, a story spine. The
preferred deliverable format is a **scrollytelling editorial explainer** (warm
paper palette, semantic colours, dark mode, scroll-reveal). A ready-to-adapt
scaffold ships at
[`assets/scrollytelling-template.html`](assets/scrollytelling-template.html). See
[`references/visual-communication.md`](references/visual-communication.md).

## Companion skill: Domain-Shared Lexicon

cubemap builds the client's vocabulary as it maps; the
[**`dsl` skill — Domain-Shared Lexicon**](https://github.com/p-vbordei/dsl) stores
and reloads it as a `LEXICON.md` so the AI always speaks the client's exact terms.
Use the two together.

## What's inside

```text
cubemap/
├── SKILL.md                       # the skill: philosophy, 4-level workflow, tooling
├── assets/
│   └── scrollytelling-template.html   # editorial explainer scaffold
└── references/                    # loaded on demand, by zoom level
    ├── guided-client-intake.md        # self-serve mode for a client manager
    ├── macro-business-mapping.md      # L1 — Event Storming the business
    ├── domain-shared-lexicon.md       # speaking the client's language
    ├── functional-requirements.md     # the deliverable
    ├── virtualization-and-enrichment.md  # simulate without complete data
    ├── contracts-and-fakes.md         # L2 — contracts + virtualization
    ├── statecharts-and-state-explosion.md  # L3 — XState behaviour
    ├── contract-drift-and-testing.md  # continuous validation
    ├── round-trip-and-ddd-guardrails.md   # L4 — code↔model sync
    ├── tooling-matrix.md              # the verified 2026 stack
    ├── project-scaffold.md            # repo layout & governance
    └── worked-example-ecommerce-return.md  # one process, all four levels
```

## Install

**Claude Cowork:** open the packaged `.skill` bundle and click *Save skill*.

**Claude Code:** copy the skill into your config and start a new session:

```bash
git clone https://github.com/p-vbordei/cubemap.git ~/src/cubemap
mkdir -p ~/.claude/skills/cubemap
cp -R ~/src/cubemap/SKILL.md ~/src/cubemap/references ~/src/cubemap/assets \
      ~/.claude/skills/cubemap/
```

Skills are auto-discovered from `~/.claude/skills/<name>/SKILL.md` on the next
session. Then just describe a business process and ask the AI to map it.

## License

[Apache License 2.0](LICENSE) — Vlad Bordei
