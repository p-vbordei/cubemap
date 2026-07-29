# Documenting As You Go: Decisions, Proof, and the Living Record

The enrichment loop keeps the *model* converging on reality. This file is
about keeping the *record* converging on the model, in the same motion, in the
same session, never "later."

The skill already insists that every client artifact is registered as
evidence. But two more kinds of things happen in a working session that leave
no file behind unless you deliberately write them down: **decisions** ("let's
lower the approval threshold to €5,000") and **proof** (the client walked the
running simulation and said "yes, that's right"). Both are as load-bearing as
any Excel the client hands over: a policy exists because someone decided it,
and a cube can only be handed off because someone confirmed it. So both get
recorded the day they happen, with the same discipline as the evidence
register.

**The rule in one line: a working session never ends with the model ahead of
the documents.**

## The three records

| What happened in the session | Where it is recorded |
|------------------------------|----------------------|
| The client handed over an artifact (Excel, template, receipt, screenshot) | The **evidence register**: `examples/register.html` |
| A decision changed the model (a policy, a threshold, scope, the human/AI split, a contract shape) | The **decision log**: `business-models/decisions.html` |
| The model was proven right (a walkthrough confirmed, a test suite passed, production verified) | The **proof block** of the affected process in `business-models/requirements.html` |

Artifacts are *things*, decisions are *choices*, proof is *events*. Don't bend
one record to hold another: a walkthrough confirmation is not an artifact and
doesn't belong in the evidence register.

## The decision log

Most model changes don't arrive as files; they arrive as a sentence in a
meeting. Three weeks later nobody can say *why* the threshold is €5,000, and
the business reason (the thing this whole method exists to preserve) is gone.
The decision log is the fix: `business-models/decisions.html`, a self-contained
HTML deliverable like every other (responsive, e-mail sharable), one short
entry per decision:

- **Date** and **who decided** (a name, not a team).
- **What changed**, as from → to ("manager-approval threshold: €10,000 → €5,000").
- **The business reason, in the client's words** ("finance wants tighter spend
  control after the Q1 audit").
- **What it rippled to**: the views updated as a consequence: which policy,
  requirement, contract operation, fake, statechart guard.
- **A link to the evidence**, if an artifact prompted it (register entry id).

Log a decision when it changes a policy, a contract shape, scope, the
human/AI/automation split, or an acceptance criterion. Don't log keystrokes:
the log is for choices the client would want explained back to them, five
lines each, cheap enough to write in the room. There is no dedicated template:
the decision log is a register like the evidence register, so adapt
`assets/evidence-register-template.html`, same plain semantic-HTML style, one
entry per decision.

## Proof: recording that the model was shown to be right

An acceptance criterion matures the way a fake does, and its proof status
should be readable at every stage:

| Rung | What it means | What you record |
|------|---------------|-----------------|
| **Stated** | Written into the requirements | The criterion itself |
| **Demonstrated** | A stakeholder walked the running simulation and confirmed it | Date, who walked it, what they confirmed, what they corrected |
| **Contract-tested** | Encoded as an automated check (Schemathesis / Pact / statechart test) running in CI | The suite and the run |
| **Verified in production** | The real implementation satisfies it live | Date and how observed |

Proof lives **with the criterion**, in a proof block per process inside
`business-models/requirements.html`, so whether a capability is ready to ship
as a cube is readable from one place. A criterion with no proof status is just
an intention.

Walkthroughs deserve special care because the skill treats them as the real
sign-off ("the stakeholder walking the running simulation", see
`references/functional-requirements.md`). A sign-off that was never written
down cannot gate a cube handoff. So every re-walk with the client ends with a
line in the proof block: who walked it, when, what they confirmed, and what
they corrected, because the corrections feed straight back into the enrichment
loop.

## The ripple rule

The map, the requirements, the simulation, and the system are four views of one
model, which means **a change to any view is a change to all views, in the
same session.** The full sync set, wider than the contract–fake–requirement
triple:

- the **policy** and the **requirement** (rules, execution model, acceptance criteria)
- the **narrative** prose, if it mentions what changed
- the **contract** (and its named `examples`)
- the **fake** and its seed data (including cases on both sides of a new boundary)
- the **statechart guard** that encodes the rule
- the **lexicon entry**, if a definition embeds what changed

The practical move: before ending the session, search the repo for the old
value or term. A stale `amount > 10000` in a statechart guard is not a typo.
It's a lower level contradicting a higher one, the exact thing the zoom levels
forbid. The ripple covers every view that *exists*: a view not yet built (a
process with no statechart yet, say) can't disagree; name the gap as a hotspot
instead of inventing the view just to update it.

## The session-close checklist

Before ending any working session that touched the model:

1. **Ripple**: the old value/term is grepped out; requirements, narratives,
   contracts, fakes, statecharts, and lexicon agree.
2. **Decisions** of the session are in the decision log, with their business
   reasons.
3. **Proof events**: walkthroughs, sign-offs, passing runs, are in the proof
   blocks.
4. **Artifacts** received are in the evidence register, with "what it changed"
   filled in.
5. **New terms** are in the lexicon.
6. **Open questions and hotspots** are current, resolved ones closed, new ones
   named.
7. **Every deliverable touched this session passes the gate**: open it with
   `#audit` (see `assets/deliverable-gate.html`) and read the panel. A document
   that looks right to the person who wrote it has not been checked.

The test: someone who wasn't in the room could reconstruct today's session
from the repo alone.

## Why not "document it at the end"

| The excuse | The reality |
|------------|-------------|
| "We'll write it up at the end" | The end is when memory is worst. Closeout becomes forensic reconstruction instead of an afternoon's assembly (see `references/project-closeout.md`). |
| "The meeting notes / chat history have it" | They aren't in the repo, don't travel with the cube, and the implementer (human or AI coding app) will never see them. |
| "It's just a parameter change" | A parameter in a policy *is* a policy change. It has a business reason, and it ripples into contracts, fakes, and guards. |
| "The client confirmed it verbally" | An unrecorded sign-off can't gate a handoff. Same rule as the register: what isn't written down didn't happen. |

Done as you go, each record costs minutes. The payoff comes twice: every
session, because the model never silently disagrees with itself, and at the
end of the project, because closeout (`references/project-closeout.md`)
becomes a short assembly pass over records that already exist.
