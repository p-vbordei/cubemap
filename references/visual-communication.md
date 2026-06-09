# Visual Communication: SVG-First, Cognition-Aligned, Story-Driven

This methodology lives or dies on whether people *see* the design. A diagram that
a client can read at a glance does more than ten pages of prose. So almost every
explanation you produce — to a client, a stakeholder, or a teammate — should be a
deliberately composed **SVG picture wrapped in a short story**, not a block of
text and not an auto-generated boxes-and-arrows dump.

## SVG, not Mermaid, not ASCII

Hand-compose SVG (or render the React Flow canvas). Avoid Mermaid, PlantUML, and
ASCII art for anything client-facing. Why the rule:

- **Control of meaning.** In SVG you decide exactly what is big, what is central,
  what is coloured, what is dimmed. Meaning is carried by those choices. Mermaid
  lays out boxes for you and flattens that meaning into uniform rectangles.
- **Cognition.** You can place the eye's entry point, control reading flow, and
  group things spatially the way the brain groups them. Auto-layout can't honour
  the story.
- **Polish.** A clean, intentional picture signals care and earns trust in a way a
  default-styled diagram never does.

(Mermaid is acceptable only as a quick internal sketch for a fellow engineer who
asked for it. Never as the artifact a client sees.)

## Design to match human cognition

People don't parse diagrams uniformly; attention and memory follow predictable
patterns. Compose for them:

- **One focal point.** Every picture has a single thing the eye should land on
  first — usually the actor or the triggering event. Make it the largest / boldest
  / most saturated element; everything else is supporting cast.
- **Flow follows reading order.** Time and causality go left-to-right or top-down,
  the way the audience reads. An arrow that runs backwards against reading order
  should be rare and meaningful (a loop, a rejection).
- **Encode with pre-attentive features.** Colour, size, and position are processed
  before conscious thought. Use them consistently: one colour = business events,
  another = systems, another = humans, another = AI. Don't decorate; encode.
- **Chunk to ~5–7 elements.** Working memory is small. If a level has more than a
  handful of moving parts, that's the signal to zoom — show the capability now,
  reveal its insides on the next picture. This is the Infinite Zoom principle
  applied to the *explanation*, not just the canvas.
- **Progressive disclosure.** Reveal in layers: the whole flow greyed out, then
  the one step you're talking about lit up. Don't show the finished complex diagram
  cold.
- **Consistency across pictures.** The same concept keeps the same shape and colour
  every time it appears, at every zoom level. A "receipt" looks like a receipt on
  the Macro map and on the Micro screen. Visual constancy is what lets people hold
  the model in their head.

## Lead with a story

A diagram explains structure; a story explains *why anyone should care*. Pair them.
The reliable structure is the smallest possible narrative:

> **Someone** (a named actor) — **wants something / something happens** (an event)
> — **and here's what unfolds, and what's at stake** (consequence).

"Maria gets a receipt at 9am. Today she keys it into the old system by hand and it
takes twenty minutes; here's where that goes wrong." Then show the picture of
exactly that. Use the client's own nouns — their roles, their documents, their
software's name — so the story is unmistakably *theirs*. Narrate the diagram back
to them out loud; the gap between your story and their reality is where the real
requirements hide.

Concretely, when you present anything:

1. Open with one or two sentences of story (a named someone, a moment, a stake).
2. Show the SVG that *is* that moment — focal point on the actor/event.
3. Walk the flow in reading order, lighting up one step at a time.
4. End on the consequence or the question you need answered.

## A minimal visual vocabulary

Keep a consistent palette and shape set across the whole engagement (define it
once, reuse everywhere):

- **Actor (human):** rounded figure / warm colour. **AI agent:** same silhouette,
  distinct accent colour, small mark. **Automation:** gear/cog, neutral colour.
- **Business event:** consistent event colour (e.g. orange), past-tense label.
- **System / container:** rectangle, cool colour. **External system:** dashed
  border.
- **Artifact (receipt, report, file):** document glyph that looks like the real
  thing.
- **Policy / rule:** a callout in a rule colour, attached to the transition it
  governs.
- **Hotspot / unknown:** loud, unmissable colour (e.g. magenta) — these should
  draw the eye precisely because they're unresolved.

When rendering an actual interactive map, that vocabulary becomes React Flow custom
nodes (see `references/tooling-matrix.md`); when rendering a still explanation,
it becomes composed SVG. Same vocabulary, two media.

## House style: the scrollytelling editorial explainer

When you produce a standalone explainer — a requirements walkthrough, a worked
example, a "here's how your process maps" deliverable — the preferred format is a
**scrollytelling article**: a long-form, single-page scroll narrative where the
reader advances the story by scrolling, and each *scene* pairs **one idea with one
visual**. Think NYT / The Pudding explainers or a good product-launch page, not a
dashboard. The format carries the methodology's whole philosophy — one idea at a
time, picture-led, story-driven — in the artifact itself.

A ready-to-adapt, self-contained implementation ships with this skill at
**`assets/scrollytelling-template.html`** (a real example — the "energy-cube"
story). Start from it rather than rebuilding the scaffold. Its structure:

- **Editorial / magazine layout.** Large serif display headlines (`--serif`), a
  single centred ~820px column, generous whitespace, and a short uppercase
  **kicker** label above each scene ("CHAPTER ONE · THE OPPORTUNITY"). This is what
  makes it read as an *article*, not an app. Each scene is a `<section class="chap">`
  with a `.kick`, an `<h2>`, a `.lede`/`.cap`, and a `<figure>` holding the SVG.
- **Diagram-as-content.** Meaning is carried by hand-built **inline SVG** per scene,
  not paragraphs. Flat vector, no gradients, no shadows. Reusable SVG classes
  (`.box`, `.amber`, `.blue`, `.green`, `.clay`, `.violet`) keep diagrams
  consistent.
- **Flat minimalist styling, warm "paper" palette,** defined once as CSS variables
  and reused everywhere (never hard-code hex). Each colour *means something*,
  consistently across the whole piece:

  | Colour | Variable | Meaning |
  |--------|----------|---------|
  | Blue | `--blue` | data / information |
  | Amber | `--amber` | decide / act |
  | Green | `--green` | ok / valid / success |
  | Clay | `--clay` | danger / error / blocked |
  | Violet | `--violet` | spare 5th category (e.g. AI/automation) |

  Show a 1-line legend the first time colour carries meaning. Encode with colour;
  don't decorate.
- **Automatic dark mode** via `@media (prefers-color-scheme:dark)` overriding the
  same CSS variables — the one document reads well in both themes.
- **Progressive reveal on scroll.** A thin **top progress bar** (`#bar`) tracks
  scroll; a **right-side dot rail** (`#rail`) marks scenes and highlights the active
  one; `.reveal` elements fade/rise in via an `IntersectionObserver` as they enter
  the viewport. Two observers: one (`threshold 0.12`) adds `.in` to reveal the
  content, one (`threshold 0.5`) lights the active rail dot. That gentle motion is
  what makes it feel alive without becoming a slideshow.

The discipline behind the polish is the same as everywhere else: one scene = one
idea + one picture, colour and motion used to mean things, and a story spine the
reader rides top to bottom.

## Deliverables are HTML — and they must travel well

Anything a human will read ships as **HTML, not Markdown**. A `.md` file can't
carry the inline SVG, the colour semantics, or the dark mode — and it renders as
raw text when a business person opens it. Markdown stays only for machine-loaded
internals (the skill's own files, `LEXICON.md`). Beyond format, every HTML
deliverable must satisfy two transport requirements, because the realistic path
of a deliverable is *attached to an e-mail and opened on whatever device is at
hand*:

- **Responsive — phone and laptop alike.** A `viewport` meta tag, a fluid
  single-column layout (a max-width column that collapses gracefully), type in
  `rem`/`clamp()` rather than fixed pixels, and SVGs sized by `viewBox` +
  percentage width so diagrams scale down legibly. Before delivering, check the
  document at ~375px width: every diagram readable, no horizontal scroll.
- **E-mail sharable — one self-contained file.** All CSS and JS inline, all
  diagrams inline SVG, no external images, fonts, or CDNs, no build step, no
  server. The file must survive being forwarded three times and double-clicked
  from a Downloads folder, offline, and still look right.

The shipped `assets/scrollytelling-template.html` already has these properties;
when adapting it, keep them.

## Practical note for the AI using this skill

Prefer rendering visuals as inline SVG widgets when the channel supports it. For a
standalone deliverable, build the scrollytelling explainer above by adapting
`assets/scrollytelling-template.html` — keep its palette, dark-mode variables,
progress bar, dot rail, and `IntersectionObserver` reveal; replace the scenes with
the client's story. For simpler outputs, generate `.svg` (or SVG embedded in HTML)
over text diagrams. Treat any request to "explain," "show," "map," or "walk
through" as a request for a picture-plus-story, not a paragraph.
