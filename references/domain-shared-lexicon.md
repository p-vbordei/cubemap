# Speaking the Client's Language: The Domain-Shared Lexicon

As you map a business, you also build its dictionary. Every business runs on
words that mean something specific inside that company, *Heat*, *Tap*,
*Charge* in a steel plant; *receipt K*, *report Y*, *the over-ten-grand rule*
in the example we keep using. The single fastest way to derail the work is for
the AI to quietly swap one of those words for a generic synonym and carry the
wrong meaning downstream into the requirements and the simulation.

The fix is a **Domain-Shared Lexicon**: a living glossary of the client's
terms that the AI builds during mapping and reloads every session, so it keeps
speaking the client's language exactly. This is handled by the companion
**`dsl` skill, Domain-Shared Lexicon** (github.com/p-vbordei/dsl). This file
explains how that skill interlocks with the mapping methodology; the `dsl`
skill itself owns the format, the matching logic, and the commands.

## How the two skills fit together

- **`cubemap` (this skill)** maps the business and surfaces terms as a natural
  by-product: every actor, artifact, event, and policy is named in the
  client's words.
- **`dsl`** captures, stores, and reloads those terms as a `LEXICON.md`, and warns
  you mid-conversation when a word is being used ambiguously.

Run them together. During a mapping session, whenever a distinctive term appears,
hand it to the lexicon. At the start of every later session, the lexicon loads
first so the AI is already fluent before mapping resumes.

## What to capture as a term

Not every word. That bloats the glossary and it stops fitting in context. Capture
a term when **any** of these is true:

- It's a noun the business uses for a thing it makes, handles, or tracks (an
  artifact, an aggregate-to-be): *receipt*, *report*, *Heat*, *return*.
- It names an event the business cares about: *Tapped*, *Report Approved*.
- It encodes a rule or threshold in shorthand: *"the over-ten-grand rule."*
- It's a word the client uses differently from its everyday meaning, these are
  the dangerous ones and the highest priority to record.
- You (the AI) were about to reach for a synonym. That hesitation is the signal a
  term needs pinning down.

For each, record what the `dsl` lexicon format expects: **Term | Definition |
Aliases to avoid**. The "aliases to avoid" column is doing the real work. It's
how you stop yourself drifting from *Tap* to *drain* to *pour* and losing
precision.

## Capturing terms in self-serve mode

When a non-technical manager is driving (see `references/guided-client-intake.md`), make term capture invisible and friendly. Don't say "I'm adding
this to the lexicon." Just ask the clarifying question naturally, *"when you
say 'heat,' you mean one furnace batch, right, not the temperature?"*, and
record the confirmed answer behind the scenes. Surface the glossary only when
you reflect the picture back, as a short "here are the words I'm using the way
you use them" list they can correct.

## Why this matters to the whole method

The lexicon is the thread that keeps all four zoom levels speaking the same
language. A term defined at Macro becomes a field name in a Meso contract, a state
in a Micro statechart, a type in Atomic code, and because they all trace to one
agreed definition, nobody downstream reinvents the meaning. Combined with the
contract (the single source of truth for *structure*), the lexicon is the single
source of truth for *meaning*. Together they're what let a business person and an
engineer look at the same model and read it the same way.
