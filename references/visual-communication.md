# Visual Communication: SVG-First, Cognition-Aligned, Story-Driven

This methodology lives or dies on whether people *see* the design. A diagram that
a client can read at a glance does more than ten pages of prose. So almost every
explanation you produce (to a client, a stakeholder, or a teammate) should be a
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
  first, usually the actor or the triggering event. Make it the largest / boldest
  / most saturated element; everything else is supporting cast.
- **Flow follows reading order.** Time and causality go left-to-right or top-down,
  the way the audience reads. An arrow that runs backwards against reading order
  should be rare and meaningful (a loop, a rejection).
- **Encode with pre-attentive features.** Colour, size, and position are processed
  before conscious thought. Use them consistently: one colour = business events,
  another = systems, another = humans, another = AI. Don't decorate; encode.
- **Chunk to ~5–7 elements.** Working memory is small. If a level has more
  than a handful of moving parts, that's the signal to zoom, show the
  capability now, reveal its insides on the next picture. This is the Infinite
  Zoom principle applied to the *explanation*, not just the canvas.
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

> **Someone** (a named actor) (**wants something / something happens** (an
event) >) **and here's what unfolds, and what's at stake** (consequence).

"Maria gets a receipt at 9am. Today she keys it into the old system by hand
and it takes twenty minutes; here's where that goes wrong." Then show the
picture of exactly that. Use the client's own nouns: their roles, their
documents, their software's name, so the story is unmistakably *theirs*.
Narrate the diagram back to them out loud; the gap between your story and
their reality is where the real requirements hide.

Concretely, when you present anything:

1. Open with one or two sentences of story (a named someone, a moment, a stake).
2. Show the SVG that *is* that moment, focal point on the actor/event.
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
- **Hotspot / unknown:** loud, unmissable colour (e.g. magenta), these should
  draw the eye precisely because they're unresolved.

When rendering an actual interactive map, that vocabulary becomes React Flow custom
nodes (see `references/tooling-matrix.md`); when rendering a still explanation,
it becomes composed SVG. Same vocabulary, two media.

## House style: the scrollytelling editorial explainer

When you produce a standalone explainer (a requirements walkthrough, a worked
example, a "here's how your process maps" deliverable) the preferred format is
a **scrollytelling article**: a long-form, single-page scroll narrative where
the reader advances the story by scrolling, and each *scene* pairs **one idea
with one visual**. Think NYT / The Pudding explainers or a good product-launch
page, not a dashboard. The format carries the methodology's whole philosophy
(one idea at a time, picture-led, story-driven) in the artifact itself.

A ready-to-adapt, self-contained implementation ships with this skill at
**`assets/scrollytelling-template.html`** (a real example: the "energy-cube"
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
- **Automatic dark mode** via `@media (prefers-color-scheme:dark)` overriding
  the same CSS variables: the one document reads well in both themes.
- **Progressive reveal on scroll.** A thin **top progress bar** (`#bar`) tracks
  scroll; a **right-side dot rail** (`#rail`) marks scenes and highlights the active
  one; `.reveal` elements fade/rise in via an `IntersectionObserver` as they enter
  the viewport. Two observers: one (`threshold 0.12`) adds `.in` to reveal the
  content, one (`threshold 0.5`) lights the active rail dot. That gentle motion is
  what makes it feel alive without becoming a slideshow.

The discipline behind the polish is the same as everywhere else: one scene = one
idea + one picture, colour and motion used to mean things, and a story spine the
reader rides top to bottom.

## Nothing may spill its box

The most common defect in hand-composed SVG is a label that does not fit: text
wider than the rectangle around it, or a caption pushed past the edge of the
`viewBox` and silently cropped. It happens because the author places text at a
coordinate and *estimates* its width, and the estimate is wrong the moment the
reader's font stack differs. On a client deliverable it reads as carelessness and
costs more trust than the diagram earns.

Compose so it cannot happen, then measure to prove it did not:

- **Size the box from the label, never the label from the box.** At font size
  `F`, a lower-case sans label runs about `0.55 × F` per character, and about
  `0.62 × F` bold or upper-case. Give a box `width ≥ chars × 0.55 × F + 24` so
  there is real padding on both sides. A 15-character label at 12px needs about
  125 units, not 92.
- **Centre arithmetically, not by eye.** With `text-anchor="middle"`, x is
  exactly `rect.x + rect.width / 2`. Two labels stacked in one box share that x.
  Any label whose x you had to nudge is a label whose box is the wrong size.
- **Anchor by position.** `start` on the left edge, `middle` in the centre,
  `end` on the right edge. A `start`-anchored label near the right edge of the
  `viewBox` is the classic overflow.
- **Leave a margin inside the `viewBox`.** Keep everything at least 8 units from
  every edge, and remember that a caption *under* a shape must be inside the
  `viewBox` height. Extend the height rather than tucking the caption up.
- **A label that will not fit is usually too long, not the box too small.**
  Shortening it is the better fix, and it is the same discipline as
  `references/plain-language.md`. Widening a box to hold a sentence is how a
  diagram becomes a paragraph with borders.
- **Do not reach for `textLength` to squeeze text into a box.** It fits by
  distorting letter spacing, which looks worse than the overflow it hides.

Then verify by measurement, because estimation is what caused the bug. The
deliverable gate (below) walks every `<text>` in the document with `getBBox()`,
expresses it in `viewBox` coordinates, and reports any label that leaves its
`viewBox` or overflows the shape it sits in, in pixels. Fix until it is silent.

## The PDF must look like the HTML

Clients print deliverables and attach the PDF to things. A scrollytelling page
that is beautiful on screen prints, by default, as **blank pages**, and nobody
notices until a client asks why the document they forwarded is empty. Five
separate causes, all of them invisible on screen:

1. **Scroll-reveal never fires.** `.reveal { opacity: 0 }` waits for an
   `IntersectionObserver` that does not run during printing, so the content is
   there and invisible. This alone empties the whole document.
2. **Dark mode follows you onto paper.** If the reader's OS is dark,
   `prefers-color-scheme: dark` applies while printing, and light text lands on a
   background the printer drops.
3. **Chrome drops CSS backgrounds** unless `print-color-adjust: exact` is set, so
   colour that carried meaning disappears.
4. **`vh` heights resolve against the page,** and a chapter set to `min-height:
   62vh` claims most of a sheet.
5. **Fixed elements**: the progress bar, the dot rail, repeat on every page.

The fix is a print stylesheet, not a PDF pipeline. It ships in
`assets/deliverable-gate.html`; paste it into every deliverable, last in
`<head>` so it wins over the dark-mode block. To produce the PDF, use the
browser that already renders the page, `Cmd/Ctrl+P → Save as PDF`, or headless
for a script:

```sh
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless --disable-gpu --no-pdf-header-footer \
  --print-to-pdf=deliverable.pdf "file:///abs/path/deliverable.html"
```

A blank or near-blank PDF has a file size to match. If the PDF of a diagram-
heavy explainer is a few kilobytes, it is empty, check before sending.

## The deliverable gate

Four properties are claimed by every deliverable this method produces, and
prose alone does not hold them. `assets/deliverable-gate.html` carries two
blocks (the print stylesheet and a dependency-free audit script) that turn the
claims into a check any reader can re-run:

| Claim | Checked by |
|---|---|
| The PDF looks like the HTML | the `@media print` block |
| No label spills its shape or its `viewBox` | `getBBox()` over every `<text>` |
| One self-contained file, e-mail sharable | every `src`/`href`/`url()` must be inline |
| Readable on a phone | nothing wider than its container |
| Plain language | sentence length, semicolons, marketing words |

Paste both blocks into the deliverable, then open it with `#audit` on the end
of the URL. Do that at 375px in the browser's device toolbar. That run is the
only one that proves the phone layout. A deliverable goes out when the panel
is green.

## Deliverables are HTML: and they must travel well

Anything a human will read ships as **HTML, not Markdown**. A `.md` file can't
carry the inline SVG, the colour semantics, or the dark mode, and it renders as
raw text when a business person opens it. Markdown stays only for machine-loaded
internals (the skill's own files, `LEXICON.md`). Beyond format, every HTML
deliverable must satisfy two transport requirements, because the realistic path
of a deliverable is *attached to an e-mail and opened on whatever device is at
hand*:

- **Responsive, phone and laptop alike.** A `viewport` meta tag, a fluid
  single-column layout (a max-width column that collapses gracefully), type in
  `rem`/`clamp()` rather than fixed pixels, and SVGs sized by `viewBox` +
  percentage width so diagrams scale down legibly. Before delivering, check
  the document at ~375px width: every diagram readable, no horizontal scroll.
- **E-mail sharable: one self-contained file.** All CSS and JS inline, all
  diagrams inline SVG, no external images, fonts, or CDNs, no build step, no
  server. The file must survive being forwarded three times and double-clicked
  from a Downloads folder, offline, and still look right. Fonts come from
  system stacks, and any raster image is a `data:` URI. The only external
  thing allowed is an `<a href>` a reader may click; nothing may *load* from
  the network. Keep the file under about 2 MB so it survives every mail
  gateway.

  "E-mail sharable" here means **attached to an e-mail**, not pasted into the
  message body. Mail clients strip `<style>` and `<script>` from a body, which
  would rule out the entire house style. If someone needs it in the body, send a
  short summary in the body and attach the document.

The shipped `assets/scrollytelling-template.html` and
`assets/evidence-register-template.html` already have these properties, and both
carry the deliverable gate; when adapting either one, keep all of it.

## Practical note for the AI using this skill

Prefer rendering visuals as inline SVG widgets when the channel supports it.
For a standalone deliverable, build the scrollytelling explainer above by
adapting `assets/scrollytelling-template.html`, keep its palette, dark-mode
variables, progress bar, dot rail, `IntersectionObserver` reveal, and the two
gate blocks; replace the scenes with the client's story. For simpler outputs,
generate `.svg` (or SVG embedded in HTML) over text diagrams. Treat any
request to "explain," "show," "map," or "walk through" as a request for a
picture-plus-story, not a paragraph.

Do not tell the person the deliverable is ready until you have run the gate
and seen it green. You composed the diagram, so your estimate of whether the
labels fit is the same estimate that put them where they are, only the
measurement counts.
