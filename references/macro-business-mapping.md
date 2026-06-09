# Macro (L1): Mapping a Business Process With the Client

This is the heart of the method and the part most teams skip. Before any system,
contract, or line of code exists, you sit with the people who actually run the
business and draw how it works — on a shared canvas, out loud, together. Everything
downstream is only as good as this picture.

The goal of a Macro session is a map the client looks at and says: *"yes, that's
how it actually works."* Not how it should work in theory, not how the brochure
says it works — how it really happens, including the messy manual parts.

## Start from the narrative, not from boxes

People don't naturally describe their business as architecture. They describe it
as a story about who does what. Your job is to capture that story faithfully and
then formalize it. So begin by getting them to narrate the *as-is* process in
plain language:

> "An employee receives a file that looks like **K** — it's a receipt. She reads
> it, keys the figures into **software X**, which produces **report Y**. She prints
> Y and emails it to **Z** for approval."

That single sentence is dense with structure. Pull it apart on the canvas:

| Element in the story | What it is on the map | From the example |
|----------------------|-----------------------|------------------|
| **Who** does the work | Actor / role | the employee |
| **What they read/receive** | Input artifact | receipt "K" |
| **The action** | Command / task | "process the receipt" |
| **Where they do it** | System / tool | software X |
| **What comes out** | Output artifact | report Y |
| **What it triggers** | Domain event | "Report Generated" |
| **Who receives it** | Downstream actor | Z (approver) |
| **The channel** | Integration | print + email |

Every business process decomposes into this shape: *an actor handles an artifact,
takes an action in a system, which produces an artifact and an event, which flows
to someone else.* Keep asking "and then what happens?" and "who needs to know?"
until the chain reaches a natural end (an outcome the business cares about) or
loops back.

## The Event Storming pass

Once the narrative is on the canvas, formalize it with a lightweight Event
Storming vocabulary. You don't need the full workshop ceremony — you need the
primitives, color-coded so the client can read the flow:

- **Domain events** (past tense): the things that happen and matter to the
  business — *Receipt Received*, *Figures Entered*, *Report Generated*,
  *Report Approved*. These are the backbone; lay them left-to-right in time order.
- **Commands**: the actions that cause events — *Enter Figures*, *Generate Report*.
- **Actors**: who issues the command — the employee, the approver Z.
- **Policies** (the rules): "*whenever* a report is over €10,000, it must be
  approved by a manager, not Z." Policies are reactive rules — "whenever X then Y."
  They are gold; they become guards in your state machines later.
- **Read models / artifacts**: the things actors look at to decide — the receipt,
  the report, a dashboard.
- **External systems**: anything outside the boundary — the email server, a bank
  portal, software X if it's third-party.
- **Hotspots**: mark disagreements, unknowns, and pain points in a loud color.
  "Nobody's sure what happens if the receipt is unreadable" is a hotspot, and
  hotspots are where the real value of the session lives.

## From process to business capabilities

A long event chain is the *flow*. Now group it into **capabilities** — the
durable, named chunks of "what this business is able to do." Receipt Received →
Figures Entered might belong to a *Document Intake* capability; Report Generated →
Report Approved to a *Reporting & Approval* capability. Capabilities become the
top-level opaque blocks on the canvas — the country-level view. At Macro you show
only capabilities and the events flowing between them. No tech. No databases. No
APIs. If someone says "we'll use Postgres for that," note it and zoom back up —
it's not time yet.

## The value stream

Across the top, trace the **value stream**: where does value (and money, and
risk, and responsibility) enter, move, and exit? In the receipt example the value
is "an approved, auditable financial report." Knowing the value stream tells you
which capabilities are core (differentiating, worth automating well) versus
supporting (necessary but commodity). This is the conversation that keeps
engineering effort pointed at what the business actually cares about.

## Capture as you go — write it down, tell it back

Macro is not a silent drawing exercise. As the map takes shape, narrate it back to
the client: *"so a receipt comes in, Maria keys it into X, X spits out a report,
she prints it and emails Z — and if it's over ten grand it goes to a manager
instead. Did I get that right?"* The act of telling it back surfaces corrections
instantly. Write the agreed narrative down alongside the canvas — a short prose
description per capability. This written process is a living artifact: you will
keep refining it as examples arrive (see
`references/virtualization-and-enrichment.md`), tightening the wording until it's
precise enough to build from.

## What you leave the session with

- A canvas of capabilities and the domain events between them, in time order.
- A written narrative of each capability's as-is process (actor → artifact →
  action → system → output → recipient).
- A list of policies (the "whenever/then" rules).
- A marked set of hotspots: unknowns, disputes, and pain points to resolve.
- A first read on the value stream and which capabilities are core.

That's enough to descend to Meso for the capability in focus — define its systems
and contracts, and stand up a simulation — *without* needing every example or
detail nailed down yet. The gaps become things you fill by enrichment, not
blockers that stall the whole engagement.

## Questions that open a Macro session

- "Walk me through what happens, start to finish, the way it really goes today —
  who touches it, and in what order?"
- "What does the thing they're working on look like when it arrives? Can you show
  me one?" (This is also your first enrichment example.)
- "What has to be true before this step can happen? What's not allowed?"
- "Who needs to know when this is done? What do they do with it?"
- "Where does this go wrong, slow down, or end up in someone's inbox for days?"
- "If this whole thing worked perfectly, what would the business get out of it?"
